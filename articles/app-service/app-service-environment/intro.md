---
title: "Introdução ao Ambiente do Serviço de Aplicativo do Azure"
description: "Uma breve visão geral sobre o Ambiente do Serviço de Aplicativo do Azure"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 0d078aefbf73a45298f397d02ab24b2c8232ecef
ms.contentlocale: pt-br
ms.lasthandoff: 06/26/2017

---
# <a name="introduction-to-the-app-service-environment"></a>Introdução ao Ambiente do Serviço de Aplicativo #
 
## <a name="overview"></a>Visão geral ##

Um Ambiente do Serviço de Aplicativo é um recurso do Serviço de Aplicativo do Azure que fornece um ambiente totalmente isolado e dedicado a executar com segurança os aplicativos do Serviço de Aplicativo do Azure em grande escala. Esse recurso pode hospedar os [Aplicativos Web][webapps], [Aplicativos Móveis][mobileapps], [Aplicativos de API][APIapps] e [Funções][Functions].

Os Ambientes de Serviço de Aplicativo são ideais para cargas de trabalho de aplicativos que exijam:

- Escala muito alta
- Isolamento e acesso seguro à rede
- Alta utilização de memória

Os clientes podem criar vários Ambientes do Serviço de Aplicativo dentro de uma única região do Azure, bem como entre várias regiões do Azure. Isso faz dos Ambientes de Serviço de Aplicativo ideais para dimensionar horizontalmente camadas de aplicativo sem estado para dar suporte a cargas de trabalho RPS altas.

Ambientes de Serviço de Aplicativo são isolados para executar somente aplicativos de um único cliente, e sempre são implantados em uma rede virtual. Os clientes têm um controle refinado sobre o tráfego de entrada e saída da rede de aplicativos, e os aplicativos podem estabelecer conexões seguras de alta velocidade por meio de VPNs com recursos corporativos locais.

Todos os artigos e instruções sobre os Ambientes do Serviço de Aplicativo estão disponíveis no [LEIAME para Ambientes do Serviço de Aplicativo][ASEReadme].

Para obter uma visão geral sobre como os Ambientes de Serviço de Aplicativo permitem alta escala e acesso seguro à rede, consulte [AzureCon Deep Dive](https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/) em Ambientes do Serviço de Aplicativo.

Para uma análise aprofundada sobre o dimensionamento horizontal usando vários Ambientes de Serviço de Aplicativo, veja o artigo sobre como configurar uma [área de aplicativo distribuído geograficamente](https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/).

Para ver como a arquitetura de segurança exibida no AzureCon Deep Dive foi configurada, consulte o artigo sobre a implementação de uma [arquitetura de segurança em camadas](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-layered-security) com os Ambientes do Serviço de Aplicativo.

Os aplicativos executados nos Ambientes do Serviço de Aplicativo podem ter seu acesso restrito por dispositivos upstream como WAF (firewalls do aplicativo Web). O artigo sobre como [configurar um WAF para Ambientes do Serviço de Aplicativo](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-web-application-firewall) aborda esse cenário.

## <a name="dedicated-environment"></a>Ambiente Dedicado ##

Um ambiente de serviço de aplicativo dedicado exclusivamente a uma única assinatura e pode hospedar 100 instâncias. Podem ser de 100 instâncias em um único plano do Serviço do Aplicativo para a 100 planos do Serviço do Aplicativo de instância única e tudo que houver entre essas duas opções.

Um Ambiente de Serviço de Aplicativo é composto de front-ends e funções de trabalho. Os front-ends são responsáveis pela terminação HTTP/HTTPS, bem como pelo balanceamento automático de cargas de solicitações do aplicativo em um Ambiente do Serviço de Aplicativo. Os front-ends são automaticamente adicionados já que os planos de Serviço do Aplicativo no Ambiente de Serviço do Aplicativo são dimensionados.

As funções de trabalho são funções que hospedam aplicativos cliente. As funções de trabalho estão disponíveis em 3 tamanhos fixos:
* 1 núcleo/3,5 GB de RAM
* 2 núcleos/7 GB de RAM
* 4 núcleos/14 GB de RAM.

