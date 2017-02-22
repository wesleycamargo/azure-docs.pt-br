---
title: "Ambiente do Serviço de Aplicativo | Microsoft Docs"
description: "O que é um Ambiente do Serviço de Aplicativo do Azure? Uma introdução ao Ambiente do Serviço de Aplicativo."
keywords: "ambiente do serviço de aplicativo do azure, rede virtual, rede segura"
services: app-service
documentationcenter: 
author: stefsch
manager: wpickett
editor: 
ms.assetid: 1db5c057-3c56-4537-b580-cdd21fe3f3a7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: stefsch
translationtype: Human Translation
ms.sourcegitcommit: df2fa1d0790f8942a0350f9734232cc4b25cadc1
ms.openlocfilehash: a54f559801e1a4f4b752bc2268ea8d6cb20b1267


---
# <a name="app-service-environment-documentation"></a>Documentação do Ambiente de Serviço de Aplicativo
Um Ambiente de Serviço de Aplicativo é uma opção de plano de serviço [Premium][PremiumTier] do Serviço de Aplicativo do Azure que fornece um ambiente totalmente isolado e dedicado para executar com segurança todos os seus aplicativos do Serviço de Aplicativo do Azure em alta escala, incluindo [Aplicativos Web][WebApps], [Aplicativos Móveis][MobileApps] e [Aplicativos de API][APIApps].  

Os Ambientes de Serviço de Aplicativo são ideais para cargas de trabalho de aplicativos que exigem:

* Escala muito alta
* Isolamento e acesso seguro à rede

Os clientes podem criar vários Ambientes de Serviço de Aplicativo dentro de uma única região do Azure, bem como entre várias regiões do Azure.  Isso faz dos Ambientes de Serviço de Aplicativo ideais para dimensionar horizontalmente camadas de aplicativo sem estado para dar suporte a cargas de trabalho RPS altas.

Ambientes de Serviço de Aplicativo são isolados para executar somente aplicativos de um único cliente, e sempre são implantados em uma rede virtual.  Os clientes têm controle refinado sobre o tráfego de rede do aplicativo de entrada e saída usando os [grupos de segurança de rede][NetworkSecurityGroups].  Os aplicativos também podem estabelecer conexões seguras de alta velocidade por redes virtuais para recursos corporativos locais.

Os aplicativos muitas vezes precisam acessar recursos corporativos, como bancos de dados internos e serviços Web.  Os aplicativos em execução em Ambientes de Serviço de Aplicativo podem acessar os recursos acessíveis via conexões VPN [Site a Site][SiteToSite] e [Azure ExpressRoute][ExpressRoute].

* [O que é um Ambiente do Serviço de Aplicativo?](../app-service-web/app-service-app-service-environment-intro.md)
* [Criando um Ambiente do Serviço de Aplicativo](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Criando Aplicativos em um Ambiente do Serviço de Aplicativo](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Criando e usando um Balanceador de Carga Interno com Ambientes do Serviço de Aplicativo](../app-service-web/app-service-environment-with-internal-load-balancer.md)
* [Configurando um Ambiente do Serviço de Aplicativo](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [Dimensionando aplicativos em um Ambiente do Serviço de Aplicativo](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [Arquitetura e Segurança de Rede](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## <a name="how-tos"></a>Instruções
[!INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]

## <a name="videos"></a>vídeos
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2016/BRK3205/player]

>[!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON325/player]

>[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3715/player]



<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/



<!--HONumber=Dec16_HO1-->


