---
title: "Leiame Ambiente do Serviço de Aplicativo do Azure"
description: "Lista a documentação que descreve o Ambiente do Serviço de Aplicativo do Azure"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 77452413-5193-4762-8b3d-5fa8e4edf1ca
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 8b401036d4fa8e6c4e6430433b1641f5cb4ae010
ms.contentlocale: pt-br
ms.lasthandoff: 06/26/2017

---

# <a name="app-service-environment-documentation"></a>Documentação do Ambiente de Serviço de Aplicativo
O Ambiente do Serviço de Aplicativo é um recurso do Serviço de Aplicativo do Azure que fornece um ambiente totalmente isolado e dedicado a executar com segurança os aplicativos do Serviço de Aplicativo do Azure em grande escala. Esse recurso pode hospedar os [Aplicativos Web][webapps], [Aplicativos Móveis][mobileapps], [Aplicativos de API][APIApps] e [Funções][Functions].

Os Ambientes de Serviço de Aplicativo são ideais para cargas de trabalho de aplicativos que exigem:

* Escala muito alta
* Isolamento e acesso seguro à rede

Os clientes podem criar vários Ambientes de Serviço de Aplicativo dentro de uma única região do Azure, bem como entre várias regiões do Azure. Isso faz dos Ambientes de Serviço de Aplicativo ideais para dimensionar horizontalmente camadas de aplicativo sem estado para dar suporte a cargas de trabalho RPS altas.

Ambientes de Serviço de Aplicativo são isolados para executar somente aplicativos de um único cliente, e sempre são implantados em uma rede virtual. Os clientes têm controle refinado sobre o tráfego de rede do aplicativo de entrada e saída usando os [grupos de segurança de rede][NSGs]. Os aplicativos também podem estabelecer conexões seguras de alta velocidade por redes virtuais para recursos corporativos locais.

Os aplicativos muitas vezes precisam acessar recursos corporativos, como bancos de dados internos e serviços Web. Os aplicativos em execução em Ambientes de Serviço de Aplicativo podem acessar os recursos acessíveis via conexões VPN [Site a Site][SiteToSite] e [Azure ExpressRoute][ExpressRoute].

* [O que é um Ambiente do Serviço de Aplicativo?][Intro]
* [Criando um Ambiente do Serviço de Aplicativo][MakeExternalASE]
* [Criando um Ambiente do Serviço de Aplicativo de Load Balancer Interno][MakeILBASE]
* [Usando um Ambiente do Serviço de Aplicativo][UsingASE]
* [Considerações de rede e o Ambiente do Serviço de Aplicativo][ASENetwork]
* [Criando um Ambiente do Serviço de Aplicativo de um modelo][MakeASEfromTemplate]


## <a name="videos"></a>Vídeos
Domine a PaaS moderna para a empresa com o Serviço de Aplicativo do Azure
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2016/BRK3205/player]

Implantando aplicativos altamente escalonáveis e seguros
>[!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON325/player]

Executando aplicativos Enterprise Web e móveis no Serviço de Aplicativo do Azure
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3715/player]

## <a name="asev1"></a>ASEv1 ##
Há duas versões para o Ambiente do Serviço de Aplicativo: ASEv1 e ASEv2. Para obter informações sobre ASEv1, consulte a [documentação do Ambiente do Serviço de Aplicativo v1][ASEv1README].


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[ASEv1README]: ../app-service-app-service-environments-readme.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-site-to-site-create.md
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

