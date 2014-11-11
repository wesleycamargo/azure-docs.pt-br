<properties linkid="mobile-services-windows-store-dotnet-get-started-data" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/19/2014" ms.author="glenga" />

# Introdução aos dados nos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-center-tutorial-subselector">
    <a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="Back-end do .NET">Back-end do .NET</a> | 
    <a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/" title="Back-end do JavaScript" class="current">Back-end do JavaScript</a>
</div>

Este tópico mostra como usar os Serviços Móveis do Azure para utilizar os dados em um aplicativo da Windows Store. Neste tutorial, você baixará um projeto Visual Studio 2013 para um aplicativo que armazena dados na memória, cria um novo serviço móvel, integra o serviço móvel ao aplicativo e entre no Portal de Gerenciamento do Azure para exibir as alterações de dados feitas durante a execução do aplicativo.

> [WACOM.NOTE]Este tópico mostra como usar o Visual Studio 2013 para adicionar os Serviços Móveis do Azure a um projeto da Windows Store. Você pode usar o mesmo serviço móvel de back-end do JavaScript para um projeto de aplicativo universal do Windows. Para obter mais informações, consulte a [versão do aplicativo universal do Windows][versão do aplicativo universal do Windows] deste tutorial.

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Baixar o projeto de aplicativo da Windows Store][Baixar o projeto de aplicativo da Windows Store]
2.  [Criar o serviço móvel no Visual Studio][Criar o serviço móvel no Visual Studio]
3.  [Adicionar uma tabela de dados para armazenamento][Adicionar uma tabela de dados para armazenamento]
4.  [Atualizar o aplicativo para usar o serviço móvel][Atualizar o aplicativo para usar o serviço móvel]
5.  [Testar o aplicativo com os Serviços Móveis][Testar o aplicativo com os Serviços Móveis]

Para concluir este tutorial, você precisará do seguinte:

-   Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].
-   Visual Studio 2013, o que torna mais fácil conectar seu aplicativo da Windows Store aos Serviços Móveis.

## <a name="download-app"></a>Baixar o projeto GetStartedWithData

Este tutorial é baseado no [aplicativo GetStartedWithMobileServices][aplicativo GetStartedWithMobileServices], que é um projeto de aplicativo da Windows Store no Visual Studio 2013. A interface do usuário deste aplicativo é idêntica ao aplicativo gerado pelo quickstart dos Serviços Móveis, exceto pelo fato de que os itens adicionados são armazenados localmente na memória.

1.  Baixe a versão C# do aplicativo de exemplo GetStartedWithMobileServices do [site de exemplos de código do desenvolvedor][aplicativo GetStartedWithMobileServices].

2.  No Visual Studio 2013, abra o projeto baixado e examine o arquivo MainPage.xaml.cs.

    Observe que os objetos **TodoItem** adicionados estão armazenados em um **ObservableCollection\<TodoItem\>** na memória.

3.  Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

4.  No aplicativo, digite um texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

    ![][0]

    Observe que o texto salvo é exibido na segunda coluna em **Consultar e atualizar dados**.

</ol>
<h2 id="create-a-new-mobile-service-from-visual-studio"><a name="create-service"></a>Criar um novo servi&ccedil;o m&oacute;vel a partir do Visual Studio</h2>
<p>[WACOM.INCLUDE <a href="../includes/mobile-services-create-new-service-vs2013.md">mobile-services-create-new-service-vs2013</a>]</p>
<ol start="7"><li><p>No Gerenciador de Solu&ccedil;&otilde;es, abra o arquivo de c&oacute;digo App.xaml.cs e observe o novo campo est&aacute;tico que foi adicionado &agrave; classe **Aplicativo**, que parece com o exemplo a seguir:</p> 

        <pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
            todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
        </code></pre>

    <p>Esse c&oacute;digo fornece acesso ao novo servi&ccedil;o m&oacute;vel em seu aplicativo usando uma inst&acirc;ncia da <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">classe MobileServiceClient</a>. O cliente &eacute; criado ao fornecer a URI e a chave de aplicativo do novo servi&ccedil;o m&oacute;vel. Este campo est&aacute;tico est&aacute; dispon&iacute;vel para todas as p&aacute;ginas em seu aplicativo.</p>
</li>
</ol>

## <a name="add-table"></a>Adicionar uma nova tabela para armazenamento de dados

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

> [WACOM.NOTE]Novas tabelas são criadas com colunas Id, \_\_createdAt, \_\_updatedAt e \_\_version. Quando o esquema dinâmico está habilitado, os Serviços Móveis geram automaticamente novas colunas com base em um objeto JSON na solicitação de inserção ou atualização. Para obter mais informações, consulte [Esquema dinâmico][Esquema dinâmico].

# <a name="update-app"></a>Atualizar o aplicativo para usar o serviço móvel

[WACOM.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]

## <a name="test-app"></a>Testar o aplicativo com seu novo serviço móvel

1.  No Visual Studio, pressione F5 para executar o aplicativo.

2.  Assim como feito anteriormente, digite o texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

    Isso envia um novo item como uma inserção no serviço móvel.

3.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

4.  Clique na guia **Dados** e, em seguida, clique em **Procurar**.

    ![][1]

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
    <br/>Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

-   [Refinar consultas com paginação][Refinar consultas com paginação]
    <br/>Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, tente um desses outros tutoriais:

-   [Introdução à autenticação][Introdução à autenticação]
    <br/>Saiba como autenticar usuários de seu aplicativo.

-   [Introdução às notificações por push][Introdução às notificações por push]
    <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

-   [Referência conceitual do tutorial do .NET de Serviços Móveis][Referência conceitual do tutorial do .NET de Serviços Móveis]
    <br/>Saiba mais sobre como usar os Serviços Móveis com o .NET.



  [mobile-services-selector-get-started-data-legacy]: ../includes/mobile-services-selector-get-started-data-legacy.md
  [versão do aplicativo universal do Windows]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data
  [Baixar o projeto de aplicativo da Windows Store]: #download-app
  [Criar o serviço móvel no Visual Studio]: #create-service
  [Adicionar uma tabela de dados para armazenamento]: #add-table
  [Atualizar o aplicativo para usar o serviço móvel]: #update-app
  [Testar o aplicativo com os Serviços Móveis]: #test-app
  [Avaliação gratuita do Azure]: http://azure.microsoft.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-br%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-dotnet-get-started-data%2F
  [aplicativo GetStartedWithMobileServices]: http://go.microsoft.com/fwlink/p/?LinkId=328660
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-quickstart-startup.png
  [mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
  [Esquema dinâmico]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj193175.aspx
  [mobile-services-windows-dotnet-update-data-app]: ../includes/mobile-services-windows-dotnet-update-data-app.md
  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
  [Validar e modificar dados com scripts]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/
  [Refinar consultas com paginação]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data/
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
  [Referência conceitual do tutorial do .NET de Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
