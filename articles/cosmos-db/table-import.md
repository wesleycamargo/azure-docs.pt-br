---
title: Importar dados para usar com a API de Tabela do Azure Cosmos DB | Microsoft Docs
description: Saiba como importar dados para usar com a API de Tabela do Azure Cosmos DB.
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: b60743e2-0227-43ab-965a-0ae3ebacd917
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: fd0454252080679fa880eecb677d609ea0734f09
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="import-data-for-use-with-the-azure-cosmos-db-table-api"></a>Importar dados para usar com a API de Tabela do Azure Cosmos DB

Este tutorial fornece instruções sobre como importar dados para usar com o a [API de Tabela](table-introduction.md) do Azure Cosmos DB. Se você tiver dados armazenados no armazenamento de tabelas do Azure, é possível usar tanto a Ferramenta de Migração de Dados quanto a AzCopy para importar os dados. Se você tiver dados armazenados em uma conta da API de Tabela do Azure Cosmos DB (versão prévia), deverá usar a ferramenta de Migração de Dados para migrar seus dados. Depois que os dados são importados, será possível tirar proveito dos recursos premium das ofertas do Azure Cosmos DB, como distribuição global de turnkey, taxa de transferência dedicada, latências de milissegundo de único dígito no 99º percentil, alta disponibilidade garantida e indexação secundária automática.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Importar dados com a ferramenta de migração de dados
> * Importar dados com o AzCopy
> * Migrar da API de Tabela (versão prévia) para a API de Tabela 

## <a name="data-migration-tool"></a>Ferramenta de migração de dados

A ferramenta de Migração de Dados do Azure Cosmos DB da linha de comando (dt.exe) pode ser usada para importar os dados de armazenamento de Tabela do Azure existentes para uma conta disponível ao público em geral da API de Tabela ou migrar dados de uma conta da API de Tabela (versão prévia) para uma conta de disponível ao público em geral da API de Tabela. Outras fontes não são atualmente compatíveis.

Para executar uma migração de dados de tabela, conclua as seguintes tarefas:

