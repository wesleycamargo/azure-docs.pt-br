<properties 
	pageTitle="Visão geral de Aplicativos de API" 
	description="Saiba por que o Serviço de Aplicativo do Azure é a melhor plataforma para desenvolver, publicar e hospedar APIs RESTful." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/30/2015" 
	ms.author="tdykstra"/>

# Visão geral de Aplicativos de API

Os Aplicativos de API são um dos quatro tipos de aplicativos oferecidos pelo [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md).

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

O [Serviço de Aplicativo](../app-service/app-service-value-prop-what-is.md) é uma plataforma totalmente gerenciada que proporciona um avançado conjunto de recursos para cenários da Web, móveis e de integração. Os aplicativos de API no Serviço de Aplicativo oferecem recursos que tornam mais fácil criar, hospedar e consumir APIs na nuvem e no local. Implante sua API como um aplicativo de API no Serviço de Aplicativo e beneficie-se da segurança de nível corporativo, controle de acesso simples, conectividade híbrida, geração automática do SDK e integração perfeita com [Aplicativos Lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md).

## Por que aplicativos de API?

Os aplicativos de API oferecem os seguintes recursos:

- **Consumo fácil** - O suporte integrado para [Metadados de API do Swagger](#concepts) torna suas APIs facilmente consumíveis por uma variedade de clientes. Geração automática de código do cliente para suas APIs em uma variedade de linguagens incluindo C#, Java e Javascript. Configure facilmente [CORS](#concepts) sem alterar seu código. Para obter mais informações, consulte [Metadados de aplicativos de API para geração de código e descoberta de API](app-service-api-metadata.md) e [Consumir um aplicativo de API a partir do JavaScript usando CORS](app-service-api-cors-consume-javascript.md). 

- **Controle de acesso simples** - Protege um aplicativo de API contra o acesso não autenticado sem fazer alterações ao seu código. Serviços de autenticação internos protegem as APIs para acesso por outros serviços ou por clientes que representam usuários. Os provedores de identidade com suporte incluem o Active Directory do Azure e provedores de terceiros, como Facebook e Twitter. Os clientes podem usar a ADAL (Biblioteca de Autenticação do Active Directory) ou o SDK de Aplicativos Móveis. Para obter mais informações, consulte [Expandindo a autenticação / autorização de Serviço de aplicativo](/blog/announcing-app-service-authentication-authorization/) e [Aplicativos de API do Serviço de Aplicativo - O que mudou](app-service-api-whats-changed.md).

- **Integração do Visual Studio** -Ferramentas dedicadas no Visual Studio simplificam o trabalho de criação, implantação, consumo, depuração e gerenciamento de aplicativos de API. Para obter mais informações, consulte [Anunciando o SDK 2.8.1 do Azure para .NET](/blog/announcing-azure-sdk-2-8-1-for-net/).

- **Integração com Aplicativos lógicos** - Aplicativos de API criados por você podem ser consumidos por [Aplicativos lógicos do Serviço de Aplicativo](../app-service-logic/app-service-logic-what-are-logic-apps.md). Aprenda como em [Usando a API personalizada hospedada no Serviço de Aplicativo com Aplicativos Lógicos](../app-service-logic/app-service-logic-custom-hosted-api.md). Para obter informações sobre mudanças contínuas sobre como os aplicativos de API se integram com aplicativos lógicos, consulte [Aplicativos de API do Serviço de Aplicativo - O que mudou](app-service-api-whats-changed.md).

- **Coloque sua API existente como ela está** - Você não precisa alterar nenhum código em suas APIs existentes para tirar proveito dos recursos de Aplicativos de API. Basta implantar seu código em um aplicativo de API. Sua API pode usar qualquer linguagem ou estrutura com suporte do Serviço de Aplicativo, incluindo ASP.NET e C#, Java, PHP, Node.js e Python.

Além disso, os recursos oferecidos por Aplicativos de API, Aplicativos Web e Aplicativos Móveis são intercambiáveis. Isso significa que uma instância de Aplicativos de API pode tirar proveito dos recursos para desenvolvimento e hospedagem web e móvel que Aplicativos Web e Aplicativos Móveis oferecem. O inverso também é verdadeiro: por exemplo, você pode usar um aplicativo web para hospedar uma API e ainda tirar proveito dos metadados Swagger para geração de código de cliente e CORS para acesso de domínio cruzado de navegador. Para obter mais informações, consulte [Visão geral de aplicativos Web](../app-service-web/app-service-web-overview.md) e [Visão geral de aplicativos móveis](../app-service-mobile/app-service-mobile-value-prop.md).

>[AZURE.NOTE]Você pode usar o [Gerenciamento de API do Azure](../api-management/api-management-key-concepts.md) para controlar o acesso de cliente às APIs que são hospedadas por Aplicativos de API do Serviço de Aplicativo. Embora os Aplicativos de API forneçam serviços de autenticação, há outros recursos de gerenciamento de acesso que eles não oferecem e que o Gerenciamento de API oferece, como consolidação de ponto de extremidade e limitação.

## Conceitos de aplicativos de API ##

- **Swagger** - Uma estrutura para a documentação e descoberta de uma API RESTful usada por padrão em Aplicativos de API. Para obter mais informações, consulte [http://swagger.io/](http://swagger.io/).
- **CORS (Compartilhamento de Recursos entre Origens)** - um mecanismo que permite que o JavaScript executado em um navegador faça chamadas para uma API hospedada em um domínio diferente daquele do qual a página da Web foi carregada. Para obter mais informações, consute [Consumir um aplicativo de API do JavaScript usando CORS](app-service-api-cors-consume-javascript.md). 
- **Gatilho** - A API REST que os [aplicativos lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md) podem chamar para iniciar um processo de fluxo de trabalho quando determinada condição é atendida. Por exemplo, um aplicativo de API pode fornecer um método que o aplicativo lógico chama periodicamente para procurar uma frase determinada em um feed do Twitter. Para obter mais informações, consulte [Gatilhos de aplicativo de API](app-service-api-dotnet-triggers.md).
- **Ação** - Uma API REST que [aplicativos lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md) podem chamar para processar dados depois que um fluxo de trabalho foi iniciado por um gatilho. Por exemplo, um aplicativo de API pode fornecer um método que chama o aplicativo lógico para responder a um tweet encontrada pelo gatilho Twitter. Ações são métodos de API expostos por uma definição de API de Swagger.

## Introdução

Para começar a usar os aplicativos de API, siga o tutorial [Introdução a Aplicativos de API](app-service-api-dotnet-get-started.md).

Para ver uma lista de problemas conhecidos dos Aplicativos de API, confira [a postagem no fórum sobre problemas conhecidos dos Aplicativos de API](https://social.msdn.microsoft.com/Forums/pt-BR/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps).

Para obter mais informações sobre a plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md).

<!---HONumber=AcomDC_1210_2015-->