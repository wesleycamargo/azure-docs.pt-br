<properties 
	pageTitle="Compilar um serviço usando um armazenamento de dados não relacionais - Serviços Móveis do Azure" 
	description="Aprenda a usar um armazenamento de dados não relacionais, como o MongoDB ou armazenamento de tabela do Azure com seu serviço móvel baseado em .NET" 
	services="mobile-services" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="mahender"/>

# Compilar um Serviço Usando o MongoDB como o armazenamento de dados com o back-end do .NET

Este tópico mostra como usar o armazenamento de dados não relacionais para seu serviço móvel. Neste tutorial, você modificará o projeto de início rápido dos Serviços Móveis para usar o MongoDB em vez de um SQL como um armazenamento de dados.

Este tutorial apresenta as etapas para configuração de um armazenamento não relacional:

1. [Criar um non-relational store]
2. [Modificar dados e controladores]
3. [Testar o aplicativo]

O tutorial exige a conclusão do tutorial [Introdução aos Serviços Móveis] ou [Introdução aos Dados].

## <a name="create-store"></a>Criar um non-relational store

1. No [Portal de Gerenciamento do Azure], clique em **Novo** e selecione **Armazenamento**.

2. Selecione o suplemento **MongoLab** e navegue pelo assistente para se inscrever em uma conta. Para saber mais sobre o MongoLab, consulte a [Página de Suplemento do MongoLab].

    ![][0]

2. Quando a conta estiver definida, selecione **Informações de conexão** e copie a cadeia de conexão.

3. Navegue até a seção de Serviços Móveis do portal e selecione a guia **Configurar**.

4. Em **Configurações do aplicativo**, insira sua cadeia de conexão com a chave "MongoConnectionString" e clique em **Salvar**.

    ![][1]

2. Adicione o seguinte a `TodoItemController`:

        static bool connectionStringInitialized = false;

        private void InitializeConnectionString(string connectionStringName)
        {
            if (!connectionStringInitialized)
            {
                connectionStringInitialized = true;
                if (!this.Services.Settings.Connections.ContainsKey(connectionStringName))
                {
                    var connectionString = this.Services.Settings[connectionStringName];
                    var connectionSetting = new ConnectionSettings(connectionStringName, connectionString);
                    this.Services.Settings.Connections.Add(connectionStringName, connectionSetting);
                }
            }
        }
    
    Esse código carregará a configuração do aplicativo e dirá ao serviço móvel para tratá-lo como uma conexão que pode ser usada por um `TableController`. Em seguida, é possível chamar esse método quando o `TodoItemController` for chamado.



## <a name="modify-service"></a>Modificar dados e controladores

1. Instale o pacote NuGet **WindowsAzure.MobileServices.Backend.Mongo**.

2. Modifique `TodoItem` para derivar de `DocumentData` em vez de `EntityData`.

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3. No `TodoItemController`, substitua o método `Initialize` pelo seguinte:

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            InitializeConnectionString(connectionStringName);
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, collectionName, Request, Services);
        }

4. No código para o método `Initialize` acima, substitua **NOME-DO-SEU-BANCO-DE-DADOS** pelo nome escolhido por você ao fornecer o suplemento MongoLab.


## <a name="test-application"></a>Testar o aplicativo

1. Republicar seu projeto de back-end de serviço móvel.

2. Executar seu aplicativo cliente. Observe que você não verá nenhum item que foi previamente armazenado no banco de dados SQL do tutorial do Início rápido.

3. Criar um novo item. O aplicativo deve se comportar como anteriormente, exceto que agora seus dados irão para seu armazenamento não relacional.


<!-- Anchors. -->
[Criar um non-relational store]: #create-store
[Modificar dados e controladores]: #modify-service
[Testar o aplicativo]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[Introdução aos Serviços Móveis]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Introdução aos Dados]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[What is the Table Service]: storage-dotnet-how-to-use-tables.md#what-is
[Página de Suplemento do MongoLab]: /gallery/store/mongolab/mongolab

<!--HONumber=54-->