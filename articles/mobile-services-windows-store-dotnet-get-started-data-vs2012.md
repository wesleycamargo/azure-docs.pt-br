<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet" urlDisplayName="Get Started with Data" pageTitle="Get started with data - Azure Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Introdução aos dados nos Serviços Móveis usando o Visual Studio 2012

<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012" title="C# da Windows Store" class="current">C# da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-js-vs2012" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este t&oacute;pico mostra como usar os Servi&ccedil;os M&oacute;veis do Azure para utilizar os dados em um aplicativo da Windows Store. Neste tutorial, voc&ecirc; baixar&aacute; um projeto Visual Studio 2012 para um aplicativo que armazena dados na mem&oacute;ria, cria um novo servi&ccedil;o m&oacute;vel, integra o servi&ccedil;o m&oacute;vel ao aplicativo e faz login no Portal de Gerenciamento do Azure para exibir as altera&ccedil;&otilde;es de dados feitas durante a execu&ccedil;&atilde;o do aplicativo. Voc&ecirc; poder&aacute; ver uma vers&atilde;o em v&iacute;deo deste tutorial clicando no clipe &agrave; direita.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-data-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o v&iacute;deo</span></a> <span class="time">15:33:00</span></div>

</div>

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Este tutorial adiciona a funcionalidade dos Servi&ccedil;os M&oacute;veis ao um aplicativo da Windows Store criado no Visual Studio 2012. O Visual Studio 2013 inclui novos recursos que tornam mais f&aacute;cil conectar seu aplicativo da Windows Store ao Servi&ccedil;os M&oacute;veis. Para obter mais informa&ccedil;&otilde;es, consulte <a href="/pt-br/develop/mobile/tutorials/get-started-with-data-dotnet/">Introdu&ccedil;&atilde;o aos dados nos Servi&ccedil;os M&oacute;veis</a>.</p>
</div>

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Baixar o projeto de aplicativo da Windows Store][Baixar o projeto de aplicativo da Windows Store]
2.  [Criar o serviço móvel][Criar o serviço móvel]
3.  [Adicionar uma tabela de dados para armazenamento][Adicionar uma tabela de dados para armazenamento]
4.  [Atualizar o aplicativo para usar Serviços Móveis][Atualizar o aplicativo para usar Serviços Móveis]
5.  [Testar o aplicativo com os Serviços Móveis][Testar o aplicativo com os Serviços Móveis]

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure. Se voc&ecirc; n&atilde;o tiver uma conta, poder&aacute; criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

## <a name="download-app"></a><span class="short-header">Baixar o projeto</span>Baixe o projeto GetStartedWithData

Este tutorial é baseado no [aplicativo GetStartedWithData][aplicativo GetStartedWithData], que é um aplicativo da Windows Store. A interface do usuário desse aplicativo é idêntica a do aplicativo gerado pelo início rápido dos Serviços Móveis, a não ser pelos os itens adicionados que são armazenados localmente na memória.

1.  Baixe a versão \#C do aplicativo de exemplo GetStartedWithData do [site de exemplos de código do desenvolvedor][aplicativo GetStartedWithData].

    ![][0]

2.  No Visual Studio 2012 Express para Windows 8, abra o projeto baixado e analise o arquivo MainPage.xaml.cs.

    Observe que os objetos **TodoItem** adicionados estão armazenados em um **ObservableCollection\<TodoItem\>** na memória.

3.  Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

4.  No aplicativo, digite um texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

    ![][1]

    Observe que o texto salvo é exibido na segunda coluna em **Consultar e atualizar dados**.

## <a name="create-service"></a><span class="short-header">Criar um novo serviço móvel</span>Crie um novo serviço móvel no Portal de Gerenciamento

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">Adicionar uma nova tabela</span>Adicione uma nova tabela para o serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

## <a name="update-app"></a><span class="short-header">Atualizar o aplicativo</span>Atualizar o aplicativo para usar o serviço móvel para acesso a dados

Agora que seu dispositivo móvel está pronto, você pode atualizar o aplicativo para armazenar itens nos Serviços Móveis em vez de na coleção local.

1.  No **Gerenciador de Soluções** do Visual Studio, clique com o botão direito do mouse no nome do projeto e selecione **Gerenciar Pacotes NuGet**.

2.  No painel esquerdo, selecione a categoria **Online**, selecione **Incluir Pré-lançamento**, pesquise por `WindowsAzure.MobileServices`, clique em **Instalar** no pacote de **Serviços Móveis do Azure** e aceite o contrato de licença.

    ![][2]

    Isso adiciona a biblioteca de cliente dos Serviços Móveis ao projeto.

3.  No Portal de Gerenciamento, clique em **Serviços Móveis** e, em seguida, clique no serviço móvel que você acabou de criar.

4.  Clique na guia **Painel** e anote a **URL do Site**, clique em **Gerenciar Chaves** e anote a **Chave do aplicativo**.

    ![][3]

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

2.  Assim como feito anteriormente, digite o texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

    Isso envia um novo item como uma inserção no serviço móvel.

3.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

4.  Clique na guia **Dados** e, em seguida, clique em **Procurar**.

    ![][4]

    Observe que a tabela **TodoItem** agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

5.  No aplicativo, verifique um dos itens na lista, depois volte para a guia Procurar no portal e clique em **Atualizar**.

    Observe que o valor completo foi alterado de **false** para **true**.

6.  No arquivo de projeto MainPage.xaml.cs, substitua o método existente **RefreshTodoItems** pelo seguinte código que filtra os itens concluídos:

        private async void RefreshTodoItems()
        {                       
            // This query filters out completed TodoItems. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

            ListItems.ItemsSource = items;            
        }

7.  No aplicativo, verifique um outro item na lista e, em seguida, clique no botão **Atualizar**.

    Observe que o item marcado agora desaparecerá da lista. Cada atualização resulta em uma viagem para o serviço móvel, que agora retorna dados filtrados.

Isso conclui o tutorial **Introdução aos dados**.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação de um aplicativo da Windows Store para trabalhar com dados nos Serviços Móveis. Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

-   [Validar e modificar dados com scripts][Validar e modificar dados com scripts]<br/>
    Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

-   [Refinar consultas com paginação][Refinar consultas com paginação]<br/>
    Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, tente um desses outros tutoriais:

-   [Introdução à autenticação][Introdução à autenticação]<br/>
    Saiba como autenticar usuários de seu aplicativo.

-   [Introdução às notificações por push][Introdução às notificações por push]<br/>
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

-   [Referência conceitual do tutorial do .NET de Serviços Móveis][Referência conceitual do tutorial do .NET de Serviços Móveis]<br/>
    Saiba mais sobre como usar os Serviços Móveis com o .NET.



  [Baixar o projeto de aplicativo da Windows Store]: #download-app
  [Criar o serviço móvel]: #create-service
  [Adicionar uma tabela de dados para armazenamento]: #add-table
  [Atualizar o aplicativo para usar Serviços Móveis]: #update-app
  [Testar o aplicativo com os Serviços Móveis]: #test-app
  [aplicativo GetStartedWithData]: http://go.microsoft.com/fwlink/?LinkId=262308
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-data-sample-download-dotnet.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-quickstart-startup.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [2]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-add-nuget-package-dotnet.png
  [3]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-dashboard-tab.png
  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  [4]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-todoitem-data-browse.png
  [Validar e modificar dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet
  [Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
  [Referência conceitual do tutorial do .NET de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library
