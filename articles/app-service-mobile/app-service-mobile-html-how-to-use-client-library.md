<properties
	pageTitle="Como usar o SDK do JavaScript para os Aplicativos Móveis do Azure"
	description="Como usar o v para os Aplicativos Móveis do Azure"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="adrianha"/>

# Como usar a biblioteca de cliente JavaScript para os Aplicativos Móveis do Azure

[AZURE.INCLUDE [App-Service-Mobile-Selector-Client-Library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia ensina a executar cenários comuns usando o mais recente [SDK do JavaScript para os Aplicativos Móveis do Azure]. Se não estiver familiarizado com os Aplicativos Móveis do Azure, primeiro conclua o [Início Rápido dos Aplicativos Móveis do Azure] para criar um back-end e uma tabela. Neste guia, vamos nos concentrar no uso do back-end móvel em aplicativos Web em HTML/JavaScript.

##<a name="Setup"></a>Configuração e Pré-requisitos

Este guia pressupõe que você tenha criado um back-end com uma tabela. Este guia pressupõe que a tabela tem o mesmo esquema das tabelas desses tutoriais.

A instalação do SDK do JavaScript para Aplicativos Móveis do Azure pode ser feita por meio do comando `npm`:

```
npm install azure-mobile-apps-client --save
```

Depois de instalada, a biblioteca estará localizada em `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.min.js`. Faça uma cópia desse arquivo e cole-a em seu site.

```
<script src="path/to/MobileServices.Web.min.js"></script>
```

A biblioteca também pode ser usada como um módulo ES2015, em ambientes de CommonJS como Browserify e Webpack e como uma biblioteca AMD. Por exemplo:

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

[AZURE.INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>Como autenticar usuários

O Serviço de Aplicativo do Azure oferece suporte à autenticação e autorização de usuários de aplicativos usando uma variedade de provedores de identidade externos: Facebook, Google, Conta da Microsoft e Twitter. Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização em scripts do servidor. Para obter mais informações, consulte o tutorial [Introdução à autenticação].

Dois fluxos de autenticação são suportados: um server flow e um client flow. O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende da interface de autenticação da web do provedor. O fluxo de cliente permite uma integração mais profunda com funcionalidades específicas do dispositivo, como logon único, uma vez que depende de SDKs específicos do provedor.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

##<a name="register-for-push"></a>Como se registrar para receber notificações por push

Instale o [phonegap-plugin-push] para manipular notificações por push. Isso pode ser facilmente adicionado usando o comando `cordova plugin add` na linha de comando ou por meio do instalador de plug-ins do Git no Visual Studio. O código a seguir em seu aplicativo Apache Cordova registrará seu dispositivo para notificações por push:

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Use o SDK dos Hubs de Notificação para enviar notificações por push do servidor. Você nunca deve enviar notificações por push diretamente dos clientes, pois isso pode ser usado para disparar um ataque de negação de serviço contra os Hubs de Notificação ou PNS.

<!-- URLs. -->
[Início Rápido dos Aplicativos Móveis do Azure]: app-service-mobile-cordova-get-started.md
[Introdução à autenticação]: app-service-mobile-cordova-get-started-users.md
[Adicionar autenticação ao seu aplicativo]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova Plugin for Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[your first Apache Cordova app]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[documentação do objeto Query]: https://msdn.microsoft.com/pt-BR/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0323_2016-->