---
title: Como despejar e restaurar no Banco de Dados do Azure para PostgreSQL
description: Descreve como extrair um Banco de Dados PostgreSQL em um arquivo de despejo e restaurar a partir de um arquivo criado por pg_dump no Banco de Dados do Azure para PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 2aa59bcf0d56358601b81730abe330a56ca35d02
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966901"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migrar seu banco de dados PostgreSQL usando despejar e restaurar
Use [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) para extrair um banco de dados PostgreSQL para um arquivo de despejo, e [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) para restaurar o banco de dados PostgreSQL de um arquivo criado por pg_dump.

## <a name="prerequisites"></a>Pré-requisitos
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


## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>Restaurar os dados no banco de dados de destino do Azure para PostgreSQL usando pg_restore
Depois de criar o banco de dados de destino, você poderá usar o comando pg_restore e o parâmetro -d, --dbname para restaurar os dados no banco de dados de destino do arquivo de despejo.
```bash
pg_restore -v --no-owner –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
A inclusão do parâmetro --no-owner faz com que todos os objetos criados durante a restauração sejam de propriedade do usuário especificado com --username. Para obter mais informações, consulte a documentação oficial do PostgreSQL em [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Se o seu servidor PostgreSQL exigir conexões SSL (ativas por padrão em servidores do Banco de Dados do Azure para PostgreSQL), defina uma variável de ambiente `PGSSLMODE=require` para que a ferramenta de pg_restore se conecte com SSL. Sem SSL, o erro pode indicar `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> Na linha de comando do Windows, execute o comando `SET PGSSLMODE=require` antes de executar o comando pg_restore. No Linux ou o Bash, execute o comando `export PGSSLMODE=require` antes de executar o comando pg_restore.
>

Neste exemplo, restauramos os dados no arquivo de despejo **testdb.dump** no banco de dados **mypgsqldb** no servidor de destino **mydemoserver.postgres.database.azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>Otimizando o processo de migração

Uma maneira de migrar seu banco de dados PostgreSQL existente para o serviço Banco de Dados do Azure para PostgreSQL é fazer o backup do banco de dados na origem e restaurá-lo no Azure. Para minimizar o tempo necessário para concluir a migração, considere usar os seguintes parâmetros com os comandos de backup e restauração.

> [!NOTE]
> Para obter informações de sintaxe detalhadas, confira os artigos [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Para o backup
- Faça o backup com a opção -Fc para que você possa executar a restauração em paralelo para acelerá-la. Por exemplo: 

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName > Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>Para a restauração
- Sugerimos que você mova o arquivo de backup para uma VM do Azure na mesma região que o servidor do Banco de Dados do Azure para PostgreSQL de destino da migração e execute o pg_restore dessa VM para reduzir a latência de rede. Também é recomendado que a VM seja criada com a [rede acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md) habilitada.
- Isso já deve estar feito por padrão, mas abra o arquivo de despejo para verificar se as instruções create index estão após a inserção dos dados. Se não estiverem, coloque as instruções create index após a inserção dos dados.
- Restauração com as opções -Fc e -j *#* para paralelizar a restauração. *#* é o número de núcleos no servidor de destino. Você também pode experimentar com *#* definido como duas vezes o número de núcleos do servidor de destino para ver o impacto. Por exemplo: 

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- Você também pode editar o arquivo de despejo, adicionando o comando *set synchronous_commit = off;* ao início e o comando *set synchronous_commit = on;* ao final. Se ele não for ativado no final, antes que os aplicativos alterem os dados, poderá ocorrer uma perda subsequente de dados.

Lembre-se de testar e validar esses comandos em um ambiente de teste antes de usá-los em produção.

## <a name="next-steps"></a>Próximas etapas
- Para migrar um banco de dados PostgreSQL usando exportar e importar, veja [Migrar seu banco de dados PostgreSQL usando exportar e importar](howto-migrate-using-export-and-import.md).
- Para obter mais informações de como migrar bancos de dados para o Banco de Dados do Azure para PostgreSQL, confira o [Guia de Migração de Banco de Dados](https://aka.ms/datamigration).
