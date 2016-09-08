<properties
	pageTitle="Adicionar notificações por push ao aplicativo Apache Cordova com os Aplicativos Móveis do Azure | Serviço de Aplicativo do Azure"
	description="Saiba como usar Aplicativos Móveis do Azure para enviar notificações por push para seu aplicativo Apache Cordova."
	services="app-service\mobile"
	documentationCenter="javascript"
	manager="ggailey777"
	editor=""
	authors="adrianhall"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="glenga"/>

# Adicionar notificações por push ao seu aplicativo Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## Visão geral

Neste tutorial, você adicionará notificações por push ao projeto de [início rápido do Apache Cordova] para que, sempre que um registro for inserido, uma notificação por push seja enviada. Este tutorial baseia-se no tutorial de [início rápido do Apache Cordova], que você deve concluir primeiro. Se você tiver um back-end do ASP.NET e não usar o projeto de servidor de início rápido baixado, será necessário adicionar o pacote de extensão de notificação por push ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure].

##<a name="prerequisites"></a>Pré-requisitos

Este tutorial cobre um aplicativo Apache Cordova que está sendo desenvolvido no Visual Studio 2015 e executado no Emulador do Google Android, em um dispositivo Android, em um dispositivo Windows e em um dispositivo iOS.

Para concluir este tutorial, você precisará do seguinte:

