---
title: Criar um aplicativo Node.js do Azure Cosmos DB usando a API do Gremlin | Microsoft Docs
description: Apresenta um exemplo de código Node.js que pode ser usado para se conectar ao BD Cosmos do Azure e consultá-lo
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.custom: quick start connect, mvc
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 01/08/2018
ms.author: lbosq
ms.openlocfilehash: 5f7e2a30ee4ea069e8c08187312f09e33a5a921a
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700021"
---
# <a name="azure-cosmos-db-build-a-nodejs-application-by-using-gremlin-api"></a>Azure Cosmos DB: criar um aplicativo Node.js usando a API do Gremlin

> [!div class="op_single_selector"]
> * [Console do Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

O Azure Cosmos DB é o serviço de banco de dados multi-modelo distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do grafo. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do Azure Cosmos DB. 

Este início rápido demonstra como criar uma conta de [API do Gremlin](graph-introduction.md) do Azure Cosmos DB, um banco de dados e um grafo usando o Portal do Azure. Em seguida, você compila e executa um aplicativo de console usando o driver [Gremlin Node.js](https://www.npmjs.com/package/gremlin) de software livre.

## <a name="prerequisites"></a>Pré-requisitos

Antes que possa executar esta amostra, você deverá ter os seguintes pré-requisitos:
* [Node.js](https://nodejs.org/en/) versão v0.10.29 ou posterior
* [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um grafo

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora vamos clonar um aplicativo de API do Gremlin do GitHub, definir a cadeia de conexão e executá-lo. Você verá como é fácil trabalhar usando dados de forma programática. 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Abra o arquivo da solução no Visual Studio. 

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como os recursos de banco de dados são criados no código, poderá examinar os trechos de código a seguir. Caso contrário, você poderá pular para [Atualizar sua cadeia de conexão](#update-your-connection-string). 

Todos os trechos de código a seguir são retirados do arquivo app.js.

* O cliente Gremlin é criado.

    ```nodejs
    const client = Gremlin.createClient(
        443, 
        config.endpoint, 
        { 
            "session": false, 
            "ssl": true, 
            "user": `/dbs/${config.database}/colls/${config.collection}`,
            "password": config.primaryKey
        });
    ```

  Todas as configurações estão em `config.js` e podem ser editadas na [seção a seguir](#update-your-connection-string).

* Uma série de funções são definidas para executar operações de Gremlin diferentes. Este é um deles:

    ```nodejs
    function addVertex1(callback)
    {
        console.log('Running Add Vertex1'); 
        client.execute("g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44).property('userid', 1)", { }, (err, results) => {
          if (err) callback(console.error(err));
          console.log("Result: %s\n", JSON.stringify(results));
          callback(null)
        });
    }
    ```

* Cada função executa um `client.execute` método com um parâmetro de cadeia de caracteres de consulta Gremlin. Aqui está um exemplo de como `g.V().count()` é executado:

    ```nodejs
    console.log('Running Count'); 
    client.execute("g.V().count()", { }, (err, results) => {
        if (err) return console.error(err);
        console.log(JSON.stringify(results));
        console.log();
    });
    ```

* No final do arquivo, todos os métodos, em seguida, são invocados usando o `async.waterfall()` método. Isso irá executá-los um após o outro:

    ```nodejs
    try{
        async.waterfall([
            dropGraph,
            addVertex1,
            addVertex2,
            addEdge,
            countVertices
            ], finish);
    } catch(err) {
        console.log(err)
    }
    ```


## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

1. Abra o arquivo config.js. 

2. Em config.js, preencha a `config.endpoint` chave com o **valor** Gremlin URI da página **Visão geral** do portal do Azure. 

    `config.endpoint = "GRAPHENDPOINT";`

    ![Exibir e copiar uma chave de acesso no Portal do Azure, folha Chaves](./media/create-graph-nodejs/gremlin-uri.png)

   Se o valor do **URI do Gremlin** estiver em branco, você pode gerar o valor a partir da página **Chaves** no portal. Use o valor do **URI**, remova https:// e altere os documentos para gremlin.cosmosdb. Se sua conta de gráfico foi criada antes de 20 de dezembro de 2017, altere os documentos para gráficos. 

   O ponto de extremidade do Gremlin deve ser apenas o nome de host sem número de porta do protocolo como `mygraphdb.gremlin.cosmosdb.azure.com` (não `https://mygraphdb.gremlin.cosmosdb.azure.com` ou `mygraphdb.gremlin.cosmosdb.azure.com:433`).

3. Em config.js, preencha o valor config.primaryKey com o valor **Chave primária** na página do portal do Azure **Chaves**. 

    `config.primaryKey = "PRIMARYKEY";`

   ![Folha “Chaves” do portal do Azure](./media/create-graph-nodejs/keys.png)

4. Insira o nome do banco de dados e o nome do grafo (contêiner) para o valor de config.database e config.collection. 

Aqui está um exemplo da aparência do seu arquivo config.js concluído:

```nodejs
var config = {}

// Note that this must not have HTTPS or the port number
config.endpoint = "testgraphacct.gremlin.cosmosdb.azure.com";
config.primaryKey = "Pams6e7LEUS7LJ2Qk0fjZf3eGo65JdMWHmyn65i52w8ozPX2oxY3iP0yu05t9v1WymAHNcMwPIqNAEv3XDFsEg==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>Execute o aplicativo de console

1. Abra uma janela de terminal e mude (por meio do comando `cd`) para um diretório de instalação para o arquivo package.json incluído no projeto.

2. Executar `npm install` para instalar os módulos npm necessários, incluindo `gremlin`.

3. Execute `node app.js` em um terminal para iniciar o aplicativo de nó.

## <a name="browse-with-data-explorer"></a>Procurar com o Data Explorer

Agora você pode voltar ao Data Explorer no Portal do Azure e exibir, consultar, modificar e trabalhar com seus novos dados dos grafos.

No Data Explorer, o novo banco de dados aparece no painel **Grafos**. Expanda o banco de dados, seguido pelo contêiner e, em seguida, selecione **Grafo**.

Os dados gerados pelo aplicativo de exemplo são exibidos no próximo painel dentro da guia **Grafo** quando você seleciona **Aplicar filtro**.

Tente preencher `g.V()` com `.has('firstName', 'Thomas')` para testar o filtro. Observe que o valor diferencia maiúsculas de minúsculas.

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>Limpar seus recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como criar uma conta do Azure Cosmos DB, como criar um grafo usando o Data Explorer e como executar um aplicativo. Agora, você pode criar consultas mais complexas e implementar uma lógica de passagem de grafo avançada usando o Gremlin. 

> [!div class="nextstepaction"]
> [Consultar usando o Gremlin](tutorial-query-graph.md)
