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
	ms.date="05/02/2016"
	ms.author="glenga"/>

# Adicionar notificações por push ao seu aplicativo Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## Visão geral

Neste tutorial, você adicionará notificações por push ao projeto de [início rápido do Apache Cordova] para que, sempre que um registro for inserido, uma notificação por push seja enviada. Este tutorial baseia-se no tutorial de [início rápido do Apache Cordova], que você deve concluir primeiro. Se você tiver um back-end do ASP.NET e não usar o projeto de servidor de início rápido baixado, será necessário adicionar o pacote de extensão de notificação por push ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure].

##<a name="prerequisites"></a>Pré-requisitos

Este tutorial cobre um aplicativo Apache Cordova que está sendo desenvolvido no Visual Studio 2015 e executado no Emulador do Google Android. Além disso, você pode concluir este tutorial em outros emuladores ou dispositivos, e em plataformas de desenvolvimento diferentes.

Para concluir este tutorial, você precisará do seguinte:

* Uma [conta do Google] com um endereço de email verificado.
* Um computador com o [Visual Studio Community 2015] ou mais recente.
* [Ferramentas do Visual Studio para Apache Cordova].
* Uma [conta ativa do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Um projeto de [início rápido do Apache Cordova] concluído. Você pode concluir outros tutoriais antes (como o de [autenticação]), mas isso não é necessário.
* Um dispositivo Android.

Embora as notificações por push recebam suporte em emuladores Android, percebemos que eles são instáveis, e não recomendamos que você teste as notificações por push em emuladores.

##<a name="create-hub"></a>Criar um hub de notificação

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##<a name="enable-gcm"></a>Habilitar as mensagens em nuvem do Google

Como nosso alvo é a plataforma Google Android, você deve habilitar o Google Cloud Messaging. Se o alvo fosse dispositivos Apple iOS, seria necessário habilitar o suporte APNS. Da mesma forma, se o alvo fosse dispositivos Microsoft Windows, seria necessário habilitar o suporte para WNS ou MPNS.

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a name="configure-backend"></a>Configurar o back-end de Aplicativo Móvel para enviar solicitações por push

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a name="update-service"></a>Atualizar o projeto de servidor para enviar notificações por push

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

##<a name="configure-device"></a>Configurar seu dispositivo Android para depuração USB

Antes de implantar seu aplicativo em seu dispositivo Android, você precisa habilitar a Depuração USB. Execute as etapas a seguir em seu telefone com Android:

1. Vá para **Configurações** > **Sobre o telefone**, toque no **Número de build** até que o modo de desenvolvedor seja habilitado (cerca de 7 vezes).
 
2. Novamente em **Configurações** > **Opções do Desenvolvedor**, habilite a **depuração USB** e conecte seu telefone Android ao computador de desenvolvimento com um Cabo USB.

Testamos isso usando um dispositivo Google Nexus 5X que executa o Android 6.0 (Marshmallow). No entanto, as técnicas são comuns em qualquer versão moderna do Android.

##<a name="add-push-to-app"></a>Adicionar notificações de push para seu aplicativo

Você deve certificar-se de que seu projeto de aplicativo do Apache Cordova esteja pronto para lidar com notificações por push. É necessário instalar o plug-in de push do Cordova, além de todos os serviços de push específicos da plataforma.

### Instalar o plug-in de push

Os aplicativos Apache Cordova não manipulam recursos de rede ou do dispositivo de forma nativa. Essas funcionalidades são fornecidas por plug-ins publicados no [npm](https://www.npmjs.com/) ou no GitHub. O plug-in `phonegap-plugin-push` é usado para manipular notificações por push de rede.

É possível instalar o plug-in de push com uma das seguintes maneiras:

**No prompt de comando:**

Execute o seguinte comando:

    cordova plugin add phonegap-plugin-push

**No Visual Studio:**

1.  No Gerenciador de Soluções, abra o arquivo `config.xml`, clique em **Plug-ins** > **Personalizado**, selecione **Git** como a fonte de instalação e insira `https://github.com/phonegap/phonegap-plugin-push` como a fonte.

	![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)

2.  Clique na seta ao lado da fonte de instalação e clique em **Adicionar**

O plug-in de push agora está instalado.

### Instalar os Serviços do Google Play

O plug-in de push depende dos Serviços do Google Play no Android para enviar notificações por push.

1.  Em **Visual Studio**, clique em **Ferramentas** > **Android** > **Gerenciador de SDK do Android**, expanda a pasta **Extras** e marque a caixa para certificar-se de que cada um dos seguintes SDKs está instalado.    
    * Biblioteca de Suporte do Android versão 23 ou mais recente
    * Repositório de Suporte do Android versão 20 ou mais recente
    * Serviços do Google Play versão 27 ou mais recente
    * Repositório do Google versão 22 ou mais recente
     
2.  Clique em **Instalar Pacotes** e aguarde a conclusão da instalação.

As bibliotecas exigidas atualmente estão listadas na [documentação de instalação do phonegap-plugin-push].

### Registrar seu dispositivo para push na inicialização

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

2. Adicione o novo método `registerForPushNotifications()` da seguinte maneira:

	    // Register for Push Notifications.
		// Requires that phonegap-plugin-push be installed.
	    var pushRegistration = null;
	    function registerForPushNotifications() {
	        pushRegistration = PushNotification.init({
	            android: {
	                senderID: 'Your_Project_ID'
	            },
	            ios: {
	                alert: 'true',
	                badge: 'true',
	                sound: 'true'
	            },
	            wns: {

	            }
	        });

	        pushRegistration.on('registration', function (data) {
	            client.push.register('gcm', data.registrationId);
	        });

	        pushRegistration.on('notification', function (data, d2) {
	            alert('Push Received: ' + data.message);
	        });

	        pushRegistration.on('error', handleError);
	    }

3. No código acima, substitua `Your_Project_ID` pela ID numérica do projeto de seu aplicativo no [Console do Desenvolvedor do Google].

## Testar notificações por push no aplicativo 

Agora é possível testar notificações por push executando o aplicativo e inserindo itens na tabela TodoItem. Você pode fazer isso no mesmo dispositivo ou em um segundo dispositivo, desde que esteja usando o mesmo back-end. Teste seu aplicativo Cordova na plataforma Android usando uma das seguintes maneiras:

- **Em um dispositivo físico:** anexe seu dispositivo Android ao computador de desenvolvimento com um cabo USB. Em vez de **Emulador do Google Android**, selecione **Dispositivo**. O Visual Studio implantará o aplicativo no dispositivo e o executará. Em seguida, você poderá interagir com o aplicativo no dispositivo. Melhore a experiência de desenvolvimento. Aplicativos de compartilhamento de tela, como o [Mobizen], podem ajudá-lo a desenvolver um aplicativo Android projetando sua tela do Android em um navegador da Web no computador.

- **Em um emulador do Android:** antes de receber notificações por push, é necessário adicionar uma conta do Google no emulador.

##<a name="next-steps"></a>Próximas etapas

* Leia sobre os [Hubs de Notificação] para saber mais sobre notificações por push.
* Se você ainda não fez isso, continue o tutorial [Adicionando autenticação] ao seu aplicativo Apache Cordova.

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
[Console do Desenvolvedor do Google]: https://console.developers.google.com/home/dashboard
[documentação de instalação do phonegap-plugin-push]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Ferramentas do Visual Studio para Apache Cordova]: https://www.visualstudio.com/pt-BR/features/cordova-vs.aspx
[Hubs de Notificação]: ../notification-hubs/notification-hubs-overview.md
[SDK do Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[SDK do Servidor ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[SDK do Servidor Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0518_2016-->