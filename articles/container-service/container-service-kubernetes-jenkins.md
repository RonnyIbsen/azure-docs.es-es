---
title: CI/CD de Jenkins con Kubernetes en Azure Container Service | Microsoft Docs
description: "Cómo automatizar un proceso de CI/CD con Jenkins para implementar y actualizar una aplicación en contenedor en Kubernetes en Azure Container Service"
services: container-service
documentationcenter: 
author: chzbrgr71
manager: johny
editor: 
tags: acs, azure-container-service, jenkins
keywords: Docker, contenedores, Kubernetes, Azure, Jenkins
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: briar
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 3d206ebb6deeaa40f8e792ec12304c99c0abe684
ms.lasthandoff: 03/29/2017


---

# <a name="jenkins-integration-with-azure-container-service-and-kubernetes"></a>Integración de Jenkins con Azure Container Service y Kubernetes 
En este tutorial, examinaremos el proceso de configurar la integración continua de una aplicación de varios contenedores en Kubernetes de Azure Container Service con la plataforma Jenkins. El flujo de trabajo actualiza la imagen del contenedor en Docker Hub y actualiza los pods de Kubernetes mediante una implementación. 

## <a name="high-level-process"></a>Proceso de alto nivel
Los pasos básicos que se detallan en este artículo son: 
- Instalación de un clúster de Kubernetes en Container Service
- Configuración de Jenkins y configuración del acceso a Container Service
- Creación de un flujo de trabajo de Jenkins
- Prueba del proceso de CI/CD de extremo a extremo

## <a name="install-a-kubernetes-cluster"></a>Instalación de un clúster de Kubernetes
    
Implemente el clúster de Kubernetes en Azure Container Service mediante los siguientes pasos. La documentación completa se encuentra [aquí](container-service-kubernetes-walkthrough.md).

### <a name="step-1-create-a-resource-group"></a>Paso 1: Creación de un grupo de recursos
```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus

az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="step-2-deploy-the-cluster"></a>Paso 2: Implementación del clúster
> [!NOTE]
> Para realizar los siguientes pasos se requiere una clave pública SSH local almacenada en la carpeta ~/.ssh.
>

```azurecli
RESOURCE_GROUP=my-resource-group
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name

az acs create \
--orchestrator-type=kubernetes \
--resource-group $RESOURCE_GROUP \ 
--name=$CLUSTER_NAME \
--dns-prefix=$DNS_PREFIX \ 
--ssh-key-value ~/.ssh/id_rsa.pub \
--admin-username=azureuser \
--master-count=1 \
--agent-count=5 \
--agent-vm-size=Standard_D1_v2
```

## <a name="set-up-jenkins-and-configure-access-to-container-service"></a>Configuración de Jenkins y configuración del acceso a Container Service

### <a name="step-1-install-jenkins"></a>Paso 1: Instalación de Jenkins
1. Cree una máquina virtual de Azure con Ubuntu 16.04 LTS. 
2. Instale Jenkins mediante estas [instrucciones](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+on+Ubuntu).
3. Se puede encontrar un tutorial más detallado en [howtoforge.com](https://www.howtoforge.com/tutorial/how-to-install-jenkins-with-apache-on-ubuntu-16-04).
4. Actualice el grupo de seguridad de red de Azure para permitir el puerto 8080 y luego busque la IP pública en el puerto 8080 para administrar Jenkins en su explorador.
5. La contraseña de administración inicial de Jenkins está almacenada en /var/lib/jenkins/secrets/initialAdminPassword.
6. Instale Docker en la máquina de Jenkins mediante estas [instrucciones](https://docs.docker.com/cs-engine/1.13/#install-on-ubuntu-1404-lts-or-1604-lts). Esto permite la ejecución de comandos de Docker en trabajos de Jenkins.
7. Configure permisos de Docker para permitir que Jenkins acceda al punto de conexión.

    ```bash
    sudo chmod 777 /run/docker.sock
    ```
8. Instale la CLI de `kubectl` en Jenkins. Se pueden encontrar más detalles en [Installing and Setting up kubectl](https://kubernetes.io/docs/tasks/kubectl/install/) (Instalación y configuración de kubectl).

    ```bash
    curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

    chmod +x ./kubectl

    sudo mv ./kubectl /usr/local/bin/kubectl
    ```

### <a name="step-2-set-up-access-to-the-kubernetes-cluster"></a>Paso 2: Configuración del acceso al clúster de Kubernetes

> [!NOTE]
> Hay varios enfoques para llevar a cabo los pasos siguientes. Use el que le sea más sencillo.
>

1. Copie el archivo de configuración `kubectl` en la máquina de Jenkins.

    ```bash
    export KUBE_MASTER=<your_cluster_master_fqdn>
        
    sudo scp -3 -i ~/.ssh/id_rsa azureuser@$KUBE_MASTER:.kube/config user@<your_jenkins_server>:~/.kube/config
        
    sudo ssh user@<your_jenkins_server> sudo chmod 777 /home/user/.kube/config

    sudo ssh -i ~/.ssh/id_rsa user@<your_jenkins_server> sudo chmod 777 /home/user/.kube/config
        
    sudo ssh -i ~/.ssh/id_rsa user@<your_jenkins_server> sudo cp /home/user/.kube/config /var/lib/jenkins/config
    ```
        
2. En Jenkins valide que el clúster Kubernetes sea accesible.
    

## <a name="create-a-jenkins-workflow"></a>Creación de un flujo de trabajo de Jenkins

### <a name="prerequisites"></a>Requisitos previos

- Cuenta de GitHub para el repositorio de código.
- Cuenta de Docker Hub para almacenar y actualizar imágenes.
- Aplicación en contenedor que se puede volver a compilar y actualizar. Puede usar esta aplicación de contenedor de ejemplo escrita en Golang: https://github.com/chzbrgr71/go-web. 

> [!NOTE]
> Los siguientes pasos se deben realizar en su propia cuenta de GitHub. No dude en clonar el repositorio anterior, pero debe usar su propia cuenta para configurar los webhooks y el acceso a Jenkins.
>

### <a name="step-1-deploy-initial-v1-of-application"></a>Paso 1: Implementación de la v1 inicial de la aplicación
1. Compile la aplicación desde la máquina del desarrollador con los siguientes comandos. Reemplace `myrepo` por el suyo propio.
    
    ```bash
    git clone https://github.com/chzbrgr71/go-web.git
    cd go-web
    docker build -t myrepo/go-web .
    ```

2. Inserte la imagen en Docker Hub.

    ```bash
    docker login
    docker push myrepo/go-web
    ```

3. Implemente el clúster de Kubernetes.
    
    > [!NOTE] 
    > Edite el archivo `go-web.yaml` para actualizar la imagen del contenedor y el repositorio.
    >
        
    ```bash
    kubectl create -f ./go-web.yaml --record
    ```
### <a name="step-2-configure-jenkins-system"></a>Paso 2: Configuración del sistema Jenkins
1. Haga clic en **Manage Jenkins** >  (Administrar Jenkins) **Configure System** (Configurar el sistema).
2. En **GitHub**, seleccione **Add GitHub Server** (Agregar servidor de GitHub).
3. Deje **API URL** (Dirección URL de API) como valor predeterminado.
4. En **Credentials** (Credenciales), agregue una credencial de Jenkins mediante **texto secreto**. Se recomienda usar tokens de acceso personal de GitHub, que se configuran en la configuración de su cuenta de usuario de GitHub. Puede encontrar más detalles al respecto [aquí.](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)
5. Haga clic en **Test connection** (Probar conexión) para asegurarse de que la configuración es correcta.
6. En **Global Properties** (Propiedades globales), agregue una variable de entorno `DOCKER_HUB` y proporcione su contraseña de Docker Hub. (Esto es útil en esta demostración, pero en un escenario de producción sería necesario un enfoque más seguro).
7. Haga clic en Save (Guardar).

![Acceso a GitHub de Jenkins](media/container-service-kubernetes-jenkins/jenkins-github-access.png)

### <a name="step-3-create-the-jenkins-workflow"></a>Paso 3: Creación del flujo de trabajo de Jenkins
1. Cree un elemento de Jenkins.
2. Proporcione un nombre (por ejemplo, "go-web") y seleccione **Freestyle Project** (Proyecto de estilo libre). 
3. Marque **GitHub project** (Proyecto de GitHub) y proporcione la dirección URL a su repositorio de GitHub.
4. En **Source Code Management** (Administración del código fuente), proporcione la dirección URL y las credenciales del repositorio de GitHub. 
5. Agregue un **paso de compilación** de tipo **Execute shell** (Ejecutar shell) y use el siguiente texto:

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME .
    docker login -u <your-dockerhub-username> -p ${DOCKER_HUB}
    docker push $WEB_IMAGE_NAME
    ```

