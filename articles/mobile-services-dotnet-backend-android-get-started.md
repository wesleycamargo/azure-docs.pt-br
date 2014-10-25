<properties pageTitle="Get Started with Azure Mobile Services for Android apps" metaKeywords="Azure android application, mobile service android, getting started Azure android, azure droid, getting started droid windows" description="Follow this tutorial to get started using Azure Mobile Services for Android development." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a um aplicativo Android usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples *To do list* que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa as linguagens do .NET com suporte usando o Visual Studio para a lógica de negócios do lado do servidor e para gerenciar o serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte [Versão de back-end do JavaScript][Versão de back-end do JavaScript] neste tópico.

Uma captura de tela do aplicativo completo está disponível abaixo:

![][]

Concluir este tutorial exige as [Ferramentas para desenvolvedores do Android][Ferramentas para desenvolvedores do Android], que inclui o ambiente de desenvolvimento integrado (IDE) Eclipse, o plug-in Android Developer Tools (ADT) e a mais recente plataforma Android. Android 4.2 ou uma versão posterior é necessária.

O projeto quickstart baixado contém o SDK dos Serviços Móveis para Android. Enquanto este projeto requer Android 4.2 ou uma versão posterior, o SDK dos Serviços Móveis exige apenas Android 2.2 ou uma versão posterior.

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure. Se voc&ecirc; n&atilde;o tem uma conta, voc&ecirc; pode se inscrever para uma avalia&ccedil;&atilde;o do Azure e obter at&eacute; 10 servi&ccedil;os m&oacute;veis gratuitos que voc&ecirc; pode continuar usando mesmo depois do fim de sua avalia&ccedil;&atilde;o. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][mobile-services-dotnet-backend-create-new-service]]

## Baixar o serviço móvel para o computador local

Agora que você criou o serviço móvel, baixe o projeto de serviço móvel personalizado que pode ser executado no computador local ou na máquina virtual.

1.  Clique no serviço móvel que você acabou de criar, em seguida, na guia quickstart, clique em **Android** em **Escolher plataforma** e expanda **Criar um novo aplicativo Android**.

    ![][1]

2.  Se ainda não tiver feito isso, baixe e instale o [Visual Studio Professional 2013][Visual Studio Professional 2013] ou uma versão posterior.

3.  Clique em **Baixar** em **Baixar e publicar seu serviço na nuvem**.

    Isso baixa o projeto do Visual Studio que implementa o serviço móvel. Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

4.  Além disso, baixe seu perfil de publicação, salve o arquivo baixado no computador local e anote o local onde o salvou.

## Testar o serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][mobile-services-dotnet-backend-test-local-service]]

## Publicar o serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

## Criar novo aplicativo Android

Nesta seção você criará um novo aplicativo Android que está conectado ao seu serviço móvel.

1.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2.  Na guia de início rápido, clique em **Android** em **Escolher a plataforma** e expanda **Criar um novo aplicativo Android**.

    ![][2]

3.  Se ainda não o fez, baixe e instale [Android Developer Tools][Ferramentas para desenvolvedores do Android] em seu computador local ou máquina virtual.

4.  Em **Baixe e execute o aplicativo**, clique em **Baixar**.

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

<!--This shows how to run your new client app against the mobile service running in Azure. Before you can test the Android app with the mobile service running on a local computer, you must configure the Web server and firewall to allow access from your Android development computer. For more information, see [Configure the local web server to allow connections to a local mobile service](/pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).-->

## <a name="next-steps"> </a>Próximas etapas

Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis:

-   [Começar a trabalhar com dados][Começar a trabalhar com dados]
    	
	Saiba mais sobre armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à autenticação][Introdução à autenticação]
    
	Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.

-   [Introdução às notificações por push][Introdução às notificações por push]
    
	Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

-   [Solucionar problemas de back-end do .NET dos Serviços Móveis][Solucionar problemas de back-end do .NET dos Serviços Móveis]
     
	Saiba como diagnosticar e corrigir problemas que podem surgir com o back-end do .NET dos Serviços Móveis.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [Versão de back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-android-get-started/
  []: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
  [Ferramentas para desenvolvedores do Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs.png
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=391934
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  [2]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
  [3]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
  [4]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png
  [5]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png
  [6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
  [Começar a trabalhar com dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
  [Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
  [Solucionar problemas de back-end do .NET dos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
