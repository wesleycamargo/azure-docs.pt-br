---
title: Como despejar e restaurar no Banco de Dados do Azure para PostgreSQL | Microsoft Docs
description: Descreve como extrair um banco de dados PostgreSQL para um arquivo de despejo e restaurar o banco de dados PostgreSQL de um arquivo criado por pg_dump no Banco de Dados do Azure para PostgreSQL.
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
ms.openlocfilehash: b056b2aee8b3d82077f61c18e3a660f3562ae068
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migrar seu banco de dados PostgreSQL usando despejar e restaurar
Use [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) para extrair um banco de dados PostgreSQL para um arquivo de despejo, e [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) para restaurar o banco de dados PostgreSQL de um arquivo criado por pg_dump.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- Um [Servidor de Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md) com regras de firewall a fim de permitir o acesso e um banco de dados sob ele.
- Utilitários de linha de comando [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) instalados

Execute as etapas abaixo para despejar e restaurar o banco de dados PostgreSQL.

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Criar um arquivo de despejo usando pg_dump que contém os dados a serem carregados
Para fazer backup de um banco de dados PostgreSQL existente no local ou em uma VM, execute o seguinte comando:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
Por exemplo, se você tiver um servidor local e um banco de dados chamado **testdb** nele
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```

## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>Restaurar os dados no banco de dados de destino do Azure para PostrgeSQL usando pg_restore
Depois de criar o banco de dados de destino, você poderá usar o comando pg_restore e o parâmetro -d, --dbname para restaurar os dados no banco de dados de destino do arquivo de despejo.
```bash
pg_restore -v –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
Neste exemplo, vamos restaurar os dados no banco de dados **mypgsqldb** no servidor de destino **mypgserver 20170401.postgres.database.azure.com** do arquivo de despejo gerado **testdb.dump**
```bash
pg_restore -v --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=mypgsqldb testdb.dump
```

## <a name="next-steps"></a>Próximas etapas
- Para migrar um banco de dados PostgreSQL usando exportar e importar, veja [Migrar seu banco de dados PostgreSQL usando exportar e importar](howto-migrate-using-export-and-import.md)
