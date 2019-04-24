---
title: Artigo sobre as limitações de migração e os problemas conhecidos das migrações online para o Banco de Dados do Azure para MySQL | Microsoft Docs
description: Saiba mais sobre as limitações de migração e os problemas conhecidos das migrações online para o Banco de Dados do Azure para MySQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 0641545c10d7f59cb1874659eae9c7e7bf65932e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532259"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-mysql"></a>Limitações de migração e problemas conhecidos das migrações online para o BD do Azure para MySQL

Os problemas conhecidos e as limitações associados às migrações online do MySQL para o Banco de Dados do Azure para MySQL são descritos nas seções a seguir. 

## <a name="online-migration-configuration"></a>Configuração de migração online
- A versão do MySQL Server de origem precisa ser a 5.6.35, a 5.7.18 ou posteriores
- O Banco de Dados do Azure para MySQL dá suporte a:
    - MySQL Community Edition
    - Mecanismo InnoDB
- Migração de mesma versão. Não há suporte para a migração do MySQL 5.6 para o Banco de Dados do Azure para MySQL 5.7.
- Habilite o log binário em my.ini (Windows) ou em my.cnf (Unix)
    - Defina Server_id para qualquer número maior ou igual a 1, por exemplo, Server_id=1 (somente para o MySQL 5.6)
    - Definir log-bin = \<caminho > (somente para o MySQL 5.6)
    - Defina binlog_format = row
    - Expire_logs_days = 5 (recomendado, apenas para o MySQL 5.6)
- O usuário precisa ter a função ReplicationAdmin.
- As ordenações definidas para o banco de dados MySQL de origem devem ser as mesmas que as definidos no Banco de Dados do Azure para MySQL de destino.
- O esquema precisa corresponder entre o banco de dados MySQL de origem e o banco de dados de destino no Banco de Dados do Azure para MySQL.
- O esquema no Banco de Dados do Azure para MySQL de destino não pode ter chaves estrangeiras. Use a consulta a seguir para remover as chaves estrangeiras:
    ```
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    Execute a remoção de chave estrangeira (que é a segunda coluna) no resultado da consulta.
- O esquema no Banco de Dados do Azure para MySQL de destino não pode ter nenhum gatilho. Para remover os gatilhos no banco de dados de destino:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Limitações de tipo de dados
- **Limitação**: Se houver um tipo de dados JSON no banco de dados MySQL de origem, a migração falhará durante a sincronização contínua.

    **Solução alternativa**: Modifique o tipo de dados JSON para texto médio ou longtext no banco de dados MySQL de origem.

- **Limitação**: Se não houver nenhuma chave primária nas tabelas, a sincronização contínua falhará.
 
    **Solução alternativa**: Defina temporariamente uma chave primária para a tabela para migração continuar. Você poderá remover a chave primária após a conclusão da migração de dados.

## <a name="lob-limitations"></a>Limitações de LOB
As colunas de LOB (Objeto Grande) são aquelas cujo tamanho pode aumentar muito. Para MySQL, texto médio, Longtext, Blob, Mediumblob, Longblob, etc. são alguns dos tipos de dados de LOB.

- **Limitação**: Se tipos de dados LOB forem usados como chaves primárias, a migração falhará.

    **Solução alternativa**: Substitua a chave primária por outros tipos de dados ou colunas que não sejam LOB.

- **Limitação**: Se o tamanho da coluna LOB (Objeto Grande) for maior que 32 KB, os dados poderão ser truncados no destino. Você pode verificar o tamanho da coluna de LOB usando esta consulta:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Solução alternativa**: Se você tiver um objeto LOB que é maior do que 32 KB, entre em contato com a equipe de engenharia [fazer migrações de banco de dados do Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com). 

## <a name="other-limitations"></a>Outras limitações
- Não há suporte para cadeia de caracteres de senha com colchetes {} de abertura e fechamento em seu início e final. Essa limitação aplica-se tanto à conexão com o MySQL de origem quanto com o Banco de Dados do Azure para MySQL de destino.
- Não há suporte para as DDLs a seguir:
    - Todas as DDLs de partição
    - Remover tabela
    - Renomear tabela
- Não há suporte para usar a instrução *alter table <table_name> add column <column_name>* para adicionar colunas ao início ou ao meio de uma tabela. A instrução *alter table <table_name> add column <column_name>* adiciona a coluna ao final da tabela.
- Não há suporte para índices criados com apenas uma parte dos dados da coluna. A instrução a seguir é um exemplo que cria um índice usando apenas uma parte dos dados da coluna:
    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- No DMS, o limite de bancos de dados a serem migrados em uma única atividade de migração é quatro.
