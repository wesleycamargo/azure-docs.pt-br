---
title: Artigo sobre as limitações de migração e os problemas conhecidos das migrações online para o Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre as limitações de migração e os problemas conhecidos das migrações online para o Banco de Dados SQL do Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: a8d91c0410d2a59e4b22866aa48d36c026f3ee8e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956288"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-db"></a>Limitações de migração e problemas conhecidos das migrações online para o BD SQL do Azure

Os problemas conhecidos e as limitações associados às migrações online do SQL Server para o Banco de Dados SQL do Azure são descritos abaixo.

### <a name="migration-of-temporal-tables-not-supported"></a>A migração das tabelas temporais não é compatível

**Sintoma**

Se seu banco de dados de origem consiste em uma ou mais tabelas temporais, a migração de banco de dados falha durante a operação de "Carregamento de dados completo" e você pode ver a seguinte mensagem:

{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Erro de migração de banco de dados", "errorEvents":"["Não foi possível definir as funcionalidades de captura. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 NativeError: 11.0 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]O uso da replicação não é compatível com a tabela temporal com versão do sistema '[Application. Cities]' Line: 1 Column: -1 "]" }
 
 ![Exemplo de erros da tabela temporal](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Solução alternativa**

1. Localize tabelas temporais no seu esquema de origem usando a consulta abaixo.
     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```
2. Exclua estas tabelas da folha **Definir configurações de migração**, em que você especifica tabelas para migração.

3. Execute a atividade de migração novamente.

**Recursos**

Para obter mais informações, confira o artigo [Tabelas temporais](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).
 
### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>A migração das tabelas inclui uma ou mais colunas com o tipo de dados hierarchyid

**Sintoma**

Você poderá ver uma exceção do SQL sugerindo que "ntext é incompatível com hierarchyid" durante a operação de "Carregamento de dados completo":
     
![Exemplo de erros de hierarchyid](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Solução alternativa**

1. Encontre as tabelas de usuário que incluem colunas com o tipo de dados hierarchyid usando a consulta abaixo.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ``` 

 2. Exclua estas tabelas da folha **Definir configurações de migração**, em que você especifica tabelas para migração.

 3. Execute a atividade de migração novamente.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Falhas de migração com várias violações de integridade com gatilhos ativos no esquema durante o "Carregamento de dados completo" ou a "Sincronização de dados incremental"

**Solução alternativa**
1. Localize os gatilhos que estão atualmente ativos no banco de dados de origem usando a consulta abaixo:
     ```
     select * from sys.triggers where is_disabled =0
     ```
2. Desabilite os gatilhos em seu banco de dados de origem usando as etapas fornecidas no artigo [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

3. Execute a atividade de migração novamente.

### <a name="support-for-lob-data-types"></a>Suporte para tipos de dados LOB

**Sintoma**

Se o tamanho da coluna de LOB (Objeto Grande) for maior que 32 KB, os dados poderão ser truncados no destino. Você pode verificar o tamanho da coluna de LOB usando a consulta abaixo: 

``` 
SELECT max(len(ColumnName)) as LEN from TableName
```

**Solução alternativa**

Se houver uma coluna de LOB com mais de 32 KB, contate a equipe de engenharia em [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problemas com colunas de carimbo de data/hora

**Sintoma**

O DMS não migra o valor do carimbo de data/hora da fonte, em vez disso, o DMS gera um novo valor de carimbo de data/hora na tabela de destino.

**Solução alternativa**

Se você precisar que o DMS migre o valor de carimbo de data/hora exato armazenado na tabela de origem, contate a equipe de engenharia em [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com).

### <a name="data-migration-errors-do-not-provide-additional-details-on-the-database-detailed-status-blade"></a>Os erros de migração de dados não fornecem detalhes adicionais sobre a folha de status detalhada do banco de dados.

**Sintoma**

Quando você encontrar as falhas de migração na exibição de status de detalhes dos bancos de dados, selecionar o link **Erros de migração de dados** na faixa de opções superior não pode fornecer detalhes adicionais específicos para as falhas de migração.

![exemplos de erros de migração de dados sem detalhes](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Solução alternativa**

Para obter detalhes de falha específicos, siga as etapas abaixo.

1. Feche a folha de status detalhado do banco de dados para exibir a tela Atividade de migração.

     ![tela de atividade de migração](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Selecione **Ver detalhes do erro** para exibir mensagens de erro específicas que ajudam você a solucionar problemas de erros de migração.
