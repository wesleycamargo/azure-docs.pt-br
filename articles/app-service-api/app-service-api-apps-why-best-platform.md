<properties 
	pageTitle="Visão geral de Aplicativos de API" 
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

# Visão geral de Aplicativos de API

Os Aplicativos de API são um dos quatro tipos de aplicativos oferecidos pelo [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md).

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

Os Aplicativos de API fornecem uma plataforma rica para compilação, hospedagem e consumo de APIs na nuvem e localmente. Implante sua API como um aplicativo de API no Serviço de Aplicativo e beneficie-se da segurança de nível corporativo, controle de acesso simples, conectividade híbrida, geração automática do SDK e integração perfeita com [Aplicativos Lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md).

## Por que aplicativos de API?

Os Aplicativos de API fornecem os seguintes recursos disponíveis atualmente na visualização pública:

- **Consumo fácil** - o suporte integrado [Swagger](#concepts) torna suas APIs facilmente consumíveis por uma variedade de clientes. Configure facilmente [CORS](#concepts) e gere automaticamente o código do cliente para suas APIs em uma variedade de linguagens, incluindo C#, Java e Javascript.

- **Controle de acesso simples** - Você pode proteger um aplicativo de API contra o acesso não autenticado sem fazer alterações ao seu código. Serviços de autenticação internos protegem as APIs para acesso por outros serviços ou por clientes que representam usuários. Os provedores de identidade com suporte incluem o Active Directory do Azure e provedores de terceiros, como Facebook e Twitter. Os clientes podem usar a ADAL (Biblioteca de Autenticação do Active Directory) ou o SDK de Aplicativos Móveis. Para obter mais informações, consulte [Autenticação para aplicativos de API e aplicativos móveis no Serviço de Aplicativo do Azure](../app-service/app-service-authentication-overview.md).

- **Integração do visual Studio** -Ferramentas dedicadas no Visual Studio simplificam o trabalho de criar, implantar, consumir, depurar e gerenciar aplicativos de API.

- **Integração com Aplicativos lógicos** - Aplicativos de API criados por você podem ser consumidos por [Aplicativos lógicos do Serviço de Aplicativo](../app-service-logic/app-service-logic-what-are-logic-apps.md).

- **Coloque sua API existente como ela está** - Você não precisa alterar nenhum código em suas APIs existentes para tirar proveito dos recursos de Aplicativos de API. Basta implantar seu código em um aplicativo de API. Sua API pode usar qualquer linguagem ou estrutura com suporte do Serviço de Aplicativo, incluindo ASP.NET e C#, Java, PHP, Node.js e Python.

Esses são apenas alguns destaques. Para saber quais são outros recursos que os Aplicativos de API podem aproveitar, consulte a [Visão geral de Aplicativos Web](../app-service-web/app-service-web-overview.md).

>[AZURE.NOTE]Você pode usar o [Gerenciamento de API do Azure](../api-management/api-management-key-concepts.md) para controlar o acesso de cliente às APIs que são hospedadas por Aplicativos de API do Serviço de Aplicativo. Embora os Aplicativos de API forneçam serviços de autenticação, há outros recursos de gerenciamento de acesso que eles não oferecem e que o Gerenciamento de API oferece, como consolidação de ponto de extremidade e limitação.
>
>Aplicativos de API estão atualmente em visualização pública. Os [Aplicativos Web do Serviço de Aplicativo](../app-service-web/app-service-web-overview.md) são um serviço GA (Geralmente Disponível) destinado à compilação e hospedagem de aplicativos críticos seguros em escala global. Se você estiver procurando por um serviço GA para compilação de sua API hoje, os aplicativos Web são uma ótima opção. Quando os Aplicativos de API ficarem disponíveis, forneceremos um caminho para usar os aplicativos Web existentes e aproveitar os recursos adicionais dos Aplicativos de API.

## Conceitos de aplicativos de API ##

- **Swagger** - Uma estrutura para a documentação e descoberta de uma API RESTful, usada por padrão em aplicativos de API. Para obter mais informações, consulte [http://swagger.io/](http://swagger.io/).
- **CORS (Compartilhamento de Recursos entre Origens)** - Um mecanismo que permite que o JavaScript executado em um navegador faça chamadas para uma API hospedada em um domínio diferente daquele do qual a página da Web foi carregada.
- **Gatilho** - A API REST que os [aplicativos lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md) podem chamar para iniciar um processo de fluxo de trabalho quando determinada condição é atendida. Por exemplo, um aplicativo de API pode fornecer um método que o aplicativo lógico chama periodicamente para procurar uma frase determinada em um feed do Twitter. Para obter mais informações, consulte [Gatilhos de aplicativo de API](app-service-api-dotnet-triggers.md).
- **Ação** - Uma API REST que [aplicativos lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md) podem chamar para processar dados depois que um fluxo de trabalho foi iniciado por um gatilho. Por exemplo, um aplicativo de API pode fornecer um método que chama o aplicativo lógico para responder a um tweet encontrada pelo gatilho Twitter. Ações são métodos de API expostos por uma definição de API de Swagger.
- **Gateway** - Um aplicativo Web que lida com funções de administração de API e autenticação para todos os aplicativos de API em um grupo de recursos.

## Introdução

Para começar a usar os aplicativos de API, siga [Criar um tutorial de aplicativo de API](app-service-dotnet-create-api-app.md).

Para ver uma lista de problemas conhecidos dos Aplicativos de API, confira [a postagem no fórum sobre problemas conhecidos dos Aplicativos de API](https://social.msdn.microsoft.com/Forums/pt-BR/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps).

Para obter mais informações sobre a plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md).

 

<!---HONumber=Nov15_HO2-->