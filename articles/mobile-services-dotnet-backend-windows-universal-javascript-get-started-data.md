<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-javascript-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# Introdução aos dados nos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-data][mobile-services-selector-get-started-data]]

Este tópico mostra como usar os Serviços Móveis do Azure como uma fonte de dados de back-end para um aplicativo da Windows Store. Neste tutorial, você baixará um projeto do Visual Studio 2013 para um aplicativo que armazena dados na memória, criará um novo serviço móvel, integrará o serviço móvel ao aplicativo e exibirá as alterações de dados feitas durante a execução do aplicativo.

O serviço móvel que você criará neste tutorial é um serviço móvel back-end .NET. O back-end .NET permite que você use linguagens .NET e o Visual Studio para a lógica dos negócios do lado do servidor no serviço móvel, e você pode executar e depurar seu serviço móvel no seu computador local. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte Versão de back-end do JavaScript neste tópico.

> [WACOM.NOTE]Esse tópico lhe mostra como usar as ferramentas no Visual Studio Professional 2013 com atualização 3 para se conectar a um novo serviço móvel para um novo aplicativo Windows universal. Se você não pode atualizar para o Visual Studio Professional 2013 Atualização 3 ou prefere adicionar manualmente seu projeto de serviço móvel a uma solução de aplicativo da Windows Store, veja [esta versão][esta versão] do tópico.

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Baixar o projeto de aplicativo da Windows Store][Baixar o projeto de aplicativo da Windows Store]
2.  [Criar um novo serviço móvel a partir do Visual Studio][Criar um novo serviço móvel a partir do Visual Studio]
3.  [Testar o projeto de serviço móvel localmente][Testar o projeto de serviço móvel localmente]
4.  [Atualizar o aplicativo para usar o serviço móvel][Atualizar o aplicativo para usar o serviço móvel]
5.  [Publicar o serviço móvel no Azure][Publicar o serviço móvel no Azure]
6.  [Testar o aplicativo no serviço hospedado no Azure][Testar o aplicativo no serviço hospedado no Azure]
7.  [Exibir os dados armazenados no Banco de Dados SQL][Exibir os dados armazenados no Banco de Dados SQL]

Para concluir este tutorial, você precisará do seguinte:

-   Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].
-   [Visual Studio Professional 2013][Visual Studio Professional 2013]. Uma versão de avaliação gratuita está disponível.

## <a name="download-app"></a>Baixar o projeto GetStartedWithData

[WACOM.INCLUDE [mobile-services-windows-universal-javascript-download-project][mobile-services-windows-universal-javascript-download-project]]

## <a name="create-service"></a>Criar um novo serviço móvel a partir do Visual Studio

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013][mobile-services-dotnet-backend-create-new-service-vs2013]]

1.  No Gerenciador de Soluções, navegue para a subpasta **services\\mobileService\\scripts**, abra o arquivo de script service.js e observe a nova variável global, que se parece com o exemplo a seguir:

        var todolistClient = new WindowsAzure.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");

    Esse código fornece acesso ao novo serviço móvel em seu aplicativo usando uma variável global. O cliente é criado ao fornecer a URI e a chave de aplicativo do novo serviço móvel. Como uma referência a esse script foi adicionada ao arquivo default.html, esta variável está disponível para todos os arquivos de script que também fazem referência desta página.

2.  Abra o arquivo do projeto default.html, localize a referência para o novo arquivo de script service.kd e certifique-se de que o caminho em referência é como o seguinte:

        <script src="/services/mobileServices/scripts/todolist.js">

    Existe atualmente um bug no Visual Studio que gera um nome incorreto de pasta no caminho.

3.  Clique com o botão direito do mouse no projeto do aplicativo Windows Phone, clique em **Adicionar**, clique em **Serviço Conectado...**, selecione o serviço móvel que você acabou de criar e, em seguida, clique em **OK**.

    O mesmo arquivo com o novo código é adicionado ao projeto do aplicativo Windows Phone Store. Certifique-se de corrigir também o caminho de referência adicionado ao arquivo default.html.

Neste momento, ambos os aplicativos do Windows Store e Windows Phone Store são conectados ao novo serviço móvel. A próxima etapa consiste em tesar o novo projeto do serviço móvel.

## <a name="test-the-service-locally"></a>Testar o projeto de serviço móvel localmente

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation][mobile-services-dotnet-backend-test-local-service-api-documentation]]

## <a name="update-app"></a>Atualizar o aplicativo para usar o serviço móvel

Nesta seção, você irá atualizar o aplicativo Windows universal para usar o serviço móvel como um serviço de back-end do aplicativo. Você precisa fazer alterações apenas no arquivo do projeto default.js na pasta de projeto GetStartedWithData.Shared.

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app][mobile-services-windows-javascript-update-data-app]]

## <a name="publish-mobile-service"></a>Publicar o serviço móvel no Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

## <a name="test-azure-hosted"></a>Testar o serviço móvel hospedado no Azure

Agora podemos testar ambos os serviços do aplicativo Windows universal em relação ao serviço móvel hospedado no Azure.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app][mobile-services-windows-universal-test-app]]

## <a name="view-stored-data"></a>Exibir os dados armazenados no Banco de Dados SQL

[WACOM.INCLUDE [mobile-services-dotnet-backend-view-sql-data][mobile-services-dotnet-backend-view-sql-data]]

Isso conclui o tutorial **Introdução aos dados**.

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um projeto do aplicativo Windows universal para trabalhar com dados nos Serviços Móveis. Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

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
    Saiba mais sobre como usar os Serviços Móveis com HTML e JavaScript.



  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [esta versão]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
  [Baixar o projeto de aplicativo da Windows Store]: #download-app
  [Criar um novo serviço móvel a partir do Visual Studio]: #create-service
  [Testar o projeto de serviço móvel localmente]: #test-the-service-locally
  [Atualizar o aplicativo para usar o serviço móvel]: #update-app
  [Publicar o serviço móvel no Azure]: #publish-mobile-service
  [Testar o aplicativo no serviço hospedado no Azure]: #test-azure-hosted
  [Exibir os dados armazenados no Banco de Dados SQL]: #view-stored-data
  [Avaliação gratuita do Azure]: http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-javascript-get-started-data%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-windows-universal-javascript-download-project]: ../includes/mobile-services-windows-universal-javascript-download-project.md
  [mobile-services-dotnet-backend-create-new-service-vs2013]: ../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md
  [mobile-services-dotnet-backend-test-local-service-api-documentation]: ../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md
  [mobile-services-windows-javascript-update-data-app]: ../includes/mobile-services-windows-javascript-update-data-app.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-windows-universal-test-app]: ../includes/mobile-services-windows-universal-test-app.md
  [mobile-services-dotnet-backend-view-sql-data]: ../includes/mobile-services-dotnet-backend-view-sql-data.md
  [Validar e modificar dados com scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js
  [Refinar consultas com paginação]: /en-us/develop/mobile/tutorials/add-paging-to-data-js
  [Introdução à autenticação]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
  [Introdução às notificações por push]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
  [Referência conceitual do tutorial do .NET de Serviços Móveis]: /en-us/documentation/articles/mobile-services-html-how-to-use-client-library/
