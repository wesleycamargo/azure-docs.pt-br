<properties pageTitle="Get Started with Azure Mobile Services for iOS apps" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo iOS usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples *To do list* que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa as linguagens do .NET com suporte usando o Visual Studio para a lógica de negócios do lado do servidor e para gerenciar o serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte [Versão de back-end do JavaScript][Versão de back-end do JavaScript] neste tópico.

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

Para concluir este tutorial, é necessário ter XCode 4.5 e iOS 5.0 ou versões posteriores.

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure. Se voc&ecirc; n&atilde;o tem uma conta, voc&ecirc; pode se inscrever para uma avalia&ccedil;&atilde;o do Azure e obter at&eacute; 10 servi&ccedil;os m&oacute;veis gratuitos que voc&ecirc; pode continuar usando mesmo depois do fim de sua avalia&ccedil;&atilde;o. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Baixar o serviço móvel para o computador local

Agora que você criou o serviço móvel, baixe o projeto de serviço móvel personalizado que pode ser executado no computador local ou na máquina virtual.

1.  Clique no serviço móvel que você acabou de criar, em seguida, na guia quickstart, clique em **iOS** em **Escolher plataforma** e expanda **Criar um novo aplicativo iOS**.

    ![][1]

2.  Se ainda não tiver feito isso, baixe e instale o Visual Studio Professional 2013 ou uma versão posterior.

3.  Clique em **Baixar** em **Baixar e publicar seu serviço na nuvem**.

    Isso baixa o projeto do Visual Studio que implementa o serviço móvel. Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

4.  Além disso, baixe seu perfil de publicação, salve o arquivo baixado no computador local e anote o local onde o salvou.

## Testar o serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publicar o serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Criar um novo aplicativo iOS

Nesta seção você criará um novo aplicativo iOS que está conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2.  Na guia de início rápido, clique em **iOS** em **Escolher a plataforma** e expanda **Criar um novo aplicativo iOS**.

3.  Se ainda não fez isso, baixe e instale o [Xcode][Xcode] v 4.4 ou uma versão posterior.

4.  Em **Baixe e execute o aplicativo**, clique em **Baixar**.

    Isso baixa o projeto para o aplicativo de exemplo *To do list* que está conectado ao serviço móvel, juntamente com o SDK do iOS dos Serviços Móveis. Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu novo aplicativo iOS

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

Isso mostra como executar seu novo aplicativo cliente no serviço móvel em execução no Azure. Antes de testar o aplicativo iOS com o serviço móvel em execução em um computador local, você deve configurar o servidor Web e o firewall para permitir acesso a partir de seu computador de desenvolvimento do iOS. Para obter mais informações, consulte [Configurar o servidor web local para permitir conexões a um Serviço Móvel local][Configurar o servidor web local para permitir conexões a um Serviço Móvel local].

## <a name="next-steps"> </a>Próximas etapas

Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis:

-   [Começar a trabalhar com dados][Começar a trabalhar com dados]
    Saiba mais sobre armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à sincronização de dados offline][Introdução à sincronização de dados offline]
    Saiba como usar a sincronização de dados offline para tornar o seu aplicativo responsivo e robusto.

-   [Introdução à autenticação][Introdução à autenticação]
    Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.

-   [Introdução às notificações por push][Introdução às notificações por push]
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

-   [Solucionar problemas de back-end do .NET dos Serviços Móveis][Solucionar problemas de back-end do .NET dos Serviços Móveis]
     Saiba como diagnosticar e corrigir problemas que podem surgir com o back-end do .NET dos Serviços Móveis.

 
 


  [Versão de back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-ios-get-started
  [0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
  [1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png
  [Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Configurar o servidor web local para permitir conexões a um Serviço Móvel local]: /pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express
  [Começar a trabalhar com dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
  [Introdução à sincronização de dados offline]: /pt-br/documentation/articles/mobile-services-ios-get-started-offline-data
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
  [Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push
  [Solucionar problemas de back-end do .NET dos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
