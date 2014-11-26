<properties pageTitle="Get Started with Azure Mobile Services for Android apps" metaKeywords="Azure android application, mobile service android, getting started Azure android, azure droid, getting started droid windows" description="Follow this tutorial to get started using Azure Mobile Services for Android development." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tutorial mostra como adicionar um servi&ccedil;o de back-end baseado na nuvem a um aplicativo Android usando os Servi&ccedil;os M&oacute;veis do Azure. Neste tutorial, voc&ecirc; criar&aacute; um novo servi&ccedil;o m&oacute;vel e um aplicativo simples <em>To do list</em> que armazena dados do aplicativo no novo servi&ccedil;o m&oacute;vel.</p>
<p>Uma captura de tela do aplicativo completo est&aacute; dispon&iacute;vel abaixo:</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o v&iacute;deo</span></a><span class="time">07:26:00</span></div>

</div>

![][0]

Concluir este tutorial exige as [Ferramentas para desenvolvedores do Android][Ferramentas para desenvolvedores do Android], que inclui o ambiente de desenvolvimento integrado (IDE) Eclipse, o plug-in Android Developer Tools (ADT) e a mais recente plataforma Android. Android 4.2 ou uma versão posterior é necessária.

O projeto quickstart baixado contém o SDK dos Serviços Móveis para Android. Enquanto este projeto requer Android 4.2 ou uma versão posterior, o SDK dos Serviços Móveis exige apenas Android 2.2 ou uma versão posterior.

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure. Se voc&ecirc; n&atilde;o tiver uma conta, poder&aacute; criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## 

## <span class="short-header">Criar um novo aplicativo</span>Criar um novo aplicativo Android

</h2>
Depois de criar seu serviço móvel, você poderá seguir um quickstart fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel.

Nesta seção você criará um novo aplicativo Android que está conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2.  Na guia de início rápido, clique em **Android** em **Escolher a plataforma** e expanda **Criar um novo aplicativo Android**.

    ![][1]

    Isso exibe as três etapas fáceis para criar um aplicativo Android conectado ao seu serviço móvel.

    ![][2]

3.  Se ainda não o fez, baixe e instale [Android Developer Tools][Ferramentas para desenvolvedores do Android] em seu computador local ou máquina virtual.

4.  Clique em **Criar tabela TodoItem** para criar uma tabela para armazenar os dados do aplicativo.

5.  Em **Baixe e execute o aplicativo**, clique em **Baixar**.

    Isso baixa o projeto para o aplicativo de exemplo *To do list* que está conectado ao seu serviço móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu aplicativo Android

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1.  Navegue até o local onde você salvou os arquivos do projeto compactados e expanda os arquivos em seu computador.

2.  No Eclipse, clique em **Arquivo**, em **Importar**, expanda **Android**, clique em **Código Android existente no espaço de trabalho** e clique em **Avançar.**

    ![][3]

3.  Clique em **Procurar**, navegue até o local dos arquivos de projeto expandidos, clique em **OK**, certifique-se de que o projeto TodoActivity está marcado e clique em **Concluir**.

    ![][4]

    Isso importa os arquivos de projeto no espaço de trabalho atual.

    ![][5]

4.  No menu **Executar**, clique em **Executar** para iniciar o projeto no emulador Android.

    <div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para que seja poss&iacute;vel executar o projeto no emulador do Android, voc&ecirc; deve definir no m&iacute;nimo um AVD (Dispositivo Virtual Android). Use o Gerenciador do AVD para criar e gerenciar esses dispositivos.</p></div>

5.  No aplicativo, digite um texto significativo, como *Concluir o tutorial* e clique em **Adicionar**.

    ![][6]

    Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.

    <div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> 
<p>Voc&ecirc; pode examinar o c&oacute;digo que acessa o servi&ccedil;o m&oacute;vel para consultar e inserir dados, que est&atilde;o localizados no arquivo ToDoActivity.java.</p>
</div>

6.  No Portal de Gerenciamento, clique na guia **Dados** e, em seguida na tabela **TodoItems**.

    ![][7]

    Isso permite que você procure os dados inseridos pelo aplicativo na tabela.

    ![][8]

## <a name="next-steps"> </a>Próximas etapas

Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis:

-   [Começar a trabalhar com dados][Começar a trabalhar com dados]
    Saiba mais sobre armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à autenticação][Introdução à autenticação]
    Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.

-   [Introdução às notificações por push][Introdução às notificações por push]
    Saiba como enviar uma notificação por push bastante básica a seu aplicativo.





  [0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png
  [Ferramentas para desenvolvedores do Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [1]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
  [2]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android.png
  [3]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
  [4]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png
  [5]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png
  [6]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
  [7]: ./media/mobile-services-android-get-started/mobile-data-tab.png
  [8]: ./media/mobile-services-android-get-started/mobile-data-browse.png
  [Começar a trabalhar com dados]: /pt-br/documentation/articles/mobile-services-android-get-started-data/
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-android-get-started-users/
  [Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
