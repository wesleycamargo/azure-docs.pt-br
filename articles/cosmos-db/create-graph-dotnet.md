---
title: Compilar um aplicativo .NET do BD Cosmos do Azure usando a API do Graph | Microsoft Docs
description: "Apresenta um exemplo de código .NET que pode ser usado para conectar e consultar o BD Cosmos do Azure"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/14/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 1794341ed0d4519eef7f065d04ccf86a7e48a4a4
ms.contentlocale: pt-br
ms.lasthandoff: 07/17/2017

---
# <a name="azure-cosmos-db-build-a-net-application-using-the-graph-api"></a>BD Cosmos do Azure: compilar um aplicativo .NET usando a API do Graph

O BD Cosmos do Azure é o serviço multimodelo de banco de dados distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do gráfico. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do BD Cosmos do Azure. 

Este início rápido demonstra como criar uma conta do BD Cosmos do Azure, um banco de dados e um gráfico (contêiner) usando o Portal do Azure. Em seguida, você compila e executa um aplicativo de console criado na [API do Graph](graph-sdk-dotnet.md) (versão prévia).  

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não tem o Visual 2017 Studio instalado, poderá baixar e usar o **Visual Studio 2017 Community Edition** [gratuito](https://www.visualstudio.com/downloads/). Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Crie uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um gráfico

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, clonaremos um aplicativo de API do Graph do GitHub, definiremos a cadeia de conexão e o executaremos. Você verá como é fácil trabalhar usando dados de forma programática. 

1. Abra uma janela de terminal do Git, como git bash, e `cd` para um diretório de trabalho.  

2. Execute o comando a seguir para clonar o repositório de exemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-dotnet-getting-started.git
    ```

3. Em seguida, abra o arquivo da solução no Visual Studio. 

## <a name="review-the-code"></a>Examine o código

Façamos uma rápida análise do que está acontecendo no aplicativo. Abra o arquivo Program.cs e você verá que essas linhas de código criam os recursos do BD Cosmos do Azure. 

* O DocumentClient é inicializado. Na versão prévia, nós adicionamos uma API de extensão de gráfico no cliente Azure Cosmos DB. Estamos trabalhando em um cliente gráfico autônomo dissociado do cliente do Azure Cosmos DB e dos recursos.

    ```csharp
    using (DocumentClient client = new DocumentClient(
        new Uri(endpoint),
        authKey,
        new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    ```

* Um novo banco de dados é criado.

    ```csharp
    Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" });
    ```

* Um novo gráfico é criado.

    ```csharp
    DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri("graphdb"),
        new DocumentCollection { Id = "graph" },
        new RequestOptions { OfferThroughput = 1000 });
    ```
* Uma série de etapas do Gremlin são executadas usando o método `CreateGremlinQuery`.

    ```csharp
    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, "g.V().count()");
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    ```

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo.

1. No Portal do Azure, em sua conta do Azure Cosmos DB, clique em **Visão geral** no painel de navegação esquerdo. Você copiará o valor de **Gremlin URI** no arquivo App.config na próxima etapa. 

    ![Exibir e copiar uma chave de acesso no Portal do Azure, folha Chaves](./media/create-graph-dotnet/gremlin-uri.png)

    Se o valor **Gremlin URI** estiver em branco, você pode gerar o valor da página **Chaves** no portal, usando o valor da **URI**, removendo https:// e alterando os documentos para gráficos. 

2. No Visual Studio 2017, abra o arquivo App.config. 

3. Copie o valor do **URI Gremlin** e transforme-o no valor da chave do Ponto de extremidade em App.config. 

    `<add key="Endpoint" value="FILLME.graphs.azure.com:443" />`

4. No portal do Azure, clique em **Chaves** no menu de navegação à esquerda, copie o valor **CHAVE PRIMÁRIA** no portal e faça dele o valor da chave AuthKey em App.config e, em seguida, salve suas alterações. 

    `<add key="AuthKey" value="FILLME" />`

    ![Exibir e copiar uma chave primária no portal do Azure, na página de Chaves](./media/create-graph-dotnet/keys.png)

Agora, você atualizou o aplicativo com todas as informações necessárias para se comunicar com o BD Cosmos do Azure. 

## <a name="run-the-console-app"></a>Execute o aplicativo de console

1. No Visual Studio, clique com o botão direito do mouse no projeto **GraphGetStarted** no **Gerenciador de Soluções** e clique em **Gerenciar Pacotes NuGet**. 

2. Na caixa **Procurar** do NuGet, digite *Microsoft.Azure.Graphs* e marque a caixa **Inclui pré-lançamento**. 

3. Com base nos resultados, instale a biblioteca **Microsoft.Azure.Graphs**. Isso instala o pacote da biblioteca de extensão gráfica do BD Cosmos do Azure e todas as dependências.

4. Clique em CTRL + F5 para executar o aplicativo.

   A janela do console exibe os vértices e bordas sendo adicionadas ao gráfico. Quando o script for concluído, pressione ENTER duas vezes para fechar a janela do console. 

## <a name="browse-using-the-data-explorer"></a>Navegar usando o Data Explorer

Agora você pode voltar ao Data Explorer no Portal do Azure e procurar e consultar seus novos dados de gráfico.

* No Data Explorer, o novo banco de dados aparece no painel Coleções. Expanda **graphdb**, **graphcoll** e, em seguida, clique em **Gráfico**.

    Os dados gerados pelo aplicativo de exemplo são exibidos no painel Gráficos.

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se você não continuar usando este aplicativo, exclua todos os recursos criados por esse início rápido no portal do Azure com as seguintes etapas: 

1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e depois clique no nome do recurso criado. 
2. Em sua página de grupo de recursos, clique em **Excluir**, digite o nome do recurso para excluir na caixa de texto e depois clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do BD Cosmos do Azure, como criar um gráfico usando o Data Explorer e como executar um aplicativo. Agora, você pode criar consultas mais complexas e implementar uma lógica de passagem de gráfico avançada usando o Gremlin. 

> [!div class="nextstepaction"]
> [Consultar usando o Gremlin](tutorial-query-graph.md)


