<properties pageTitle="Introdução aos Serviços Móveis do Azure para aplicativos Android" metaKeywords="Aplicativo android do Azure, android de serviço móvel, introdução ao Azure android, azure droid, introdução ao droid windows" description="Siga este tutorial para começar a usar os Serviços Móveis do Azure para desenvolvimento Android." metaCanonical="" services="" documentationCenter="Mobile" title="Introdução aos Serviços Móveis" authors="glenga" solutions="" manager="" editor="" />

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding">
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started" title="C# da Windows Store">C# da Windows Store</a>
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started" title="JavaScript da Windows Store">JavaScript da Windows Store</a>
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started" title="iOS">iOS</a>
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started/" title="Back-end do .NET" class="current">Back-end do .NET</a> | 
	<a href="/pt-br/documentation/articles/mobile-services-android-get-started/"  title="Back-end do JavaScript">Back-end do JavaScript</a>
</div>

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a um aplicativo Android usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples _To do list_ que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa as linguagens do .NET com suporte usando o Visual Studio para a lógica de negócios do lado do servidor e para gerenciar o serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte a [Versão de back-end do JavaScript](/pt-br/documentation/articles/mobile-services-android-get-started/) neste tópico.

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

Concluir este tutorial exige as [Ferramentas para desenvolvedores do Android][Android SDK], que inclui o ambiente de desenvolvimento integrado (IDE) Eclipse, o plug-in Android Developer Tools (ADT) e a mais recente plataforma Android. Android 4.2 ou uma versão posterior é necessária. 

O projeto quickstart baixado contém o SDK dos Serviços Móveis para Android. Enquanto este projeto requer Android 4.2 ou uma versão posterior, o SDK dos Serviços Móveis exige apenas Android 2.2 ou uma versão posterior.

<div class="dev-callout"><strong>Observação</strong> <p>Para concluir este tutorial, você precisará de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Avaliação gratuita do Azure</a>.</p></div>

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Baixar o serviço móvel para o computador local

Agora que você criou o serviço móvel, baixe o projeto de serviço móvel personalizado que pode ser executado no computador local ou na máquina virtual.

1. Clique no serviço móvel que você acabou de criar, em seguida, na guia quickstart, clique em **Android** em **Escolher plataforma** e expanda **Criar um novo aplicativo Android**.

2. Se ainda não tiver feito isso, baixe e instale o [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) ou uma versão posterior.

3. Clique em **Download** em **Baixar e publicar o serviço na nuvem**.

	Isso baixa o projeto do Visual Studio que implementa o serviço móvel. Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

4. Além disso, baixe seu perfil de publicação, salve o arquivo baixado no computador local e anote o local onde o salvou.

## Testar o serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publicar o serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Criar novo aplicativo Android

Nesta seção você criará um novo aplicativo Android que está conectado ao seu serviço móvel.

1. No [Portal de Gerenciamento], clique em **Serviços Móveis** e clique no serviço móvel que você acabou de criar.

2. Na guia de início rápido, clique em **Android** em **Escolher a plataforma** e expanda **Criar um novo aplicativo Android**.   

3. Se ainda não o fez, baixe e instale [Android Developer Tools][Android SDK] em seu computador local ou máquina virtual.

4. Clique em **Criar tabela TodoItem** para criar uma tabela para armazenar os dados do aplicativo.

5. Em **Baixe e execute seu aplicativo**, clique em **Baixar**. 

  	Isso baixa o projeto para o aplicativo de exemplo _To do list_ que está conectado ao seu serviço móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu aplicativo Android

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1. Navegue até o local onde você salvou os arquivos do projeto compactados e expanda os arquivos em seu computador.

2. No Eclipse, clique em **Arquivo**, em **Importar**, expanda **Android**, clique em **Código Android Existente no Espaço de Trabalho** e clique em **Avançar**. 

 	![][14]

3. Clique em **Procurar**, navegue até o local dos arquivos do projeto expandidos, clique em **OK**, verifique se o projeto TodoActivity está marcado e clique em **Concluir**. 

 	![][15]

	Isso importa os arquivos de projeto no espaço de trabalho atual.

   	![][8]

4. No menu **Executar**, clique em **Executar** para iniciar o projeto no emulador do Android.

	<div class="dev-callout"><strong>Nota</strong> <p>Para poder executar o projeto no emulador Android, você deve definir no mínimo um Dispositivo Virtual Android (AVD). Use o Gerenciador AVD para criar e gerenciar esses dispositivos.</p></div>

5. No aplicativo, digite um texto significativo, como _Concluir o tutorial_ e clique em **Adicionar**.

   	![][10]

   	Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.

	<div class="dev-callout"><strong>Observação</strong> 
   	<p>Você pode examinar o código que acessa o serviço móvel para consultar e inserir dados, que estão localizados no arquivo ToDoActivity.java.</p>
 	</div>

<!--This shows how to run your new client app against the mobile service running in Azure. Before you can test the Android app with the mobile service running on a local computer, you must configure the Web server and firewall to allow access from your Android development computer. For more information, see [Configure the local web server to allow connections to a local mobile service](/pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).-->

<!--
## <a name="next-steps"> </a>Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis: 

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.
-->
<!-- Anchors. -->
[Introdução aos Serviços Móveis]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Definir a instância do serviço móvel]:#define-mobile-service-instance
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs.png




[6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
[SDK do Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[SDK Android de Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portal de Gerenciamento]: https://manage.windowsazure.com/

