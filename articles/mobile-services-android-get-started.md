<properties pageTitle="Introdução aos Serviços Móveis do Azure para aplicativos Android" metaKeywords="Aplicativo android do Azure, android de serviço móvel, introdução ao Azure android, azure droid, introdução ao windows droid" description="Siga este tutorial para começar a usar o os Serviços Móveis do Azure para desenvolvimento Android." metaCanonical="" services="" documentationCenter="Mobile" title="Introdução aos Serviços Móveis" authors=""  solutions="" writer="glenga" manager="" editor=""  />

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding">
	<a href="/pt-br/documentation/articles/mobile-services-windows-store-get-started" title="Windows Store">Windows Store</a>
	<a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/pt-br/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a>
	<a href="/pt-br/documentation/articles/mobile-services-android-get-started" title="Android" class="current">Android</a>
	<a href="/pt-br/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a>
	<a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
	<a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
	<a href="/pt-br/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
	<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started/" title="Back-end do .NET">Back-end do .NET</a> | 
	<a href="/pt-br/documentation/articles/mobile-services-android-get-started/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo do Android usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples <em>To do list</em> que armazena dados do aplicativo no novo serviço móvel.</p>
<p>Uma captura de tela do aplicativo completo está disponível abaixo:</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o Vídeo</span></a><span class="time">7:26</span></div>
</div>

![][0]

Concluir este tutorial exige as [Ferramentas para desenvolvedores do Android][Android SDK], que inclui o ambiente de desenvolvimento integrado (IDE) Eclipse, o plug-in Android Developer Tools (ADT) e a mais recente plataforma Android. Android 4.2 ou uma versão posterior é necessária. 

O projeto quickstart baixado contém o SDK dos Serviços Móveis para Android. Enquanto este projeto requer Android 4.2 ou uma versão posterior, o SDK dos Serviços Móveis exige apenas Android 2.2 ou uma versão posterior.

<div class="dev-callout"><strong>Observação</strong> <p>para concluir este tutorial, você precisará de uma conta do Azure. Se você não tiver uma conta, será possível criar uma conta de avaliação gratuita em questão de minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Avaliação gratuita do Azure</a>.</p></div>

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2><span class="short-header">Criar um novo aplicativo</span>Criar um novo aplicativo Android</h2>

Depois de criar seu serviço móvel, você poderá seguir as etapas de início rápido no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conectar-se a seu serviço móvel. 

Nesta seção você criará um novo aplicativo Android que está conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2. Na guia de início rápido, clique em **Android** em **Escolher a plataforma** e expanda **Criar um novo aplicativo Android**.

   	![][6]

   	Isso exibe as três etapas fáceis para criar um aplicativo Android conectado ao seu serviço móvel.

  	![][7]

3. Se ainda não o fez, baixe e instale [Android Developer Tools][Android SDK] em seu computador local ou máquina virtual.

4. Clique em **Criar tabela TodoItem** para criar uma tabela para armazenar os dados do aplicativo.

5. Em **Baixe e execute o aplicativo**, clique em **Baixar**. 

  	Isso baixa o projeto para o aplicativo de exemplo _To do list_ que está conectado ao seu serviço móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu aplicativo Android

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1. Navegue até o local onde você salvou os arquivos do projeto compactados e expanda os arquivos em seu computador.

2. No Eclipse, clique em **Arquivo**, em **Importar**, expanda **Android**, clique em **Código Android existente no espaço de trabalho** e clique em **Avançar.** 

 	![][14]

3. Clique em **Procurar**, navegue até o local dos arquivos de projeto expandidos, clique em **OK**, certifique-se de que o projeto TodoActivity está marcado e clique em **Concluir**. 

 	![][15]

	Isso importa os arquivos de projeto no espaço de trabalho atual.

   	![][8]

4. No menu **Executar**, clique em **Executar** para iniciar o projeto no emulador Android.

	<div class="dev-callout"><strong>Nota</strong> <p>Para poder executar o projeto no emulador Android, você deve definir no mínimo um Dispositivo Virtual Android (AVD). Use o Gerenciador AVD para criar e gerenciar esses dispositivos.</p></div>

5. No aplicativo, digite um texto significativo, como _Complete the tutorial_ e clique em **Adicionar**.

   	![][10]

   	Isso envia uma solicitação POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.

	<div class="dev-callout"><strong>Observação</strong> 
   	<p>Você pode examinar o código que acessa o serviço móvel para consultar e inserir dados, que estão localizados no arquivo ToDoActivity.java.</p>
 	</div>

6. No Portal de Gerenciamento, clique na guia **Dados** e, em seguida na tabela **TodoItems**.

   	![][11]

   	Isso permite que você procure os dados inseridos pelo aplicativo na tabela.

   	![][12]

## <a name="next-steps"> </a>Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis: 

* [Começar com os dados]
  <br/>Saiba mais sobre armazenamento e consulta de dados usando os Serviços Móveis.

* [Introdução à autenticação]
  <br/>Aprenda a autenticar os usuários de seu aplicativo com um provedor de identidade.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

<!-- Anchors. -->
[Introdução aos Serviços Móveis]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Definir a instância de serviço móvel]:#define-mobile-service-instance
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png





[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Começar com os dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-android
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-android
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-android
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[SDK Android de Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portal de Gerenciamento]: https://manage.windowsazure.com/

