<properties linkid="develop-mobile-tutorials-get-started" urlDisplayName="Get Started" pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C#, VB, or JavaScript. " metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a um aplicativo universal do Windows usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples de *Tarefas pendentes* em HTML e JavaScript que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você cria usa as linguagens do .NET com suporte usando o Visual Studio para a lógica de negócios do lado do servidor e para gerenciar o serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte Versão do JavaScript neste tópico.

[WACOM.INCLUDE [mobile-services-windows-universal-get-started][mobile-services-windows-universal-get-started]]

Para concluir este tutorial, você precisará do seguinte:

-   Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].
-   [Visual Studio Professional 2013][Visual Studio Professional 2013]. Uma versão de avaliação gratuita está disponível.

## Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][mobile-services-dotnet-backend-create-new-service]]

## Criar um novo aplicativo universal do Windows

Depois de criar seu serviço móvel, você poderá seguir um quickstart fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel.

Nesta seção, você criará um novo aplicativo universal do Windows conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2.  Na guia de início rápido, clique em **Windows** em **Escolher plataforma** e expanda **Criar um novo aplicativo da Windows Store**.

    ![][]

    Isso exibe as três etapas fáceis para criar um aplicativo da Windows Store conectado ao seu serviço móvel.

    ![][1]

3.  Se ainda não o fez, baixe e instale o [Visual Studio Professional 2013][Visual Studio Professional 2013] no computador local ou na máquina virtual.

4.  Em **Baixar e executar seu aplicativo e serviço localmente**, selecione um idioma para seu aplicativo da Windows Store e clique em **Baixar**.

    Isso baixa uma solução que contém projetos para o serviço móvel e para o aplicativo de exemplo *To do list* que está conectado ao seu serviço móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

## Testar o aplicativo no serviço móvel local

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet][mobile-services-dotnet-backend-test-local-service-dotnet]]

> [WACOM.NOTE]Você poderá examinar o código que acessa o seu serviço móvel para consultar e inserir dados, que estão localizados no arquivo default.js.

## Publicar o serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

1.  No projeto do código compartilhado, abra o arquivo default.js, localize o código que cria uma instância de [WindowsAzure.MobileServiceClient][WindowsAzure.MobileServiceClient], comente o código que cria esse cliente usando *localhost* e remova o comentário do código que cria o cliente usando a URL de serviço móvel remoto, de maneira semelhante à seguinte:

        var client = new WindowsAzure.MobileServiceClient(
                    "https://todolist.azure-mobile.net/",
                    "XXXXXX-APPLICATION-KEY-XXXXXX"
                );

    Agora, o cliente acessará o serviço móvel publicado no Azure.

2.  Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

3.  No aplicativo, digite um texto com sentido, como *Concluir o tutorial*, em **Inserir um TodoItem** e clique em **Salvar**.

    Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure.

4.  (Opcional) Em uma solução universal do Windows, altere o projeto inicial padrão para o outro aplicativo e pressione **F5** novamente.

    Observe que os dados salvos da etapa anterior são carregados a partir do serviço móvel após o aplicativo ser iniciado.

## Próximas etapas

Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis:

-   [Começar a trabalhar com dados][Começar a trabalhar com dados]
    
	Saiba mais sobre armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à autenticação][Introdução à autenticação]
    
	Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.

-   [Introdução às notificações por push][Introdução às notificações por push]
    
	Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

Para obter mais informações sobre os aplicativos universais do Windows, consulte [Suporte a plataformas de múltiplos dispositivos por meio de um único serviço móvel][Suporte a plataformas de múltiplos dispositivos por meio de um único serviço móvel].

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [mobile-services-windows-universal-get-started]: ../includes/mobile-services-windows-universal-get-started.md
  [Avaliação gratuita do Azure]: http://azure.microsoft.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-br%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  []: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png
  [1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png
  [mobile-services-dotnet-backend-test-local-service-dotnet]: ../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [WindowsAzure.MobileServiceClient]: http://msdn.microsoft.com/library/azure/jj554219.aspx
  [Começar a trabalhar com dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
  [Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push
  [Suporte a plataformas de múltiplos dispositivos por meio de um único serviço móvel]: /pt-br/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs
