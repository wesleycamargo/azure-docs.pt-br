---
title: Indexação do Armazenamento de Tabelas do Azure com a Pesquisa do Azure
description: Aprenda a indexar dados armazenados em Tabelas do Azure com a Pesquisa do Azure
services: search
documentationcenter: ''
author: chaosrealm
manager: pablocas
editor: ''

ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/16/2016
ms.author: eugenesh

---
# Indexação do Armazenamento de Tabelas do Azure com a Pesquisa do Azure
Este artigo mostra como usar a Pesquisa do Azure para indexar dados armazenados no Armazenamento de Tabelas do Azure. O novo indexador de tabelas da Pesquisa do Azure torna esse processo rápido e direto.

> [!IMPORTANT]
> Atualmente, essa funcionalidade está no modo de visualização. Ela está disponível somente na API REST usando a versão **2015-02-28-Preview** e na versão 2.0-preview do SDK do .NET. Lembre-se de que as APIs de visualização servem para teste e avaliação, e não devem ser usadas em ambientes de produção.
> 
> 

## Configuração da indexação de tabela do Azure
Para instalar e configurar um indexador de tabelas do Azure, você pode usar a API REST da Pesquisa do Azure para criar e gerenciar **indexadores** e **fontes de dados**, conforme descrito em [Operações do indexador](https://msdn.microsoft.com/library/azure/dn946891.aspx). Você também pode usar a [versão 2.0-preview](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) do SDK do .NET. No futuro, o suporte para a indexação de tabela será adicionado ao Portal do Azure.

Uma fonte de dados especifica quais dados indexar, as credenciais necessárias para acessar os dados e as políticas que permitem à Pesquisa do Azure identificar com eficiência as alterações nos dados (linhas novas, modificadas ou excluídas).

Um indexador lê dados de uma fonte de dados e carrega-os em um índice de pesquisa de destino.

Para configurar a indexação de tabela:

1. Criar uma fonte de dados
   * Definir o parâmetro `type` para `azuretable`
   * Passe a cadeia de conexão de sua conta de armazenamento como o parâmetro `credentials.connectionString`
   * Especifique o nome da tabela usando o parâmetro `container.name`
   * Opcionalmente, especifique uma consulta usando o parâmetro `container.query`. Sempre que possível, use um filtro em PartitionKey para obter o melhor desempenho. Qualquer outra consulta resultará em uma verificação completa da tavbela que pode resultar em baixo desempenho em tabelas grandes.
2. Crie um índice de pesquisa com o esquema que corresponde às colunas na tabela que você deseja indexar.
3. Crie o indexador conectando a fonte de dados ao índice de pesquisa.

### Criar a fonte de dados
    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Para obter mais informações sobre Criar a API da Fonte de Dados, consulte [Criar Fonte de Dados](search-api-indexers-2015-02-28-preview.md#create-data-source).

### Criar índice
    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
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

### Criar indexador
Por fim, crie o indexador que faz referência à fonte de dados e ao índice de destino. Por exemplo:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Para obter mais detalhes sobre Criar a API do Indexador, consulte [Criar Indexador](search-api-indexers-2015-02-28-preview.md#create-indexer).

E isso é tudo. Boa indexação!

## Lidando com nomes de campos diferentes
Geralmente, os nomes de campos no índice existente serão diferentes dos nomes de propriedades da sua tabela. Você pode usar os **mapeamentos de campo** para mapear os nomes de propriedade da tabela para os nomes de campo em seu índice de pesquisa. Para saber mais sobre os mapeamentos de campo, veja [Os mapeamentos de campo do indexador da Pesquisa do Azure reduzem as diferenças entre fontes de dados e índices de pesquisa](search-indexer-field-mappings.md).

## Manipulando chaves de documento
Na Pesquisa do Azure, a chave do documento identifica exclusivamente um documento. Cada índice de pesquisa deve ter exatamente um campo de chave do tipo `Edm.String`. O campo de chave é necessário para cada documento adicionado ao índice (é, na verdade, o único campo obrigatório).

Como as linhas de tabela têm uma chave composta, a Pesquisa do Azure gera um campo sintético chamado `Key` que é uma concatenação dos valores de chave de linha e de chave de partição. Por exemplo, se a PartitionKey de uma linha for `PK1` e a RowKey for `RK1`, o valor do campo `Key` será `PK1RK1`.

> [!NOTE]
> O valor `Key` pode conter caracteres inválidos em chaves de documento, como traços. É possível lidar com caracteres inválidos usando a [função de mapeamento de campo](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode`. Se você fizer isso, lembre-se também de usar a codificação de Base 64 protegida por URL ao transmitir as chaves de documento nas chamadas à API como Pesquisa.
> 
> 

## Indexação incremental e detecção de exclusão
Ao configurar um indexador de tabela para ser executado em um agendamento, ele reindexará somente linhas novas ou atualizadas, conforme determinado pelo valor `Timestamp` de uma linha. Não é necessário especificar uma política de detecção de alteração, a indexação incremental é habilitada automaticamente para você.

Para indicar que determinados documentos devem ser removidos do índice, você pode usar uma estratégia de exclusão reversível: em vez de excluir uma linha, adicione uma propriedade para indicar que ela deve ser excluída e configure uma política de detecção de exclusão reversível na fonte de dados. Por exemplo, a política mostrada abaixo considerará que uma linha foi excluída se tiver uma propriedade `IsDeleted` com o valor `"true"`:

    PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "query" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   


## Ajude-nos a aprimorar a Pesquisa do Azure
Se você tiver solicitações de recursos ou ideias para o aperfeiçoamentos, entre em contato conosco pelo [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0817_2016-->