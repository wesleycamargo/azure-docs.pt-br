<properties urlDisplayName="Build a Service Using a Non-Relational Data Store" pageTitle="Compilar um serviço usando um armazenamento de dados não relacionais - Serviços Móveis do Azure" metaKeywords="" description="Aprenda a usar um armazenamento de dados não relacionais, como o MongoDB ou armazenamento de tabela do Azure com seu serviço móvel baseado em .NET" metaCanonical="" services="" documentationCenter="Mobile" title="Build a Service Using a Non-Relational Data Store" authors="yavorg, mahender" solutions="" manager="dwrede" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg, mahender" />

# Compilar um Serviço Usando o MongoDB como o armazenamento de dados com o back-end do .NET

Este tópico mostra como usar o armazenamento de dados não relacionais para seu serviço móvel. Neste tutorial, você modificará o projeto de início rápido dos Serviços Móveis para usar o MongoDB em vez de um SQL como um armazenamento de dados.

Este tutorial apresenta as etapas para configuração de um armazenamento não relacional:

1. [Criar um armazenamento não relacional]
2. [Modificar dados e controladores]
3. [Testar o aplicativo]

O tutorial requer a conclusão de um dos tutoriais [Introdução aos Serviços Móveis] ou [Introdução aos Dados].

## <a name="create-store"></a>Criar um armazenamento não relacional

1. No [Portal de Gerenciamento do Azure], clique em **Novo** e selecione **Armazenamento**.

2. Selecione o suplemento **MongoLab** e navegue pelo assistente para se inscrever em uma conta. Para saber mais sobre o MongoLab, consulte a [Página do Suplemento MongoLab].

    ![][0]

2. Quando a conta estiver definida, selecione **Informações de Conexão** e copie a cadeia de conexão.

3. Navegue até a seção de Serviços Móveis do portal e selecione a guia **Configurar**.

4. Em **Configurações do aplicativo**, insira sua cadeia de conexão com a chave "MongoConnectionString" e clique em **Salvar**.

    ![][1]

2. Adicione o seguinte código ao `TodoItemController`:

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
    
    Esse código carregará a configuração de aplicativo e dirá ao serviço móvel para tratá-lo como uma conexão que pode ser usada por um `TableController`. Em seguida, é possível chamar esse método quando o `TodoItemController` for chamado.



## <a name="modify-service"></a>Modificar dados e controladores

1. Instale o pacote NuGet do **WindowsAzure.MobileServices.Backend.Mongo**.

2. Modifique `TodoItem` para que seja derivado de `DocumentData` em vez de `EntityData`.

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

4. No código para o método `Initialize` acima, substitua **SEU-NOME-DO-BANCO-DE-DADOS** pelo nome escolhido por você ao fornecer o suplemento MongoLab.


## <a name="test-application"></a>Testar o aplicativo

1. Republicar seu projeto de back-end de serviço móvel.

2. Executar seu aplicativo cliente. Observe que você não verá nenhum item que foi previamente armazenado no banco de dados SQL do tutorial do Início rápido.

3. Criar um novo item. O aplicativo deve se comportar como anteriormente, exceto que agora seus dados irão para seu armazenamento não relacional.


<!-- Anchors. -->
[Criar um armazenamento não relacional]: #create-store
[Modificar dados e controladores]: #modify-service
[Testar o aplicativo]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[Introdução aos Dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[O que é o serviço Tabela]: /pt-br/documentation/articles/storage-dotnet-how-to-use-tables/#what-is
[Página de suplemento MongoLab]: /pt-br/gallery/store/mongolab/mongolab
