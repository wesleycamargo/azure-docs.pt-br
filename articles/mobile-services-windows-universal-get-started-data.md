<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services (universal Windows apps)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Introdução aos dados nos Serviços Móveis (aplicativo do Windows Universal)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-ios-get-started-data/" title="iOS">iOS</a><a href="/pt-br/documentation/articles/mobile-services-android-get-started-data/" title="Android">Android</a><a href="/pt-br/documentation/articles/mobile-services-html-get-started-data/" title="HTML">HTML</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/" title="Xamarin.Android">Xamarin.Android</a></div>

<!---<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title=".NET backend">.NET backend</a> | <a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/"  title="JavaScript backend" class="current">JavaScript backend</a></div>-->

Este tópico mostra como usar os Serviços Móveis do Azure para utilizar os dados em um aplicativo do Windows 8.1 Universal. Neste tutorial, você baixará um projeto do Visual Studio para um aplicativo do Windows Universal que armazena dados na memória, cria um novo serviço móvel, integra o serviço móvel ao aplicativo e faz login no Portal de Gerenciamento do Azure para exibir as alterações de dados feitas durante a execução do aplicativo.

> [WACOM.NOTE]Este tutorial requer o Visual Studio 2013 Atualização 2, que .

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Baixar o projeto de aplicativo da Windows Store][Baixar o projeto de aplicativo da Windows Store]
2.  [Criar o serviço móvel no Visual Studio][Criar o serviço móvel no Visual Studio]
3.  [Adicionar uma tabela de dados para armazenamento e atualização do aplicativo][Adicionar uma tabela de dados para armazenamento e atualização do aplicativo]
4.  [Testar o aplicativo com os Serviços Móveis][Testar o aplicativo com os Serviços Móveis]

> [WACOM.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].

## <a name="download-app"></a>Baixar o projeto GetStartedWithData

Este tutorial é baseado no [aplicativo GetStartedWithMobileServices][aplicativo GetStartedWithMobileServices], que é um projeto de aplicativo da Windows Store no Visual Studio 2013. A interface do usuário deste aplicativo é idêntica ao aplicativo gerado pelo quickstart dos Serviços Móveis, exceto pelo fato de que os itens adicionados são armazenados localmente na memória.

1.  Baixe a versão C# do aplicativo de exemplo GetStartedWithMobileServices do [site de exemplos de código do desenvolvedor][aplicativo GetStartedWithMobileServices].

    ![][0]

2.  No Visual Studio 2013, abra o projeto baixado e examine o arquivo MainPage.xaml.cs.

    Observe que os objetos **TodoItem** adicionados são armazenados na memória **ObservableCollection\<TodoItem\>**.

3.  Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

4.  No aplicativo, digite um texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

    ![][1]

    Observe que o texto salvo é exibido na segunda coluna em **Consultar e atualizar dados**.

## <a name="create-service"></a><span class="short-header">Criar o serviço móvel</span>Criar um novo serviço móvel a partir do Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

1.  No Gerenciador de Soluções, abra o arquivo de código App.xaml.cs e observe o novo campo estático que foi adicionado à classe \*\*App\*\*, que se parece com o exemplo a seguir:

        public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
                    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
                

    Esse código fornece acesso ao novo serviço móvel em seu aplicativo usando uma instância da [classe MobileServiceClient][classe MobileServiceClient]. O cliente é criado ao fornecer a URI e a chave de aplicativo do novo serviço móvel. Este campo estático está disponível para todas as páginas em seu aplicativo.

## <a name="add-table"></a><span class="short-header">Adicionar uma nova tabela</span>Adicionar uma nova tabela ao serviço móvel e atualizar o aplicativo

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

1.  No arquivo MainPage.xaml.cs, adicione ou remova o comentário dos seguintes usando instruções:

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json;

