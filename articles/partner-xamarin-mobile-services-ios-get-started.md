<properties pageTitle="Get Started with Mobile Services for Xamarin iOS apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Xamarin iOS development." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with Mobile Services" authors="craigd" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="craigd" />

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Xamarin.iOS usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples *To do list* que armazena dados do aplicativo no novo serviço móvel.

Se você preferir assistir a um vídeo, o clipe abaixo segue as mesmas etapas deste tutorial.

Vídeo: “Introdução aos Serviços Móveis do Xamarin e do Azure" com Craig Dunn, desenvolvedor evangelista para Xamarin (duração: 10:05 min)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

A conclusão deste tutorial requer o XCode 4.5 e o iOS 5.0 ou versões posteriores bem como o [Xamarin Studio][Xamarin Studio] para OS X ou o plug-in Xamarin Visual Studio para Visual Studio no Windows.

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure. Se voc&ecirc; n&atilde;o tem uma conta, voc&ecirc; pode se inscrever para uma avalia&ccedil;&atilde;o do Azure e obter at&eacute; 10 servi&ccedil;os m&oacute;veis gratuitos que voc&ecirc; pode continuar usando mesmo depois do fim de sua avalia&ccedil;&atilde;o. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <span class="short-header">Criar um novo aplicativo</span>Criar um novo aplicativo Xamarin.iOS

Depois de criar seu serviço móvel, você poderá seguir um quickstart fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel.

Nesta seção você criará um novo aplicativo Xamarin.iOS que está conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2.  Na guia de início rápido, clique em **Xamarin.iOS** sob **Escolher Plataforma** e expanda **Criar um novo aplicativo Xamarin.iOS**.

    ![][1]

    Isso exibe as três etapas fáceis de criar um aplicativo Xamarin.iOS conectado a seu serviço móvel.

    ![][2]

3.  Se ainda não fez isso, baixe e instale o [Xcode] v 4.4 ou uma versão posterior e o [Xamarin Studio][Xamarin Studio].

4.  Clique em **Criar tabela TodoItems** para criar uma tabela para armazenar dados de aplicativo.

5.  Em **Baixe e execute o aplicativo**, clique em **Baixar**.

    Isso baixa o projeto para o aplicativo *To do list* que está conectado a seu serviço móvel e faz referência ao componente dos Serviços Móveis do Azure para Xamarin.iOS. Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

## <span class="short-header">Executar o aplicativo</span>Executar o novo aplicativo Xamarin.iOS

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1.  Navegue até o local onde você salvou os arquivos compactados do projeto, expanda os arquivos em seu computador e abra o arquivo da solução **XamarinTodoQuickStart.iOS.sln** usando o Xamarin Studio ou o Visual Studio.

    ![][3]

    ![][4]

2.  Pressione o botão **Executar** para criar o projeto e iniciar o aplicativo no emulador do iPhone, que é o padrão para este projeto.

3.  No aplicativo, digite um texto significativo, como *Concluir o tutorial* e clique no ícone de adição (**+**).

    ![][5]

    Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.

    <div class="dev-callout"> 
<b>Observa&ccedil;&atilde;o</b> 
<p>Voc&ecirc; pode examinar o c&oacute;digo que acessa o servi&ccedil;o m&oacute;vel para consultar e inserir dados que est&atilde;o localizados no arquivo TodoService.cs C#.</p> 
</div>

4.  No Portal de Gerenciamento, clique na guia **Dados** e, em seguida na tabela **TodoItems**.

    ![][6]

    Isso permite que você procure os dados inseridos pelo aplicativo na tabela.

    ![][7]

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

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
  [Xamarin Studio]: http://xamarin.com/download
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
  [2]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
  [3]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
  [4]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
  [5]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
  [6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
  [7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png
  [Começar a trabalhar com dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [Introdução à sincronização de dados offline]: /pt-br/develop/mobile/tutorials/mobile-services-xamarin-ios-get-started-offline-data
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-ios
  [Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-ios
