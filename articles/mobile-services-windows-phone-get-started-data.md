<properties linkid="develop-mobile-tutorials-get-started-with-data-wp8" urlDisplayName="Get Started with Data" pageTitle="Get started with data (WP8) - Azure Mobile Services" metaKeywords="" description="Learn how to get started using data from your Azure Mobile Services Windows Phone 8 app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/19/2014" ms.author="glenga" />

# Introdução aos dados nos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Este t&oacute;pico mostra como usar os Servi&ccedil;os M&oacute;veis do Azure para utilizar dados em um aplicativo do Windows Phone 8. Neste tutorial, voc&ecirc; baixar&aacute; um aplicativo que armazena dados na mem&oacute;ria, criar&aacute; um novo servi&ccedil;o m&oacute;vel, integrar&aacute; o servi&ccedil;o m&oacute;vel ao aplicativo e far&aacute; logon no Portal de Gerenciamento do Azure para exibir as altera&ccedil;&otilde;es nos dados feitas durante a execu&ccedil;&atilde;o do aplicativo.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o v&iacute;deo</span></a> <span class="time">12:54:00</span></div>

</div>

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>O objetivo deste tutorial &eacute; ajudar voc&ecirc; a compreender melhor como os Servi&ccedil;os M&oacute;veis permitem usar o Azure para armazenar e recuperar dados em um aplicativo do Windows Phone 8. Desse modo, este t&oacute;pico explica muitas das etapas que s&atilde;o conclu&iacute;das para voc&ecirc; no Guia de in&iacute;cio r&aacute;pido dos Servi&ccedil;os M&oacute;veis. Se esta for sua primeira experi&ecirc;ncia com os Servi&ccedil;os M&oacute;veis, primeiro conclua o tutorial <a href="/pt-br/develop/mobile/tutorials/get-started-wp8">Introdu&ccedil;&atilde;o aos Servi&ccedil;os M&oacute;veis</a>.</p>
</div>

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Baixar o projeto de aplicativo do Windows Phone 8][Baixar o projeto de aplicativo do Windows Phone 8]
2.  [Criar o serviço móvel][Criar o serviço móvel]
3.  [Adicionar uma tabela de dados para armazenamento][Adicionar uma tabela de dados para armazenamento]
4.  [Atualizar o aplicativo para usar Serviços Móveis][Atualizar o aplicativo para usar Serviços Móveis]
5.  [Testar o aplicativo com os Serviços Móveis][Testar o aplicativo com os Serviços Móveis]

Este tutorial requer o Visual Studio 2012 Express para Windows Phone 8 e o [SDK do Windows Phone 8][SDK do Windows Phone 8] em execução no Windows 8. Para concluir este tutorial para criar um aplicativo do Windows Phone 8.1, você deve usar a atualização 2 do Visual Studio 2013 ou uma versão posterior.

> [WACOM.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].

## <a name="download-app"></a>Baixar o projeto GetStartedWithData

Este tutorial é baseado no [aplicativo GetStartedWithData][aplicativo GetStartedWithData], que é um aplicativo do projeto do Windows Phone Silverlight 8. A interface do usuário desse aplicativo é idêntica a do aplicativo gerado pelo início rápido dos Serviços Móveis, a não ser pelos os itens adicionados que são armazenados localmente na memória.

1.  Baixe o projeto do aplicativo de exemplo GetStartedWithData do [Site de Exemplos de Código do Desenvolvedor][aplicativo GetStartedWithData].

    > [WACOM.NOTE]Para criar um aplicativo do Windows Phone Silverlight 8.1, simplesmente altere o SO de destino no projeto do aplicativo do Windows Phone Silverlight 8 baixado para o Windows Phone 8.1. Para criar um aplicativo do Windows Phone Store, baixe a [versão do aplicativo do Windows Phone Store][versão do aplicativo do Windows Phone Store] do projeto do aplicativo de exemplo GetStartedWithData.

2.  No Visual Studio, abra o projeto baixado e examine o arquivo MainPage.xaml.cs.

    Observe que os objetos **TodoItem** adicionados estão armazenados em um **ObservableCollection\<TodoItem\>** na memória.

3.  Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

4.  No aplicativo, digite algum texto na caixa de texto e clique no botão **Salvar**.

    ![][0]

    Observe que o texto salvo é exibido na lista abaixo.

## <a name="create-service"></a><span class="short-header">Criar um novo serviço móvel</span>Crie um novo serviço móvel no Portal de Gerenciamento

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">Adicionar uma nova tabela</span>Adicione uma nova tabela para o serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

## <a name="update-app"></a><span class="short-header">Atualizar o aplicativo</span>Atualizar o aplicativo para usar o serviço móvel para acesso a dados

Agora que seu dispositivo móvel está pronto, você pode atualizar o aplicativo para armazenar itens nos Serviços Móveis em vez de na coleção local.

