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
	ms.topic="hero-article" 
	ms.date="02/13/2015" 
	ms.author="ricksal;glenga"/>

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started-EC.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Android usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples de <em>Lista de tarefas Pendentes</em> que armazena dados do aplicativo no novo serviço móvel.</p>
<p>Uma captura de tela do aplicativo completo está disponível abaixo:</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Executar o vídeo</span></a><span class="time">07:26</span></div>
</div>

![][0]

A conclusão deste tutorial requer as [Ferramentas para desenvolvedores do Android][SDK para Android], que inclui o IDE (Ambiente de Desenvolvimento Eclipse)integrado, o plug-in do ADT (Android Developer Tools) e a mais recente plataforma Android. Android 4.2 ou uma versão posterior é necessária. 

O projeto de início rápido baixado contém o SDK de Serviços Móveis para Android. Enquanto este projeto requer Android 4.2 ou uma versão posterior, o SDK dos Serviços Móveis exige apenas Android 2.2 ou uma versão posterior.

> [AZURE.IMPORTANTE] Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28"%20target="_blank).

<!-- -->

>[AZURE.NOTE] Se você gostaria de ver o código-fonte do aplicativo concluído, clique <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStarted/Android" target="_blank">aqui</a>.

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2>Criar novo aplicativo Android</h2>

Depois de criar seu serviço móvel, você poderá seguir um guia de início rápido fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel. 

Nesta seção você criará um novo aplicativo Android que está conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2. Na guia de início rápido, clique em **Android** em **Escolher a plataforma** e expanda **Criar um novo aplicativo Android**.

   	![][6]

   	Isso exibe as três etapas fáceis para criar um aplicativo Android conectado ao seu serviço móvel.

  	![][7]

3. Se ainda não fez isso, baixe e instale o [Android Developer Tools][Android SDK] em seu computador local ou máquina virtual.

4. Clique em **Criar tabela TodoItem** para criar uma tabela para armazenar os dados do aplicativo.


5. Baixe agora o seu aplicativo:
	- A versão mais recente do aplicativo usa o SDK de Serviços Móveis para Android 2.0. Você pode baixar essa versão <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures">aqui</a>. Clique em **Baixar Zip**, descompacte-o e o projeto está em GettingStarted na pasta Android.
	 
	- Uma versão mais antiga usa a versão anterior do SDK. Para usá-la, em **Baixar e executar o aplicativo**, clique em **Baixar**. Isso baixa o projeto para o aplicativo de exemplo _To do list_ que está conectado ao seu serviço móvel. Os arquivos de projeto estão compactados, então navegue até sua localização e descompacte os arquivos em seu computador.


## Executar seu aplicativo Android

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

2. No Eclipse, clique em **Arquivo**, em **Importar**, expanda **Android**, clique em **Código Android Existente no Espaço de Trabalho** e clique em **Avançar**. 

 	![][14]

3. Clique em **Procurar**, navegue até o local dos arquivos do projeto expandidos, clique em **OK**, verifique se o projeto TodoActivity está marcado e clique em **Concluir**. 

 	![][15]

	Isso importa os arquivos de projeto no espaço de trabalho atual.

   	![][8]

4. Se você baixou a versão 2.0 do SDK, você precisa atualizar o código com a Url e a chave do seu serviço móvel:
	- 	Encontre o método **OnCreate** em **TodoActivity.java** e localize o código que instancia o cliente de serviços móveis. O código está visível na imagem anterior.
	- 	Substitua "MobileServiceUrl" pela verdadeira Url do serviço móvel.
	- 	Substitua "AppKey" pela chave do serviço móvel.
	- 	Para obter mais detalhes, consulte o tutorial <a href="http://azure.microsoft.com/documentation/articles/mobile-services-android-get-started-data/">Adicionar Serviços Móveis a um aplicativo existente</a>. 



4. No menu **Executar**, clique em **Executar** para iniciar o projeto no emulador do Android.

	> [AZURE.IMPORTANT] Para que seja possível executar o projeto no emulador do Android, você deve definir no mínimo um AVD (Dispositivo Virtual Android). Use o Gerenciador do AVD para criar e gerenciar esses dispositivos.

5. No aplicativo, digite um texto significativo, como _Complete the tutorial_ e clique em **Adicionar**.

   	![][10]

   	Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.

	> [AZURE.NOTE] Você pode examinar o código que acessa o serviço móvel para consultar e inserir dados, que estão localizados no arquivo ToDoActivity.java.

6. No Portal de Gerenciamento, clique na guia **Dados** e, em seguida na tabela **TodoItems**.

   	![][11]

   	Isso permite que você procure os dados inseridos pelo aplicativo na tabela.

   	![][12]

## <a name="next-steps"> </a>Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis: 

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à autenticação]
  <br/>Aprenda a autenticar usuários do aplicativo com um provedor de identidade.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

<!-- Anchors. -->
[Introdução aos Serviços Móveis]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Definir a instância do serviço móvel]:#define-mobile-service-instance
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png





[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android-EC.png
[8]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Introdução aos dados]: mobile-services-android-get-started-data.md
[Introdução à autenticação]: mobile-services-android-get-started-users.md
[Introdução às notificações por push]: mobile-services-javascript-backend-android-get-started-push.md
[SDK para Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[SDK para Android de Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portal de Gerenciamento]: https://manage.windowsazure.com/


<!--HONumber=52-->