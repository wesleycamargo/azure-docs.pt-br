<properties 
	pageTitle="Introdução ao Azure Mobile Engagement para iOS" 
	description="Saiba como usar o Azure Mobile Engagement com Notificações por Push e Análises no iOS" 
	services="mobile-engagement" 
	documentationCenter="Mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />

# Introdução ao Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS"  class="current">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

Este tópico mostra como usar o Azure Mobile Engagement para entender o uso do aplicativo e enviar notificações por push para usuários segmentados para um aplicativo iOS. 
Neste tutorial, você cria um aplicativo de iOS em branco que coleta dados básicos e recebe notificações por push usando o Apple Push Notification System (APNS). Ao concluir, você poderá transmitir notificações por push para todos os dispositivos ou usuários específicos de destino com base em suas propriedades de dispositivos.

Este tutorial demonstra o simples cenário de transmissão usando o Mobile Engagement. Certifique-se de seguir o próximo tutorial para aprender a usar o Mobile Engagement para atender a usuários e grupos específicos de dispositivos. 

Este tutorial exige o seguinte:

+ Xcode, que pode ser instalado em seu MAC App Store
+ O [SDK do Mobile Engagement iOS]
+ Certificado de notificação por push (.p12) que pode ser obtido no seu centro de desenvolvimento da Apple

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais do Mobile Engagement para aplicativos para iOS. 

<div class="dev-callout"><strong>Observação</strong> <p>Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação Gratuita do Azure</a>.</p></div>

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>Configure o Mobile Engagement para o seu aplicativo

1. Faça logon no [Portal de Gerenciamento do Azure] e clique em **+NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativo**, depois em **Mobile Engagement** e, em seguida, **Criar**.

   	![][7]

3. No pop-up que aparece, você tem alguns campos para preencher:
 
   	![][8]

	1. *Nome do aplicativo*: você pode digitar o nome do seu aplicativo. Fique à vontade para usar qualquer caractere
	2. *Plataforma*: Selecione a plataforma de destino para aquele aplicativo (se o seu aplicativo for destinado para várias plataformas, repita este tutorial para cada plataforma)
	3. *Nome de recurso do aplicativo*: Esse é o nome pelo qual este aplicativo poderá ser acessado por meio de APIs e URLs. É recomendável que você use apenas caracteres de URL convencionais: o nome gerado automaticamente deve fornecer uma base sólida. Também é recomendável anexar o nome da plataforma para evitar qualquer conflito de nome, pois esse nome deve ser exclusivo
	4. *Local*: Selecione o datacenter em que esse aplicativo (e, o mais importante, a sua coleção - veja abaixo) será hospedado
	5. *Coleção*: Se você já tiver criado um aplicativo, selecione uma Coleção criada anteriormente, caso contrário, selecione a Nova Coleção


	Quando terminar, clique no botão Verificar para concluir a criação do seu aplicativo

4. Agora clique/selecione o aplicativo que você acabou de criar na guia Aplicativo:
 
   	![][9]

5. Em seguida, clique em "Informações de conexão" para exibir as configurações de conexão para colocar em sua integração SDK:
 
   	![][10]

6. Por fim, anote a "cadeia de conexão" que é o que você precisará para identificar esse aplicativo no seu código de Aplicativo:

   	![][11]

	>[AZURE.TIP] Você pode usar o ícone de "cópia" à direita da Cadeia de Conexão para copiá-lo para a área de transferência como uma conveniência.

##<a id="connecting-app"></a>Conectando seu aplicativo no back-end do Mobile Engagement

Este tutorial apresenta uma "integração básica" que é o conjunto mínimo necessário para coletar dados e enviar uma notificação por push. A documentação de integração completa pode ser encontrada na [documentação do SDK do Mobile Engagement iOS]

Vamos criar um aplicativo básico com o Android Studio para demonstrar a integração

###Criar um novo projeto iOS

Você pode ignorar esta etapa se já tiver um aplicativo e estiver familiarizado com o desenvolvimento do iOS

