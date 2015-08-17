<properties
	pageTitle="Introdução aos Serviços Móveis do Azure para aplicativos Android"
	description="Siga este tutorial para começar a usar os serviços móveis do Azure para desenvolvimento Android."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article" 
	ms.date="06/03/2015"
	ms.author="ricksal"/>

# Introdução aos Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">


<p>Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Android usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples <em>To do list</em> que armazena dados do aplicativo no novo serviço móvel.</p>
<p>Uma captura de tela do aplicativo completo está disponível abaixo:</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o vídeo</span></a><span class="time">07:26:00</span></div>
</div>

![](./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png)

## Pré-requisitos

A conclusão deste tutorial requer as [Ferramentas para desenvolvedores do Android][Android Studio], que incluem o ambiente de desenvolvimento integrado do Android Studio e a plataforma Android mais recente. Android 4.2 ou uma versão posterior é necessária.

O projeto de início rápido baixado contém o SDK de Serviços Móveis do Azure para Android.

> [AZURE.IMPORTANT]Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28).


## Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Criar novo aplicativo Android

Depois de criar seu serviço móvel, você poderá seguir um início rápido fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel.

Nesta seção você criará um novo aplicativo Android que está conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2. Na guia de início rápido, clique em **Android** em **Escolher a plataforma** e expanda **Criar um novo aplicativo Android**.

   	![](./media/mobile-services-android-get-started/mobile-portal-quickstart-android1.png)

   	Isso exibe as três etapas fáceis para criar um aplicativo Android conectado ao seu serviço móvel.

  	![](./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png)

3. Se ainda não o fez, baixe e instale [Android Developer Tools][Android SDK] em seu computador local ou máquina virtual.

4. Clique em **Criar tabela TodoItem** para criar uma tabela para armazenar os dados do aplicativo.


5. Baixe agora o seu aplicativo:
	- A versão mais recente do aplicativo usa o SDK de Serviços Móveis para Android 2.0. Você pode baixar essa versão <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures">aqui</a>. Clique em **Baixar Zip**, descompacte-o e o projeto estará em GettingStarted, na pasta Android.

	- Uma versão mais antiga usa a versão anterior do SDK. Para usá-la, em **Baixe e execute seu aplicativo**, clique em **Baixar**. Isso baixa o projeto para o aplicativo de exemplo _To do list_ que está conectado ao seu serviço móvel. Os arquivos de projeto estão compactados, então navegue até sua localização e descompacte os arquivos em seu computador.


## Executar seu aplicativo Android

[AZURE.INCLUDE [mobile-services-run-your-app](../../includes/mobile-services-android-get-started.md)]

### Examinar o código (opcional)

Se você gostaria de ver o código-fonte do aplicativo concluído, clique [aqui](https://github.com/RickSaling/mobile-services-samples/tree/androidStudio/GettingStarted/AndroidStudio).


Para ver a versão Eclipse deste tutorial, vá para : [Introdução (Eclipse)](mobile-services-android-get-started-EC.md).

## <a name="next-steps"> </a>Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis:

* [Introdução aos dados] <br/> Saiba mais sobre armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à autenticação] <br/>Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.

* [Introdução às notificações por push] <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.




<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png
[8]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png
[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png
[14]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Get started (Eclipse)]: mobile-services-android-get-started-EC.md
[Introdução aos dados]: mobile-services-android-get-started-data.md
[Introdução à autenticação]: mobile-services-android-get-started-users.md
[Introdução às notificações por push]: mobile-services-javascript-backend-android-get-started-push.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=August15_HO6-->