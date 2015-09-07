<properties
	pageTitle="Introdução ao Mobile Engagement do Azure para iOS em Objective C"
	description="Aprenda a usar o Mobile Engagement do Azure com análises e notificações por push para aplicativos iOS."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="MehrdadMzfr"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="08/05/2015"
	ms.author="MehrdadMzfr"/>

# Introdução ao Mobile Engagement do Azure para aplicativos iOS em Objective C

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Este tópico mostra como usar o Azure Mobile Engagement para entender o uso do aplicativo e enviar notificações por push para usuários segmentados para um aplicativo iOS. Neste tutorial, você cria um aplicativo iOS em branco que coleta dados básicos e recebe notificações por push usando o Sistema de Notificação por Push da Apple (APNS). Ao concluir este tutorial, você poderá transmitir notificações por push para todos os dispositivos ou usuários específicos do destino com base nas propriedades dos dispositivos deles.

Esse tutorial demonstra um cenário de transmissão simples usando o Mobile Engagement. Siga o tutorial a seguir para aprender a usar o Mobile Engagement para endereçar usuários e grupos de dispositivos específicos.

Este tutorial exige o seguinte:

+ XCode, que pode ser instalado a partir da MAC App Store
+ o [SDK do Mobile Engagement iOS]
+ Certificado de notificação por push (. p12), que pode ser obtido no centro de desenvolvimento da Apple

A conclusão desse tutorial é um pré-requisito para todos os outros tutoriais do Mobile Engagement para os aplicativos iOS.

> [AZURE.IMPORTANT]A conclusão desse tutorial é um pré-requisito para todos os outros tutoriais do Mobile Engagement para os aplicativos iOS e para concluí-lo, você deve ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-BR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação Gratuita do Azure</a>.

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>Configurar o Mobile Engagement para seu aplicativo

1. Entre no Portal do Azure e clique em **+NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativos**, clique em **Mobile Engagement** e, em seguida, clique em **Criar**.

	![][7]

3. No pop-up exibido, insira as seguintes informações:

   ![][8]

   - **Nome do Aplicativo**: digite o nome do seu aplicativo. Fique à vontade para usar qualquer caractere.
   - **Plataforma**: selecione a plataforma de destino (**iOS**) para o aplicativo (se seu aplicativo for destinado para várias plataformas, repita esse tutorial para cada uma).
   - **Nome do Recurso do Aplicativo**: é o nome pelo qual o aplicativo poderá ser acessado via APIs e URLs. Você só deve usar caracteres URL convencionais. O nome gerado automaticamente deve fornecer uma base sólida. Você também deve acrescentar o nome da plataforma para evitar qualquer conflito de nomes, pois o nome deve ser exclusivo.
   - **Local**: selecione o datacenter no qual o aplicativo (e, mais importante, sua Coleção) será hospedado.
   - **Coleção**: se você já tiver criado um aplicativo, selecione uma **Coleção** criada anteriormente; caso contrário, selecione **Nova Coleção**.
   - **Nome da Coleção**: isso representa seu grupo de aplicativos. Também garante que todos os seus aplicativos estejam em um grupo que permite o cálculo agregado de métricas. Você deve usar o nome da empresa ou departamento aqui, se aplicável.

4. Selecione o aplicativo que você acabou de criar na guia **Aplicativos**.

5. Clique em **Informações da Conexão** para exibir as configurações da conexão para colocar em sua integração SDK no aplicativo móvel.

	![][10]

6. Copiar a **Cadeia de Conexão** – isso é o que você precisará para identificar esse aplicativo no código do Aplicativo e conectar-se ao Mobile Engagement a partir do aplicativo do telefone.

	![][11]

##<a id="connecting-app"></a>Conecte o seu aplicativo ao back-end do Mobile Engagement

Este tutorial apresenta uma "integração básica" que é o conjunto mínimo exigido para coletar dados e enviar uma notificação por push. A documentação de integração completa pode ser encontrada na documentação do SDK do Mobile Engagement iOS.

Criaremos um aplicativo básico com XCode para demonstrar a integração.

###Crie um novo projeto iOS

Você pode ignorar esta etapa se já tiver um aplicativo e estiver familiarizado com o desenvolvimento do iOS

1. Inicie o Xcode e no pop-up, selecione **Criar um novo projeto do Xcode**.

	![][12]

2. Selecione **Aplicativo de Modo de Exibição Único** e clique em **Próximo**.

	![][14]

3. Preencha o **Nome do Produto**, **Nome da Organização** e **Identificador da Organização**. Verifique se você selecionou **Objective-C** na caixa **Idioma**.

	![][13]

O Xcode criará o aplicativo de demonstração ao qual integraremos o Mobile Engagement.

###Conectar o aplicativo ao back-end do Mobile Engagement

1. Baixe o [SDK do Mobile Engagement iOS].
2. Extraia o arquivo .tar.gz para uma pasta no seu computador.
3. Clique com botão direito no projeto e, em seguida, selecione **Adicione arquivos a**.

	![][17]

4. Navegue até a pasta onde você extraiu o SDK e selecione a pasta `EngagementSDK`, depois pressione **OK**.

	![][18]

5. Abra a guia **Criar Fases** e, no menu **Link Binário com as Bibliotecas**, adicione as estruturas, como mostrado abaixo:

	![][19]

6. Volte para o portal do Azure na página **Informações de Conexão** de seu aplicativo e copie a cadeia de conexão.

	![][11]

7. Abra o seu arquivo de implementação Application Delegate e adicione a linha de código a seguir.

		#import "EngagementAgent.h"

8. Agora, cole a cadeia de conexão no `didFinishLaunchingWithOptions` delegado.

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

##<a id="monitor"></a>Habilitar monitoramento em tempo real

