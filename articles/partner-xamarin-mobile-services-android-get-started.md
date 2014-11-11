<properties pageTitle="Get Started with Mobile Services for Xamarin.Android" metaKeywords="Azure Xamarin.Android application, mobile service XamarinAndroid, getting started Azure Xamarin.Android" writer="craigd" description="Learn how to use Azure Mobile Services with your Xamarin.Android app." metaCanonical="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="donnam" />

# <a name="getting-started"></a>Introdução aos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tutorial mostra como adicionar um servi&ccedil;o de back-end baseado em nuvem a um aplicativo Xamarin.Android usando os Servi&ccedil;os M&oacute;veis do Azure. Neste tutorial, voc&ecirc; criar&aacute; um novo servi&ccedil;o m&oacute;vel e um aplicativo simples <em>To do list</em> que armazena dados do aplicativo no novo servi&ccedil;o m&oacute;vel.</p>
<p>Uma captura de tela do aplicativo completo est&aacute; dispon&iacute;vel abaixo:</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-xamarin-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o v&iacute;deo</span></a> <span class="time">10:05:00</span></div>

</div>

![][0]

A conclusão deste tutorial requer o [Xamarin.Android][Xamarin.Android], que instala o Xamarin Studio e um plug-in do Visual Studio (no Windows), bem como a mais recente plataforma Android. SDK do Android 4.2 ou uma versão posterior é necessária.

O projeto quickstart baixado contém o componente dos Serviços Móveis do Azure para o Xamarin.Android. Embora esse projeto tenha como alvo o Android 4.2 ou uma versão posterior, o SDK dos Serviços Móveis exige apenas o Android 2.2 ou uma versão posterior.

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure. Se voc&ecirc; n&atilde;o tem uma conta, voc&ecirc; pode se inscrever para uma avalia&ccedil;&atilde;o do Azure e obter at&eacute; 10 servi&ccedil;os m&oacute;veis gratuitos que voc&ecirc; pode continuar usando mesmo depois do fim de sua avalia&ccedil;&atilde;o. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A9C9624B5" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## 

## <span class="short-header">Criar um novo aplicativo</span>Criar um novo aplicativo

</h2>
Depois de criar seu serviço móvel, você poderá seguir um quickstart fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel.

Nesta seção, você criará um novo aplicativo Xamarin.Android que está conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2.  Na guia de início rápido, clique em **Xamarin.Android** sob **Escolher plataforma** e expanda **Criar um novo aplicativo Android**.

    ![][1]

    Isso exibe as três etapas fáceis de criar um aplicativo Xamarin.Android conectado a seu serviço móvel.

    ![][2]

3.  Clique em **Criar tabela TodoItem** para criar uma tabela para armazenar os dados do aplicativo.

4.  Em **Baixe e execute o aplicativo**, clique em **Baixar**.

    Isso baixa o projeto para o aplicativo de exemplo *To do list* que está conectado ao seu serviço móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu aplicativo Android

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1.  Navegue até o local onde você salvou os arquivos do projeto compactados e expanda os arquivos em seu computador.

2.  No Xamarin Studio ou no Visual Studio, clique em **Arquivo** e, em seguida, em **Abrir**, navegue até os arquivos de exemplo não compactados e selecione **XamarinTodoQuickStart.Android.sln** para abri-lo.

    ![][3]

    ![][4]

3.  Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo. Você será solicitado a selecionar um emulador ou um dispositivo USB conectado.

    <div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para que seja poss&iacute;vel executar o projeto no emulador do Android, voc&ecirc; deve definir no m&iacute;nimo um AVD (Dispositivo Virtual Android). Use o Gerenciador do AVD para criar e gerenciar esses dispositivos.</p></div>

4.  No aplicativo, digite um texto significativo, como *Concluir o tutorial* e clique em **Adicionar**.

    ![][5]

    Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.

    > [WACOM.NOTE]
    > Você pode examinar o código que acessa seu serviço móvel para consultar e inserir dados que estão localizados no arquivo ToDoActivity.cs C#.

5.  No Portal de Gerenciamento, clique na guia **Dados** e, em seguida na tabela **TodoItems**.

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
  [0]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
  [Xamarin.Android]: http://xamarin.com/download
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
  [2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
  [3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
  [4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
  [5]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
  [6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
  [7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
  [Começar a trabalhar com dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-android
  [Introdução à sincronização de dados offline]: /pt-br/documentation/articles/mobile-services-xamarin-android-get-started-offline-data
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-android
  [Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-android
