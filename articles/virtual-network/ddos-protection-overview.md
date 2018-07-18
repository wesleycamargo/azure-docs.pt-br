---
title: Visão geral de Proteção contra DDoS do Azure Standard | Microsoft Docs
description: Saiba mais sobre o serviço de Proteção contra DDoS do Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2018
ms.author: jdial
ms.openlocfilehash: 705f69f9143e3d2b27a3099f340218aaa12931f8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2018
ms.locfileid: "31601892"
---
# <a name="azure-ddos-protection-standard-overview"></a>Visão geral de Proteção contra DDoS do Azure Standard

Ataques de DDoS (negação de serviço distribuído) são uma das maiores preocupações de disponibilidade e de segurança enfrentadas pelos clientes que estão migrando seus aplicativos para a nuvem. Um ataque de DDoS tenta esgotar os recursos de um aplicativo, fazendo com que o aplicativo não fique disponível para usuários legítimos. Ataques de DDoS podem ser direcionadas a qualquer ponto de extremidade publicamente acessível pela Internet.

A Proteção contra DDoS do Azure, combinada com as práticas recomendadas de design de aplicativo, oferece defesa contra ataques de DDoS. A Proteção contra DDoS do Azure fornece as seguintes camadas de serviço:

- **Básica**: habilitada automaticamente como parte da plataforma Azure, sem custo adicional. A mitigação em tempo real de ataques comuns no nível de rede e o monitoramento de tráfego Always On fornecem os mesmos tipos de proteção utilizados pelos serviços online da Microsoft. A escala inteira da rede global do Azure pode ser usada para distribuir e reduzir o tráfego de ataques entre regiões. A proteção é fornecida para [endereços IP públicos](virtual-network-public-ip-address.md) IPv4 e IPv6 do Azure.
- **Standard**: fornece funcionalidades de mitigação adicionais à camada de serviço Básica ajustadas especificamente para os recursos de Rede Virtual do Azure. A Proteção contra DDoS Standard é simples de habilitar e não exige nenhuma alteração no aplicativo. Políticas de proteção são ajustadas por meio do monitoramento de tráfego dedicado e algoritmos de aprendizado de máquina. As políticas aplicadas a Endereços IP Públicos associados aos recursos implantados em redes virtuais, como instâncias do Azure Load Balancer, Gateway de Aplicativo do Azure e Azure Service Fabric. A telemetria em tempo real está disponível por meio de exibições do Azure Monitor durante um ataque e para fins de histórico. Proteções de camada de aplicativo podem ser adicionadas por meio do [Firewall do Aplicativo Web do Gateway de Aplicativo do Azure](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). A proteção é fornecida para [endereços IP públicos](virtual-network-public-ip-address.md) IPv4 do Azure.

![Proteção contra DDoS do Azure Standard](./media/ddos-protection-overview/ddospic.png)

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Tipos de ataques de DDoS mitigados pela Proteção contra DDoS Standard

A Proteção contra DDoS Standard pode mitigar os tipos de ataques a seguir:

- **Ataques volumétricos**: a meta do ataque é inundar a camada de rede com uma quantidade significativa de tráfego aparentemente legítimo. Eles incluem inundações de UDP, inundações de amplificação e outras inundações de pacotes falsificados. A Proteção contra DDoS Standard mitiga esses ataques de potencialmente vários gigabytes absorvendo-os e depurando-os com a escala de rede global do Azure automaticamente.
- **Ataques de protocolo**: esses ataques renderizam um destino inacessível explorando uma vulnerabilidade na pilha de protocolos das camadas 3 e 4. Eles incluem ataques de inundação SYN, ataques de reflexão e outros ataques de protocolo. A Proteção contra DDoS Standard mitiga esses ataques diferenciando entre o tráfego mal-intencionado e o legítimo, interagindo com o cliente e bloqueando o tráfego mal-intencionado. 
- **Ataques de camada de recursos (aplicativo)**: esses ataques são direcionados a pacotes de aplicativo Web para interromper a transmissão de dados entre os hosts. Esses ataques incluem violações de protocolo HTTP, injeção de SQL, scripts intersites e outros ataques de camada 7. Use o [firewall do aplicativo Web do Gateway de Aplicativo](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure com a Proteção contra DDoS Standard para fornecer proteção contra esses ataques. Também há ofertas de firewall do aplicativo Web de terceiros disponível no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

A Proteção contra DDoS Standard protege os recursos em uma rede virtual, incluindo endereços IP públicos associados a máquinas virtuais, balanceadores de carga e gateways de aplicativo. Quando combinada com o firewall do aplicativo Web do Gateway de Aplicativo, a Proteção contra DDoS Standard pode fornecer uma funcionalidade de mitigação completa da camada 3 a 7.

## <a name="ddos-protection-standard-features"></a>Recursos da Proteção contra DDoS do Azure Standard

![Funcionalidade de DDoS](./media/ddos-protection-overview/ddosfeatures.png)

Os recursos da Proteção contra DDoS do Azure Standard:

- **Integração de plataforma nativa:** nativamente integrado ao Azure. Inclui a configuração por meio do Portal do Azure. A Proteção contra DDoS Standard compreende seus recursos e a respectiva configuração.
- **Proteção imediata:** a configuração simplificada protege de maneira imediata todos os recursos em uma rede virtual assim que a Proteção contra DDoS Standard é habilitada. Nenhuma definição ou intervenção do usuário é necessária. A Proteção contra DDoS Standard atenua o ataque de maneira instantânea e automática, assim que ele é detectado.
- **Monitoramento de tráfego Always On:** seus padrões de tráfego do aplicativo são monitorados 24 horas por dia, 7 dias por semana, procurando indicadores de ataques de DDoS. A mitigação é realizada quando as políticas de proteção são excedidas.
- **Ajuste adaptável:** a criação de perfil de tráfego inteligente aprende sobre o tráfego do seu aplicativo ao longo do tempo e seleciona e atualiza o perfil é o mais adequado para seu serviço. O perfil se ajusta conforme o tráfego é alterado ao longo do tempo.
- **Proteção das camadas 3 a 7:** fornece Proteção contra DDoS para toda a pilha quando usado com um Firewall do Aplicativo Web.
- **Escala de mitigação ampla:** mais de 60 tipos de ataques diferentes podem ser atenuados, com capacidade global, para proteger contra os maiores ataques de DDoS conhecidos.
- **Métricas de ataque:** métricas resumidas de cada ataque são acessíveis por meio do Azure Monitor.
- **Alerta de ataque:** alertas podem ser configurados no início, durante e após a interrupção de um ataque, usando métricas de ataque internas. Os alertas integram-se ao software operacional, como o Microsoft Azure Log Analytics, Splunk, Armazenamento do Microsoft Azure, Email e o Portal do Azure.
- **Garantia de custo:** créditos de serviço de expansão de aplicativo para ataques de DDoS documentados.

## <a name="ddos-protection-standard-mitigation"></a>Mitigação da Proteção contra DDoS do Azure Standard

A Proteção contra DDoS Standard monitora a utilização de tráfego real e compara-a constantemente com os limites definidos na Política de DDoS. Quando esse limite de tráfego for excedido, a mitigação de DDoS será iniciada automaticamente. Quando o tráfego retorna para baixo do limite, a mitigação é removida.

![Redução](./media/ddos-protection-overview/mitigation.png)

Durante a mitigação, o tráfego enviado para o recurso protegido é redirecionado pelo serviço de Proteção contra DDoS e diversas verificações são executadas, incluindo as seguintes:

- Assegure que os pacotes estejam em conformidade com as especificações de Internet e não sejam malformados.
- Interagir com o cliente para determinar se o tráfego é potencialmente um pacote adulterado (por exemplo: Aut. SYN ou Cookie SYN ou removendo um pacote para que a fonte o retransmita).
- Estabelecer limites de taxa para pacotes se nenhum outro método de imposição puder ser executado.

A Proteção contra DDoS bloqueia o tráfego e encaminha o tráfego restante para seu destino pretendido. Em poucos minutos de detecção de ataque, você será notificado pelo uso de métricas no Azure Monitor. Configurando o registro em log na telemetria da Proteção contra DDoS Standard, você pode gravar os logs de opções disponíveis para análise futura. Os dados de métrica no Azure Monitor para a Proteção contra DDoS Standard são mantidos por 30 dias.

A Microsoft firmou parceria com a [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para criar uma interface em que você pode gerar tráfego contra Endereços IP Públicos com a Proteção contra DDoS habilitada para fins de simulação. A simulação do BreakPoint Cloud permite que você:

- Valide como a Proteção contra DDoS Standard do Microsoft Azure protege seus recursos do Azure contra ataques de DDoS
- Otimize o processo de resposta a incidentes durante ataques de DDoS
- Documente a conformidade de DDoS
- Treine suas equipes de segurança de rede

## <a name="next-steps"></a>Próximas etapas

- [Configurar a Proteção contra DDoS Standard](manage-ddos-protection.md)