Os clientes não precisam gerenciar os front-ends e as funções de trabalho. Toda a infraestrutura é adicionada automaticamente cconforme os clientes dimensionam os planos de Serviço do Aplicativo. Como os planos de Serviço do Aplicativo são criados ou dimensionados em um Ambiente do Serviço de Aplicativo, a infraestrutura necessária é adicionada ou removida conforme necessário.

Existe uma taxa mensal fixa para um Ambiente do Serviço de Aplicativo que paga pela infraestrutura e não altera com o tamanho do Ambiente do Serviço de Aplicativo. Além disso, há um custo por núcleo do plano do Serviço do Aplicativo. Todos os aplicativos hospedados no Ambiente do Serviço de Aplicativo estão em um SKU de preços Isolado. Para obter detalhes sobre os preços para um Ambiente do Serviço de Aplicativo, consulte a página [Preço do Serviço de Aplicativo][Pricing] e analise as opções disponíveis para Ambientes do Serviço de Aplicativo.

## <a name="virtual-network-support"></a>Suporte a Rede Virtual ##

Um Ambiente do Serviço de Aplicativo pode ser criado apenas em uma rede virtual do Azure Resource Manager. Você pode saber mais sobre Redes Virtuais do Azure aqui [Perguntas frequentes sobre redes virtuais do Azure](https://azure.microsoft.com/documentation/articles/virtual-networks-faq/). Como um Ambiente de Serviço de Aplicativo sempre existe em uma rede virtual, mais precisamente em uma sub-rede de uma rede virtual, você pode aproveitar os recursos de segurança de redes virtuais para controlar tanto a comunicação de rede de entrada quanto a de saída para os seus aplicativos.

Um Ambiente de Serviço de Aplicativo pode ser voltado para a Internet com um endereço IP público ou voltado para o interior com apenas um endereço ILB (Load Balancer Interno).

Você pode usar [grupos de segurança de rede][NSGs] para restringir a comunicação de rede de entrada à sub-rede na qual reside um Ambiente do Serviço de Aplicativo. Isso permite que você execute aplicativos por trás de dispositivos e serviços upstream, como firewalls de aplicativo Web e provedores SaaS de rede.

Aplicativos frequentemente precisam acessar recursos corporativos, como bancos de dados internos e serviços da Web. Se o Ambiente do Serviço de Aplicativo é implantado em uma Rede Virtual do Azure que tem uma conexão VPN à rede local, os aplicativos no Ambiente de Serviço do Aplicativo serão capazes de acessar os recursos locais. Isso é verdadeiro independentemente se a VPN é [Site a Site](https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/) ou [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/).

Para obter mais informações sobre o funcionamento dos Ambientes do Serviço de Aplicativo com redes virtuais e redes locais, consulte [Considerações de rede do Ambiente do Serviço de Aplicativo][ASENetwork].

## <a name="asev1"></a>ASEv1 ##

O Ambiente do Serviço de Aplicativo tem duas versões: ASEv1 e ASEv2. As informações anteriores tiveram como foco o ASEv2. Esta seção mostra as diferenças entre o ASEv1 e o ASEv2. 

No ASEv1, você precisa gerenciar todos os recursos manualmente. Isso inclui os front-ends, as funções de trabalho e os endereços IP usados para o SSL baseado em IP. Antes de poder escalar horizontalmente o plano do Serviço de Aplicativo, primeiro você precisa escalar horizontalmente o pool de trabalho no qual você deseja hospedá-lo.

O ASEv1 usa um modelo de preço diferente do ASEv2. No ASEv1, você precisa pagar por cada núcleo alocado. Isso inclui os núcleos usados para front-ends ou funções de trabalho que não hospedam nenhuma carga de trabalho. No ASEv1, o tamanho máximo de escala padrão de um Ambiente do Serviço de Aplicativo é de 55 hosts no total. Isso inclui funções de trabalho e front-ends. A vantagem do ASEv1 é que ele pode ser implantado em uma rede virtual clássica, bem como em uma rede virtual do Resource Manager. Saiba mais sobre o ASEv1 aqui: [Introdução ao Ambiente do Serviço de Aplicativo v1][ASEv1Intro]

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

