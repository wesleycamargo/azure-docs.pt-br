<properties 
	pageTitle="Criar um serviço usando um repositório de dados não relacional | Microsoft Azure" 
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
	ms.date="08/08/2015" 
	ms.author="mahender"/>

# Criar um Serviço Móvel de back-end .NET que usa o MongoDB em vez de um Banco de Dados SQL para armazenamento

Este tópico mostra como usar um repositório de dados não relacional para seu serviço móvel de back-end .NET. Neste tutorial, você modificará o projeto de início rápido de Serviços Móveis para usar o MongoDB no lugar do repositório de dados padrão do Banco de Dados SQL do Azure.

Este tutorial exige a conclusão do tutorial [Introdução ao Serviços Móveis] ou [Adicionar Serviços Móveis a um aplicativo existente]. Você também precisará adicionar o serviço MongoLab à sua assinatura.

## <a name="create-store"></a>Criar o repositório não relacional MongoLab

1. No [Portal de Gerenciamento do Azure], clique em **Novo** e em **Marketplace**.

2. Clique no complemento **MongoLab** e conclua o assistente para se inscrever em uma conta do MongoLab.

	Para saber mais sobre o MongoLab, consulte a [Página do complemento MongoLab].

2. Quando a conta estiver definida, clique em **Informações de Conexão** e copie a cadeia de conexão.

3. No seu serviço móvel, clique na guia **Configurar**, role para baixo até **Cadeias de conexão** e insira uma nova cadeia de conexão com um **Nome** de `MongoConnectionString` e um **Valor** que é sua conexão com o MongoDB e clique em **Salvar**.

	![Adicionar a cadeia de conexão do MongoDB](./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png)

	A cadeia de conexão da conta de armazenamento é armazenada criptografada em configurações do aplicativo. Você pode acessar essa cadeia em qualquer controlador de tabela no tempo de execução.

8. No Gerenciador de Soluções do Visual Studio, abra o arquivo Web.config do projeto de serviço móvel e adicione a seguinte cadeia de conexão nova:

		<add name="MongoConnectionString" connectionString="<MONGODB_CONNECTION_STRING>" />

9. Substitua o espaço reservado `<MONGODB_CONNECTION_STRING>` pela cadeia de conexão do MongoDB.

	O serviço móvel usa essa cadeia de conexão quando é executado no computador local, o que permite testar o código antes de publicá-lo. Quando em execução no Azure, o serviço móvel usa o valor de cadeia de conexão definido no portal e ignora a cadeia de conexão no projeto.

## <a name="modify-service"></a>Modificar tipos de dados e controladores de tabela

1. Instale o pacote NuGet **WindowsAzure.MobileServices.Backend.Mongo**.

2. Modifique **TodoItem** para que seja derivado de **DocumentData** em vez de **EntityData**.

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3. Em **TodoItemController**, substitua o método **Initialize** pelo seguinte:

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, 
				collectionName, Request, Services);
        }

4. No código do método **Initialize** acima, substitua `<YOUR-DATABASE-NAME>` pelo nome escolhido por você ao fornecer o complemento MongoLab.

Agora você está pronto para testar o aplicativo.

## <a name="test-application"></a>Testar o aplicativo

1. (Opcional) Publique novamente seu projeto de back-end .NET do serviço móvel.

	Você também pode testar seu serviço móvel localmente antes de publicar o projeto de back-end .NET no Azure. Se você testar localmente ou no Azure, o serviço móvel usará o MongoDB para armazenamento.

4. Usando o botão **Experimente agora** na página inicial ou usando um aplicativo cliente conectado ao seu aplicativo móvel, consulte itens no banco de dados.
 
	Observe que você não verá nenhum item que foi previamente armazenado no banco de dados SQL do tutorial do Início rápido.

	>[AZURE.NOTE]Quando você usar o botão **Experimente agora** para abrir as páginas de API de Ajuda, lembre-se de fornecer a chave de aplicativo como a senha (com um nome de usuário em branco).

3. Criar um novo item.

	O aplicativo e o serviço móvel devem se comportar como antes, exceto pelo fato de que agora os dados estão sendo armazenados no repositório não relacional, e não no Banco de Dados SQL.

##Próximas etapas

Agora que você viu como é fácil usar o armazenamento de tabela com o back-end .NET, que tal explorar algumas das outra opções de armazenamento de back-end?

+ [Criar um Serviço Móvel de back-end .NET que use armazenamento de tabela em vez de um Banco de Dados SQL](mobile-services-dotnet-backend-store-data-table-storage.md)</br>Assim como o tutorial que você acabou de concluir, este tópico mostra como usar um repositório de dados não relacional para seu serviço móvel. Neste tutorial, você modificará o projeto de início rápido de Serviços Móveis para usar o Armazenamento do Azure no lugar de um Banco de Dados SQL como o repositório de dados.
 
+ [Conectar-se a um SQL Server local usando Conexões Híbridas](mobile-services-dotnet-backend-hybrid-connections-get-started.md)</br>As Conexões Híbridas permitem que o serviço móvel se conecte com segurança aos ativos locais. Dessa forma, você pode disponibilizar seus dados locais para os clientes móveis usando o Azure. Os ativos com suporte incluem qualquer recurso que seja executado em uma porta TCP estática, incluindo Microsoft SQL Server, MySQL, APIs da web HTTP e os serviços Web mais personalizados.

+ [Carregar imagens no Armazenamento do Azure usando Serviços Móveis](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md)</br>Mostra como estender o exemplo TodoList do projeto para permitir que você carregue imagens do seu aplicativo no armazenamento de Blob do Azure.


<!-- Anchors. -->
[Create a non-relational store]: #create-store
[Modify data and controllers]: #modify-service
[Test the application]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[Introdução ao Serviços Móveis]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Adicionar Serviços Móveis a um aplicativo existente]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[What is the Table Service]: ../storage-dotnet-how-to-use-tables.md#what-is
[Página do complemento MongoLab]: /gallery/store/mongolab/mongolab
 

<!---HONumber=August15_HO7-->