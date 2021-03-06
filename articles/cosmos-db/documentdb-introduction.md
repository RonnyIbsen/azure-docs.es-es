---
title: 'Azure Cosmos DB: API de DocumentDB | Microsoft Docs'
description: "Aprenda a usar Azure Cosmos DB para almacenar y consultar grandes volúmenes de documentos JSON con latencia baja mediante SQL y JavaScript."
keywords: base de datos json, base de datos de documentos
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 686cdd2b-704a-4488-921e-8eefb70d5c63
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/22/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 08fd1956d3c6499f059b138de22d3b104a9da6c1
ms.contentlocale: es-es
ms.lasthandoff: 05/31/2017


---
# <a name="introduction-to-azure-cosmos-db-documentdb-api"></a>Introducción a Azure Cosmos DB: API de DocumentDB

[Azure Cosmos DB](introduction.md) es un servicio de base de datos con varios modelos y distribución global de Microsoft para aplicaciones críticas. Azure Cosmos DB ofrece una [distribución global inmediata](distribute-data-globally.md), [escalado elástico de rendimiento y almacenamiento](partition-data.md) en todo el mundo, latencias inferiores a 10 milisegundos en el percentil 99, [cinco niveles de coherencia bien definidos](consistency-levels.md) y alta disponibilidad garantizada, todo ello respaldado por [acuerdos de nivel de servicio líderes del sector](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexa datos automáticamente](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sin que haya que ocuparse de la administración de esquemas ni de índices. Sigue varios modelos y es compatible con los modelos de datos de documento, de clave-valor, de gráfico y de columnas. 

![API de Azure DocumentDB](./media/documentdb-introduction/cosmosdb-documentdb.png) 

Con la API de DocumentDB, Azure Cosmos DB proporciona [funcionalidades de consulta SQL](documentdb-sql-query.md) enriquecidas y familiares con latencias bajas coherentes de datos JSON sin esquemas. En este artículo se proporciona información general sobre la API de DocumentDB de Azure Cosmos DB y sobre cómo puede usarla para almacenar volúmenes de datos JSON enormes, consultarlos con una latencia del orden de milisegundos y modificar el esquema con facilidad. 

## <a name="how-can-i-learn-about-the-documentdb-api"></a>¿Cómo puedo obtener información acerca de la API de DocumentDB?
Una forma rápida de obtener información sobre la API de DocumentDB y verla en acción es seguir estos tres pasos: 

1. Vea el vídeo de dos minutos [What is Azure Cosmos DB?](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/) (¿Qué es Azure Cosmos DB?), que presenta las ventajas de usar Azure Cosmos DB.
2. Vea el vídeo de tres minutos [Create Azure Cosmos DB on Azure](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) (Creación de una instancia de Azure Cosmos DB en Azure), que destaca cómo empezar a trabajar con Azure Cosmos DB desde Azure Portal.
3. Visite [Query Playground](http://www.documentdb.com/sql/demo)(Área de juegos de consultas), donde puede recorrer distintas actividades para obtener información sobre la sofisticada funcionalidad de consulta disponible en DocumentDB. A continuación, diríjase a la pestaña Sandbox (Espacio aislado) y ejecute consultas SQL personalizadas y experimente con DocumentDB.

A continuación, vuelva a este artículo, para conocer más detalles.  

## <a name="what-capabilities-and-key-features-does-azure-cosmos-db-offer"></a>¿Qué funcionalidades y características clave ofrece Azure Cosmos DB?
Mediante la API de DocumentDB, Azure Cosmos DB ofrece las siguientes funcionalidades y ventajas clave:

* **Rendimiento y almacenamiento con escalabilidad flexible**: escale o reduzca verticalmente la base de datos de JSON para cubrir las necesidades de su aplicación. Los datos se almacenan en discos de estado sólido (SSD) para las bajas latencias predecibles. Azure Cosmos DB admite contenedores para almacenar datos JSON denominados colecciones que se pueden escalar a tamaños de almacenamiento prácticamente ilimitados y de rendimiento aprovisionado. Según vaya creciendo su aplicación, puede escalar Azure Cosmos DB de manera flexible con un rendimiento predecible impecable. 


* **Replicación en varias regiones:** Azure Cosmos DB replica los datos de forma transparente en todas las regiones que tenga asociadas a la cuenta de Azure Cosmos DB; esto permite desarrollar aplicaciones que requieran acceso global a los datos al mismo tiempo que proporciona un equilibrio entre la coherencia, la disponibilidad y el rendimiento, todo ello con las garantías pertinentes. Azure Cosmos DB proporciona conmutación por error regional transparente con las API de hospedaje múltiple, así como la capacidad de escalar de forma elástica el rendimiento y el almacenamiento en todo el mundo. Más información en [Distribución de datos global con Azure Cosmos DB](distribute-data-globally.md).

* **Consultas ad hoc con sintaxis SQL conocida:** almacene los diferentes documentos JSON en y consúltelos con una sintaxis SQL conocida. Azure Cosmos DB utiliza una tecnología de indexación estructurada mediante registros sin bloqueo y sumamente simultánea para indexar automáticamente todo el contenido documental. Esto permite consultas enriquecidas en tiempo real sin necesidad de especificar consejos de esquemas, índices secundarios o vistas. Más información en [Consultas en Azure Cosmos DB](documentdb-sql-query.md). 
* **Ejecución de JavaScript en la base de datos** : expresa la lógica de las aplicaciones como procedimientos almacenados, desencadenadores y funciones definidas por el usuario (UDF) con JavaScript estándar. Esto permite que la lógica de su aplicación opere con datos sin preocuparse de la discordancia entre la aplicación y el esquema de la base de datos. La API de DocumentDB proporciona una ejecución transaccional completa de la lógica de aplicaciones de JavaScript directamente en el motor de la base de datos. La integración profunda de JavaScript permite la ejecución de las operaciones INSERTAR, REEMPLAZAR, ELIMINAR y SELECCIONAR desde un programa JavaScript como una transacción aislada. Obtenga más información en [Programación de servidor DocumentDB](programming.md).

* **Niveles de coherencia ajustables**: cinco niveles de coherencia bien definidos donde elegir para lograr un equilibrio óptimo entre coherencia y rendimiento. Para las consultas y las operaciones de lectura, Azure Cosmos DB ofrece cinco niveles de coherencia diferentes: segura, obsolescencia limitada, sesión, prefijo coherente y posible. Estos niveles de coherencia bien definidos y pormenorizados le permiten realizar equilibrios profundos entre la coherencia, la disponibilidad y la latencia. Más información en el artículo sobre el [Uso de los niveles de coherencia para maximizar la disponibilidad y el rendimiento](consistency-levels.md).

* **Completamente administrados** : elimine la necesidad de administrar recursos de bases de datos y máquinas. Como un servicio de Microsoft Azure completamente administrado, no es necesario que administre las máquinas virtuales, ni que implemente y configure software, administre escalado o realice complejas actualizaciones a nivel de datos. Se realiza una copia de seguridad de cada base de datos además de protegerlas contra fallas regionales. Puede agregar fácilmente una cuenta de Azure Cosmos DB y aprovisionar capacidad a medida que la necesite, lo que le permite concentrarse en su aplicación en lugar de en la operación y administración de la base de datos. 

* **Diseño abierto** : comience rápidamente con las destrezas y herramientas existentes. La programación de la API de DocumentDB es sencilla, asequible y no necesita herramientas nuevas ni extensiones personalizadas de JSON o JavaScript. Puede tener acceso a todas las funciones de la base de datos incluido el procesamiento de CRUD, las consultas y JavaScript en una interfaz RESTful HTTP sencilla. La API de DocumentDB abarca formatos, lenguajes y estándares actuales y ofrece funcionalidades de base de datos de gran valor.

* **Indexación automática:** de manera predeterminada, Azure Cosmos DB indexa automáticamente todos los documentos de la base de datos y no espera ni requiere ningún esquema, ni tampoco la creación de índices secundarios. ¿No desea tener que indexar todo? No se preocupe, también puede [renunciar a las rutas de acceso en los archivos JSON](indexing-policies.md) .

## <a name="data-management"></a>¿Cómo se administran los datos con la API DocumentDB?
La API de DocumentDB ayuda a administrar los datos JSON con recursos de base de datos bien definidos. Estos recursos se replican para ofrecer elevados niveles de disponibilidad y solo se pueden direccionar a través de su URI lógico. La Base de datos de documentos ofrece un modelo de programación RESTful basado en HTTP sencillo para todos los recursos. 


La cuenta de base de datos de Azure Cosmos DB es un espacio de nombres único que le proporciona acceso a la Azure Cosmos DB. Para poder crear una cuenta de base de datos, debe disponer de una suscripción de Azure que le proporcione acceso a una variedad de servicios de Azure. 

Todos los recursos de Azure Cosmos DB se modelan y almacenan como documentos JSON. Los recursos se administran como elementos, que son documentos JSON que contienen metadatos, y como fuentes que son recopilaciones de elementos. Las fuentes respectivas contienen los conjuntos de elementos.

La imagen siguiente muestra las relaciones entre los recursos de Azure Cosmos DB:

![Relación jerárquica entre los recursos de Azure Cosmos DB][1] 

Una cuenta de base de datos consta de un conjunto de bases de datos, cada una con varias recopilaciones. Cada recopilación puede contener, a su vez, procedimientos almacenados, desencadenadores, UDF, documentos y datos adjuntos relacionados. Una base de datos también tiene usuarios asociados, cada uno con un conjunto de permisos que proporciona acceso a diferentes recopilaciones, procedimientos almacenados, desencadenadores, UDF, documentos o datos adjuntos. Mientras que las bases de datos, los usuarios, permisos y las recopilaciones son recursos definidos por el sistema con esquemas conocidos, los documentos, procedimientos almacenados, desencadenadores, UDF y datos adjuntos contienen contenido JSON arbitrario y definido por el usuario.  

> [!NOTE]
> Como la API de DocumentDB antes se conocía como el servicio Azure DocumentDB, puede continuar el aprovisionamiento, la supervisión y la administración de las cuentas creadas a través de la API de REST o las herramientas de administración de recursos de Azure con los nombres de los recursos de Azure DocumentDB o Azure Cosmos DB. Cuando hacemos referencia a las API de Azure DocumentDB, usamos los nombres indistintamente. 

## <a name="develop"></a> ¿Cómo puedo desarrollar aplicaciones con la API de DocumentDB?

Azure Cosmos DB expone recursos mediante la API de REST de DocumentDB, que se puede invocar con cualquier lenguaje que realice solicitudes de HTTP/HTTPS. Además, ofrecemos bibliotecas de programación para varios lenguajes populares de la API de DocumentDB. Estas bibliotecas de cliente simplifican muchos aspectos del trabajo con la API, al controlar detalles como el almacenamiento en caché de las direcciones, la administración de excepciones, los reintentos automáticos, etc. Actualmente hay bibliotecas disponibles para los siguientes lenguajes y plataformas:  

| Descargar | Documentación |
| --- | --- |
| [.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) |[Biblioteca de .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx) |
| [SDK de Node.js](http://go.microsoft.com/fwlink/?LinkID=402990) |[Biblioteca de Node.js](http://azure.github.io/azure-documentdb-node/) |
| [SDK de Java](http://go.microsoft.com/fwlink/?LinkID=402380) |[Biblioteca de Java](http://azure.github.io/azure-documentdb-java/) |
| [SDK de JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991) |[Biblioteca de JavaScript](http://azure.github.io/azure-documentdb-js/) |
| N/D |[SDK del servidor de JavaScript](http://azure.github.io/azure-documentdb-js-server/) |
| [SDK de Python](https://pypi.python.org/pypi/pydocumentdb) |[Biblioteca de Python](http://azure.github.io/azure-documentdb-python/) |
| N/D | [API para MongoDB](mongodb-introduction.md)


Mediante el [Emulador de Azure Cosmos DB](local-emulator.md), puede desarrollar y probar su aplicación localmente con la API de DocumentDB, sin crear una suscripción de Azure ni incurrir en otros costos. Cuando esté satisfecho con el funcionamiento de la aplicación en el emulador, puede cambiar a una cuenta de Azure Cosmos DB en la nube.

Además de las operaciones básicas de creación, lectura, actualización y eliminación, la API de DocumentDB proporciona una interfaz de consulta SQL enriquecida para recuperar documentos JSON y como soporte técnico del servidor para la ejecución transaccional de la lógica de aplicaciones de JavaScript. Las interfaces de ejecución de consultas y script están disponibles a través de todas las bibliotecas de las plataformas además de las API REST. 

### <a name="sql-query"></a>Consulta SQL
La API de DocumentDB es compatible con las consultas de documentos mediante lenguaje SQL, que se basa en el sistema de tipos de JavaScript, y con expresiones que admiten consultas relacionales, jerárquicas y espaciales. El lenguaje de consulta de DocumentDB es una interfaz sencilla pero poderosa para consultar documentos JSON. El lenguaje admite un subconjunto de gramática ANSI SQL e incorpora una integración profunda del objeto de JavaScript, matrices, construcción de objetos e invocación de funciones. La Base de datos de documentos proporciona su modelo de consulta sin ningún esquema explícito o consejo de indexación por parte del desarrollador.

Las funciones definidas por el usuario (UDF) se pueden registrar con la API DocumentDB y se puede hacer referencia a ellas como parte de una consulta SQL, lo cual amplía la gramática a una lógica de aplicación personalizada compatible. Estas UDF se crean como programas de JavaScript y se ejecutan en la base de datos. 

Para los desarrolladores de .NET, el [SDK de .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx) de DocumentDB también ofrece un proveedor de consultas LINQ. 

### <a name="transactions-and-javascript-execution"></a>Transacciones y ejecución de JavaScript
La API de DocumentDB permite crear lógica de aplicaciones como programas con nombres escritos completamente en JavaScript. Estos programas se registran para una recopilación y pueden emitir operaciones de base de datos en los documentos dentro de una recopilación determinada. JavaScript se puede registrar para la ejecución como desencadenador, procedimiento almacenado o función definida por el usuario. Los desencadenadores y procedimientos almacenados pueden crear, leer, actualizar y eliminar documentos; mientras que las funciones definidas por el usuario se ejecutan como parte de la lógica de ejecución de consulta sin acceso de escritura a la colección.

La ejecución de JavaScript en la API de DocumentDB se modela de acuerdo con los conceptos admitidos por los sistemas de base de datos relacionales, con JavaScript como sustituto moderno de Transact-SQL. Toda la lógica de JavaScript se ejecuta en un entorno de transacciones ACID con aislamiento de la instantánea. Durante el transcurso de esta ejecución, si JavaScript lanza una excepción, entonces se cancela toda la transacción.

## <a name="are-there-any-online-courses-on-azure-cosmos-db"></a>¿Hay cursos en línea de Azure Cosmos DB?

Sí, en la [Microsoft Virtual Academy](https://mva.microsoft.com/en-US/training-courses/azure-documentdb-planetscale-nosql-16847) hay un curso de Azure DocumentDB. 

>[!VIDEO https://mva.microsoft.com/en-US/training-courses-embed/azure-documentdb-planetscale-nosql-16847]
>
>

## <a name="next-steps"></a>Pasos siguientes
¿Ya tiene una cuenta de Azure? Entonces puede empezar a trabajar con Azure Cosmos DB; deje que nuestras [guías de inicio rápido](../cosmos-db/create-documentdb-dotnet.md) le indiquen el proceso de creación de una cuenta e iniciación a Cosmos DB.

[1]: ./media/documentdb-introduction/json-database-resources1.png


