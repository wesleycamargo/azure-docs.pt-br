<properties
	pageTitle="Como usar o SDK do JavaScript para os Aplicativos Móveis do Azure"
	description="Como usar o v para os Aplicativos Móveis do Azure"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="06/29/2016"
	ms.author="adrianha;ricksal"/>

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

###<a name="configure-external-redirect-urls"></a>Como configurar o Serviço de Aplicativo Móvel para URLs de Redirecionamento Externo.

Vários tipos de aplicativos JavaScript usam uma funcionalidade de loopback para manipular fluxos de interface do usuário OAuth, como ao executar seu serviço localmente, usando a recarga dinâmica no Framework Ionic ou ao redirecionar para o Serviço de Aplicativo para autenticação. Isso pode causar problemas porque, por padrão, a autenticação de Serviço de Aplicativo só está configurada para permitir o acesso de back-end do Aplicativo Móvel.

Use as etapas a seguir para alterar as configurações de Serviço de Aplicativo para habilitar a autenticação de seu localhost:

1. Faça logon no [Portal do Azure], navegue até seu back-end do Aplicativo Móvel, clique em **Ferramentas** > **Gerenciador de Recursos** > **Ir** para abrir uma nova janela do gerenciador de recursos para seu back-end de Aplicativo Móvel (site).

2. Expanda o nó **config** para seu aplicativo, em seguida, clique em **authsettings** > **Editar**, encontre o elemento **allowedExternalRedirectUrls**, que deve ser nulo e altere-o para o seguinte:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Substitua as URLs na matriz de pelas URLs do serviço, que neste exemplo é `http://localhost:3000` para o serviço local de exemplo do Node.js. Você também pode usar `http://localhost:4400` para o serviço Ripple ou alguma outra URL, dependendo de como seu aplicativo é configurado.
    
3. Na parte superior da página, clique em **Leitura/Gravação** e, em seguida, clique em **PUT** para salvar as atualizações.

    Você ainda precisa adicionar as mesmas URLs de loopback para as configurações de lista branca do CORS:

4. No [Portal do Azure] no back-end do aplicativo móvel, clique em **Todas as Configurações** > **CORS**, adicione as URLs de loopback à lista branca e clique em **Salvar**.

Após a atualização do back-end, você poderá usar as novas URLs de loopback em seu aplicativo.

<!-- URLs. -->
[Início Rápido dos Aplicativos Móveis do Azure]: app-service-mobile-cordova-get-started.md
[Introdução à autenticação]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[SDK do JavaScript para os Aplicativos Móveis do Azure]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/pt-BR/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0629_2016-->