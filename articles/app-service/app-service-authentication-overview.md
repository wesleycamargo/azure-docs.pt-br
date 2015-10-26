<properties 
	pageTitle="Autenticação em Aplicativos de API do Serviço de Aplicativo do Azure e aplicativos móveis" 
	description="Saiba como configurar e usar a autenticação para aplicativos de API e aplicativos móveis no Serviço de Aplicativo do Azure." 
	services="app-service" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/22/2015" 
	ms.author="tdykstra"/>

# Autenticação para aplicativos de API e aplicativos móveis no Serviço de Aplicativo do Azure

## Visão geral

Este artigo explica os recursos de autenticação interna para [Aplicativos de API](../app-service-api/app-service-api-apps-why-best-platform.md) e [Aplicativos Móveis](../app-service-mobile/app-service-mobile-value-prop-preview.md).

A seção [Próximas etapas](#next-steps) no final do artigo fornece links para documentação de procedimentos relacionados.

## Gateway de Serviço de Aplicativo do Azure

O Serviço de Aplicativo do Azure oferece serviços de autenticação internos que implementam [OAuth 2.0](#oauth) e [OpenID Connect](#oauth) e trabalham com vários *provedores de identidade*. Um provedor de identidade é um serviço externo, no qual o Serviço de Aplicativo do Azure confia, que autentica usuários do seu aplicativo. O Serviço de Aplicativo oferece suporte para os provedores de identidade mais populares:

* Active Directory do Azure
* Conta da Microsoft
* Google
* Twitter
* Facebook

### Arquitetura do gateway

Qualquer grupo de recursos do Azure que contém aplicativos de API ou aplicativos móveis inclui um *gateway*. O gateway é um recurso do Azure executado em um aplicativo Web e manipula tarefas administrativas, incluindo a autenticação para aplicativos móveis e aplicativos de API no grupo de recursos.

O gateway trata procedimentos de logon, gerencia tokens e evita que as chamadas não autenticadas acessem aplicativos de API [configurados para exigir acesso autenticado](../app-service-api/app-service-api-dotnet-add-authentication.md#protect-the-api-app). O gateway pode controlar o acesso aos aplicativos de API porque todas as solicitações HTTP recebidas destinadas a aplicativos de API no grupo de recursos são roteadas através do gateway.

O diagrama a seguir ilustra essas funções de gateway.

![](./media/app-service-authentication-overview/gateway.png)

### Recursos de gateway

Os serviços de autenticação de gateway oferecem várias vantagens sobre executar sua própria implementação do OAuth 2.0:

* A Microsoft fornece SDKs que permitem executar tarefas de autenticação e autorização usando uma sintaxe simplificada.

* Uma vez que o Serviço de Aplicativo trata mais tarefas de autenticação, o tempo de desenvolvimento e teste é minimizado e você evita a maior parte ou todo o impacto das alterações nas implementações do provedor de OAuth.

* Se você tiver vários aplicativos para proteger e os mantiver em um grupo de recursos, você só precisa de um ID do cliente e o segredo do cliente para cada provedor de autenticação, pois há apenas uma URL de redirecionamento para o gateway.

* Monitoramento e solução de problemas são mais fáceis porque você pode monitorar o tráfego relacionado à autenticação para todo um grupo de recursos inteiro monitorando o gateway.

* A depuração é mais fácil porque você pode configurar um programa para usar o gateway durante a execução no modo de depuração localmente, e você não precisa alterar as URLs de redirecionamento na sua conta do provedor de identidade.

## Fluxo de servidor versus o fluxo de cliente

O gateway de Serviço de Aplicativo oferece duas maneiras de autenticar clientes: *fluxo de cliente* e *fluxo de servidor*. Em ambos os fluxos, o aplicativo cliente envia as credenciais do usuário (normalmente, o nome de usuário e a senha) diretamente ao provedor de identidade. Nem o gateway nem seu aplicativo recebe as credenciais do usuário em um fluxo.

### Fluxo de cliente

Fluxo de cliente significa que o aplicativo cliente se comunica diretamente com o provedor de identidade para obter um token de acesso do provedor. O aplicativo cliente envia o token de acesso do provedor para o gateway. O gateway cria e envia ao cliente um token de contexto de usuário. Esse token de contexto de usuário também é conhecido como um token Zumo, do nome de código original dos [Azure Mobile Services](/documentation/services/mobile-services/) (Serviços Móveis do Azure). (Serviços de autenticação para Aplicativos de API e Aplicativos Móveis são criados na mesma arquitetura originalmente desenvolvida para os Serviços Móveis).

O diagrama a seguir ilustra esse fluxo.

![](./media/app-service-authentication-overview/clientflow.png)

O cliente fornece o token Zumo em solicitações HTTP quando chama aplicativos de API ou Aplicativos Móveis protegidos. O gateway armazena os tokens de provedor e controla quais estão associados com quais tokens Zumo.

### Fluxo de servidor

Fluxo de servidor significa que o aplicativo cliente se baseia no gateway para se comunicar com o provedor de identidade para iniciar o logon. O navegador do cliente vai para uma URL de gateway, e o gateway redireciona a solicitação para a página de logon do provedor de identidade. Depois que o usuário fizer logon, o gateway obtém o token do provedor de identidade, cria o token Zumo e envia o token Zumo ao cliente.

![](./media/app-service-authentication-overview/serverflow.png)

A interação subsequente entre o aplicativo cliente e os aplicativos de API ou aplicativos móveis protegidos é tratada da mesma maneira que o fluxo de cliente: o cliente fornece o token Zumo em solicitações HTTP.

### Como escolher entre os fluxos de cliente e de servidor

O fluxo de cliente geralmente é a melhor opção se o provedor de identidade que você deseja usar tiver um SDK para a plataforma de cliente à qual você deseja oferecer suporte. O fluxo de cliente fornece a melhor experiência de usuário porque reduz o número de vezes que o usuário precisa inserir credenciais. Por exemplo, se o usuário tiver um dispositivo android, ele provavelmente terá uma conta do Google associada ao dispositivo, então, se ele puder usar essa conta sem precisar inserir novamente o nome de usuário e senha, isso possibilita uma melhor experiência ao usuário. O mesmo vale para uma conta do Facebook em um dispositivo Android, iOS ou Windows Phone ou uma conta da Microsoft em uma área de trabalho do Windows ou Windows Phone.

Em outros cenários, o fluxo de servidor pode ser uma opção melhor:

- Não há um SDK de cliente nativo para o provedor de identidade e a plataforma de cliente aos quais você deseja oferecer suporte.

- Você deseja colocar algo em produção rapidamente e melhorar a experiência do usuário conforme o tempo permitir mais tarde. O fluxo de servidor permite que o Serviço de Aplicativo do Azure faça mais do trabalho de autenticação, o que minimiza a quantidade de desenvolvimento e teste que você precisa fazer.

## Chamadas realizadas “em nome de” para plataformas SaaS

Você pode escrever código para fazer chamadas de saída para plataformas de Software-como um serviço (SaaS) em nome de um usuário conectado ou você pode usar um [aplicativo de API do conector](../app-service-mobile/app-service-logic-what-are-biztalk-api-apps.md). Por exemplo, para publicar uma tweet usando a conta do Twitter do usuário, você pode usar [um SDK do Twitter](https://dev.twitter.com/overview/api/twitter-libraries) ou provisionar um [Conector do Twitter](../app-service-mobile/app-service-logic-connector-twitter.md) na sua assinatura do Azure e chamá-lo. Esta seção é sobre como acessar uma plataforma SaaS por meio do código que é executado em um aplicativo de API ou aplicativo móvel.

### <a id="obotoidprovider"></a> Uso do token do provedor de identidade 

O gateway mantém um *repositório de token* no qual associa um token Zumo a um ou mais tokens de acesso do provedor de identidade e tokens de atualização. Quando uma solicitação HTTP com um token válido do Zumo é recebida, o gateway sabe qual tokens do provedor de identidade se referem a esse usuário.
  
Quando o código em execução em seu aplicativo de API ou aplicativo móvel precisar fazer uma chamada para um recurso protegido em nome do usuário conectado, ele pode recuperar e usar o token do provedor de identidade do repositório de token do gateway, conforme mostrado no diagrama a seguir. O diagrama supõe que o cliente já tenha sido autenticado com o gateway e tenha o token Zumo.

![](./media/app-service-authentication-overview/idprovidertoken.png)

Por exemplo, suponha que o provedor de identidade seja o AAD (Active Directory do Azure) e seu aplicativo de API deseje usar o token de acesso do AAD para chamar a Graph API do AAD ou solicitar acesso a um site do SharePoint para o qual o usuário tenha permissões. Você pode enviar uma solicitação para o gateway para recuperar o token AAD e usar o token AAD para chamar a Graph API ou obter um token de acesso para o site do SharePoint.

### <a id="obotosaas"></a>Obtenção do consentimento do usuário para acessar outros recursos

O gateway também tem recursos internos para obter o consentimento do usuário para acessar recursos protegidos por um provedor diferente do provedor de identidade original. Por exemplo, para um usuário que fez logon usando o Active Directory do Azure, você talvez queira acessar arquivos na conta do Dropbox do usuário.

O gateway do serviço de aplicativo inclui suporte interno para obter o consentimento do usuário para esse tipo de acesso dos seguintes provedores:

* Box
* DropBox
* Facebook
* Google
* Office365
* OneDrive
* QuickBooks
* Salesforce
* SharePointOnline
* Twitter
* Yammer
* Azure Active Directory
* Conta da Microsoft

Para esses fornecedores, o gateway mantém tokens de acesso e os associa o token Zumo, como o faz para o token de acesso do provedor de identidade. O processo de obter consentimento do usuário e chamar uma plataforma SaaS é ilustrado no diagrama a seguir. O diagrama supõe que o cliente já tenha sido autenticado com o gateway e tenha o token Zumo.

![](./media/app-service-authentication-overview/saastoken.png)

Os SDKs e o suporte a tempo de execução do Serviço de Aplicativo tornam relativamente fácil escrever código que acesse os recursos protegidos por um desses provedores. Há uma etapa preliminar não mostrada no diagrama: você deve configurar uma conta com o provedor e definir a ID de cliente do provedor e as configurações de segredo do cliente no Serviço de Aplicativo do Azure.

Para esses e muitos outros provedores, você também pode acessar recursos protegidos usando um [aplicativo de API do conector](../app-service-mobile/app-service-logic-what-are-biztalk-api-apps.md) pré-empacotado.

## Disponibilidade do SDK

Para aplicativos de API, o SDK para .NET fornece funcionalidade de autenticação:

* [Microsoft.Azure.AppService](http://www.nuget.org/packages/Microsoft.Azure.AppService) - para uso em um cliente de aplicativo de API.  
* [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/) - para uso em um projeto de API da Web executado em um aplicativo de API.
 
Além disso, o Visual Studio pode gerar automaticamente código que funciona com o SDK para .NET simplificar ainda mais o código que você escreve para chamar o aplicativo de API. Para obter mais informações, consulte [Consumir um aplicativo de API no Serviço de Aplicativo do Azure de um cliente .NET](../app-service-api/app-service-api-dotnet-consume.md).

Para Aplicativos Móveis, SDKs estão disponíveis para as seguintes plataformas:

- [.NET Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) 
- [iOS](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-preview.md)
- [Xamarin iOS](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md)
- [Xamarin Android](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview.md)
- [Windows](../app-service-mobile/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md)
- JavaScript (tutorial em desenvolvimento)

## Métodos de autenticação alternativos

Se os serviços de autenticação de gateway não atenderem às necessidades do seu aplicativo, você mesmo pode gerenciar a autenticação ou usar o serviço de Gerenciamento de API do Azure.

### <a id="doityourself"></a>Autenticação do tipo faça você mesmo

Você pode executar uma estrutura de autenticação como [Identidade ASP.NET](http://www.asp.net/identity) ou [Thinktecture](http://www.thinktecture.com/identityAndAccessControl) no Azure. Isso permite o controle sobre como tudo funciona, mas você também precisará dedicar mais tempo para desenvolver e testar a funcionalidade de autenticação. Além disso, se você tiver vários aplicativos para proteger com várias URLs de redirecionamento, precisa configurar vários IDs de cliente e segredos de cliente com provedores de autenticação de terceiros, como Facebook, Google, Twitter.

No momento, o Serviço de Aplicativo não oferece suporte ao uso de uma solução do tipo faça você mesmo junto com autenticação de gateway, como é possível em [Serviços Móveis](mobile-services-dotnet-backend-get-started-custom-authentication.md).

### <a id="apim"></a>Gerenciamento de API do Azure

Se você tiver as APIs que deseja proteger com a autenticação, pode fazer isso com o serviço de Gerenciamento de API do Azure. Para obter informações sobre como usar o Gerenciamento de API com aplicativos da API, consulte esta postagem de blog de Panos Kefalidis: [Aproveitando o Gerenciamento de API para Aplicativos de API](http://www.kefalidis.me/2015/06/taking-advantage-of-api-management-for-api-apps/)

## Próximas etapas

Este artigo explicou os serviços de autenticação fornecidos pelo Serviço de Aplicativo do Azure para Aplicativos de API e Aplicativos Móveis. Aqui estão alguns links para recursos para saber mais sobre os protocolos de autenticação subjacente e documentação sobre como usar os recursos de autenticação do Serviço de Aplicativo.

* [OAuth 2.0, OpenID Connect e JSON Web Tokens (JWT)](#oauth)
* Recursos de Aplicativos de API
	* [Fluxo de cliente de Aplicativos de API](#apiaclient)
	* [Fluxo de servidor de Aplicativos de API](#apiaserver)
	* [Chamadas em nome de Aplicativos de API](#apiaobo)
* Recursos de Aplicativos Móveis
	* [Fluxo de cliente de Aplicativos Móveis](#maclient)
	* [Fluxo de servidor de Aplicativos Móveis](#maserver)
	* [Chamadas “em nome de” dos Aplicativos Móveis](#maobo)

### <a id="oauth"></a>OAuth 2.0, OpenID Connect e JSON Web Tokens (JWT)

* [Introdução ao OAuth 2.0](http://shop.oreilly.com/product/0636920021810.do "Introdução ao OAuth 2.0") 
* [Introdução ao OAuth2, OpenID Connect e JSON Web Tokens (JWT) - PluralSight Course](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction) 
* [Criando e protegendo uma API RESTful para vários clientes no ASP.NET - PluralSight course](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

### <a id="apiaclient"></a>Fluxo de cliente de Aplicativos de API

* [Proteger um aplicativo de API](../app-service-api/app-service-api-dotnet-add-authentication.md) - A parte de configuração do aplicativo a API se aplica ao fluxo de cliente e servidor, mas a parte de teste no navegador ilustra o fluxo de servidor.
* [Consumir um aplicativo de API no Serviço de Aplicativo do Azure de um cliente .NET](../app-service-api/app-service-api-dotnet-consume.md) - O aplicativo de exemplo para uma chamada autenticada ilustra o fluxo de servidor, mas ele é seguido por uma seção de [fluxo de cliente](../app-service-api/app-service-api-dotnet-consume.md#client-flow) com o código de exemplo.

### <a id="apiaserver"></a>Fluxo de servidor de Aplicativos de API

* [Proteger um aplicativo de API](../app-service-api/app-service-api-dotnet-add-authentication.md) - A parte de configuração do aplicativo de API se aplica ao fluxo de cliente e de servidor, mas a parte de teste no navegador ilustra o fluxo de servidor.
* [Consumir um aplicativo de API no Serviço de Aplicativo do Azure por meio de um cliente .NET](../app-service-api/app-service-api-dotnet-consume.md) - o código de exemplo de uma chamada autenticada ilustra o fluxo de servidor. 

### <a id="apiaobo"></a>Chamadas em nome de Aplicativos de API

* [Implantar e configurar um aplicativo de API do conector de SaaS no Serviço de Aplicativo do Azure](../app-service-api/app-service-api-connnect-your-app-to-saas-connector.md) - ilustra como provisionar um aplicativo de API do conector pré-empacotados, configurá-lo e chamá-la usando ferramentas do navegador.
* [Conectar-se a uma plataforma SaaS a partir de um aplicativo de API ASP.NET no Serviço de Aplicativo do Azure](../app-service-api/app-service-api-dotnet-connect-to-saas.md) - ilustra como escrever seu próprios conector – ou seja, provisionar, configurar e escrever código para um aplicativo de API personalizado que faz chamadas em nome dele para um provedor SaaS.

### <a id="maclient"></a>Fluxo de cliente de Aplicativos Móveis

* [Adicionar logon único do Active Directory do Azure ao seu aplicativo iOS](../app-service-mobile/app-service-mobile-dotnet-backend-ios-aad-sso-preview.md)

### <a id="maserver"></a>Fluxo de servidor de Aplicativos Móveis

* [Adicione autenticação ao seu aplicativo do iOS](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-users-preview.md)
* [Adicionar autenticação ao aplicativo Xamarin.iOS](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md)
* [Adicione autenticação ao aplicativo Xamarin.Android](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-android-get-started-users-preview.md)
* [Adicionar autenticação ao seu aplicativo do Windows](../app-service-mobile/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md)

### <a id="maobo"></a>Chamadas “em nome de” para Aplicativos Móveis para recursos protegidos

* [Obter um token de acesso e chamar a API do SharePoint em um aplicativo móvel](../app-service-mobile/app-service-mobile-dotnet-backend-get-started-connect-to-enterprise.md#obtain-token)

<!---HONumber=Oct15_HO3-->