Para iniciar o envio de dados e assegurar que os usuários estejam ativos, você deve enviar pelo menos uma tela (Atividade) para o back-end do Mobile Engagement.

- Abra o arquivo **ViewController.h**, importe **EngagementViewController.h** e substitua a superclasse da interface **ViewController** por **EngagementViewController**, conforme mostrado abaixo:

![][22]

###Verificar se seu aplicativo está conectado com o monitoramento em tempo real

Esta seção mostra como assegurar que seu aplicativo se conecte ao back-end do Mobile Engagement usando o recurso de monitoramento em tempo real do Mobile Engagement.

1. Navegue até o portal do Mobile Engagement

	No portal do Azure, verifique se você está no aplicativo que estamos usando para este projeto e, em seguida, clique no botão **Acionar** na parte inferior.

	![][26]

2. Você será levado para a página **Configurações** no Portal do Engagement de seu aplicativo. Nela, clique na guia **Monitorar**:

	![][30]

3. O monitor está pronto para mostrar qualquer dispositivo, em tempo real, que iniciará o aplicativo:

	![][31]

4. No Xcode, inicie o aplicativo no simulador ou em um dispositivo conectado.

5. Se ele funcionou, agora você deverá ver uma sessão no monitor!

**Parabéns!** Você teve êxito na primeira etapa deste tutorial e tem um aplicativo que se conecta ao back-end do Mobile Engagement e que já está enviando dados.

6. Clique no botão **Página inicial** do simulador para retornar o número de sessões no monitor para 0, conforme mostrado acima.

	![][33]

##<a id="integrate-push"></a>Habilitar notificações por push e mensagens no aplicativo

O Mobile Engagement permite interagir com seus usuários e o REACH com notificações por push e mensagens no aplicativo no contexto das campanhas. Esse módulo é chamado de REACH no portal do Mobile Engagement. As seções a seguir configuram seu aplicativo para recebê-las.

### Habilitar seu aplicativo para receber Notificações por push silenciosas

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]


### Adicionar a biblioteca Reach ao seu projeto

1. Clique com o botão direito em seu projeto.
2. Selecione **Adicionar arquivo para**.
3. Navegue até a pasta onde você extraiu o SDK.
4. Selecione a pasta `EngagementReach`.
5. Clique em **Adicionar**.

### Modifique seu Representante do Aplicativo

1. Na parte superior do seu arquivo de implementação, importe o módulo do Engagement Reach.

		#import "AEReachModule.h"

2. Dentro do método **application:didFinishLaunchingWithOptions**, crie um módulo Reach e passe-o para sua linha de inicialização do Engagement existente:

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			[...]
			return YES;
		}

###Habilite seu aplicativo para receber Notificações por Push do APNS

1. Adicione a seguinte linha para o método **application: didFinishLaunchingWithOptions**:

		if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			[application registerForRemoteNotifications];
		}
		else {

			[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

2. Adicione o método **application:didRegisterForRemoteNotificationsWithDeviceToken** da seguinte maneira:

		- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
		{
 			[[EngagementAgent shared] registerDeviceToken:deviceToken];
		}

3. Adicione o método **didReceiveRemoteNotification:fetchCompletionHandler** da seguinte maneira:

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
		{
			[[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
		}

###Conceda acesso ao Mobile Engagement para o seu certificado de push

Para permitir que o Mobile Engagement envie notificações por push em seu nome, você precisa conceder acesso ao certificado. Isso é feito configurando e inserindo seu certificado no portal do Mobile Engagement. Verifique se você obtém seu certificado .p12, conforme explicado na documentação da Apple.

1. Navegue até o portal do Mobile Engagement. Verifique se você está no aplicativo que estamos usando para este projeto e, em seguida, clique no botão **Acionar** na parte inferior:

	![][26]

2. Você será levado para a página **Configurações** no portal do Engagement. Nela, clique na seção **Push Nativo** para carregar seu certificado p12:

	![][27]

3. Selecione seu p12, carregue-o e, em seguida, digite sua senha:

	![][28]

4. Agora, adicione o perfil de provisionamento e crie seu aplicativo para um dispositivo de destino.

Você está pronto. Agora vamos verificar se você realizou corretamente essa integração básica.

##<a id="send"></a>Envie uma notificação para seu aplicativo

Agora, criaremos uma campanha de notificação por push simples que enviará um push para nosso aplicativo:

1. Navegue até a guia **Reach** no seu portal do Mobile Engagement.

2. Clique em **Novo Comunicado** para criar sua campanha de notificação por push.

	![][35]

3. Configure o primeiro campo de sua campanha:

	![][36]

	- 	Atribua qualquer nome desejado à sua campanha.
	- 	Selecione a **Hora de entrega** como **Somente fora do aplicativo**: esse é o tipo de notificação por push simples da Apple que apresenta algum texto.
	- 	No texto da Notificação, primeiro digite o título, que será a primeira linha no push.
	- 	Em seguida, digite a mensagem, que será a segunda linha.


4. Role para baixo e, na seção **Conteúdo**, selecione "Somente Notificação".

	![][37]

5. Você concluiu a configuração da campanha mais básica possível. Agora role para baixo novamente e, em seguida, crie sua campanha para salvá-la. ![][38]

6. Última etapa: Ativar sua campanha. ![][39]

7. Você verá uma notificação por push em seu dispositivo!

<!-- URLs. -->
[SDK do Mobile Engagement iOS]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-ios-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[26]: ./media/mobile-engagement-ios-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-get-started/native-push-settings.png
[30]: ./media/mobile-engagement-ios-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-get-started/monitor.png
[33]: ./media/mobile-engagement-ios-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-get-started/campaign-activate.png

<!---HONumber=August15_HO9-->