1. Inicie o Xcode e, no pop-up, selecione "Criar um novo projeto do Xcode"

   	![][12]

2. Preencha o nome do aplicativo, o nome da empresa e o identificador. Anote-os, pois você precisará deles mais tarde, clique em Avançar

   	![][13]

3. Agora, selecione o aplicativo Modo de exibição único e clique em Avançar

   	![][14]


O Xcode criará o aplicativo de demonstração ao qual integraremos o Mobile Engagement

###Incluir a biblioteca de SDK em seu projeto

Baixe e integre a biblioteca de SDK

1. Baixe o [SDK do Mobile Engagement iOS]
2. Extraia o arquivo .tar.gz para uma pasta no seu computador
3. Clique com botão direito do mouse no projeto e selecione "Adicionar arquivos a..."

	![][17]

4. Navegue até a pasta onde você extraiu o SDK e selecione a pasta  `EngagementSDK` e pressione OK

	![][18]

5. Abra a guia  `Criar fases` e no menu  `Link binário com bibliotecas`, adicione as estruturas, como mostrado abaixo:

	![][19]


###Conectar o seu aplicativo ao back-end do Mobile Engagement com a Cadeia de conexão

1. Copie a seguinte linha de código na parte superior do seu arquivo de implementação do Aplicativo Delegado

		#import "EngagementAgent.h"

2. Volte para o portal do Azure na página  *Informações de conexão* de seu aplicativo e copie a Cadeia de conexão

	![][11]

3. Cole-o em delegar  `didFinishLaunchingWithOptions`		

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

##Enviar uma tela para o Mobile Engagement

Para iniciar o envio de dados e garantir que os usuários estejam ativos, você deve enviar pelo menos 1 tela (atividade) para o back-end do Mobile Engagement. Vamos fazer isso pela sobrecarga da classe  `UIViewController` com o  `EngagementViewController` que o nosso SDK fornece.
Para isso, abra o arquivo  `ViewController.h`, importe  `EngagementViewController.h` e substitua a superclasse da interface  `ViewController` por  `EngagementViewController`, conforme mostrado abaixo:

![][22]

##<a id="monitor"></a>Como verificar se o seu aplicativo está conectado com monitoramento em tempo real

Esta seção mostra como se certificar de que seu aplicativo se conecta ao back-end do Mobile Engagement usando o recurso de monitoramento em tempo real do Mobile Engagement.

1. Navegue até o portal do Mobile Engagement

	Do seu portal do Azure, certifique-se de que você esteja no aplicativo que está usando para este projeto e, em seguida, clique no botão "Acionar" na parte inferior:

	![][26]

2. Você será levado para a página de configurações em seu Portal do Engagement para o seu aplicativo. Lá, clique na guia "Monitorar":

	![][30]

3. O monitor está pronto para mostrar qualquer dispositivo, em tempo real, que iniciará o aplicativo:

	![][31]

4. No Xcode, inicie o aplicativo no simulador ou em um dispositivo conectado

5. Caso tenha funcionado, agora você deve ver a 1 sessão no monitor! 

**Parabéns!** Foi bem-sucedida a primeira etapa deste tutorial; um aplicativo foi conectado ao back-end do Mobile Engagement e já está enviando dados

6. Clicar no botão Home do simulador trará o número de sessões no monitor de volta para 0, conforme mostrado acima

	![][33]



##<a id="integrate-push"></a>Habilitando notificações por push e em mensagens no aplicativo

O Mobile Engagement permite interagir e REACH seus usuários com notificações por push e em mensagens no aplicativo no contexto das campanhas. Esse módulo é chamado REACH no portal do Mobile Engagement.
As seções a seguir configurarão o seu aplicativo para recebê-los.

### Adicionar a biblioteca Reach ao seu projeto

1. Clique com botão direito do mouse no seu projeto
2. Selecione `Adicionar arquivo para...`
3. Navegue até a pasta onde você extraiu o SDK
4. Selecione a pasta  `EngagementReach`
5. Clique em Adicionar

