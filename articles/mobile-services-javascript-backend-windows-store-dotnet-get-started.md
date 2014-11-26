<properties pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="hero-article" ms.date="08/18/2014" ms.author="glenga" />

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a um aplicativo universal do Windows usando os Serviços Móveis do Azure. Soluções de aplicativos universais do Windows incluem projetos para a Windows Store 8.1 e a Windows Phone Store 8.1, e um projeto compartilhado comum. Para obter mais informações, consulte [Criar aplicativos universais do Windows voltados para Windows e Windows Phone][Criar aplicativos universais do Windows voltados para Windows e Windows Phone].

Neste tutorial, você criará um novo serviço móvel e um aplicativo simples *To do list* que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa JavaScript para a lógica de negócios do lado do servidor. Para criar um serviço móvel que permita escrever sua lógica de negócios do servidor nas linguagens do .NET compatíveis usando o Visual Studio, consulte a versão para back-end do .NET deste tópico.

> [WACOM.NOTE]Este tópico mostra como criar um novo projeto de serviço móvel e aplicativo universal do Windows usando o Portal de Gerenciamento do Azure. Usando o Visual Studio 2013, você também pode adicionar um novo projeto de serviço móvel a uma solução existente do Visual Studio. Para obter mais informações, consulte [Início rápido: Adicionar um serviço móvel (back-end JavaScript)][Início rápido: Adicionar um serviço móvel (back-end JavaScript)].

> Para adicionar um serviço móvel a um projeto de aplicativo para Windows Phone 8.0 ou Windows Phone Store 8.1, consulte [Introdução aos dados para Windows Phone][Introdução aos dados para Windows Phone].

[WACOM.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

Para concluir este tutorial, você precisará do seguinte:

-   Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].
-   [Visual Studio 2013 Express para Windows][Visual Studio 2013 Express para Windows]

## Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Criar um novo aplicativo universal do Windows

Depois de criar seu serviço móvel, você poderá seguir um fácil início rápido no Portal de Gerenciamento para criar um novo aplicativo universal do Windows ou modificar um projeto de aplicativo existente da Windows Store ou Windows Phone para se conectar ao seu serviço móvel.

Nesta seção, você criará um novo aplicativo universal do Windows conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2.  Na guia de início rápido, clique em **Windows** em **Escolher plataforma** e expanda **Criar um novo aplicativo da Windows Store**.

    ![][0]

    Isso exibe as três etapas fáceis para criar um aplicativo da Windows Store conectado ao seu serviço móvel.

    ![][1]

3.  Se ainda não o fez, baixe e instale [Visual Studio 2013 Express para Windows][Visual Studio 2013 Express para Windows] em seu computador local ou máquina virtual.

4.  Clique em **Criar tabela TodoItem** para criar uma tabela para armazenar os dados do aplicativo.

5.  Em **Baixe e execute seu aplicativo**, selecione um idioma para seu aplicativo e clique em **Baixar**.

    Isso baixa o projeto para o aplicativo de exemplo *To do list* que está conectado ao seu serviço móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu aplicativo do Windows

[WACOM.INCLUDE [mobile-services-javascript-backend-run-app](../includes/mobile-services-javascript-backend-run-app.md)]

> [WACOM.NOTE]Você pode revisar o código que acessa seu serviço móvel para consultar e inserir dados, ele é encontrado no arquivo MainPage.xaml.cs.

## Próximas etapas

Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis:

-   [Introdução aos dados][Introdução aos dados]
    Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à autenticação][Introdução à autenticação]
    Saiba como autenticar usuários de seu aplicativo com um provedor de identidades.

-   [Introdução às notificações por push][Introdução às notificações por push]
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

Para obter mais informações sobre os aplicativos universais do Windows, consulte [Suporte a plataformas de múltiplos dispositivos por meio de um único serviço móvel][Suporte a plataformas de múltiplos dispositivos por meio de um único serviço móvel].

 
 


  [Criar aplicativos universais do Windows voltados para Windows e Windows Phone]: http://msdn.microsoft.com/pt-br/library/windows/apps/xaml/dn609832.aspx
  [Início rápido: Adicionar um serviço móvel (back-end JavaScript)]: http://msdn.microsoft.com/pt-br/library/windows/apps/xaml/dn263180.aspx
  [Introdução aos dados para Windows Phone]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-data
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-br%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F
  [Visual Studio 2013 Express para Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-portal-quickstart.png
  [1]: ./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png
  [Introdução aos dados]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-users
  [Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push
  [Suporte a plataformas de múltiplos dispositivos por meio de um único serviço móvel]: /pt-br/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs
