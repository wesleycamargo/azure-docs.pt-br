<properties 
	pageTitle="Exemplos de Banco de Dados de Documentos para MongoDB | Microsoft Azure" 
	description="Encontre exemplos de suporte de protocolo do Banco de Dados de Documentos para MongoDB." 
	keywords="exemplos do mongodb"
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/23/2016" 
	ms.author="stbaro"/>

# Exemplos de suporte de protocolo do Banco de Dados de Documentos para MongoDB
Para usar estes exemplos, você deve:

- [Criar](documentdb-create-mongodb-account.md) uma conta do Banco de Dados de Documentos do Azure com suporte de protocolo para MongoDB.
- Recuperar as informações de [cadeia de conexão](documentdb-connect-mongodb-account.md) da sua conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB.

## Começar com uma amostra de aplicativo de lista de tarefas do ASP.NET MVC

Você pode usar o tutorial [Criar um aplicativo Web do Azure que se conecte ao MongoDB em execução em uma máquina virtual](../app-service-web/web-sites-dotnet-store-data-mongodb-vm.md), com modificação mínima, para configurar rapidamente um aplicativo MongoDB (localmente ou publicado em um aplicativo Web do Azure) que se conecte a uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB.

1. Siga o tutorial, com uma modificação. Substitua o código Dal.cs por este:
	
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
            		settings.Server = new MongoServerAddress(host, 10250);
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
            		settings.Server = new MongoServerAddress(host, 10250);
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

2.	Modifique as seguintes variáveis no arquivo Dal.cs de acordo com as configurações da sua conta:

        private string userName = "<your user name>";
    	private string host = "<your host>";
        private string password = "<your password>";

3. Use o aplicativo!

## Próximas etapas

- Leia sobre as [diretrizes de desenvolvimento de visualização](documentdb-mongodb-guidelines.md) do Banco de Dados de Documentos com suporte de protocolo para MongoDB.
- Saiba como [usar o MongoChef](documentdb-mongodb-mongochef.md) com uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB.

 

<!---HONumber=AcomDC_0824_2016-->