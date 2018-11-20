---
title: Aprender a gerenciar contas de banco de dados no Azure Cosmos DB
description: Aprender a gerenciar contas de banco de dados no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: 97fb5c2558d55b3f80f2e771971faa109a930c5f
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626416"
---
# <a name="manage-indexing-in-azure-cosmos-db"></a>Gerenciar indexação no Azure Cosmos DB

No Azure Cosmos DB, você pode escolher se deseja que um contêiner indexe automaticamente todos os itens ou não. Por padrão, todos os itens no contêiner do Azure Cosmos são indexados automaticamente, mas você pode desligar a indexação automática. Quando a indexação estiver desativada, os itens poderão ser acessados pelos self-links ou por meio de consultas usando a ID do item, como ID de documento. Você pode solicitar explicitamente os resultados sem o uso de índice passando o cabeçalho **x-ms-documentdb-enable-scan**  na API REST ou na opção de solicitação **EnableScanInQuery** usando o SDK do .NET.

Com a indexação automática desativada, você ainda pode adicionar seletivamente itens específicos ao índice. Em contrapartida, é possível deixar a indexação automática ativada e optar seletivamente para excluir itens específicos. As configurações de Ativar/desativar a indexação são úteis quando você tem um subconjunto dos itens que precisa ser consultado.  

A taxa de transferência de gravação e unidades de solicitação são proporcionais ao número de valores que precisam ser indexados, o que é especificado pelo conjunto incluído na política de indexação. Se você tiver uma boa compreensão dos padrões de consulta, escolha explicitamente o subconjunto de inclusão/exclusão de caminhos para melhorar a taxa de transferência de gravação.

## <a name="manage-indexing-using-azure-portal"></a>Gerenciar a indexação usando o portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com/).

2. Crie uma nova conta do Azure Cosmos ou selecione uma conta existente.

3. Abra o painel **Data Explorer**.

4. Selecione um contêiner existente, expanda-o e modifique os valores a seguir:

   * Abra a janela **Escala e Configurações**.
   * Altere **indexingMode** de *consistent* para *none*, ou inclua/exclua determinados caminhos da indexação.
   * Clique em **OK** para salvar as alterações.

   ![Gerenciar a indexação usando o portal do Azure](./media/how-to-manage-indexing/how-to-manage-indexing-portal.png)

## <a name="manage-indexing-using-azure-sdks"></a>Gerenciar a indexação usando os SDKs do Azure

### <a id="dotnet"></a>SDK .NET

O exemplo a seguir mostra como incluir um item explicitamente usando o [SDK do .NET na API do SQL](sql-api-sdk-dotnet.md) e a propriedade [RequestOptions.IndexingDirective](/dotnet/api/microsoft.azure.documents.client.requestoptions.indexingdirective).

```csharp
// To override the default behavior to exclude (or include) a document in indexing,
// use the RequestOptions.IndexingDirective property.
client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new { id = "myDocumentId", isRegistered = true },
    new RequestOptions { IndexingDirective = IndexingDirective.Include });
```

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre indexação nos artigos a seguir:

* [Visão geral de indexação](index-overview.md)
* [Política de indexação](index-policy.md)
* [Tipos de índice](index-types.md)
* [Caminhos de índice](index-paths.md)