6. Agregue otro **paso de compilación** de tipo **Execute shell** (Ejecutar shell) y use el siguiente texto:

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    kubectl set image deployment/go-web go-web=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

![Pasos de compilación de Jenkins](media/container-service-kubernetes-jenkins/jenkins-build-steps.png)
    
7. Guarde el elemento de Jenkins y pruébelo con **Build Now** (Compilar ahora).

### <a name="step-4-connect-github-webhook"></a>Paso 4: Conexión del webhook de GitHub
1. En el elemento de Jenkins que creó, haga clic en **Configure** (Configurar).
2. En **Build Triggers** (Compila desencadenadores), seleccione **GitHub hook trigger for GITScm polling** (Desencadenador de enlace de GitHub para sondeo de GITScm) y haga clic en **Save** (Guardar). Esta operación configura automáticamente el webhook de GitHub.
3. En el repositorio de GitHub para go-web, haga clic en **Settings > Webhooks** (Configuración > Webhooks).
4. Compruebe que la dirección URL del webhook de Jenkins se agregó correctamente. La dirección URL debe finalizar con "github webhook".

![Configuración del webhook de Jenkins](media/container-service-kubernetes-jenkins/jenkins-webhook.png)

## <a name="test-the-cicd-process-end-to-end"></a>Prueba del proceso de CI/CD de extremo a extremo

1. Actualice el código del repositorio y realice la inserción y sincronización con el repositorio de GitHub.
2. En la consola de Jenkins, compruebe el **historial de compilación** y valide que el trabajo se ha ejecutado. Examine la salida de consola para ver los detalles.
3. En Kubernetes, vea los detalles de la implementación actualizada:

    ```bash
    kubectl rollout history deployment/go-web
    ```

## <a name="next-steps"></a>Pasos siguientes

- Implemente Azure Container Registry y almacene las imágenes en un repositorio seguro. Consulte [Documentación de Azure Container Registry](https://docs.microsoft.com/azure/container-registry).
- Cree un flujo de trabajo más complejo que incluya la implementación en paralelo y pruebas automatizadas de Jenkins.
- Para más información sobre CI/CD con Jenkins y Kubernetes, consulte el [blog de Jenkins](https://jenkins.io/blog/2015/07/24/integrating-kubernetes-and-jenkins/).

