<properties 
	pageTitle="Introdução ao ambiente de Serviço de Aplicativo" 
	description="Saiba mais sobre o recurso de ambiente de serviço de aplicativo que fornece unidades de escala seguras, dedicadas e unidas por VNet para executar todos os seus aplicativos." 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="stefsh"/>

# Introdução ao ambiente de Serviço de Aplicativo

## Visão geral ##
Um ambiente de Serviço de Aplicativo é uma opção de plano de serviço [Premium][PremiumTier] do Serviço de Aplicativo do Azure que fornece um ambiente totalmente isolado e dedicado a executar com segurança todos os seus aplicativos. Isso inclui [aplicativos Web][WebApps], [aplicativos móveis][MobileApps], [aplicativos de API][APIApps] e [aplicativos lógicos][LogicApps], com opções de dimensionamento expandidas.

Recursos de computação para um ambiente de serviço de aplicativo são dedicados exclusivamente a executar somente seus aplicativos. Um ambiente de serviço de aplicativo sempre é criado em uma rede virtual regional, que fornece a seus aplicativos novas opções para isolamento de rede. Além disso, um ambiente de serviço de aplicativo oferece suporte a opções adicionais de dimensionamento, com até cinquenta \(50\) recursos de computação disponíveis para executar seus aplicativos. Fora de um ambiente de serviço de aplicativo, há um limite de 20 recursos de computação para hospedar seus aplicativos.

## Suporte a Rede Virtual ##
Um ambiente de serviço de aplicativo podem ser criado em uma rede virtual regional já existente ou uma nova rede virtual regional \([obter mais informações sobre redes virtuais][MoreInfoOnVirtualNetworks]\). Como um ambiente de serviço de aplicativo sempre existe em uma rede virtual regional, mais precisamente em uma sub-rede de uma rede virtual regional, você pode aproveitar os recursos de segurança de redes virtuais para controlar tanto a comunicação de rede de entrada quanto a de saída.

Você pode usar [grupos de segurança de rede][NetworkSecurityGroups] para restringir a comunicação de rede de entrada à sub-rede na qual reside um ambiente de serviço de aplicativo. Isso permite que você execute aplicativos por trás de dispositivos e serviços upstream, como firewalls de aplicativo Web e provedores SaaS de rede.

Aplicativos frequentemente precisam acessar recursos corporativos, como bancos de dados internos e serviços da Web. Uma abordagem comum é disponibilizar esses pontos de extremidade apenas ao tráfego de rede interno que flui dentro de uma rede virtual do Azure. Depois que um ambiente de serviço de aplicativo é combinado à mesma rede virtual que os serviços internos, os aplicativos que são executados no ambiente podem acessá-los, incluindo pontos de extremidade acessíveis via conexões [Site a Site][SiteToSite] e [Rota Expressa do Azure][ExpressRoute].

## Recursos de computação dedicados ##
Todos os recursos de computação em um ambiente de serviço de aplicativo são dedicados exclusivamente a uma única assinatura. Um ambiente de Serviço de Aplicativo é composto de um pool de recursos de computação único de front-end, bem como um a três pools de recursos de computação de trabalho.

O pool de front-end contém recursos de computação responsáveis pela terminação SSL, bem como balanceamento automático de carga de solicitações do aplicativo em um ambiente de serviço de aplicativo.

Cada pool de trabalho contém recursos de computação alocados para [planos de serviço do aplicativo][AppServicePlan], que por sua vez contêm um ou mais aplicativos de serviço de aplicativo do Azure. Como pode haver até três pools de trabalho diferentes em um ambiente de serviço de aplicativo, você tem a flexibilidade de escolher os recursos de computação diferentes para cada pool de trabalho.

Por exemplo, isso permite a criação de um pool de trabalho com menos recursos de computação poderosos para planos de serviço de aplicativo destinados a aplicativos de desenvolvimento ou teste. Um segundo \(ou até mesmo terceiro\) pool de trabalho de pode usar recursos de computação mais poderosos para planos de serviço de aplicativo executando aplicativos de produção.

Um ambiente de serviço de aplicativo pode ser configurado com até cinquenta \(50\) recursos de computação em um único pool de trabalho. Para obter mais detalhes sobre a quantidade de recursos de computação disponíveis para os pools de front-end e de trabalho, consulte [Como configurar um ambiente de serviço de aplicativo][HowToConfigureanAppServiceEnvironment].

Para obter detalhes sobre os tamanhos de recursos de computação disponíveis com suporte em um determinado ambiente de serviço de aplicativo, consulte a página [Preços de serviços de aplicativo][AppServicePricing] e revise as opções disponíveis para ambientes de serviço de aplicativo na camada de preços Premium.


## Introdução

Para se familiarizar com os Ambientes de Serviço de Aplicativo, consulte [Como Criar um Ambiente de Serviço de Aplicativo][HowToCreateAnAppServiceEnvironment]

Para obter mais informações sobre a plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure][AzureAppService].

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AzurePortal]: http://portal.azure.com
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[NetworkSecurityGroups]: https://msdn.microsoft.com/library/azure/dn848316.aspx
[SiteToSite]: https://msdn.microsoft.com/library/azure/dn133795.aspx
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/TBD/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/

<!-- IMAGES -->


<!--HONumber=52-->
