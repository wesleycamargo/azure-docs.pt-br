<properties pageTitle="Get Started with Azure Mobile Services for iOS apps" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="hero-article" ms.date="01/01/1900" ms.author="krisragh" />

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tutorial mostra como adicionar um servi&ccedil;o de back-end baseado em nuvem a um aplicativo iOS usando os Servi&ccedil;os M&oacute;veis do Azure.</p>
<p>Se voc&ecirc; preferir assistir a um v&iacute;deo, o clipe &agrave; direita segue as mesmas etapas deste tutorial.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-ios-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o v&iacute;deo</span></a> <span class="time">09:38:00</span></div>

</div>

Neste tutorial, você criará um novo serviço móvel e um aplicativo simples *To do list* que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa JavaScript para a lógica de negócios do lado do servidor. Para criar um serviço móvel que permite que você grave sua lógica de negócios do lado do servidor nos idiomas com suporte de .NET usando o Visual Studio, consulte [Versão de back-end do .NET][Versão de back-end do .NET] neste tópico.

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

Para concluir este tutorial, é necessário ter XCode 4.5 e iOS 5.0 ou versões posteriores.

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure. Se voc&ecirc; n&atilde;o tem uma conta, voc&ecirc; pode se inscrever para uma avalia&ccedil;&atilde;o do Azure e obter at&eacute; 10 servi&ccedil;os m&oacute;veis gratuitos que voc&ecirc; pode continuar usando mesmo depois do fim de sua avalia&ccedil;&atilde;o. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <span class="short-header">Criar um novo aplicativo</span>Criar um novo aplicativo iOS

Depois de criar seu serviço móvel, você poderá seguir um quickstart fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel.

Nesta seção você criará um novo aplicativo iOS que está conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2.  Na guia de início rápido, clique em **iOS** em **Escolher a plataforma** e expanda **Criar um novo aplicativo iOS**.

    ![][1]

    Isso exibe as três etapas fáceis para criar um aplicativo iOS conectado ao seu serviço móvel.

    ![][2]

3.  Se ainda não fez isso, baixe e instale o [Xcode][Xcode] v 4.4 ou uma versão posterior.

4.  Clique em **Criar tabela TodoItems** para criar uma tabela para armazenar dados de aplicativo.

5.  Em **Baixe e execute o aplicativo**, clique em **Baixar**.

    Isso baixa o projeto para o aplicativo de exemplo *To do list* que está conectado ao serviço móvel, juntamente com o SDK do iOS dos Serviços Móveis. Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu novo aplicativo iOS

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

1.  

    No Portal de Gerenciamento, clique na guia **Dados** e, em seguida na tabela **TodoItems**.

    <p>

</p>
![][3]

Isso permite que você procure os dados inseridos pelo aplicativo na tabela.

</p>
![][4]

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

 
 


  [Versão de back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started
  [0]: ./media/mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
  [1]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
  [2]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
  [Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [3]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
  [4]: ./media/mobile-services-ios-get-started/mobile-data-browse.png
  [Começar a trabalhar com dados]: /pt-br/documentation/articles/mobile-services-ios-get-started-data/
  [Introdução à sincronização de dados offline]: /pt-br/documentation/articles/mobile-services-ios-get-started-offline-data
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-ios-get-started-users/
  [Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
