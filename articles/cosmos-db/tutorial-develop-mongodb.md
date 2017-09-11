---
title: Usar a API do Azure Cosmos DB para MongoDB para compilar um aplicativo Web | Microsoft Docs
description: Um tutorial do Azure Cosmos DB que cria um aplicativo Web de banco de dados online usando a API do MongoDB.
keywords: exemplos do mongodb
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 61a2ab3a-2fc3-4d49-a263-ed87c66628f6
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: anhoh
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 47ec9998c68455a90b505c917156d9b6b0fe0f24
ms.contentlocale: pt-br
ms.lasthandoff: 06/03/2017

---
# <a name="azure-cosmos-db-connect-to-a-mongodb-app-using-net"></a>Azure Cosmos DB: Conectar-se a um aplicativo do MongoDB usando .NET

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do gráfico. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do Azure Cosmos DB. 

Este tutorial demonstra como criar uma conta do Azure Cosmos DB usando o Portal do Azure e como criar um banco de dados e coleção para armazenar dados usando a [API do MongoDB](mongodb-introduction.md). 

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma conta do Azure Cosmos DB 
> * Atualizar sua cadeia de conexão
> * Criar um aplicativo do MongoDB em uma máquina virtual 


## <a name="create-a-database-account"></a>Crie uma conta de banco de dados

Vamos começar criando uma conta do Azure Cosmos DB no portal do Azure.  

> [!TIP]
> * Já tem uma conta do Azure Cosmos DB? Nesse caso, pule para [Configurar sua solução do Visual Studio](#SetupVS)
> * Você tinha uma conta do Azure DocumentDB? Se sua conta agora é uma conta do Azure Cosmos DB, você pode pular para [Configurar sua solução do Visual Studio](#SetupVS).  
> * Se estiver usando o Emulador do Azure Cosmos DB, execute as etapas em [Emulador do Azure Cosmos DB](local-emulator.md) para configurar o emulador e pule para [Configurar sua solução do Visual Studio](#SetupVS). 
>
>

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

1. No Portal do Azure, na página **Azure Cosmos DB**, selecione a API para a conta do MongoDB. 
2. Na barra esquerda da folha da conta, clique em **Início rápido**. 
3. Escolha sua plataforma (*driver do .NET*, *driver do Node.js*, *Shell do MongoDB*, *driver do Java*, *driver do Python*). Caso não veja seu driver ou ferramenta na lista, não se preocupe, pois documentamos continuamente mais trechos de código de conexão. 
4. Copie e cole o trecho de código no seu aplicativo MongoDB e você estará pronto para seguir adiante.

## <a name="set-up-your-mongodb-app"></a>Configurar seu aplicativo MongoDB

Você pode usar o tutorial [Criar um aplicativo Web do Azure que se conecte ao MongoDB em execução em uma máquina virtual](../app-service-web/web-sites-dotnet-store-data-mongodb-vm.md), com modificação mínima, para configurar rapidamente um aplicativo MongoDB (localmente ou publicado em um aplicativo Web do Azure) que se conecte a uma conta da API do MongoDB.  

1. Siga o tutorial, com uma modificação.  Substitua o código Dal.cs por este:

    ```csharp   
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MyTaskListApp.Models;
    using MongoDB.Driver;
    using MongoDB.Bson;
    using System.Configuration;
    using System.Security.Authentication;
   
    namespace MyTaskListApp
    {
        public class Dal : IDisposable
        {
            //private MongoServer mongoServer = null;
            private bool disposed = false;
   
            // To do: update the connection string with the DNS name
            // or IP address of your server. 
            //For example, "mongodb://testlinux.cloudapp.net
            private string connectionString = "mongodb://localhost:27017";
            private string userName = "<your user name>";
            private string host = "<your host>";
            private string password = "<your password>";
   
            // This sample uses a database named "Tasks" and a 
            //collection named "TasksList".  The database and collection 
            //will be automatically created if they don't already exist.
            private string dbName = "Tasks";
            private string collectionName = "TasksList";
   
            // Default constructor.        
            public Dal()
            {
            }
   
            // Gets all Task items from the MongoDB server.        
            public List<MyTask> GetAllTasks()
            {
                try
                {
                    var collection = GetTasksCollection();
                    return collection.Find(new BsonDocument()).ToList();
                }
                catch (MongoConnectionException)
                {
                    return new List<MyTask>();
                }
            }
   
            // Creates a Task and inserts it into the collection in MongoDB.
            public void CreateTask(MyTask task)
            {
                var collection = GetTasksCollectionForEdit();
                try
                {
                    collection.InsertOne(task);
                }
                catch (MongoCommandException ex)
                {
                    string msg = ex.Message;
                }
            }
   
            private IMongoCollection<MyTask> GetTasksCollection()
            {
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
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
   
            private IMongoCollection<MyTask> GetTasksCollectionForEdit()
            {
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
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
   
            # region IDisposable
   
            public void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
   
            protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                    }
                }
   
                this.disposed = true;
            }
   
            # endregion
        }
    }
    ```

2. Modifique as seguintes variáveis no arquivo Dal.cs de acordo com as configurações da sua conta na página Chaves do Portal do Azure:

    ```csharp   
    private string userName = "<your user name>";
    private string host = "<your host>";
    private string password = "<your password>";
    ```

3. Use o aplicativo!

## <a name="clean-up-resources"></a>Limpar recursos

Se você não continuar usando este aplicativo, siga as seguintes etapas para excluir todos os recursos criados neste tutorial no portal do Azure. 

1. No menu à esquerda no portal do Azure, clique em **Grupos de recursos** e depois clique no nome do recurso criado. 
2. Em sua página de grupo de recursos, clique em **Excluir**, digite o nome do recurso para excluir na caixa de texto e depois clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Criar uma conta do Azure Cosmos DB 
> * Atualizar sua cadeia de conexão
> * Criar um aplicativo do MongoDB em uma máquina virtual

Você pode prosseguir para o próximo tutorial e importar seus dados do MongoDB para o Azure Cosmos DB.  

> [!div class="nextstepaction"]
> [Importar dados do MongoDB no Azure Cosmos DB](mongodb-migrate.md)


