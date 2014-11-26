<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Introdução aos dados nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="iOS" class="current">iOS</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Android" class="current">Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="Back-end do .NET" class="current">Back-end do .NET</a> | <a href="/pt-br/develop/mobile/tutorials/get-started-with-data-ios/"  title="Back-end do JavaScript">Back-end do JavaScript</a></div>

Este tópico mostra como usar os Serviços Móveis do Azure para aproveitar dados em um aplicativo iOS. Neste tutorial, você baixará um aplicativo que armazena dados na memória, criará um novo serviço móvel, integrará o serviço móvel ao aplicativo e visualizará as alterações aos dados feitas ao executar o aplicativo.

O serviço móvel que você criará neste tutorial oferecerá suporte ao tempo de execução do .NET no Serviço Móvel. Isso permitirá que você use linguagens .NET e o Visual Studio para a lógica dos negócios do lado do servidor no serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte [Versão de back-end do JavaScript][Versão de back-end do JavaScript] neste tópico.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Este tutorial requer o Visual Studio 2013.</p>
</div>

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>O objetivo deste tutorial &eacute; ajudar voc&ecirc; a compreender melhor como os Servi&ccedil;os M&oacute;veis permitem usar o Azure para armazenar e recuperar dados de um aplicativo iOS. Desse modo, este t&oacute;pico explica muitas das etapas que s&atilde;o conclu&iacute;das para voc&ecirc; no Guia de in&iacute;cio r&aacute;pido dos Servi&ccedil;os M&oacute;veis. Se esta for sua primeira experi&ecirc;ncia com os Servi&ccedil;os M&oacute;veis, primeiro conclua o tutorial <a href="/pt-br/develop/mobile/tutorials/get-started-ios">Introdu&ccedil;&atilde;o aos Servi&ccedil;os M&oacute;veis</a>.</p>
</div>

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Baixar o projeto do aplicativo iOS][Baixar o projeto do aplicativo iOS]
2.  [Criar o serviço móvel][Criar o serviço móvel]
3.  [Baixar o serviço localmente][Baixar o serviço localmente]
4.  [Testar o serviço móvel][Testar o serviço móvel]
5.  [Publicar o serviço móvel no Azure][Publicar o serviço móvel no Azure]
6.  [Atualizar o aplicativo para usar Serviços Móveis][Atualizar o aplicativo para usar Serviços Móveis]
7.  [Testar o aplicativo com os Serviços Móveis][Testar o aplicativo com os Serviços Móveis]

Este tutorial exige o seguinte:

-   [SDK do iOS dos Serviços Móveis][SDK do iOS dos Serviços Móveis] e [XCode 4.5][XCode 4.5] e iOS 5.0 ou versões posteriores.
-   Visual Studio 2013 (você pode obter o [Visual Studio Express for Web][Visual Studio Express for Web] gratuitamente).
-   Uma conta do Microsoft Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].

## <a name="download-app"></a>Baixar o projeto GetStartedWithData

Este tutorial é baseado no [aplicativo GetStartedWithData][aplicativo GetStartedWithData], que é um aplicativo iOS. A interface do usuário desse aplicativo é idêntica a do aplicativo gerado pelo início rápido do iOS dos Serviços Móveis, com a exceção de que os itens são armazenados localmente na memória.

1.  Baixar o [aplicativo de amostra][aplicativo GetStartedWithData] GetStartedWithData.

2.  Em Xcode, abra o projeto baixado e examine o arquivo TodoService.m.

    Observe que há oito comentários **// TODO** que especificam as etapas que você deve seguir para fazer com que esse aplicativo funcione com o serviço móvel.

3.  Pressione o botão **Executar** (ou a tecla Command+R) para recompilar o projeto e iniciar o aplicativo.

4.  No aplicativo, digite algum texto na caixa de texto e clique no botão **+**.

    ![][0]

    Observe que o texto salvo é exibido na lista abaixo.

## <a name="create-service"></a>Criar um novo serviço móvel no Portal de Gerenciamento

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## <a name="download-the-service-locally"></a>Baixar o serviço em seu computador local

[WACOM.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

## <a name="test-the-service"></a> Testar o serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <a name="publish-mobile-service"></a>Publicar o serviço móvel no Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="update-app"></a>Atualizar o aplicativo para usar o serviço móvel para acessar dados

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

## <a name="test-app"></a>Testar o aplicativo com relação ao seu novo serviço móvel

1.  No Xcode, selecione um emulador para o qual implantar (iPhone ou iPad), pressione o botão **Executar** (ou a tecla Command+R) para recompilar o projeto e iniciar o aplicativo.

    Isso executa seu cliente de Serviços Móveis do Azure, criado com o SDK do iOS, que consulta itens em seu serviço móvel.

2.  Como antes, digite texto na caixa de texto e clique no botão **+**.

    Isso envia um novo item como uma inserção no serviço móvel. Cada novo todoItem é armazenado e atualizado no banco de dados SQL configurado anteriormente para o serviço móvel no Portal de Gerenciamento do Azure.

3.  Pare e reinicie o aplicativo para ver se as alterações foram persistidas para o banco de dados no Azure.

    Você também pode examinar o banco de dados usando o Portal de Gerenciamento do Azure ou o Pesquisador de Objetos do SQL Server. As próximas duas etapas usam o [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure] para exibir as alterações no banco de dados.

4.  No Portal de Gerenciamento do Azure, clique em gerenciar para o banco de dados associado a seu serviço móvel.

    ![][1]

5.  No Portal de Gerenciamento, execute uma consulta para exibir as alterações feitas pelo aplicativo. Sua consulta será semelhante à consulta a seguir, mas usará seu nome de banco de dados em vez de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![][2]

Isso conclui o tutorial **Introdução aos dados**.

## <a name="next-steps"></a>Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo iOS para trabalhar com dados nos Serviços Móveis.

Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

-   [Validar e modificar dados com scripts][Validar e modificar dados com scripts]
    Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

-   [Refinar consultas com paginação][Refinar consultas com paginação]
    Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

Depois de ter concluído a série de dados, tente estes outros tutoriais do iOS:

-   [Introdução à autenticação][Introdução à autenticação]
    Saiba como autenticar usuários de seu aplicativo.

-   [Introdução às notificações por push][Introdução às notificações por push]
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo com os Serviços Móveis.





  [Versão de back-end do JavaScript]: /pt-br/develop/mobile/tutorials/get-started-with-data-ios
  [Baixar o projeto do aplicativo iOS]: #download-app
  [Criar o serviço móvel]: #create-service
  [Baixar o serviço localmente]: #download-the-service-locally
  [Testar o serviço móvel]: #test-the-service
  [Publicar o serviço móvel no Azure]: #publish-mobile-service
  [Atualizar o aplicativo para usar Serviços Móveis]: #update-app
  [Testar o aplicativo com os Serviços Móveis]: #test-app
  [SDK do iOS dos Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Visual Studio Express for Web]: http://go.microsoft.com/p/?linkid=9832232
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F
  [aplicativo GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622
  [0]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
  [2]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png
  [Validar e modificar dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-ios
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-ios
  [Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-ios
