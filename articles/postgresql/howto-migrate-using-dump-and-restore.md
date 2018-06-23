---
title: Como despejar e restaurar no Banco de Dados do Azure para PostgreSQL
description: Descreve como extrair um Banco de Dados PostgreSQL em um arquivo de despejo e restaurar a partir de um arquivo criado por pg_dump no Banco de Dados do Azure para PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 586df8d72dc05104bbf589eabcf3bd2245c268c8
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737241"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migrar seu banco de dados PostgreSQL usando despejar e restaurar
Use [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) para extrair um banco de dados PostgreSQL para um arquivo de despejo, e [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) para restaurar o banco de dados PostgreSQL de um arquivo criado por pg_dump.

## <a name="prerequisites"></a>pré-requisitos
Para seguir este guia de instruções, você precisa:
- Um [Servidor de Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md) com regras de firewall a fim de permitir o acesso e um banco de dados sob ele.
- Utilitários de linha de comando [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) instalados

Siga as etapas abaixo para despejar e restaurar seu banco de dados PostgreSQL:

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Criar um arquivo de despejo usando pg_dump que contém os dados a serem carregados
Para fazer backup de um banco de dados PostgreSQL que existe localmente ou em uma VM, execute o seguinte comando:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
Por exemplo, se você tiver um servidor local e um banco de dados chamado **testdb** nele
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```

> [!IMPORTANT]
> Copie os arquivos de backup para um blob/armazenamento do Azure e execute a restauração de lá, o que deve ser muito mais rápido do que executar a restauração pela Internet.
> 

## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>Restaurar os dados no banco de dados de destino do Azure para PostgreSQL usando pg_restore
Depois de criar o banco de dados de destino, você poderá usar o comando pg_restore e o parâmetro -d, --dbname para restaurar os dados no banco de dados de destino do arquivo de despejo.
```bash
pg_restore -v --no-owner –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
A inclusão do parâmetro --no-owner faz com que todos os objetos criados durante a restauração sejam de propriedade do usuário especificado com --username. Para obter mais informações, consulte a documentação oficial do PostgreSQL em [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

Neste exemplo, restauramos os dados no arquivo de despejo **testdb.dump** no banco de dados **mypgsqldb** no servidor de destino **mydemoserver.postgres.database.azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="next-steps"></a>Próximas etapas
- Para migrar um banco de dados PostgreSQL usando exportar e importar, veja [Migrar seu banco de dados PostgreSQL usando exportar e importar](howto-migrate-using-export-and-import.md).
- Para obter mais informações de como migrar bancos de dados para o Banco de Dados do Azure para PostgreSQL, confira o [Guia de Migração de Banco de Dados](http://aka.ms/datamigration).
