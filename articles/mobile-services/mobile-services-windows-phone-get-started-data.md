<properties 
	pageTitle="Adicionar Serviços Móveis a um aplicativo existente (WP8) | Microsoft Azure" 
	description="Saiba como começar a usar os dados do seu aplicativo do Windows Phone 8 nos serviços móveis do Azure." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/11/2015" 
	ms.author="glenga"/>


# Adicionar Serviços Móveis a um aplicativo existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##Visão geral

Este tópico mostra como usar os Serviços Móveis do Azure para utilizar dados em um aplicativo do Windows Phone 8. Neste tutorial, você baixará um aplicativo que armazena dados na memória, criará um novo serviço móvel, integrará o serviço móvel ao aplicativo e fará logon no Portal de Gerenciamento do Azure para exibir as alterações nos dados feitas durante a execução do aplicativo.

Você também pode ver Nick Harris demonstrar isso no vídeo a seguir:
>[AZURE.VIDEO mobile-get-started-with-data-windows-phone]

##Pré-requisitos 

+ O Visual Studio 2012 Express para Windows Phone 8 e o [SDK do Windows Phone 8] em execução no Windows 8. Para concluir este tutorial para criar um aplicativo Windows Phone 8.1, você deve usar o Visual Studio 2013 atualização 2 ou posterior. 

+ Uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Farticles%2Fdocumentation%2Fmobile-services-windows-phone-get-started-data%2F).

##<a name="download-app"></a>Baixar o projeto GetStartedWithData

Este tutorial é baseado no [aplicativo GetStartedWithData][Developer Code Samples site], que é um aplicativo do projeto do Windows Phone Silverlight 8.

1. Baixe o projeto do aplicativo de exemplo GetStartedWithData do [Site de Exemplos de Código do Desenvolvedor]. 

	>[AZURE.NOTE]Para criar um aplicativo do Windows Phone Silverlight 8.1, simplesmente altere o SO de destino no projeto do aplicativo para Windows Phone Silverlight 8 baixado para o Windows Phone 8.1. Para criar um aplicativo do Windows Phone Store, baixe a [versão do aplicativo do Windows Phone Store](http://go.microsoft.com/fwlink/p/?LinkId=397372) do projeto do aplicativo de exemplo GetStartedWithData.

2. No Visual Studio, abra o projeto baixado e examine o arquivo MainPage.xaml.cs.

   	Observe que os objetos **TodoItem** adicionados estão armazenados em um **ObservableCollection&lt;TodoItem&gt;** na memória.

3. Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

4. No aplicativo, digite algum texto na caixa de texto e clique no botão **Salvar**.

   	![][0]

   	Observe que o texto salvo é exibido na lista abaixo.

##<a name="create-service"></a>Criar um novo serviço móvel no Portal de Gerenciamento

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>Adicionar uma nova tabela ao serviço móvel

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

##<a name="update-app"></a>Atualizar o aplicativo para usar o serviço móvel para acesso a dados

Agora que seu dispositivo móvel está pronto, você pode atualizar o aplicativo para armazenar itens nos Serviços Móveis em vez de na coleção local.

1. No **Gerenciador de Soluções** do Visual Studio, clique com o botão direito do mouse no nome do projeto e selecione **Gerenciar Pacotes NuGet**.

2. No painel esquerdo, selecione a categoria **Online**, procure `WindowsAzure.MobileServices`, clique em **Instalar** no pacote **Serviços Móveis do Azure** e aceite o contrato de licença.

  	![][7]

  	Isso adiciona a biblioteca de cliente dos Serviços Móveis ao projeto.

3. No Portal de Gerenciamento, clique em **Serviços Móveis** e, em seguida, clique no serviço móvel que você acabou de criar.

4. Clique na guia **Painel** e anote a **URL do Site**, clique em **Gerenciar Chaves** e anote a **Chave do aplicativo**.

   	![][8]

  	Você precisará desses valores ao acessar o serviço móvel com o código de seu aplicativo.

5. No Visual Studio, abra o arquivo App.xaml.cs e adicione ou remova o comentário da seguinte instrução `using`:

       	using Microsoft.WindowsAzure.MobileServices;

6. Nesse mesmo arquivo, remova os comentários do código a seguir que define a variável **MobileService** e forneça a URL e a chave do aplicativo do serviço móvel no construtor **MobileServiceClient**, nessa ordem.

		//public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

  	Isso criará uma nova instância de **MobileServiceClient** que é usada para acessar seu serviço móvel.

6. No arquivo MainPage.cs, adicione ou remova o comentário das seguintes instruções `using`:

       	using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json;

7. Nesta pasta DataModel, substitua a definição da classe **TodoItem** pelo seguinte código:

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

7. Comente a linha que define a coleção existente **items** e remova os comentários das linhas a seguir:

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
			App.MobileService.GetTable<TodoItem>();

   	Esse código cria uma coleção de ligação com reconhecimento dos serviços móveis (**items**) e uma classe de proxy para a tabela de Banco de Dados SQL **TodoItem** (**todoTable**).

7. No método **InsertTodoItem**, remova a linha de código que define a propriedade **TodoItem**.**Id**, adicione o modificador **async** ao método e remova o comentário da seguinte linha de código:

        await todoTable.InsertAsync(todoItem);

  	Esse código insere um novo item na tabela.

8. No método **RefreshTodoItems**, adicione o modificador **async** ao método e, em seguida, remova o comentário da linha de código a seguir:

        items = await todoTable.ToCollectionAsync();

   	Isso define a associação à coleção de itens em todoTable, que contém todos os objetos TodoItem retornados do serviço móvel.

9. No método **UpdateCheckedTodoItem**, adicione o modificador **async** ao método e remova o comentário da linha de código a seguir:

         await todoTable.UpdateAsync(item);

   	Isso envia uma atualização de item para o serviço móvel.

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, é hora de testar o aplicativo com os Serviços Móveis.

##<a name="test-app"></a>Testar o aplicativo com seu novo serviço móvel

1. No Visual Studio, pressione F5 para executar o aplicativo.

2. Como anteriormente, digite texto na caixa de texto e clique no botão **Salvar**.

   	Isso envia um novo item como uma inserção no serviço móvel.

3. No [Portal de Gerenciamento], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

4. Clique na guia **Dados** e, em seguida, clique em **Procurar**.

   	![][9]
  
   	Observe que a tabela **TodoItem** agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

Isso conclui o tutorial.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a habilitação de um aplicativo do Windows Phone 8 para trabalhar com dados nos Serviços Móveis. Em seguida, considere a leitura de um destes outros tópicos:

* [Introdução à autenticação para seu aplicativo](mobile-services-windows-phone-get-started-users.md) <br/>Saiba como autenticar usuários de seu aplicativo.

* [Adicionar notificações por push ao seu aplicativo](mobile-services-javascript-backend-windows-phone-get-started-push.md) <br/>Saiba como enviar uma notificação por push bem básica ao seu aplicativo com os Serviços Móveis.

* [Referência conceitual de tutorial de C# dos Serviços Móveis](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>Saiba mais sobre como usar os Serviços Móveis com o .NET.
 
<!-- Anchors. -->
[Download the Windows Phone 8 app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png
[7]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
[8]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png

<!-- URLs. -->

[Azure Management Portal]: https://manage.windowsazure.com/
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[SDK do Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=271146
[Site de Exemplos de Código do Desenvolvedor]: http://go.microsoft.com/fwlink/p/?LinkId=271146
 

<!---HONumber=Nov15_HO4-->