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
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/06/2015"
	ms.author="stefsch"/>

# Introdução ao ambiente de Serviço de Aplicativo

## Visão geral ##
Um Ambiente de Serviço de Aplicativo é uma opção de plano de serviço [Premium][PremiumTier] do Serviço de Aplicativo do Azure que fornece um ambiente totalmente isolado e dedicado para executar com segurança todos os seus aplicativos do Serviço de Aplicativo do Azure em alta escala, incluindo [Aplicativos Web][WebApps], [Aplicativos Móveis][MobileApps] e [Aplicativos de API][APIApps].

Os Ambientes de Serviço de Aplicativo são ideais para cargas de trabalho de aplicativos que exigem:

- Escala muito alta
- Isolamento e acesso seguro à rede

Os clientes podem criar vários Ambientes de Serviço de Aplicativo dentro de uma única região do Azure, bem como entre várias regiões do Azure. Isso faz dos Ambientes de Serviço de Aplicativo ideais para dimensionar horizontalmente camadas de aplicativo sem estado para dar suporte a cargas de trabalho RPS altas.

Ambientes de Serviço de Aplicativo são isolados para executar somente aplicativos de um único cliente, e sempre são implantados em uma rede virtual. Os clientes têm um controle refinado sobre o tráfego de entrada e saída da rede de aplicativos, e os aplicativos podem estabelecer conexões seguras de alta velocidade por meio de redes virtuais com recursos corporativos locais.

Para obter uma visão geral sobre como os Ambientes de Serviço de Aplicativo permitem expansão e acesso seguro à rede, consulte [Aprofundamento no AzureCon][AzureConDeepDive] em Ambientes de Serviço de Aplicativo!

Para uma análise aprofundada sobre a expansão horizontal usando vários Ambientes de Serviço de Aplicativo, consulte o artigo sobre como configurar uma [Pegada de aplicativo distribuído geograficamente][GeodistributedAppFootprint].

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Recursos de computação dedicados ##
Todos os recursos de computação em um Ambiente de Serviço de Aplicativo são dedicados exclusivamente a uma única assinatura, e um Ambiente de Serviço de Aplicativo pode ser configurado com até 50 (cinquenta) recursos de computação para uso exclusivo por um único aplicativo.

Um Ambiente de Serviço de Aplicativo é composto de um pool de recursos de computação de front-end, bem como um a três pools de recursos de computação de trabalho.

O pool de front-end contém recursos de computação responsáveis pela terminação SSL, bem como balanceamento automático de carga de solicitações do aplicativo em um ambiente de serviço de aplicativo.

Cada pool de trabalho contém recursos de computação alocados para [planos de serviço do aplicativo][AppServicePlan], que por sua vez contêm um ou mais aplicativos de serviço de aplicativo do Azure. Como pode haver até três pools de trabalho diferentes em um ambiente de serviço de aplicativo, você tem a flexibilidade de escolher os recursos de computação diferentes para cada pool de trabalho.

Por exemplo, isso permite a criação de um pool de trabalho com menos recursos de computação poderosos para planos de serviço de aplicativo destinados a aplicativos de desenvolvimento ou teste. Um segundo (ou até mesmo terceiro) pool de trabalho de pode usar recursos de computação mais poderosos para planos de serviço de aplicativo executando aplicativos de produção.

Para obter mais detalhes sobre a quantidade de recursos de computação disponíveis para os pools de front-end e de trabalho, consulte [Como configurar um ambiente de serviço de aplicativo][HowToConfigureanAppServiceEnvironment].

Para obter detalhes sobre os tamanhos de recursos de computação disponíveis com suporte em um determinado ambiente de serviço de aplicativo, consulte a página [Preços de serviços de aplicativo][AppServicePricing] e revise as opções disponíveis para ambientes de serviço de aplicativo na camada de preços Premium.

## Suporte a Rede Virtual ##
Um Ambiente de Serviço de Aplicativo pode ser criado em uma rede virtual regional clássica "v1" existente ou uma nova rede virtual regional clássica "v1" ([obter mais informações sobre redes virtuais][MoreInfoOnVirtualNetworks]). Como um ambiente de serviço de aplicativo sempre existe em uma rede virtual regional, mais precisamente em uma sub-rede de uma rede virtual regional, você pode aproveitar os recursos de segurança de redes virtuais para controlar tanto a comunicação de rede de entrada quanto a de saída.

Você pode usar [grupos de segurança de rede][NetworkSecurityGroups] para restringir a comunicação de rede de entrada à sub-rede na qual reside um ambiente de serviço de aplicativo. Isso permite que você execute aplicativos por trás de dispositivos e serviços upstream, como firewalls de aplicativo Web e provedores SaaS de rede.

Aplicativos frequentemente precisam acessar recursos corporativos, como bancos de dados internos e serviços da Web. Uma abordagem comum é disponibilizar esses pontos de extremidade apenas ao tráfego de rede interno que flui dentro de uma rede virtual do Azure. Depois que um ambiente de serviço de aplicativo é combinado à mesma rede virtual que os serviços internos, os aplicativos que são executados no ambiente podem acessá-los, incluindo pontos de extremidade acessíveis via conexões [Site a Site][SiteToSite] e [Rota Expressa do Azure][ExpressRoute].

Para obter mais detalhes sobre o funcionamento dos Ambientes de Serviço de Aplicativo com redes virtuais e redes locais, consulte os seguintes artigos sobre [Arquitetura de rede][NetworkArchitectureOverview], [Controle do tráfego de entrada][ControllingInboundTraffic] e [Conexão segura com back-ends][SecurelyConnectingToBackends].

**Observação:** um Ambiente do Serviço de Aplicativo não pode ser criado em uma rede virtual "v2".

## Introdução

Para se familiarizar com os Ambientes de Serviços de Aplicativo, consulte [Como criar um Ambiente de Serviço de Aplicativo][HowToCreateAnAppServiceEnvironment]

Para obter mais informações sobre a plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure][AzureAppService].

Para obter uma visão geral da arquitetura de rede do Ambiente de Serviço de Aplicativo, consulte o artigo [Visão geral da arquitetura de rede][NetworkArchitectureOverview].

Para obter detalhes sobre como usar um Ambiente de Serviço de Aplicativo com o ExpressRoute, consulte o seguinte artigo sobre a [Rota Expressa e Ambientes de Serviço de Aplicativo][NetworkConfigDetailsForExpressRoute].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[Azure preview portal]: http://portal.azure.com
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]: https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[ControllingInboundTraffic]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SecurelyConnectingToBackends]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NetworkArchitectureOverview]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/

<!-- IMAGES -->

<!---HONumber=Oct15_HO3-->