* Um computador com o [Visual Studio Community 2015] ou mais recente.
* [Ferramentas do Visual Studio para Apache Cordova].
* Uma [conta ativa do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Um projeto de [início rápido do Apache Cordova] concluído. Você pode concluir outros tutoriais antes (como o de [autenticação]), mas isso não é necessário.
* (Android) Uma [conta do Google] com um endereço de email verificado e um dispositivo Android.
* (iOS) Uma associação ao Programa de Desenvolvedores da Apple e um dispositivo iOS (o simulador iOS não dá suporte a envio por push)
* (Windows) Uma conta de desenvolvedor da Windows Store e um dispositivo com Windows 10

Embora as notificações por push recebam suporte em emuladores Android, percebemos que eles são instáveis, e não recomendamos que você teste as notificações por push em emuladores.

##<a name="create-hub"></a>Criar um hub de notificação

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

[Assista a um vídeo que mostra etapas semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub)

##Atualizar o projeto de servidor para enviar notificações por push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a name="add-push-to-app"></a>Modificar seu aplicativo Cordova para receber notificações por push

Você deve certificar-se de que seu projeto de aplicativo do Apache Cordova esteja pronto para lidar com notificações por push. É necessário instalar o plug-in de push do Cordova, além de todos os serviços de push específicos da plataforma.

#### Atualize a versão do Cordova em seu projeto.

Recomendamos que você atualize o projeto cliente para Cordova 6.1.1 se seu projeto está configurado usando uma versão mais antiga. Para atualizar o projeto, clique no arquivo config.xml para abrir o designer de configuração. Selecione a guia Plataformas e escolha 6.1.1 na caixa de texto **Cordova CLI**.

Escolha **Criar** e **Compilar Solução** para atualizar o projeto.

#### Instalar o plug-in de push

Os aplicativos Apache Cordova não manipulam recursos de rede ou do dispositivo de forma nativa. Essas funcionalidades são fornecidas por plug-ins publicados no [npm](https://www.npmjs.com/) ou no GitHub. O plug-in `phonegap-plugin-push` é usado para manipular notificações por push de rede.

É possível instalar o plug-in de push com uma das seguintes maneiras:

**No prompt de comando:**

Execute o seguinte comando:

    cordova plugin add phonegap-plugin-push

**No Visual Studio:**

1.  No Gerenciador de Soluções, abra o arquivo `config.xml`, clique em **Plug-ins** > **Personalizado**, selecione **Git** como a fonte de instalação e insira `https://github.com/phonegap/phonegap-plugin-push` como a fonte.

	![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)

2.  Clique na seta ao lado da fonte de instalação.

3. Em **SENDER\_ID**, se você já tiver uma ID numérica do projeto para o projeto de Console de Desenvolvedor do Google, poderá adicioná-la aqui. Caso contrário, insira um valor de espaço reservado, como 777777 e, se tiver como alvo o Android, atualize esse valor no arquivo config.xml mais tarde.

4. Clique em **Adicionar**.

O plug-in de push agora está instalado.

####Instalar o plug-in do dispositivo

Siga o mesmo procedimento usado para instalar o plug-in de envio por push, mas você encontrará o plug-in Dispositivo na lista de plug-ins Principais (clique em **Plug-ins** > **Principais** para encontrá-lo). Você precisa desse plug-in para obter o nome da plataforma (`device.platform`).

#### Registrar seu dispositivo para push na inicialização

Inicialmente, incluiremos alguns códigos mínimos para o Android. Posteriormente, faremos algumas pequenas modificações para serem executados no iOS ou no Windows 10.

1. Adicione uma chamada para **registerForPushNotifications** durante o retorno de chamada para o processo de logon, ou na parte inferior do método **onDeviceReady**:

		// Login to the service.
		client.login('google')
		    .then(function () {
		        // Create a table reference
		        todoItemTable = client.getTable('todoitem');

		        // Refresh the todoItems
		        refreshDisplay();

		        // Wire up the UI Event Handler for the Add Item
		        $('#add-item').submit(addItemHandler);
		        $('#refresh').on('click', refreshDisplay);

				    // Added to register for push notifications.
		        registerForPushNotifications();

		    }, handleError);

	Este exemplo mostra uma chamada para **registerForPushNotifications** após a autenticação bem-sucedida, o que é recomendado ao usar notificações por push e autenticação em seu aplicativo.

2. Adicione o novo método **registerForPushNotifications** da seguinte maneira:

		// Register for Push Notifications. Requires that phonegap-plugin-push be installed.
		var pushRegistration = null;
		function registerForPushNotifications() {
		  pushRegistration = PushNotification.init({
		      android: { senderID: 'Your_Project_ID' },
		      ios: { alert: 'true', badge: 'true', sound: 'true' },
		      wns: {}
		  });

		// Handle the registration event.
		pushRegistration.on('registration', function (data) {
		  // Get the native platform of the device.
		  var platform = device.platform;
		  // Get the handle returned during registration.
		  var handle = data.registrationId;
		  // Set the device-specific message template.
		  if (platform == 'android' || platform == 'Android') {
		      // Register for GCM notifications.
		      client.push.register('gcm', handle, {
		          mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
		      });
		  } else if (device.platform === 'iOS') {
		      // Register for notifications.            
		      client.push.register('apns', handle, {
		          mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
		      });
		  } else if (device.platform === 'windows') {
		      // Register for WNS notifications.
		      client.push.register('wns', handle, {
		          myTemplate: {
		              body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
		              headers: { 'X-WNS-Type': 'wns/toast' } }
		      });
		  }
		});

		pushRegistration.on('notification', function (data, d2) {
		  alert('Push Received: ' + data.message);
		});

		pushRegistration.on('error', handleError);
		}

3. (Android) No código acima, substitua `Your_Project_ID` pela ID numérica do projeto de seu aplicativo no [Console do Desenvolvedor do Google].

## (Opcional) Configurar e executar o aplicativo no Android

Conclua esta seção para habilitar notificações por push para o Android.

####<a name="enable-gcm"></a>Habilitar as mensagens em nuvem do Google

Como nosso alvo é a plataforma Google Android, primeiro você deve habilitar o Google Cloud Messaging. Da mesma forma, se o alvo fosse dispositivos Microsoft Windows, seria necessário habilitar o suporte para WNS.

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

[Assista a um vídeo que mostra etapas semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-4-Set-up-gcm-for-push)

####<a name="configure-backend"></a>Configurar seu back-end de Aplicativo Móvel para enviar solicitações por push usando GCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

####Configurar seu aplicativo Cordova para Android

Em seu aplicativo Cordova, abra o arquivo config.xml e substitua `Your_Project_ID` pelo valor numérico da ID de projeto para seu aplicativo no [Console de Desenvolvedor do Google].

		<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
			<variable name="SENDER_ID" value="Your_Project_ID" />
		</plugin>

Abra index.js e atualize o código para usar o valor numérico da ID do projeto.

		pushRegistration = PushNotification.init({
			android: { senderID: 'Your_Project_ID' },
			ios: { alert: 'true', badge: 'true', sound: 'true' },
			wns: {}
		});

####<a name="configure-device"></a>Configurar seu dispositivo Android para depuração USB

Antes de implantar seu aplicativo em seu dispositivo Android, você precisa habilitar a Depuração USB. Execute as etapas a seguir em seu telefone com Android:

1. Vá para **Configurações** > **Sobre o telefone**, toque no **Número de build** até que o modo de desenvolvedor seja habilitado (cerca de sete vezes).

2. Novamente em **Configurações** > **Opções do Desenvolvedor**, habilite a **depuração USB** e conecte seu telefone Android ao computador de desenvolvimento com um Cabo USB.

Testamos isso usando um dispositivo Google Nexus 5X que executa o Android 6.0 (Marshmallow). No entanto, as técnicas são comuns em qualquer versão moderna do Android.

#### Instalar os Serviços do Google Play

O plug-in de push depende dos Serviços do Google Play no Android para enviar notificações por push.

1.  Em **Visual Studio**, clique em **Ferramentas** > **Android** > **Gerenciador de SDK do Android**, expanda a pasta **Extras** e marque a caixa para certificar-se de que cada um dos seguintes SDKs está instalado.
    * Repositório de Suporte do Android versão 20 ou mais recente
    * Serviços do Google Play versão 27 ou mais recente
    * Repositório do Google versão 22 ou mais recente

2.  Clique em **Instalar Pacotes** e aguarde a conclusão da instalação.

As bibliotecas exigidas atualmente estão listadas na [documentação de instalação do phonegap-plugin-push].

#### Testar notificações por push no aplicativo em Android

Agora é possível testar notificações por push executando o aplicativo e inserindo itens na tabela TodoItem. Você pode fazer isso no mesmo dispositivo ou em um segundo dispositivo, desde que esteja usando o mesmo back-end. Teste seu aplicativo Cordova na plataforma Android usando uma das seguintes maneiras:

- **Em um dispositivo físico:** anexe seu dispositivo Android ao computador de desenvolvimento com um cabo USB. Em vez de **Emulador do Google Android**, selecione **Dispositivo**. O Visual Studio implantará o aplicativo no dispositivo e o executará. Em seguida, você poderá interagir com o aplicativo no dispositivo. Melhore a experiência de desenvolvimento. Aplicativos de compartilhamento de tela, como o [Mobizen], podem ajudá-lo a desenvolver um aplicativo Android projetando sua tela do Android em um navegador da Web no computador.

- **Em um emulador do Android**: há etapas de configuração adicionais exigidas durante a execução em um emulador.

	Verifique se você está implantando ou depurando em um dispositivo virtual com APIs do Google definidas como destino, conforme mostrado abaixo no Gerenciador de AVD (dispositivo virtual Android).

	![](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

	Se você quiser usar um emulador x86 mais rápido, [instale o driver HAXM](https://taco.visualstudio.com/pt-BR/docs/run-app-apache/#HAXM) e configure o emulador para usá-lo.

	Adicione uma conta do Google ao dispositivo Android clicando em **Aplicativos** > **Configurações** > **Adicionar conta**, siga os prompts para adicionar uma conta existente do Google ao dispositivo (recomendamos usar uma conta existente em vez de criar uma nova).

	![](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

	Execute o aplicativo todolist como feito anteriormente s e insira um novo item de tarefa pendente. Dessa vez, um ícone de notificação é exibido na área de notificação. Você pode abrir a gaveta de notificações para exibir o texto completo da notificação.

	![](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

##(Opcional) Configurar e executar no iOS

Esta seção é para executar o projeto Cordova em dispositivos iOS. Você poderá ignorá-la se não estiver trabalhando com dispositivos iOS.

####Instalar e executar o agente de remotebuild iOS em um Mac ou serviço de nuvem

Antes de executar um aplicativo Cordova no iOS usando o Visual Studio, percorra as etapas no [Guia de configuração do iOS](http://taco.visualstudio.com/pt-BR/docs/ios-guide/) para instalar e executar o agente remotebuild.

Verifique se que você pode criar o aplicativo para iOS. As etapas no guia de instalação são necessárias para criar para iOS no Visual Studio. Se você não tiver um Mac, poderá criar para iOS usando o agente remotebuild em um serviço como o MacInCloud. Para saber mais, confira [Executar seu aplicativo iOS na nuvem](http://taco.visualstudio.com/pt-BR/docs/build_ios_cloud/).

>[AZURE.NOTE] O XCode 7 ou superior é necessário para usar o plug-in de push no iOS.

####Localizar a ID a ser usada como a ID do aplicativo

Antes de registrar seu aplicativo para notificações por push, abra o config.xml em seu aplicativo Cordova, localize o valor de atributo `id` no elemento de widget e copie-o para uso posterior. No XML a seguir, a ID é `io.cordova.myapp7777777`.

		<widget defaultlocale="pt-BR" id="io.cordova.myapp7777777"
  		version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
			xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

Depois, use esse identificador ao criar uma ID de aplicativo no portal do desenvolvedor da Apple. (Se você criar uma ID de aplicativo diferente no portal do desenvolvedor e desejar usá-la, precisará executar algumas etapas adicionais posteriormente neste tutorial para alterar essa ID no arquivo config.xml. A ID no elemento de widget deve corresponder à ID de aplicativo no portal do desenvolvedor.)

####Registrar o aplicativo nas notificações por push no portal do desenvolvedor da Apple

[AZURE.INCLUDE [Hubs de notificação Xamarin habilitam as notificações por push da Apple](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]

[Assista a um vídeo que mostra etapas semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

####Configurar o Azure para enviar notificações por push

1. Faça logon no [Portal do Azure](https://portal.azure.com/). Clique em **Procurar** > **Aplicativos Móveis** > seu aplicativo móvel > **Configurações** > **Push** > **Apple (APNS)** > **Carregar Certificado**. Carregue o arquivo de certificado de push .p12 exportado anteriormente. Selecione **Sandbox** se tiver criado um certificado de push de desenvolvimento para desenvolvimento e teste. Caso contrário, escolha **Produção**. Agora, seu serviço móvel está configurado para funcionar com notificações por push para iOS.

	![](./media/app-service-mobile-cordova-get-started-push/mobile-app-upload-apns-cert.png)

####Verifique se a ID do aplicativo corresponde ao aplicativo Cordova

Se a ID do aplicativo que criou na sua conta de desenvolvedor da Apple já corresponde à ID do elemento widget em config.xml, você pode ignorar esta etapa. No entanto, se as IDs não correspondem, siga estas etapas:

1. Exclua a pasta plataformas do seu projeto.

2. Exclua a pasta plugins do seu projeto.

3. Exclua a pasta node\_modules do seu projeto.

4. Atualize o atributo id do elemento widget em config.xml para usar a ID do aplicativo que você criou na sua conta de desenvolvedor da Apple.

5. Recompile o projeto.

#####Testar notificações por push em seu aplicativo iOS

1. No Visual Studio, verifique se **iOS** está selecionado como o destino da implantação e escolha **Dispositivo** para executar no dispositivo iOS conectado.

	Você pode executar em um dispositivo iOS conectado ao seu PC usando o iTunes. O simulador do iOS não dá suporte a notificações por push.

2. Pressione o botão **Executar** ou **F5** no Visual Studio para compilar o projeto e iniciar o aplicativo em um dispositivo iOS; em seguida, clique em **OK** para aceitar as notificações por push.

	>[AZURE.NOTE] Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.

3. No aplicativo, digite uma tarefa e, em seguida, clique no ícone do sinal de adição (+).

4. Verifique se uma notificação é recebida e clique em OK para ignorar a notificação.

##(Opcional) Configurar e executar no Windows

Esta seção é para executar o projeto de aplicativo do Apache Cordova em dispositivos com Windows 10 (o plug-in de notificação por push PhoneGap tem suporte no Windows 10). Você poderá ignorá-la se não estiver trabalhando com dispositivos Windows.

####Registrar seu aplicativo Windows para notificações por push com WNS

Para usar as opções de Loja no Visual Studio, selecione um destino do Windows na lista Plataformas da Solução, como **Windows x64** ou **Windows x86** (evite **Windows AnyCPU** para notificações por push).

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Assista a um vídeo que mostra etapas semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push)

####Configurar o hub de notificação para WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

####Configurar seu aplicativo Cordova para dar suporte a notificações por push do Windows

Abra o designer de configuração (clique com o botão direito do mouse no arquivo config.xml e selecione **Designer de Exibição**), selecione a guia **Windows** e escolha **Windows 10** em **Versão de Destino do Windows**.

>[AZURE.NOTE] Se você estiver usando uma versão do Cordova anterior ao Cordova 5.1.1 (recomendamos a 6.1.1), defina também o sinalizador Compatível com Toast como true no arquivo config.xml.

Para dar suporte a envio de notificações por push nas versões padrão, abra o arquivo build.json. Copie a configuração de "versão" em sua configuração de depuração.

		"windows": {
			"release": {
				"packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
				"publisherId": "CN=yourpublisherID"
			}
		}

Após a atualização, o código anterior deverá ser assim.

	"windows": {
		"release": {
			"packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
			"publisherId": "CN=yourpublisherID"
			},
		"debug": {
			"packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
			"publisherId": "CN=yourpublisherID"
			}
		}

Compile o aplicativo e verifique se não há erros. Agora, o aplicativo cliente deve se registrar para notificações no back-end do Aplicativo Móvel. Repita esta seção para cada projeto do Windows em sua solução.

####Testar as notificações por push em seu aplicativo para Windows

No Visual Studio, verifique se uma plataforma Windows está selecionada como o destino de implantação, como **Windows x64** ou **Windows x86**. Para executar o aplicativo em um PC com Windows 10 hospedando o Visual Studio, escolha **Computador Local**.

Pressione o botão Executar para compilar o projeto e iniciar o aplicativo.

No aplicativo, digite um nome para um novo todoitem e clique no ícone de adição (+) para realizar esta ação.

Verifique se uma notificação é recebida quando o item é adicionado.

##<a name="next-steps"></a>Próximas etapas

* Leia sobre os [Hubs de Notificação] para saber mais sobre notificações por push.
* Se você ainda não fez isso, continue o tutorial [Adicionando autenticação] para o seu aplicativo Apache Cordova.

Saiba como usar os SDKs.

* [SDK do Apache Cordova]
* [SDK do Servidor ASP.NET]
* [SDK do Servidor Node.js]

<!-- URLs -->
[Adicionando autenticação]: app-service-mobile-cordova-get-started-users.md
[início rápido do Apache Cordova]: app-service-mobile-cordova-get-started.md
[autenticação]: app-service-mobile-cordova-get-started-users.md
[Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[conta do Google]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Console de Desenvolvedor do Google]: https://console.developers.google.com/home/dashboard
[Console do Desenvolvedor do Google]: https://console.developers.google.com/home/dashboard
[documentação de instalação do phonegap-plugin-push]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Ferramentas do Visual Studio para Apache Cordova]: https://www.visualstudio.com/pt-BR/features/cordova-vs.aspx
[Hubs de Notificação]: ../notification-hubs/notification-hubs-overview.md
[SDK do Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[SDK do Servidor ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[SDK do Servidor Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0824_2016-->