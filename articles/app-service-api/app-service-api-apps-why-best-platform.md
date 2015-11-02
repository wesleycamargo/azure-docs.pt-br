<properties 
	pageTitle="O que são aplicativos de API?" 
	description="Saiba por que o Serviço de Aplicativo do Azure é a melhor plataforma para desenvolver, publicar e hospedar APIs RESTful." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2015" 
	ms.author="tdykstra"/>

# O que são aplicativos de API?

Os Aplicativos de API fornecem uma plataforma rica para compilação, hospedagem e consumo de APIs na nuvem e localmente. Implante sua API como um aplicativo de API e beneficie-se da segurança de nível corporativo, do controle de acesso simples, conectividade híbrida e SaaS, geração automática do SDK e integração perfeita com [Aplicativos Lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md).

O recurso Aplicativos de API faz parte do [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md), que também inclui Aplicativos Web, Aplicativos Móveis e Aplicativos Lógicos.

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

## Por que aplicativos de API?

Aplicativos de API oferecem recursos para desenvolver, implantar, publicar, consumir e gerenciar APIs da Web RESTful. O Serviço de Aplicativo fornece os seguintes recursos disponíveis atualmente na exibição pública:

- **Consumo fácil** - o suporte integrado [Swagger](http://swagger.io/) torna suas APIs facilmente consumíveis por uma variedade de clientes. O SDK de aplicativos de API é capaz de gerar o código do cliente para suas APIs em uma variedade de linguagens incluindo C#, Java e Javascript.

- **Controle de acesso simples** - Os serviços internos de autenticação dão suporte ao Active Directory do Azure ou serviços de terceiros, como o Facebook e Twitter. Você pode proteger um aplicativo de API contra o acesso não autenticado sem fazer alterações ao seu código. Se você estiver familiarizado com os serviços de autenticação fornecidos pelo [Serviços Móveis do Azure](../mobile-services-windows-dotnet-how-to-use-client-library.md#authentication), os aplicativos de API são compilados nesse framework e o estendem a APIs hospedadas por Aplicativos de API. O SDK do Serviço de Aplicativo também permite que você use uma sintaxe simplificada como código de autorização. Para obter mais informações, consulte [Autenticação para aplicativos de API e aplicativos móveis no Serviço de Aplicativo do Azure](../app-service/app-service-authentication-overview.md).

- **Conexão fácil com plataformas SaaS** - [Aplicativos de API do conector](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md) no Azure Marketplace são fornecidos pela Microsoft e terceiros para simplificar o código que você escreve para interagir com SalesForce, Office 365, Twitter, Facebook, Dropbox e muitos outros.

- **Integração com Aplicativos lógicos** - Aplicativos de API criados por você podem ser consumidos por [Aplicativos lógicos do Serviço de Aplicativo](../app-service-logic/app-service-logic-what-are-logic-apps.md).

- **Integração do visual Studio** -Ferramentas dedicadas no Visual Studio simplificam o trabalho de [criar](app-service-dotnet-create-api-app.md), [implantar](app-service-dotnet-deploy-api-app.md), [depurar](app-service-dotnet-remotely-debug-api-app) e gerenciar aplicativos de API.

Você pode colocar sua API existente como ela é: você não precisa alterar nenhuma parte do código em suas APIs existentes para tirar proveito dos recursos do aplicativo de API, basta implantar seu código em um aplicativo de API. Você pode usar ASP.NET, Java, PHP, Node.js ou Python para suas APIs.

Além disso, os aplicativos de API incluem [recursos de Aplicativo Web do Serviço de Aplicativo](../app-service-web/app-service-web-overview.md).

>[AZURE.NOTE] [Azure API Management](/services/api-management/) é um serviço separado que oferece recursos como a otimização e consolidação de pontos de extremidade. Você pode usar o Gerenciamento de API com Aplicativos de API.
>
>Aplicativos de API estão atualmente em visualização pública. Os [Aplicativos Web do Serviço de Aplicativo](../app-service-web/app-service-web-overview.md) são um serviço GA (Geralmente Disponível) destinado à compilação e hospedagem de aplicativos críticos seguros em escala global. Se você estiver procurando por um serviço GA para compilação de sua API hoje, os aplicativos Web são uma ótima opção. Quando os Aplicativos de API ficarem disponíveis, forneceremos um caminho para usar os aplicativos Web existentes e aproveitar os recursos adicionais dos Aplicativos de API.

## Conceitos de aplicativos de API ##

- **Gateway** - Um aplicativo Web que lida com funções de administração de API e autenticação para todos os aplicativos de API em um grupo de recursos. 
- **Swagger** - Uma estrutura para a documentação interativa e descoberta de uma API RESTful, usada por padrão em aplicativos de API. Para obter mais informações, consulte [http://swagger.io/](http://swagger.io/).
- **Conector** - Um tipo de aplicativo de API que facilita a conexão com plataformas de SaaS como Salesforce e Office 365. Para obter mais informações, consulte [O que são conectores e aplicativos de API do BizTalk](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md).
- **Gatilho** - A API REST que os [aplicativos lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md) podem chamar para iniciar um processo de fluxo de trabalho quando determinada condição é atendida. Por exemplo, um aplicativo de API pode fornecer um método que o aplicativo lógico chama periodicamente para procurar uma frase determinada em um feed do Twitter. Para obter mais informações, consulte [Gatilhos de aplicativo de API](app-service-api-dotnet-triggers.md).
- **Ação** - Uma API REST que [aplicativos lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md) podem chamar para processar dados depois que um fluxo de trabalho foi iniciado por um gatilho. Por exemplo, um aplicativo de API pode fornecer um método que chama o aplicativo lógico para responder a um tweet encontrada pelo gatilho Twitter. Ações são métodos de API expostos por uma definição de API de Swagger.

## Introdução

Para começar a usar os aplicativos de API, siga [Criar um tutorial de aplicativo de API](app-service-dotnet-create-api-app.md).

Para ver uma lista de problemas conhecidos com aplicativos de API, consulte [este artigo no fórum do MSDN](https://social.msdn.microsoft.com/Forums/pt-BR/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps).

Para obter mais informações sobre a plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md).

 

<!---HONumber=Oct15_HO4-->