### Modificar seu Representante de Aplicativo

1. Na parte superior do seu arquivo de implementação, importe o módulo Engagement Reach

		#import "AEReachModule.h"
	
2. Dentro do  `application:didFinishLaunchingWithOptions`, crie um módulo de alcance e passe-o para a sua linha de inicialização do Engagement existente:

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			[...]
			return YES;
		}	

###Habilitar seu aplicativo para receber notificações por push do APNS

1. Adicione a seguinte linha ao método  `application:didFinishLaunchingWithOptions`:

		if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			[application registerForRemoteNotifications];
		}
		else {

			[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

2. Adicione o método  `application:didRegisterForRemoteNotificationsWithDeviceToken` da seguinte maneira:
 
		- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
		{
 		   [[EngagementAgent shared] registerDeviceToken:deviceToken];
		}

3. Adicione o método  `didReceiveRemoteNotification` da seguinte maneira:
4. 
		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
		{
		    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo];
		}

###Permitir acesso ao seu certificado Push ao Mobile Engagement

Para permitir que o Mobile Engagement envie notificações por Push em seu nome, você precisa conceder acesso ao certificado. Isso é feito configurando e inserindo seu certificado no portal do Mobile Engagement. Certifique-se de obter seu certificado .p12 conforme explicado na documentação da Apple.

1. Navegue até o portal do Mobile Engagement

	Do seu portal do Azure, certifique-se de que você esteja no aplicativo que está usando para este projeto e, em seguida, clique no botão "Acionar" na parte inferior:

	![][26]

2. Você será levado para a página de configurações em seu Portal do Engagement. Lá, clique na seção "Push nativo" para carregar seu certificado p12:

	![][27]

3. Selecione seu p12, carregue-o e digite sua senha:

	![][28]

4. Agora adicione o perfil de provisionamento e compile seu aplicativo para um dispositivo de destino

Já estamos prontos, agora, verificaremos se a integração básica foi feita corretamente

##<a id="send"></a>Como enviar uma notificação para seu aplicativo

Agora, vamos criar uma campanha de notificação por push simples que enviará um push para nosso aplicativo:

1. Navegue até a guia Reach em seu portal do Mobile Engagement

2. Clique em "Novo comunicado" para criar sua campanha de push
	
	![][35]

3. Configure o primeiro campo da sua campanha:

	![][36]

	1. Nomeie a sua campanha com qualquer nome que você deseje
	2. Selecione a hora de entrega como "Somente fora do aplicativo": esse é o tipo de notificação por push Apple simples que apresenta algum texto
	3. No texto de notificação, primeiro digite o título que será a primeira linha no push
	4. Em seguida, digite a mensagem que será a segunda linha

4. Role para baixo e, na seção conteúdo, selecione "Somente notificação"

	![][37]

5. Você concluiu a configuração da campanha mais básica possível; agora role novamente para baixo e crie a sua campanha para salvá-la!
![][38]

6. Última etapa, ativar a sua campanha
![][39]

7. Você verá uma notificação por push em seu dispositivo!

<!-- URLs. -->
[SDK do Mobile Engagement iOS]: http://go.microsoft.com/?linkid=9864553
[Documentação do SDK do Mobile Engagement Android]: http://go.microsoft.com/?linkid=9874682
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-ios-get-started/select-app.png
[10]: ./media/mobile-engagement-ios-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[23]: ./media/mobile-engagement-ios-get-started/copy-resources.png
[24]: ./media/mobile-engagement-ios-get-started/paste-resources.png
[25]: ./media/mobile-engagement-ios-get-started/paste-resources.png
[26]: ./media/mobile-engagement-ios-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-ios-get-started/api-key.png
[30]: ./media/mobile-engagement-ios-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-get-started/monitor.png
[32]: ./media/mobile-engagement-ios-get-started/launch.png
[33]: ./media/mobile-engagement-ios-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-get-started/campaign-activate.png

<!--HONumber=47-->