1. Baixe a ferramenta de migração do [Centro de Download da Microsoft](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) ou do [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Execute `dt.exe` usando os argumentos de linha de comando para seu cenário.

dt.exe usa um comando no seguinte formato:

    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 

As opções para o comando são:

    /ErrorLog: Optional. Name of the CSV file to redirect data transfer failures
    /OverwriteErrorLog: Optional. Overwrite error log file
    /ProgressUpdateInterval: Optional, default is 00:00:01. Time interval to refresh on-screen data transfer progress
    /ErrorDetails: Optional, default is None. Specifies that detailed error information should be displayed for the following errors: None, Critical, All

### <a name="command-line-source-settings"></a>Configurações de fonte de linha de comando

Use as seguintes opções de fonte ao definir Armazenamento de Tabela do Azure ou API de Tabela (versão prévia) como fonte da migração.

    /s:AzureTable: Reads data from Azure Table storage
    /s.ConnectionString: Connection string for the table endpoint. This can be retrieved from the Azure portal
    /s.LocationMode: Optional, default is PrimaryOnly. Specifies which location mode to use when connecting to Azure Table storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
    /s.Table: Name of the Azure Table
    /s.InternalFields: Set to All for table migration as RowKey and PartitionKey are required for import.
    /s.Filter: Optional. Filter string to apply
    /s.Projection: Optional. List of columns to select

Para recuperar a cadeia de conexão da fonte ao importar do armazenamento de Tabela do Azure, abra o portal do Azure e clique em **Contas de armazenamento** > **Conta** > **Chaves de acesso** e, em seguida, use o botão copiar para copiar a **Cadeia de conexão**.

![Captura de tela das opções de origem do HBase](./media/table-import/storage-table-access-key.png)

Para recuperar a cadeia de conexão da fonte ao importar de uma conta de API de Tabela do Azure Cosmos DB, abra o portal do Azure, clique em **Azure Cosmos DB** > **Conta** > **Cadeia de conexão** e use o botão copiar para copiar a **Cadeia de conexão**.

![Captura de tela das opções de origem do HBase](./media/table-import/cosmos-connection-string.png)

[Exemplo de comando de Armazenamento de Tabela do Azure](#azure-table-storage)

[Exemplo de comando da API de Tabela do Azure Cosmos DB (versão prévia)](#table-api-preview)

### <a name="command-line-target-settings"></a>Configurações de destino de linha de comando

Use as seguintes opções de destino ao definir a API de Tabela do Azure Cosmos DB como destino da migração.

    /t:TableAPIBulk: Uploads data into Azure CosmosDB Table in batches
    /t.ConnectionString: Connection string for the table endpoint
    /t.TableName: Specifies the name of the table to write to
    /t.Overwrite: Optional, default is false. Specifies if existing values should be overwritten
    /t.MaxInputBufferSize: Optional, default is 1GB. Approximate estimate of input bytes to buffer before flushing data to sink
    /t.Throughput: Optional, service defaults if not specified. Specifies throughput to configure for table
    /t.MaxBatchSize: Optional, default is 2MB. Specify the batch size in bytes

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Exemplo de comando: a fonte é o armazenamento de Tabela do Azure

Aqui está um exemplo de linha de comando mostrando como importar do armazenamento de Tabela do Azure para a API de Tabela:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey==<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```
<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Exemplo de comando: a fonte é a API de Tabela do Azure Cosmos DB (versão prévia)

Aqui está um exemplo de linha de comando para importar da API de Tabela (versão prévia) para a API de Tabela disponível ao público em geral:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="azcopy-command"></a>Comando AzCopy

Outra opção para migrar dados de armazenamento de tabela do Azure para a API de Tabela do Azure Cosmos DB é usar o utilitário de linha de comando AzCopy. Para usar o AzCopy, primeiro exporte seus dados conforme descrito em [Exportar dados do armazenamento de tabela](../storage/common/storage-use-azcopy.md#export-data-from-table-storage), depois importe os dados para o Azure Cosmos DB conforme descrito em [API de Tabela do Azure Cosmos DB](../storage/common/storage-use-azcopy.md#import-data-into-table-storage).

Ao executar a importação para o Azure Cosmos DB, consulte o exemplo a seguir. Observe que o valor de /Dest usa cosmosdb e não core.

Exemplo de comando de importação:

```
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migrar da API de Tabela (versão prévia) para a API de Tabela

> [!WARNING]
> Se você quiser aproveitar imediatamente os benefícios das tabelas disponíveis ao público em geral, migre suas tabelas da versão prévia conforme especificado nesta seção; caso contrário, vamos executar migrações automáticas para clientes da versão prévia nas próximas semanas, observe, no entanto, que as tabelas da versão prévia migradas automaticamente terão certas restrições nelas que não há nas tabelas recém-criadas.
> 

A API de Tabela já está disponível ao público em geral. Há diferenças entre as versões prévia e disponível ao público em geral das tabelas tanto no código que é executado na nuvem como no código que é executado no cliente. Portanto, não é recomendável tentar combinar um cliente do SDK de versão prévia com uma conta da API de Tabela disponível ao público em geral e vice-versa. Os clientes da versão prévia da API de Tabela que desejem continuar a usar as tabelas existentes, mas em um ambiente de produção precisam migrar da versão prévia para o ambiente disponível ao público em geral ou aguardar a migração automática. Se você esperar a migração automática, será notificado sobre as restrições nas tabelas migradas. Após a migração, você poderá criar novas tabelas em sua conta existente sem restrições (somente tabelas migradas terão restrições).

Para migrar da API de Tabela (versão prévia) para a API de Tabela disponível ao público em geral:

1. Crie uma nova conta do Azure Cosmos DB e defina seu tipo de API como Tabela do Azure, conforme descrito em [Criar uma conta de banco de dados](create-table-dotnet.md#create-a-database-account).

2. Altere os clientes para usar uma versão de lançamento dos [SDKs da API de Tabela](table-sdk-dotnet.md).

3. Migre os dados do cliente das tabelas de versão prévia para tabelas disponíveis ao público em geral usando a ferramenta de Migração de Dados. Instruções sobre como usar a ferramenta de migração de dados para esse fim são descritas em [Ferramenta de Migração de Dados](#data-migration-tool). 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Importar dados com a ferramenta de migração de dados
> * Importar dados com o AzCopy
> * Migrar da API de Tabela (versão prévia) para a API de Tabela

Agora você pode seguir para o próximo tutorial e saber como consultar dados usando a API de Tabela do Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Como consultar os dados?](../cosmos-db/tutorial-query-table.md)
