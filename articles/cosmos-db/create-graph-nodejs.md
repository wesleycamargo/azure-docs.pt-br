---
title: Compilar um aplicativo Node.js do BD Cosmos do Azure usando a API do Graph | Microsoft Docs
description: "Apresenta um exemplo de código Node.js que pode ser usado para se conectar ao BD Cosmos do Azure e consultá-lo"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/21/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 5c996068ff5fbadda6730244c34c0d0d1f8fb447
ms.contentlocale: pt-br
ms.lasthandoff: 05/25/2017


---
# <a name="azure-cosmos-db-build-a-nodejs-application-using-the-graph-api"></a>BD Cosmos do Azure: compilar um aplicativo Node.js do BD Cosmos do Azure usando a API do Graph

O BD Cosmos do Azure é o serviço multimodelo de banco de dados distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do gráfico. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do BD Cosmos do Azure. 

Este início rápido demonstra como criar uma conta do BD Cosmos do Azure para a API do Graph (versão prévia), um banco de dados e um gráfico usando o Portal do Azure. Em seguida, você compila e executa um aplicativo de console usando o driver [Gremlin Node.js](https://www.npmjs.com/package/gremlin-secure) do OSS.  

> [!NOTE]
> O módulo NPM `gremlin-secure` é uma versão modificada do `gremlin` módulo, com suporte para SSL e SASL necessários para se conectar com Azure Cosmos DB. O código-fonte está disponível no [GitHub](https://github.com/CosmosDB/gremlin-javascript).
>

## <a name="prerequisites"></a>Pré-requisitos

* Antes que possa executar esta amostra, você deverá ter os seguintes pré-requisitos:
    * [Node.js](https://nodejs.org/en/) versão v0.10.29 ou superior
    * [Git](http://git-scm.com/)

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Em seguida, abra o arquivo da solução no Visual Studio. 

## <a name="review-the-code"></a>Examine o código

Façamos uma rápida análise do que está acontecendo no aplicativo. Abra o arquivo `app.js` e você descobrirá que essas linhas de código. 

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

Todas as configurações estão em `config.js` e podem ser editadas na seção Editar a seguir.

* Uma série de etapas do Gremlin são executadas usando o método `client.execute`.

    ```nodejs
    console.log('Running Count'); 
    client.execute("g.V().count()", { }, (err, results) => {
        if (err) return console.error(err);
        console.log(JSON.stringify(results));
        console.log();
    });
    ```

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo.

1. No [Portal do Azure](http://portal.azure.com/), na sua conta do BD Cosmos do Azure, no painel de navegação esquerdo, clique em **Chaves** e, em seguida, clique em **Chaves de leitura/gravação**. Você usa os botões de cópia no lado direito da tela para copiar o URI e a Chave Primária para o arquivo `app.js` na próxima etapa.

    ![Exibir e copiar uma chave de acesso no Portal do Azure, folha Chaves](./media/create-documentdb-dotnet/keys.png)

2. Copie o valor do URI do Gremlin do portal (usando o botão copiar) e transforme-o no valor da chave `config.endpoint` em config.js. O ponto de extremidade do Gremlin deve ser apenas o nome de host sem número de porta do protocolo como `mygraphdb.graphs.azure.com` (NÃO `https://mygraphdb.graphs.azure.com` ou `mygraphdb.graphs.azure.com:433`).

    `config.endpoint = "GRAPHENDPOINT";`

3. Em seguida, copie o valor da CHAVE PRIMÁRIA do portal e transforme-a no valor de config.primaryKey no config.js. Agora, você atualizou o aplicativo com todas as informações necessárias para se comunicar com o BD Cosmos do Azure. 

    `config.primaryKey = "PRIMARYKEY";`

4. Insira o nome do banco de dados e o nome do gráfico (contêiner) para o valor de config.database e config.collection. 

Aqui está um exemplo da aparência seu arquivo config.js concluído:

```nodejs
var config = {}

// Note that this must not have HTTPS or the port number
config.endpoint = "mygraphdb.graphs.azure.com";
config.primaryKey = "OjlhK6tjxfSXyKtrmCiM9O6gQQgu5DmgAoauzD1PdPIq1LZJmILTarHvrolyUYOB0whGQ4j21rdAFwoYep7Kkw==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>Execute o aplicativo de console

1. Abra uma janela de terminal e `cd` para um diretório de instalação para o arquivo package.json incluído no projeto.  

2. Execute `npm install` para instalar os módulos npm necessários. Isso inclui `gremlin-secure`.

3. Execute `node app.js` em um terminal para iniciar o aplicativo de nó.

Agora, é possível voltar ao Data Explorer e ver a consulta, modificar e trabalhar com esses novos dados. 

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
