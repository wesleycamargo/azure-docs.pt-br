<properties
	pageTitle="Como usar o Plug-in do Apache Cordova para os Aplicativos Móveis do Azure"
	description="Como usar o Plug-in do Apache Cordova para os Aplicativos Móveis do Azure"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="ggailey"/>

# Como usar a biblioteca de clientes do Apache Cordova para os Aplicativos Móveis do Azure

[AZURE.INCLUDE [App-Service-Mobile-Selector-Client-Library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia ensina a executar cenários comuns usando o mais recente [Plug-in do Apache Cordova para os Aplicativos Móveis do Azure]. Se você for novo nos Aplicativos Móveis do Azure, primeiro conclua o [Início Rápido dos Aplicativos Móveis do Azure] para criar um back-end, criar uma tabela e baixar um projeto Apache Cordova pré-criado. Neste guia, abordaremos o Plug-in do Apache Cordova do lado do cliente.

##<a name="Setup"></a>Configuração e Pré-requisitos

Este guia pressupõe que você tenha criado um back-end com uma tabela. Este guia pressupõe que a tabela tem o mesmo esquema das tabelas desses tutoriais. Este guia também pressupõe que você adicionou o Plug-in do Apache Cordova ao seu código. Se você não tiver feito isso, poderá adicionar o plug-in do Apache Cordova ao seu projeto na linha de comando:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Para saber mais sobre como criar [seu primeiro aplicativo do Apache Cordova], consulte a documentação.

[AZURE.INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>Como autenticar usuários

O Serviço de Aplicativo do Azure oferece suporte à autenticação e autorização de usuários de aplicativos usando uma variedade de provedores de identidade externos: Facebook, Google, Conta da Microsoft e Twitter. Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização em scripts do servidor. Para obter mais informações, consulte o tutorial [Introdução à autenticação].

Ao usar a autenticação em um aplicativo Apache Cordova, os seguintes plugins Cordova devem estar disponíveis:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Dois fluxos de autenticação são suportados: um server flow e um client flow. O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende da interface de autenticação da web do provedor. O fluxo de cliente permite uma integração mais profunda com recursos específicos do dispositivo, como logon único, uma vez que depende de provedores específicos e SDKs específicos do dispositivo.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>Como configurar o Serviço de Aplicativo Móvel para URLs de redirecionamento externo.

Vários tipos de aplicativos do Apache Cordova usam uma funcionalidade de loopback para manipular fluxos de Interface do usuário do OAuth. Isso causa problemas pois o serviço de autenticação só sabe utilizar seu serviço por padrão. Exemplos disso são usar o emulador Ripple, executar seu serviço localmente ou em um Serviço de Aplicativo do Azure diferente e redirecionar para o Serviço de Aplicativo do Azure para autenticação ou recarregar em tempo real com o Ionic. Siga estas instruções para adicionar as definições locais à configuração:

1. Fazer logon no [portal do Azure]
2. Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do seu Aplicativo Móvel.
3. Clique em **Ferramentas**
4. Clique em **Gerenciador de Recursos** no menu OBSERVAR e clique em **Ir**. Uma nova janela ou guia será aberta.
5. Expanda os nós **config**, **authsettings** do seu site no painel de navegação esquerdo.
6. Clique em **Editar**
7. Procure o elemento "allowedExternalRedirectUrls". Ele será definido como null. Altere-o para o seguinte:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Substitua as URLs pelas URLs do seu serviço. Exemplos incluem "http://localhost:3000" (para o serviço de exemplo do Node.js) ou "http://localhost:4400" (para o serviço Ripple). No entanto, esses são exemplos - sua situação, incluindo para os serviços mencionados nos exemplos, pode ser diferente.
8. Clique no botão **Leitura/Gravação** no canto superior direito da tela.
9. Clique no botão verde **PUT**.

As configurações serão salvas neste momento. Não feche a janela do navegador até que as configurações sejam salvas. Você também precisará adicionar essas URLs de loopback às configurações de CORS:

1. Fazer logon no [portal do Azure]
2. Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do seu Aplicativo Móvel.
3. A folha Configurações será aberta automaticamente. Se não for, clique em **Todas as Configurações**.
4. Clique em **CORS** no menu de API.
5. Digite a URL que você deseja adicionar na caixa fornecida e pressione Enter.
6. Insira URLs adicionais conforme necessário.
7. Clique em **Salvar** para salvar as configurações.

Serão necessários cerca de 10 a 15 segundos para que as novas configurações tenham efeito.

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
[portal do Azure]: https://portal.azure.com
[Início Rápido dos Aplicativos Móveis do Azure]: app-service-mobile-cordova-get-started.md
[Introdução à autenticação]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Plug-in do Apache Cordova para os Aplicativos Móveis do Azure]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[seu primeiro aplicativo do Apache Cordova]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/pt-BR/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0824_2016-->