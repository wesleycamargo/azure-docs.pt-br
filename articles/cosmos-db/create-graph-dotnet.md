---
title: Compilar um aplicativo .NET Framework ou Core do Azure Cosmos DB usando a API do Graph | Microsoft Docs
description: Apresenta um exemplo de código do .NET Framework/Core que pode ser usado para conectar e consultar o Azure Cosmos DB
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.custom: quick start connect, mvc
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/08/2018
ms.author: lbosq
ms.openlocfilehash: 75bca93b8aa4329a20e7491f1a0e1318cdbc13a5
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214652"
---
# <a name="azure-cosmos-db-build-a-net-framework-or-core-application-using-the-graph-api"></a>Azure Cosmos DB: compilar um aplicativo .NET Framework ou Core usando a API do Graph

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do grafo. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do Azure Cosmos DB. 

Este início rápido demonstra como criar uma conta de [API do Graph](graph-introduction.md) do Azure Cosmos DB, um banco de dados e um grafo (contêiner) usando o Portal do Azure. Depois, compile e execute um aplicativo de console criado usando o driver de código-aberto [Gremlin.Net](http://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet).  

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não tem o Visual 2017 Studio instalado, poderá baixar e usar o **Visual Studio 2017 Community Edition** [gratuito](https://www.visualstudio.com/downloads/). Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.

Se você já tem o Visual Studio 2017 instalado, verifique se está com o [Visual Studio 2017 Atualização 3](https://www.visualstudio.com/en-us/news/releasenotes/vs2017-relnotes).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um grafo

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, clonaremos um aplicativo de API do Graph do GitHub, definiremos a cadeia de conexão e o executaremos. Você verá como é fácil trabalhar usando dados de forma programática. 

1. Abra um prompt de comando, crie uma nova pasta chamada exemplos de git e feche o prompt de comando.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para a nova pasta para instalar o aplicativo de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-gremlindotnet-getting-started.git
    ```

4. Em seguida, abra o Visual Studio e o arquivo da solução.

5. Restaure os pacotes NuGet no projeto. Isso deve incluir o driver Gremlin.Net, bem como o pacote Newtonsoft.Json.


6. Também é possível instalar o driver Gremlin.Net manualmente, usando o gerenciador de pacotes Nuget ou o [utilitário de linha de comando do nuget](https://docs.microsoft.com/en-us/nuget/install-nuget-client-tools): 

    ```bash
    nuget install Gremlin.Net
    ```

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como os recursos de banco de dados são criados no código, poderá examinar os trechos de código a seguir. Caso contrário, você poderá pular para [Atualizar sua cadeia de conexão](#update-your-connection-string). 

Todos os trechos de código a seguir são retirados do arquivo Program.cs.

* Defina os parâmetros de conexão baseados na conta criada acima (linha 19): 

    ```csharp
    private static string hostname = "your-endpoint.gremlin.cosmosdb.azure.com";
    private static int port = 443;
    private static string authKey = "your-authentication-key";
    private static string database = "your-database";
    private static string collection = "your-graph-container";
    ```

* Os comandos de Gremlin a serem executados são listados em um dicionário (linha 26):

    ```csharp
    private static Dictionary<string, string> gremlinQueries = new Dictionary<string, string>
    {
        { "Cleanup",        "g.V().drop()" },
        { "AddVertex 1",    "g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44)" },
        { "AddVertex 2",    "g.addV('person').property('id', 'mary').property('firstName', 'Mary').property('lastName', 'Andersen').property('age', 39)" },
        { "AddVertex 3",    "g.addV('person').property('id', 'ben').property('firstName', 'Ben').property('lastName', 'Miller')" },
        { "AddVertex 4",    "g.addV('person').property('id', 'robin').property('firstName', 'Robin').property('lastName', 'Wakefield')" },
        { "AddEdge 1",      "g.V('thomas').addE('knows').to(g.V('mary'))" },
        { "AddEdge 2",      "g.V('thomas').addE('knows').to(g.V('ben'))" },
        { "AddEdge 3",      "g.V('ben').addE('knows').to(g.V('robin'))" },
        { "UpdateVertex",   "g.V('thomas').property('age', 44)" },
        { "CountVertices",  "g.V().count()" },
        { "Filter Range",   "g.V().hasLabel('person').has('age', gt(40))" },
        { "Project",        "g.V().hasLabel('person').values('firstName')" },
        { "Sort",           "g.V().hasLabel('person').order().by('firstName', decr)" },
        { "Traverse",       "g.V('thomas').out('knows').hasLabel('person')" },
        { "Traverse 2x",    "g.V('thomas').out('knows').hasLabel('person').out('knows').hasLabel('person')" },
        { "Loop",           "g.V('thomas').repeat(out()).until(has('id', 'robin')).path()" },
        { "DropEdge",       "g.V('thomas').outE('knows').where(inV().has('id', 'mary')).drop()" },
        { "CountEdges",     "g.E().count()" },
        { "DropVertex",     "g.V('thomas').drop()" },
    };
    ```


* Crie um objeto de conexão `GremlinServer` usando os parâmetros fornecidos acima (linha 52):

    ```csharp
    var gremlinServer = new GremlinServer(hostname, port, enableSsl: true, 
                                                    username: "/dbs/" + database + "/colls/" + collection, 
                                                    password: authKey);
    ```

* Crie um novo objeto `GremlinClient` (linha 56):

    ```csharp
    var gremlinClient = new GremlinClient(gremlinServer);
    ```

* Execute cada consulta Gremlin usando o objeto `GremlinClient` com uma tarefa assíncrona (linha 63). Isso lerá as consultas Gremlin do dicionário definido acima (linha 26):

    ```csharp
    var task = gremlinClient.SubmitAsync<dynamic>(query.Value);
    task.Wait();
    ```

* Recupere o resultado e leia os valores, que são formatados como um dicionário, usando a classe `JsonSerializer` do Newtonsoft.Json:

    ```csharp
    foreach (var result in task.Result)
    {
        // The vertex results are formed as dictionaries with a nested dictionary for their properties
        string output = JsonConvert.SerializeObject(result);
        Console.WriteLine(String.Format("\tResult:\n\t{0}", output));
    }
    ```

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo.

1. No [portal do Azure](http://portal.azure.com/), navegue até sua conta de banco de dados do gráfico. Na guia **Visão geral**, você pode ver dois pontos de extremidade - 
 
   **.NET SDK URI** - esse valor é usado quando você se conectar à conta do graph usando a biblioteca Microsoft.Azure.Graphs. 

   **Ponto de Extremidade do Gremlin** - Esse valor é usado ao se conectar à conta do Graph usando a biblioteca Gremlin.Net.

    ![Copiar o ponto de extremidade](./media/create-graph-dotnet/endpoint.png)

   Para executar este exemplo, copie o valor do **Ponto de Extremidade do Gremlin**, exclua o número da porta no final e o URI se tornará `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com`

2. Em Program.cs, cole o valor sobre `your-endpoint` na variável `hostname` na linha 19. 

    `"private static string hostname = "<your cosmos db account name>.gremlin.cosmosdb.azure.com";`

    Agora, o valor de ponto de extremidade deve ter esta aparência:

    `"private static string hostname = "testgraphacct.gremlin.cosmosdb.azure.com";`

3. Em seguida, navegue até a guia **Chave** e copie **CHAVE PRIMÁRIA**`authkey` no portal e cole-o na variável, substituindo o espaço reservado `"your-authentication-key"` na linha 21. 

    `private static string authKey = "your-authentication-key";`

4. Usando as informações do banco de dados criado acima, cole o nome do banco de dados dentro da variável `database` na linha 22. 

    `private static string database = "your-database";`

5. De modo semelhante, usando as informações do contêiner criada acima, cole o contêiner (que também é o nome do grafo) dentro da variável `collection` na linha 23. 

    `private static string collection = "your-collection-or-graph";`

6. Salve o arquivo Program.cs. 

Agora, você atualizou o aplicativo com todas as informações necessárias para se comunicar com o Azure Cosmos DB. 

## <a name="run-the-console-app"></a>Execute o aplicativo de console

Clique em CTRL + F5 para executar o aplicativo. O aplicativo imprimirá os comandos e resultados de consulta Gremlin no console.

   A janela do console exibe os vértices e bordas sendo adicionadas ao grafo. Quando o script for concluído, pressione ENTER para fechar a janela do console.

## <a name="browse-using-the-data-explorer"></a>Navegar usando o Data Explorer

Agora você pode voltar ao Data Explorer no Portal do Azure e procurar e consultar seus novos dados de grafo.

1. No Data Explorer, o novo banco de dados aparece no painel Grafos. Expanda os nós do banco de dados e do contêiner, depois clique em **Grafo**.

2. Clique no botão **Aplicar Filtro** para usar a consulta padrão para exibir todos os vértices no grafo. Os dados gerados pelo aplicativo de exemplo são exibidos no painel Grafos.

    É possível ampliar e diminuir o zoom do grafo, expandir o espaço de exibição do grafo, adicionar outros vértices e mover vértices na superfície de exibição.

    ![Exibir o grafo no Data Explorer no Portal do Azure](./media/create-graph-dotnet/graph-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do Azure Cosmos DB, como criar um grafo usando o Data Explorer e como executar um aplicativo. Agora, você pode criar consultas mais complexas e implementar uma lógica de passagem de grafo avançada usando o Gremlin. 

> [!div class="nextstepaction"]
> [Consultar usando o Gremlin](tutorial-query-graph.md)

