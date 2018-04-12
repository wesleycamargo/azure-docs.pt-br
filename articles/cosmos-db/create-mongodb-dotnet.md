---
title: 'BD Cosmos do Azure: compilar um aplicativo Web com o .NET e com a API do MongoDB | Microsoft Docs'
description: Apresenta um exemplo de código .NET que pode ser usado para se conectar à API do MongoDB do BD Cosmos do Azure e consultá-la
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/19/2018
ms.author: sngun
ms.openlocfilehash: ab14261e939063c5e50050774d1aae3edf1bef19
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-build-a-mongodb-api-web-app-with-net-and-the-azure-portal"></a>BD Cosmos do Azure: compilar um aplicativo Web da API do MongoDB com o .NET e com o Portal do Azure

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do grafo. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do Azure Cosmos DB. 

Este início rápido demonstra como criar uma conta de [API do MongoDB](mongodb-introduction.md) do Azure Cosmos DB, um banco de dados de documento e uma coleção usando o Portal do Azure. Você compilará e implantará um aplicativo Web de lista de tarefas compilado no [driver .NET do MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Pré-requisitos para executar o aplicativo de exemplo

Para executar o exemplo, você precisará do [Visual Studio](https://www.visualstudio.com/downloads/) e uma conta válida do Azure CosmosDB.

Caso ainda não tenha o Visual Studio, baixe o [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) com a carga de trabalho de **desenvolvimento Web e ASP.NET** inserida na instalação.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="clone-the-sample-app"></a>Clonar o aplicativo de exemplo

Primeiro, baixe o aplicativo de API de exemplo do MongoDB do GitHub. Ele implementa uma lista de tarefas com o modelo de armazenamento de documentos do MongoDB.

1. Abra uma janela de terminal do Git, como git bash, e `cd` para um diretório de trabalho.
2. Execute o comando a seguir para clonar o repositório de exemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
    ```

Se não quiser usar o git, você também pode [baixar o projeto como um arquivo ZIP](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started/archive/master.zip).

## <a name="review-the-code"></a>Examine o código

Façamos uma rápida revisão do que está acontecendo no aplicativo. Abra o arquivo **Dal.cs** no diretório **DAL** e você verá que essas linhas de código criam os recursos do BD Cosmos do Azure. 

* Inicialize o cliente Mongo.

    ```cs
        MongoClientSettings settings = new MongoClientSettings();
        settings.Server = new MongoServerAddress(host, 10255);
        settings.UseSsl = true;
        settings.SslSettings = new SslSettings();
        settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

        MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
        MongoIdentityEvidence evidence = new PasswordEvidence(password);

        settings.Credentials = new List<MongoCredential>()
        {
            new MongoCredential("SCRAM-SHA-1", identity, evidence)
        };

        MongoClient client = new MongoClient(settings);
    ```

* Recupere o banco de dados e a coleção.

    ```cs
    private string dbName = "Tasks";
    private string collectionName = "TasksList";

    var database = client.GetDatabase(dbName);
    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
    ```

* Recupere todos os documentos.

    ```cs
    collection.Find(new BsonDocument()).ToList();
    ```

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao Portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo.

1. No [Portal do Azure](http://portal.azure.com/), na sua conta do BD Cosmos do Azure, no painel de navegação esquerdo, clique em **Cadeia de Conexão** e, em seguida, clique em **Chaves de leitura/gravação**. Você usará os botões de cópia no lado direito da tela para copiar o Nome de usuário, Senha e Host para o arquivo Dal.cs na próxima etapa.

2. Abra o arquivo **Dal.cs** no diretório **DAL**. 

3. Copie o valor do **nome de usuário** do portal (usando o botão de cópia) e transforme-o no valor do **nome de usuário** no arquivo **Dal.cs**. 

4. Em seguida, copie o valor de **host** do portal e transforme-o no valor do **host** no arquivo **Dal.cs**. 

5. Em seguida, copie sua **senha** do portal e transforme-o no valor da **senha** no arquivo **Dal.cs**. 

Agora, você atualizou o aplicativo com todas as informações necessárias para se comunicar com o Azure Cosmos DB. 
    
## <a name="run-the-web-app"></a>Executar o aplicativo Web

1. No Visual Studio, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e clique em **Gerenciar Pacotes NuGet**. 

2. Na caixa **Procurar** do NuGet, digite *MongoDB.Driver*.

3. Nos resultados, instale a biblioteca **MongoDB.Driver**. Isso instala o pacote do MongoDB.Driver, bem como todas as dependências.

4. Clique em CTRL + F5 para executar o aplicativo. Seu aplicativo é exibido no navegador. 

5. Clique em **Criar** no navegador e crie algumas novas tarefas em seu aplicativo de lista de tarefas.

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se você não continuar usando este aplicativo, exclua todos os recursos criados por esse início rápido no portal do Azure com as seguintes etapas:

1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e depois clique no nome do recurso criado. 
2. Em sua página de grupo de recursos, clique em **Excluir**, digite o nome do recurso para excluir na caixa de texto e depois clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do BD Cosmos do Azure e executar um aplicativo Web usando a API para MongoDB. Agora, é possível importar outros dados para sua conta do Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados no BD Cosmos do Azure para a API do MongoDB](mongodb-migrate.md)

