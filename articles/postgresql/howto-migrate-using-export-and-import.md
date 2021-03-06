---
title: "Migración de una base de datos mediante importación y exportación en Azure Database for PostgreSQL | Microsoft Docs"
description: "Describe cómo extraer una base de datos PostgreSQL en un archivo de script e importar los datos en la base de datos de destino desde ese archivo."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 275519089d682b6ef3c7858446ab2c4baface77f
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migración de una base de datos de PostgreSQL mediante exportación e importación
Puede usar [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) para extraer una base de datos PostgreSQL en un archivo de script y [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) para importar los datos en la base de datos de destino desde ese archivo.

## <a name="prerequisites"></a>Requisitos previos
Para seguir esta guía, necesitará:
- Un [servidor de Azure Database for PostgreSQL](quickstart-create-server-database-portal.md) con reglas de firewall para permitir el acceso a las bases de datos que hay en él.
- La utilidad de línea de comandos [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) instalada.
- La utilidad de línea de comandos [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) instalada.

Siga estos pasos para exportar e importar la base de datos de PostgreSQL.

## <a name="create-a-script-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Creación de un archivo de script mediante pg_dump con los datos que se van a cargar
Para exportar la base de datos de PostgreSQL existente en el entorno local o en una máquina virtual a un archivo de script de SQL, ejecute el siguiente comando:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Por ejemplo, si tiene un servidor local y una base de datos llamada **testdb** en él:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postrgesql"></a>Importación de los datos en la instancia de Azure Database for PostrgeSQL de destino
Puede usar la utilidad de la línea de comandos psql y el parámetro -d, --dbname para importar los datos en la instancia de Azure Database for PostrgeSQL que creamos y cargar los datos desde el archivo de SQL.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
En este ejemplo, usaremos psql y el archivo de script **testdb.sql** del paso anterior para importar los datos en la base de datos **mypgsqldb**, en el servidor de destino **mypgserver 20170401.postgres.database.azure.com**.
```bash
psql --file=testdb.sql --host=mypgserver-20170401.database.windows.net --port=5432 --username=mylogin@mypgserver-20170401 --dbname=mypgsqldb
```

## <a name="next-steps"></a>Pasos siguientes
- Para migrar una base de datos de PostgreSQL mediante volcado y restauración, consulte [Migración de una base de datos de PostgreSQL mediante volcado y restauración](howto-migrate-using-dump-and-restore.md).
