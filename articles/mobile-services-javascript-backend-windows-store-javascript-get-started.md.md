<properties pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="hero-article" ms.date="08/18/2014" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started][]]

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a um aplicativo universal do Windows usando os Serviços Móveis do Azure.

Neste tutorial, você criará um novo serviço móvel e um aplicativo simples *To do list* que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa JavaScript para a lógica de negócios do lado do servidor. Para criar um serviço móvel que permita escrever sua lógica de negócios do servidor nas linguagens do .NET compatíveis usando o Visual Studio, consulte a versão para back-end do .NET deste tópico.

[WACOM.INCLUDE [mobile-services-windows-universal-get-started][]]

Para concluir este tutorial, você precisará do seguinte:

-   Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][].
-   [Visual Studio 2013 Express para Windows][]

## Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service][]]

## Criar um novo aplicativo universal do Windows

Depois de criar seu serviço móvel, você poderá seguir um fácil início rápido no Portal de Gerenciamento para criar um novo aplicativo universal do Windows ou modificar um projeto de aplicativo existente da Windows Store ou Windows Phone para se conectar ao seu serviço móvel.

Nesta seção, você criará um novo aplicativo universal do Windows conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis** e, em seguida, clique no serviço móvel que você acabou de criar.

2.  Na guia de início rápido, clique em **Windows** em **Escolher plataforma** e expanda **Criar um novo aplicativo da Windows Store**.

    ![][]

    Isso exibe as três etapas fáceis para criar um aplicativo da Windows Store conectado ao seu serviço móvel.

    ![][1]

3.  Se ainda não o fez, baixe e instale o [Visual Studio 2013][Visual Studio 2013 Express para Windows] no computador local ou na máquina virtual.

4.  Clique em **Criar tabela TodoItem** para criar uma tabela para armazenar os dados do aplicativo.

5.  Em **Baixe e execute seu aplicativo**, selecione um idioma para seu aplicativo e clique em **Baixar**.

    Isso baixa o projeto para o aplicativo de exemplo *To do list* que está conectado ao seu serviço móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu aplicativo do Windows

[WACOM.INCLUDE [mobile-services-javascript-backend-run-app][]]

> [WACOM.NOTE]Você poderá examinar o código que acessa o seu serviço móvel para consultar e inserir dados, que estão localizados no arquivo default.js.

## Próximas etapas

Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis:

-   [Introdução aos dados][]
    Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à autenticação][]
    Saiba como autenticar usuários de seu aplicativo com um provedor de identidades.

-   [Introdução às notificações por push][]
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

Para obter mais informações sobre os aplicativos universais do Windows, consulte [Suporte a plataformas de múltiplos dispositivos por meio de um único serviço móvel][].

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [mobile-services-windows-universal-get-started]: ../includes/mobile-services-windows-universal-get-started.md
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F
  [Visual Studio 2013 Express para Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  []: ./media/mobile-services-javascript-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png
  [1]: ./media/mobile-services-javascript-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png
  [mobile-services-javascript-backend-run-app]: ../includes/mobile-services-javascript-backend-run-app.md
  [Introdução aos dados]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-data
  [Introdução à autenticação]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users
  [Introdução às notificações por push]: /en-us/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push
  [Suporte a plataformas de múltiplos dispositivos por meio de um único serviço móvel]: /en-us/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs
