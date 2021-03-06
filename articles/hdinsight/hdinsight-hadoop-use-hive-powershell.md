---
title: Uso de Hive de Hadoop con PowerShell en HDInsight | Microsoft Docs
description: Use PowerShell para ejecutar consultas de Hive en Hadoop en HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cb795b7c-bcd0-497a-a7f0-8ed18ef49195
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 897358e87937f9094d312cc88f630c81e7d46f4f
ms.contentlocale: es-es
ms.lasthandoff: 05/18/2017


---
# <a name="run-hive-queries-using-powershell"></a>Ejecución de consultas de Hive con PowerShell
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

En este documento se ofrece un ejemplo de uso de Azure PowerShell en el modo Grupo de recursos de Azure para ejecutar consultas de Hive en un clúster Hadoop en HDInsight.

> [!NOTE]
> Este documento no ofrece una descripción detallada de las instrucciones de HiveQL que se usan en los ejemplos. Para obtener información sobre el lenguaje HiveQL que se usa en este ejemplo, vea [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md).

**Requisitos previos**

* **Un clúster de Azure HDInsight**: no importa si el clúster está basado en Windows o en Linux.

  > [!IMPORTANT]
  > Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date).

* **Una estación de trabajo con Azure PowerShell**.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-hive-queries-using-azure-powershell"></a>Ejecución de consultas de Hive con Azure PowerShell

Azure PowerShell proporciona *cmdlets* que le permiten ejecutar de manera remota las consultas de Hive en HDInsight. Internamente, los cmdlets realizan llamadas de REST a la instancia de [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) del clúster de HDInsight.

Los siguientes cmdlets se utilizan al ejecutar las consultas de Hive en un clúster de HDInsight remoto:

* **Add-AzureRmAccount**: autentica a Azure PowerShell en la suscripción de Azure.
* **New-AzureRmHDInsightHiveJobDefinition**: crea una nueva *definición de trabajo* con las instrucciones HiveQL especificadas.
* **Start-AzureRmHDInsightJob**: envía la definición del trabajo a HDInsight, inicia el trabajo y devuelve un objeto de *trabajo* que se puede usar para comprobar el estado del trabajo.
* **Wait-AzureRmHDInsightJob**: usa el objeto de trabajo para comprobar el estado del trabajo. Esperará hasta que el trabajo se complete o se supere el tiempo de espera.
* **Get-AzureRmHDInsightJobOutput**: se usa para recuperar la salida del trabajo.
* **Invoke-AzureRmHDInsightHiveJob**: se usa para ejecutar instrucciones de HiveQL. Este cmdlet bloqueará la consulta completa, a continuación, devuelve los resultados.
* **Use-AzureRmHDInsightCluster**: establece el clúster actual para usarlo para el comando **Invoke-AzureRmHDInsightHiveJob**.

Los pasos siguientes muestran cómo usar estos cmdlets para ejecutar un trabajo en el clúster de HDInsight:

1. Mediante un editor, guarde el código siguiente como **hivejob.ps1**.

    [!code-powershell[main](../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Abra un nuevo símbolo del sistema de **Azure PowerShell** . Cambie los directorios a la ubicación del archivo **hivejob.ps1** y, a continuación, utilice el siguiente comando para ejecutar el script:

        .\hivejob.ps1

    Cuando se ejecute el script, se le pedirá que escriba el nombre del clúster y las credenciales de la cuenta de administrador/HTTPS del clúster. Puede que también se le pida que inicie la sesión en su suscripción de Azure.

3. Cuando el trabajo se complete, debe devolver información de manera similar a la siguiente:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Como se mencionó anteriormente **Invoke-Hive** puede utilizarse para ejecutar una consulta y esperar la respuesta. Use el siguiente script para ver cómo funciona Invoke-Hive:

    [!code-powershell[main](../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    La salida tendrá un aspecto similar al siguiente:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]
   > Para consultas de HiveQL más extensas, puede usar el cmdlet **Here-Strings** de Azure PowerShell o archivos de script de HiveQL. El siguiente fragmento de código indica cómo usar el cmdlet **Invoke-Hive** para ejecutar un archivo de script de HiveQL. El archivo de script de HiveQL debe cargarse en wasbs://.
   >
   > `Invoke-AzureRmHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Para más información acerca de **Here-Strings**, consulte <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Using Windows PowerShell Here-Strings</a> (Uso de Here-Strings de Windows PowerShell).

## <a name="troubleshooting"></a>Solución de problemas

Si no se devuelve ninguna información cuando se completa el trabajo, pudo haberse producido un error durante el procesamiento. Para ver información de error para este trabajo, agregue lo siguiente al final del archivo **hivejob.ps1** , guárdelo y, a continuación, ejecútelo de nuevo.

```powershell
# Print the output of the Hive job.
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Este cmdlet devuelve la información escrita en STDERR en el servidor cuando ejecute el trabajo.

## <a name="summary"></a>Resumen

Como puede ver, Azure PowerShell proporciona una manera fácil de ejecutar consultas de Hive en un clúster de HDInsight, de supervisar el estado del trabajo y de recuperar la salida.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información general acerca de Hive en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras maneras en que puede trabajar con Hadoop en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

