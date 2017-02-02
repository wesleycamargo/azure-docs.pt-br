---
title: "Indexação do Armazenamento de Tabelas do Azure com a Pesquisa do Azure"
description: Aprenda a indexar dados armazenados em Tabelas do Azure com a Pesquisa do Azure
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 1cc27411-d0cc-40ed-8aed-c7cb9ab402b9
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 12/15/2016
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 714045750ab16364ecd1095f1f346d3da1d4c4a5
ms.openlocfilehash: 4bfcf719cb071a28421c64dbb4d6c132f45ba9f9

---

# <a name="indexing-azure-table-storage-with-azure-search"></a>Indexação do Armazenamento de Tabelas do Azure com a Pesquisa do Azure
Este artigo mostra como usar a Pesquisa do Azure para indexar dados armazenados no Armazenamento de Tabelas do Azure. O novo indexador de tabelas da Pesquisa do Azure torna esse processo rápido e direto.

## <a name="setting-up-azure-table-indexing"></a>Configuração da indexação de tabela do Azure
Para instalar e configurar um indexador de tabelas do Azure, você pode usar a API REST do Azure Search para criar e gerenciar **indexadores** e **fontes de dados**, conforme descrito em [Operações do indexador](https://msdn.microsoft.com/library/azure/dn946891.aspx). Você também pode usar a [versão 2.0-preview](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) do SDK do .NET. No futuro, o suporte para a indexação de tabela será adicionado ao Portal do Azure.

Uma fonte de dados especifica quais dados indexar, as credenciais necessárias para acessar os dados e as políticas que permitem à Pesquisa do Azure identificar com eficiência as alterações nos dados (linhas novas, modificadas ou excluídas).

Um indexador lê dados de uma fonte de dados e carrega-os em um índice de pesquisa de destino.

Para configurar a indexação de tabela:

1. Criar uma fonte de dados
   * Definir o parâmetro `type` para `azuretable`
   * Passe a cadeia de conexão da conta de armazenamento como o parâmetro `credentials.connectionString`. É possível obter a cadeia de conexão no Portal do Azure navegando para a folha da conta de armazenamento > **Chaves** > **de Configuração** (para contas de armazenamento Clássicas) ou **Configuração** > **Chaves de Acesso** (para contas de armazenamento ARM). Observe que atualmente o Azure Search não oferece suporte para credenciais de Assinatura de Acesso Compartilhado. Se você desejar usar o SAS, vote [nessa sugestão de UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/12368244-support-shared-access-signature-for-blob-datasourc).
   * Especifique o nome da tabela usando o parâmetro `container.name`
   * Opcionalmente, especifique uma consulta usando o parâmetro `container.query`. Sempre que possível, use um filtro em PartitionKey para obter o melhor desempenho. Qualquer outra consulta resultará em uma verificação completa da tavbela que pode resultar em baixo desempenho em tabelas grandes.
2. Crie um índice de pesquisa com o esquema que corresponde às colunas na tabela que você deseja indexar.
3. Crie o indexador conectando a fonte de dados ao índice de pesquisa.

### <a name="create-data-source"></a>Criar a fonte de dados
    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Para obter mais informações sobre Criar a API da Fonte de Dados, consulte [Criar Fonte de Dados](https://msdn.microsoft.com/library/azure/dn946876.aspx).

### <a name="create-index"></a>Criar índice
    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

Para obter mais informações sobre Criar a API de Índice, consulte [Criar Índice](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="create-indexer"></a>Criar indexador
Por fim, crie o indexador que faz referência à fonte de dados e ao índice de destino. Por exemplo:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Para obter mais detalhes sobre Criar a API do Indexador, consulte [Criar Indexador](https://msdn.microsoft.com/library/azure/dn946899.aspx).

E isso é tudo. Boa indexação!

## <a name="dealing-with-different-field-names"></a>Lidando com nomes de campos diferentes
Geralmente, os nomes de campos no índice existente serão diferentes dos nomes de propriedades da sua tabela. Você pode usar os **mapeamentos de campo** para mapear os nomes de propriedade da tabela para os nomes de campo em seu índice de pesquisa. Para saber mais sobre os mapeamentos de campo, veja [Os mapeamentos de campo do indexador da Pesquisa do Azure reduzem as diferenças entre fontes de dados e índices de pesquisa](search-indexer-field-mappings.md).

## <a name="handling-document-keys"></a>Manipulando chaves de documento
Na Pesquisa do Azure, a chave do documento identifica exclusivamente um documento. Cada índice de pesquisa deve ter exatamente um campo de chave do tipo `Edm.String`. O campo de chave é necessário para cada documento adicionado ao índice (é, na verdade, o único campo obrigatório).

Como as linhas de tabela têm uma chave composta, a Pesquisa do Azure gera um campo sintético chamado `Key` que é uma concatenação dos valores de chave de linha e de chave de partição. Por exemplo, se a PartitionKey de uma linha for `PK1` e a RowKey for `RK1`, o valor do campo `Key` será `PK1RK1`.

> [!NOTE]
> O valor `Key` pode conter caracteres inválidos em chaves de documento, como traços. É possível lidar com caracteres inválidos usando a `base64Encode` [função de mapeamento de campo](search-indexer-field-mappings.md#base64EncodeFunction). Se você fizer isso, lembre-se também de usar a codificação de Base 64 protegida por URL ao transmitir as chaves de documento nas chamadas à API como Pesquisa.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Indexação incremental e detecção de exclusão 
Ao configurar um indexador de tabela para ser executado em um agendamento, ele reindexará somente linhas novas ou atualizadas, conforme determinado pelo valor `Timestamp` de uma linha. Não é necessário especificar uma política de detecção de alteração, a indexação incremental é habilitada automaticamente para você.

Para indicar que determinados documentos devem ser removidos do índice, você pode usar uma estratégia de exclusão reversível: em vez de excluir uma linha, adicione uma propriedade para indicar que ela deve ser excluída e configure uma política de detecção de exclusão reversível na fonte de dados. Por exemplo, a política mostrada abaixo considerará que uma linha foi excluída se tiver uma propriedade `IsDeleted` com o valor `"true"`:

    PUT https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "query" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   


## <a name="help-us-make-azure-search-better"></a>Ajude-nos a aprimorar a Pesquisa do Azure
Se você tiver solicitações de recursos ou ideias para o aperfeiçoamentos, entre em contato conosco pelo [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).



<!--HONumber=Dec16_HO3-->


