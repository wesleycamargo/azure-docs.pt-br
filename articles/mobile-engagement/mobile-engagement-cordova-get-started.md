<properties
	pageTitle="Introdução ao Azure Mobile Engagement para Cordova/Phonegap"
	description="Aprenda a usar o Mobile Engagement do Azure com Análises e Notificações por Push para Aplicativos Cordova/Phonegap."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-phonegap"
	ms.devlang="js"
	ms.topic="get-started-article" 
	ms.date="07/02/2015"
	ms.author="piyushjo" />

# Introdução ao Azure Mobile Engagement para Cordova/Phonegap

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Este tópico mostra como usar o Mobile Engagement do Azure para entender o uso do aplicativo e enviar notificações por push para usuários segmentados para um aplicativo móvel desenvolvido com Cordova.

Neste tutorial, criamos um aplicativo do Cordova em branco usando o Mac e integramos o SDK do Mobile Engagement. Ele coleta dados de análise básica e recebe notificações por push usando o sistema de notificações de Push da Apple (APNS) para iOS e Google Cloud Messaging (GCM) para o Android. Nós implantaremos em um dispositivo iOS ou Android para teste.

> [AZURE.IMPORTANT]Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação Gratuita do Azure</a>.

Este tutorial exige o seguinte:

+ Xcode, que pode ser instalado em seu Mac App Store (para implantação no iOS)
+ [SDK do Android & Emulador](http://developer.android.com/sdk/installing/index.html) (para implantação em Android)
+ Certificado de notificação por push (.p12) que pode ser obtido no seu centro de desenvolvimento da Apple para APNS
+ Número do projeto do GCM que pode ser obtido seu Console de Desenvolvedor do Google para GCM
+ [Plug-in do Mobile Engagement Cordova](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE]Você pode encontrar o código-fonte e o arquivo Leiame para o plug-in Cordova em [Github](https://github.com/Azure/azure-mobile-engagement-cordova)

##<a id="setup-azme"></a>Configuração do Mobile Engagement para seu aplicativo

1. Faça logon no Portal de Gerenciamento do Azure e clique em **+NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativo**, **Mobile Engagement**, então, em **Criar**.

   	![][1]

3. No pop-up exibido, insira as seguintes informações:

   	![][2]

	- **Nome do Aplicativo**: digite o nome do seu aplicativo. 
	- **Plataforma**: a plataforma de destino para o aplicativo (escolha **iOS** ou **Android** dependendo de onde você está implantando o App Cordova)
	- **Nome do Recurso do Aplicativo**: nome pelo qual o aplicativo poderá ser acessado via APIs e URLs. 
	- **Local**: data center em que esse aplicativo e a coleção de aplicativos serão hospedados.
	- **Coleção**: selecione uma coleção criada anteriormente ou selecione “Nova coleção”.
	- **Nome da Coleção**: representa seu grupo de aplicativos. Isso garantirá que todos os seus aplicativos estejam em um grupo que permitirá cálculos agregados das métricas. Você deve usar o nome da empresa ou departamento aqui, se aplicável.

4. Selecione o aplicativo que você acabou de criar na guia **Aplicativos**.

5. Clique em **Informações da Conexão** para exibir as configurações da conexão para colocar em sua integração SDK no aplicativo móvel.

   	![][3]

6. Cópia da **Cadeia de Conexão** - isso é o que você precisará para identificar esse aplicativo no Código do aplicativo e conectar o Mobile Engagement a partir de seu Aplicativo para Telefone.

   	![][4]

##<a id="connecting-app"></a>Conectando seu aplicativo ao back-end do Mobile Engagement

Este tutorial apresenta uma "integração básica" que é o conjunto mínimo exigido para coletar dados e enviar uma notificação por push.

Criaremos um aplicativo básico com Cordova para demonstrar a integração:

###Crie um novo projeto com Cordova

1. Inicie a janela *Terminal* no seu computador Mac e digite o seguinte que criará um novo projeto Cordova a partir do modelo padrão:

		$ cordova create azme-cordova com.mycompany.myapp
		$ cd azme-cordova

> [AZURE.IMPORTANT]Certifique-se de que o perfil de publicação que você usará para implantar seu aplicativo iOS esteja usando “com.mycompany.myapp” como o ID do aplicativo

2. Execute o seguinte para configurar seu projeto para **iOS** e executá-lo no simulador do iOS:

		$ cordova platform add ios 
		$ cordova run ios

3. Execute o seguinte para configurar seu projeto para **Android** e executá-lo no simulador do Android:

		$ cordova platform add android
		$ cordova run android

4. 	Adicione o plug-in do Console do Cordova.

		$ cordova plugin add cordova-plugin-console 

###Conecte seu aplicativo ao back-end do Mobile Engagement

1. Instale o plug-in do Mobile Engagement Cordova do Azure e forneça os valores de variáveis para configurar o plug-in:

		cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
			--variable AZME_IOS_COLLECTION=<iOSAppCollectionName>.device.mobileengagement.windows.net
	        --variable AZME_IOS_SDKKEY=... 
	        --variable AZME_IOS_APPID=... 
	        --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
	        --variable AZME_ANDROID_COLLECTION=<AndroidAppCollectionName>.device.mobileengagement.windows.net
	        --variable AZME_ANDROID_SDKKEY=...
	        --variable AZME_ANDROID_APPID=...
			--variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
	        --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
	        --variable AZME_REDIRECT_URL=... (URL scheme which triggers the app for deep linking)
	        --variable AZME_ENABLE_LOG=true|false

	Por exemplo:

		cordova plugin add cordova-plugin-ms-azure-mobile-engagement   
			--variable AZME_IOS_COLLECTION=apps-Collection.device.mobileengagement.windows.net
	        --variable AZME_IOS_SDKKEY=26dxxxxxxxxxxxxb794 
	        --variable AZME_IOS_APPID=piyxxxxxx
	        --variable AZME_IOS_REACH_ICON=icon.png 
			--variable AZME_ANDROID_COLLECTION=apps-Collection.device.mobileengagement.windows.net
	        --variable AZME_ANDROID_SDKKEY=c3d296xxxxxxxxxxc6540
	        --variable AZME_ANDROID_APPID=piyxxxxxxx
			--variable AZME_ANDROID_REACH_ICON=icon   
	        --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=393xxxxxxx718
			--variable AZME_REDIRECT_URL=myapp 
			--variable AZME_ENABLE_LOG=true

> [AZURE.TIP]A AppId, SDKKey e a coleção podem ser recuperadas da cadeia de conexão **Endpoint={YourAppCollection.Domain};SdkKey={YourSDKKey};AppId={YourAppId}**

##<a id="monitor"></a>Habilitar monitoramento em tempo real

1. No projeto Cordova - editar **www/js/index.js** para adicionar a chamada para o Mobile Engagement para declarar uma nova atividade uma vez que o evento *deviceReady* é recebido.

		 onDeviceReady: function() {
		        app.receivedEvent('deviceready');
		        AzureEngagement.startActivity("myPage",{});
		    }

2. Executar o aplicativo:
		
	- **Para iOS**
	
		Na janela `Terminal`, inicie seu aplicativo em uma nova instância do simulador, executando o seguinte:

			cordova run ios

	- **Para Android**
		
		Na janela `Terminal`, inicie seu aplicativo em uma nova instância do emulador, executando o seguinte:

			cordova run android

3. Você pode ver o seguinte nos logs do console:

		[Engagement] Agent: Session started
		[Engagement] Agent: Activity 'myPage' started
		[Engagement] Connection: Established
		[Engagement] Connection: Sent: appInfo
		[Engagement] Connection: Sent: startSession
		[Engagement] Connection: Sent: activity name='myPage'

###Verifique se seu aplicativo está conectado com o monitoramento em tempo real

Esta seção mostra como assegurar que seu aplicativo conecta o back-end do Mobile Engagement usando o recurso de monitoramento em tempo real do Mobile Engagement.

1. Navegue até o portal do Mobile Engagement

	No portal do Azure, verifique se você está no aplicativo que estamos usando para este projeto e, em seguida, clique no botão **Acionar** na parte inferior:

	![][6]

2. Você será levado para a página de configurações em seu **Portal do Engagement** para o seu aplicativo. Lá, clique na guia **Monitorar**:

	![][7]

3. Se seu aplicativo em execução no emulador estiver configurado corretamente, você verá uma sessão conectada em tempo real enquanto seu aplicativo estiver sendo executado:

	![][8]

##<a id="integrate-push"></a>Habilitar Notificações por Push e mensagens no aplicativo

O Mobile Engagement permite a você interagir com seus usuários com Notificações por Push e Mensagens no Aplicativo no contexto de campanhas. Esse módulo é chamado REACH no portal do Mobile Engagement. As seções a seguir irão configurar o aplicativo para recebê-los.

###Configure credenciais de Push para o Mobile Engagement

Para permitir que o Mobile Engagement envie notificações por Push em seu nome, você precisa conceder acesso ao certificado Apple iOS ou à chave da API do servidor de GCM.
	
1. Navegue até o portal do Mobile Engagement. Verifique se você está no aplicativo que estamos usando para este projeto e, em seguida, clique no botão **Engajar** na parte inferior:
	
	![][10]
	
2. Você será levado para a página de configurações em seu Portal do Engagement. A partir daí, clique na seção **Push Nativo**:
	
	![][11]

3. Configure o certificado do iOS/a chave da API do servidor de GCM

	**[iOS]**

	a. Selecione seu .p12, carregue-o e digite sua senha:
	
	![][12]

	**[Android]**

	a. Clique no ícone de edição na frente de **chave API**, na seção Configurações do GCM, conforme mostrado abaixo:
		
	![][20]
	
	b. Na janela pop-up, cole a chave de servidor GCM e clique em **Ok**.
	
	![][21]

###Habilitar notificações por push no aplicativo Cordova

Editar **www/js/index.js** para adicionar a chamada para o Mobile Engagement para solicitar notificações por push e declarar um manipulador:

	 onDeviceReady: function() {
	        app.receivedEvent('deviceready');
	        AzureEngagement.registerForPushNotification();
	        AzureEngagement.onOpenURL(function(_url) { alert(_url); });
	        AzureEngagement.startActivity("myPage",{});
	    }

###Execute o aplicativo

**[iOS]**

1. Nós usaremos o XCode para criar e implantar o aplicativo no dispositivo para testar as notificações por push já que o iOS permite apenas notificações por push para um dispositivo real. Vá para o local onde o projeto Cordova é criado e navegue até **...\\platforms\\ios** location. Abra o arquivo .xcodeproj nativo no XCode. 
	
2. Crie e implante o aplicativo Cordova para o dispositivo iOS usando a conta que tem o perfil de provisionamento que contém o certificado que você acabou de carregar para o portal do Mobile Engagement e a Id do aplicativo que corresponde à que você forneceu ao criar o aplicativo Cordova. Você pode consultar o *Identificador de pacote* em seu arquivo **Resources*-info.plist** no XCode para fazer a correspondência.

3. Você verá o popup iOS padrão em seu dispositivo dizendo que o aplicativo solicita de permissão para enviar notificações. Conceder permissão.

**[Android]**

Você pode simplesmente usar o emulador para executar o aplicativo Android, já que as notificações GCM têm suporte no emulador do Android.

	cordova run android

##<a id="send"></a>Envie uma notificação para seu aplicativo

Agora, vamos criar uma campanha de notificação por push simples que enviará um push para seu aplicativo executando o dispositivo:

1. Navegue até a guia Reach em seu portal do Mobile Engagement

2. Clique em **Novo anúncio** para criar sua campanha de push.

	![][13]

3. Forneça entradas para criar sua campanha:

	![][14]

	- 	Forneça um nome para sua campanha. 
	- 	**[Android]** Selecione o **tipo de entrega** como *Sistema de notificação* - *Simples*
	- 	Selecione o Tipo de entrega como: 
		- 	Para **iOS** : *"Somente fora do aplicativo"*
		- 	Para **Android** : *"Sempre"*
		
		Esse é o tipo de notificação de push simples que apresenta algum texto.
	- 	No texto de notificação, primeiro digite o título que será a primeira linha no push
	- 	Em seguida, digite a mensagem que será a segunda linha

4. Role para baixo e, na seção de conteúdo, selecione **Notificação apenas**

	![][15]

5. [Opcional] Você também pode fornecer uma URL de ação. Certifique-se de que ele usa um esquema de URL fornecido ao configurar o plug-in **URL de REDIRECIONAMENTO de AZME** variável de exemplo, *myapp://test*. 

5. Você concluiu a configuração mais básica da campanha possível, agora role para baixo novamente e **crie** sua campanha para salvá-la!
	
	![][16]

6. Última etapa, **ativar** a sua campanha
	
	![][17]

7. Agora você deve ver uma notificação por push em seu dispositivo ou emulador como parte desta campanha.

##<a id="next-steps"></a>Próximas etapas
[Visão geral de todos os métodos disponíveis no SDK do Cordova Mobile Engagement](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553

<!-- Images. -->
[1]: ./media/mobile-engagement-cordova-get-started/create-mobile-engagement-app.png
[2]: ./media/mobile-engagement-cordova-get-started/create-azme-popup.png
[3]: ./media/mobile-engagement-cordova-get-started/app-main-page-select-connection-info.png
[4]: ./media/mobile-engagement-cordova-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[7]: ./media/mobile-engagement-cordova-get-started/clic-monitor-tab.png
[8]: ./media/mobile-engagement-cordova-get-started/monitor.png
[10]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[11]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[12]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[13]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[14]: ./media/mobile-engagement-cordova-get-started/campaign-first-params.png
[15]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[16]: ./media/mobile-engagement-cordova-get-started/campaign-create.png
[17]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[18]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[19]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[20]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[21]: ./media/mobile-engagement-cordova-get-started/api-key.png

<!---HONumber=July15_HO2-->