1.  No **Gerenciador de Soluções** do Visual Studio, clique com o botão direito do mouse no nome do projeto e selecione **Gerenciar Pacotes NuGet**.

2.  No painel esquerdo, selecione a categoria **Online**, pesquise por `WindowsAzure.MobileServices`, clique em **Instalar** no pacote de **Serviços Móveis do Azure** e aceite o contrato de licença.

    ![][1]

    Isso adiciona a biblioteca de cliente dos Serviços Móveis ao projeto.

3.  No Portal de Gerenciamento, clique em **Serviços Móveis** e, em seguida, clique no serviço móvel que você acabou de criar.

4.  Clique na guia **Painel** e anote a **URL do Site**, clique em **Gerenciar Chaves** e anote a **Chave do aplicativo**.

    ![][2]

    Você precisará desses valores ao acessar o serviço móvel com o código de seu aplicativo.

5.  No Visual Studio, abra o arquivo App.xaml.cs e adicione ou remova o comentário da seguinte instrução `using`:

        using Microsoft.WindowsAzure.MobileServices;

6.  Nesse mesmo arquivo, remova os comentários do código que define a variável **MobileService** e forneça a URL e a chave do aplicativo do serviço móvel no construtor **MobileServiceClient**, nessa ordem.

        //public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

    Isso criará uma nova instância de **MobileServiceClient** que é usada para acessar seu serviço móvel.

7.  No arquivo MainPage.xaml.cs, adicione ou remova o comentário das seguintes instruções `using`:

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json;

8.  Nesse mesmo arquivo, substitua a definição da classe **TodoItem** pelo seguinte código:

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

9.  Comente a linha que define a coleção existente **items** e remova os comentários das linhas a seguir:

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.MobileService.GetTable<TodoItem>();

    Esse código cria uma coleção de ligação com reconhecimento dos serviços móveis (**items**) e uma classe de proxy para a tabela de Banco de Dados SQL **TodoItem** (**todoTable**).

10. No método **InsertTodoItem**, remova a linha de código que define a propriedade **TodoItem**.**Id**, adicione o modificador **async** ao método e remova o comentário da seguinte linha de código:

        await todoTable.InsertAsync(todoItem);

    Esse código insere um novo item na tabela.

11. No método **RefreshTodoItems**, adicione o modificador **async** ao método e, em seguida, remova o comentário da linha de código a seguir:

        items = await todoTable.ToCollectionAsync();

    Isso define a associação à coleção de itens em todoTable, que contém todos os objetos TodoItem retornados do serviço móvel.

12. No método **UpdateCheckedTodoItem**, adicione o modificador **async** ao método e remova o comentário da linha de código a seguir:

         await todoTable.UpdateAsync(item);

    Isso envia uma atualização de item para o serviço móvel.

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, é hora de testar o aplicativo com os Serviços Móveis.

## <a name="test-app"></a><span class="short-header">Testar o aplicativo</span>Teste o aplicativo com o novo serviço móvel

1.  No Visual Studio, pressione F5 para executar o aplicativo.

2.  Como anteriormente, digite texto na caixa de texto e clique no botão **Salvar**.

    Isso envia um novo item como uma inserção no serviço móvel.

3.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

4.  Clique na guia **Dados** e, em seguida, clique em **Procurar**.

    ![][3]

    Observe que a tabela **TodoItem** agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

Isso conclui o tutorial **Introdução a dados** para Windows Phone 8.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a habilitação de um aplicativo do Windows Phone 8 para trabalhar com dados nos Serviços Móveis. Em seguida, considere concluir o seguinte tutorial que é baseado no aplicativo GetStartedWithData que você criou neste tutorial:

-   [Validar e modificar dados com scripts][Validar e modificar dados com scripts]
    
	Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

-   [Refinar consultas com paginação][Refinar consultas com paginação]
    
	Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, você também pode tentar um dos seguintes tutoriais do Windows Phone 8:

-   [Introdução à autenticação][Introdução à autenticação]
    
	Saiba como autenticar usuários de seu aplicativo.

-   [Introdução às notificações por push][Introdução às notificações por push]
    
	Saiba como enviar uma notificação por push bastante básica a seu aplicativo com os Serviços Móveis.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-data-legacy]: ../includes/mobile-services-selector-get-started-data-legacy.md
  [Baixar o projeto de aplicativo do Windows Phone 8]: #download-app
  [Criar o serviço móvel]: #create-service
  [Adicionar uma tabela de dados para armazenamento]: #add-table
  [Atualizar o aplicativo para usar Serviços Móveis]: #update-app
  [Testar o aplicativo com os Serviços Móveis]: #test-app
  [SDK do Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkID=268374
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-wp8%2F
  [aplicativo GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=271146
  [versão do aplicativo do Windows Phone Store]: http://go.microsoft.com/fwlink/p/?LinkId=397372
  [0]: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [1]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
  [2]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  [3]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png
  [Validar e modificar dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
  [Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-wp8
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-wp8
  [Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-wp8