2.  Nesse mesmo arquivo, substitua a definição da classe TodoItem pelo seguinte código:

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")] 
            public bool Complete { get; set; }
        }

    O **JsonPropertyAttribute** é usado para definir o mapeamento entre os nomes das propriedades no tipo de cliente para nomes de coluna na tabela de dados subjacente.

3.  Comente a linha que define a coleção de itens existentes e, em seguida, remova os comentários ou adicione as seguintes linhas e substitua o campo *\<yourClient\>* pelo campo `MobileServiceClient` adicionado ao arquivo App.xaml.cs quando você conecta seu projeto ao serviço móvel:

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.<yourClient>.GetTable<TodoItem>();

    Esse código cria uma coleção de ligação com reconhecimento de serviços móveis (itens) e uma classe de proxy para a tabela de banco de dados (todoTable).

4.  No método **InsertTodoItem**, remova a linha de código que define a propriedade **TodoItem.Id**, adicione o modificador **async** ao método e remova o comentário da seguinte linha de código:

        await todoTable.InsertAsync(todoItem);

    Esse código insere um novo item na tabela.

    <div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong><p>Novas tabelas s&atilde;o criadas com as colunas Id, __createdAt, __updatedAt e __version. Quando o esquema din&acirc;mico est&aacute; habilitado, os Servi&ccedil;os M&oacute;veis geram automaticamente novas colunas com base em um objeto JSON na solicita&ccedil;&atilde;o de inser&ccedil;&atilde;o ou atualiza&ccedil;&atilde;o. Para obter mais informa&ccedil;&otilde;es, consulte <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/jj193175.aspx">Esquema din&acirc;mico</a>.</p></div>

5.  No método **RefreshTodoItems**, adicione o modificador **async** ao método e, em seguida, remova o comentário da linha de código a seguir:

        items = await todoTable.ToCollectionAsync();

    Isso define a associação à coleção de itens em `todoTable`, que contém todos os objetos **TodoItem** retornados do serviço móvel.

6.  No método **UpdateCheckedTodoItem**, adicione o modificador **async** ao método e remova o comentário da linha de código a seguir:

        await todoTable.UpdateAsync(item);

    Isso envia uma atualização de item para o serviço móvel.

Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, é hora de testar o aplicativo com os Serviços Móveis.

## <a name="test-app"></a><span class="short-header">Testar o aplicativo</span>Teste o aplicativo com o novo serviço móvel

1.  No Visual Studio, pressione F5 para executar o aplicativo.

2.  Assim como feito anteriormente, digite o texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

    Isso envia um novo item como uma inserção no serviço móvel.

3.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

4.  Clique na guia **Dados** e, em seguida, clique em **Procurar**.

    ![][2]

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

-   [Validar e modificar dados com scripts][Validar e modificar dados com scripts]
    Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

-   [Refinar consultas com paginação][Refinar consultas com paginação]
    Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, tente um desses outros tutoriais:

-   [Introdução à autenticação][Introdução à autenticação]
    Saiba como autenticar usuários de seu aplicativo.

-   [Introdução às notificações por push][Introdução às notificações por push]
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

-   [Referência conceitual do tutorial do .NET de Serviços Móveis][Referência conceitual do tutorial do .NET de Serviços Móveis]
    Saiba mais sobre como usar os Serviços Móveis com o .NET.





  [Baixar o projeto de aplicativo da Windows Store]: #download-app
  [Criar o serviço móvel no Visual Studio]: #create-service
  [Adicionar uma tabela de dados para armazenamento e atualização do aplicativo]: #add-table
  [Testar o aplicativo com os Serviços Móveis]: #test-app
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28
  [aplicativo GetStartedWithMobileServices]: http://go.microsoft.com/fwlink/p/?LinkId=328660
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-data-sample-download-dotnet-vs12.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-quickstart-startup.png
  [classe MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  [2]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
  [Validar e modificar dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet
  [Introdução às notificações por push]: ../mobile-services-windows-store-dotnet-get-started-push/
  [Referência conceitual do tutorial do .NET de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library
