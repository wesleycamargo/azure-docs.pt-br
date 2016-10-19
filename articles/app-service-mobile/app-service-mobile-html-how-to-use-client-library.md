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
	ms.date="09/12/2016"
	ms.author="adrianha;ricksal"/>

# Como usar a biblioteca de cliente JavaScript para os Aplicativos Móveis do Azure

[AZURE.INCLUDE [App-Service-Mobile-Selector-Client-Library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia ensina a executar cenários comuns usando o mais recente [SDK do JavaScript para os Aplicativos Móveis do Azure]. Se não estiver familiarizado com os Aplicativos Móveis do Azure, primeiro conclua o [Início Rápido dos Aplicativos Móveis do Azure] para criar um back-end e uma tabela. Neste guia, vamos nos concentrar no uso do back-end móvel em aplicativos Web em HTML/JavaScript.

## Plataformas com suporte

Limitamos o suporte de navegador às versões atuais e mais recentes dos principais navegadores: Google Chrome, Microsoft Edge, Microsoft Internet Explorer e Mozilla Firefox. Esperamos que o SDK funcione com todos os navegadores relativamente modernos.

O pacote é distribuído como um Módulo de JavaScript Universal e, portanto, ele dá suporte aos formatos AMD, CommonJS e globais.

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

O Serviço de Aplicativo do Azure oferece suporte à autenticação e autorização de usuários de aplicativos usando vários provedores de identidade externos: Facebook, Google, Conta da Microsoft e Twitter. Você pode definir permissões em tabelas para restringir o acesso a operações específicas apenas para usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização em scripts do servidor. Para obter mais informações, consulte o tutorial [Introdução à autenticação].

Dois fluxos de autenticação são suportados: um server flow e um client flow. O fluxo de servidor fornece a experiência de autenticação mais simples, pois depende da interface de autenticação da web do provedor. O fluxo de cliente permite uma integração mais profunda com funcionalidades específicas do dispositivo, como logon único, uma vez que depende de SDKs específicos do provedor.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>Como configurar o Serviço de Aplicativo Móvel para URLs de redirecionamento externo.

Vários tipos de aplicativo JavaScript usam uma funcionalidade de loopback para manipular fluxos de interface do usuário do OAuth. Esses recursos incluem:

* Executar o serviço localmente
* Usar o Live Reload com o Ionic Framework
* Redirecionar para o Serviço de Aplicativo para autenticação.

A execução local pode causar problemas porque, por padrão, a autenticação de Serviço de Aplicativo só está configurada para permitir o acesso do back-end do Aplicativo Móvel. Use as seguintes etapas para alterar as configurações de Serviço de Aplicativo de modo a habilitar a autenticação ao executar o servidor localmente:

1. Faça logon no [Portal do Azure]
2. Navegue até o back-end do Aplicativo Móvel.
3. Escolha **Gerenciador de recursos** no menu **FERRAMENTAS DE DESENVOLVIMENTO**.
4. Clique em **Ir** para abrir o gerenciador de recursos para o back-end do Aplicativo Móvel em uma nova janela ou guia.
5. Expanda o nó **config** > **authsettings** do seu aplicativo.
6. Clique no botão **Editar** para habilitar a edição do recurso.
7. Encontre o elemento **allowedExternalRedirectUrls**, que deve ser nulo. Adicione as URLs em uma matriz:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Substitua as URLs na matriz pelas URLs do serviço, que neste exemplo é `http://localhost:3000` para o serviço local de exemplo do Node.js. Você também pode usar `http://localhost:4400` para o serviço Ripple ou alguma outra URL, dependendo de como seu aplicativo é configurado.

8. Na parte superior da página, clique em **Leitura/Gravação** e clique em **PUT** para salvar as atualizações.

Você também precisa adicionar as mesmas URLs de loopback às configurações de lista branca do CORS:

1. Navegue até o [Portal do Azure].
2. Navegue até o back-end do Aplicativo Móvel.
3. Clique em **CORS** no menu **API**.
4. Insira cada URL na caixa de texto **Origens Permitidas** vazia. Uma nova caixa de texto é criada.
5. Clique em **SALVAR**
    
Após a atualização do back-end, você poderá usar as novas URLs de loopback em seu aplicativo.

<!-- URLs. -->
[Início Rápido dos Aplicativos Móveis do Azure]: app-service-mobile-cordova-get-started.md
[Introdução à autenticação]: app-service-mobile-cordova-get-started-users.md
[Adicionar autenticação ao seu aplicativo]: app-service-mobile-cordova-get-started-users.md

[Portal do Azure]: https://portal.azure.com/
[SDK do JavaScript para os Aplicativos Móveis do Azure]: https://www.npmjs.com/package/azure-mobile-apps-client
[documentação do objeto Query]: https://msdn.microsoft.com/pt-BR/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0928_2016-->