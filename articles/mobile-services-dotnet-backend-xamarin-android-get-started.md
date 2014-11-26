<properties linkid="mobile-services-dotnet-backend-xamarin-android-get-started" urlDisplayName="Get Started with Mobile Services for Xamarin Android apps" pageTitle="Get Started with Mobile Services for Xamarin Android apps - Azure Mobile Services" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Xamarin Android development" metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with Mobile Services for Xamarin Android apps" authors="donnam" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Xamarin Android usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples *To do list* que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa as linguagens do .NET com suporte usando o Visual Studio para a lógica de negócios do lado do servidor e para gerenciar o serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte [Versão de back-end do JavaScript][Versão de back-end do JavaScript] neste tópico.

> [WACOM.NOTE]Este tópico mostra como criar um novo projeto de serviço móvel usando o Portal de Gerenciamento do Azure. Usando o Visual Studio 2013 Update 2, você também pode adicionar um novo projeto de serviço móvel em uma solução existente do Visual Studio. Para saber mais, confira [Início rápido: Adicionar um serviço móvel (back-end .NET)][Início rápido: Adicionar um serviço móvel (back-end .NET)]

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Serviços Móveis para aplicativos Xamarin Android.

> [WACOM.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].
> Este tutorial requer o [Visual Studio Professional 2013][Visual Studio Professional 2013]. Uma versão de avaliação gratuita está disponível.

## Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Criar um novo aplicativo Xamarin Android

Depois de criar seu serviço móvel, você poderá seguir um quickstart fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel.

Nesta seção você baixará um novo aplicativo Xamarin Android e um projeto de serviço para seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis** e, em seguida, clique no serviço móvel que você acabou de criar.

2.  Na guia de início rápido, clique em **Xamarin** em **Escolher plataforma** e expanda **Criar um novo aplicativo Xamarin**.

    ![][1]

    Isso exibe as três etapas fáceis para criar um aplicativo Xamarin Android conectado ao seu serviço móvel.

    ![][2]

3.  Se ainda não tiver feito isso, baixe e instale o [Visual Studio Professional 2013][Visual Studio Professional 2013] no computador local ou na máquina virtual.

4.  Se ainda não tiver feito isso, baixe e instale o [Xamarin Studio][Xamarin Studio] ou o Xamarin for Visual Studio.

5.  Em **Baixar e publicar seu serviço para nuvem**, selecione **Android** e clique em **Baixar**.

    Isso baixa uma solução que contém projetos para o serviço móvel e para o aplicativo de exemplo *To do list* que está conectado ao seu serviço móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

6.  Baixe seu perfil de publicação, salve o arquivo baixado no computador local e anote o local onde o salvou.

## Testar o serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publicar seu serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Executar o aplicativo Xamarin Android

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1.  Navegue para o projeto cliente dentro da solução de serviço móvel, no Visual Studio ou no Xamarin Studio.

    ![][3]

    ![][4]

2.  Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo. Você será solicitado a selecionar um emulador ou um dispositivo USB conectado.

    <div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>Para poder executar o projeto no emulador do Android, voc&ecirc; deve definir no m&iacute;nimo um Android Virtual Device (AVD). Use o Gerenciador do AVD para criar e gerenciar esses dispositivos.</p></div>

3.  No aplicativo, digite um texto que tenha sentido, como *Concluir o tutorial* e clique no ícone de sinal de mais (**+**).

    ![][5]

    Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.

    > [WACOM.NOTE]
    > Você pode examinar o código que acessa seu serviço móvel para consultar e inserir dados que estão localizados no arquivo C# ToDoActivity.cs.

## Próximas etapas

Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis:

-   [Introdução à autenticação][Introdução à autenticação]
    Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.

-   [Introdução às notificações por push][Introdução às notificações por push]
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo.





  [Versão de back-end do JavaScript]: /pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started
  [0]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-completed-android.png
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-android-get-started
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [1]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
  [2]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-steps-xamarin-android.png
  [Xamarin Studio]: http://xamarin.com/download
  [3]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
  [4]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
  [5]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-startup-android.png
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users
  [Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push
