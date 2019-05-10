---
title: Visão geral de Proteção contra DDoS do Azure Standard | Microsoft Docs
description: Saiba mais sobre o serviço de Proteção contra DDoS do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/13/2018
ms.author: kumud
ms.openlocfilehash: 41e9d88df49d153089e6dc7a12c5873ccc167279
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209455"
---
# <a name="azure-ddos-protection-standard-overview"></a>Visão geral de Proteção contra DDoS do Azure Standard

Ataques de DDoS (negação de serviço distribuído) são uma das maiores preocupações de disponibilidade e de segurança enfrentadas pelos clientes que estão migrando seus aplicativos para a nuvem. Um ataque de DDoS tenta esgotar os recursos de um aplicativo, fazendo com que o aplicativo não fique disponível para usuários legítimos. Ataques de DDoS podem ser direcionadas a qualquer ponto de extremidade publicamente acessível pela Internet.

A Proteção contra DDoS do Azure, combinada com as práticas recomendadas de design de aplicativo, oferece defesa contra ataques de DDoS. A proteção contra DDoS do Azure fornece as seguintes camadas de serviço:

- **Básico**: Habilitada automaticamente como parte da plataforma Azure. A mitigação em tempo real de ataques comuns no nível de rede e o monitoramento de tráfego Always On fornecem os mesmos tipos de proteção utilizados pelos serviços online da Microsoft. A escala inteira da rede global do Azure pode ser usada para distribuir e reduzir o tráfego de ataques entre regiões. A proteção é fornecida para [endereços IP públicos](virtual-network-public-ip-address.md) IPv4 e IPv6 do Azure.
- **Standard**: Fornece funcionalidades de mitigação adicionais sobre a camada de serviço básica ajustadas especificamente para os recursos de rede Virtual do Azure. A Proteção contra DDoS Standard é simples de habilitar e não exige nenhuma alteração no aplicativo. Políticas de proteção são ajustadas por meio do monitoramento de tráfego dedicado e algoritmos de aprendizado de máquina. As políticas são aplicadas a endereços IP públicos associados a recursos implantados em redes virtuais, como as instâncias do Azure Load Balancer, do Azure Application Gateway e do Azure Service Fabric, mas essa proteção não se aplica a Ambientes do Serviço de Aplicativo. A telemetria em tempo real está disponível por meio de exibições do Azure Monitor durante um ataque e para fins de histórico. A análise avançada de mitigação de ataque está disponível por meio das configurações de diagnóstico. A proteção da camada de aplicativo pode ser adicionada por meio do [Firewall do aplicativo Web do Gateway de Aplicativo do Azure](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou instalando um firewall de terceiros do Azure Marketplace. A proteção é fornecida para [endereços IP públicos](virtual-network-public-ip-address.md) IPv4 e IPv6 do Azure.

![Proteção contra DDoS do Azure Básica versus Standard](./media/ddos-protection-overview/ddoscomparison.png)

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Tipos de ataques de DDoS mitigados pela Proteção contra DDoS Standard

A Proteção contra DDoS Standard pode mitigar os tipos de ataques a seguir:

- **Ataques volumétricos**: A meta do ataque é inundar a camada de rede com uma quantidade significativa de tráfego aparentemente legítimo. Eles incluem inundações de UDP, inundações de amplificação e outras inundações de pacotes falsificados. A Proteção contra DDoS Standard mitiga esses ataques de potencialmente vários gigabytes absorvendo-os e depurando-os com a escala de rede global do Azure automaticamente.
- **Ataques de protocolo**: Esses ataques renderizam um destino inacessível, explorando uma vulnerabilidade na camada 3 e a pilha de protocolo de 4 de camada. Eles incluem ataques de inundação SYN, ataques de reflexão e outros ataques de protocolo. A Proteção contra DDoS Standard mitiga esses ataques diferenciando entre o tráfego mal-intencionado e o legítimo, interagindo com o cliente e bloqueando o tráfego mal-intencionado. 
- **Ataques de camada de recursos (aplicativo)**: Esses ataques são direcionados a pacotes de aplicativo web para interromper a transmissão de dados entre os hosts. Esses ataques incluem violações de protocolo HTTP, injeção de SQL, scripts intersites e outros ataques de camada 7. Use o [firewall do aplicativo Web do Gateway de Aplicativo](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure com a Proteção contra DDoS Standard para fornecer proteção contra esses ataques. Também há ofertas de firewall do aplicativo Web de terceiros disponível no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

A Proteção contra DDoS Standard protege os recursos em uma rede virtual, incluindo endereços IP públicos associados a máquinas virtuais, balanceadores de carga e gateways de aplicativo. Quando combinada com o firewall do aplicativo Web do Gateway de Aplicativo, a Proteção contra DDoS Standard pode fornecer uma funcionalidade de mitigação completa da camada 3 a 7.

## <a name="ddos-protection-standard-features"></a>Recursos da Proteção contra DDoS do Azure Standard

![Funcionalidade de DDoS](./media/ddos-protection-overview/ddosfeatures.png)

Os recursos da Proteção contra DDoS do Azure Standard:

- **Integração de plataforma nativa:** nativamente integrada ao Azure. Inclui a configuração por meio do Portal do Azure. A Proteção contra DDoS Standard compreende seus recursos e a respectiva configuração.
- **Proteção imediata:** a configuração simplificada protege de maneira imediata todos os recursos em uma rede virtual assim que a Proteção contra DDoS Standard é habilitada. Nenhuma definição ou intervenção do usuário é necessária. A Proteção contra DDoS Standard atenua o ataque de maneira instantânea e automática, assim que ele é detectado.
- **Monitoramento de tráfego Always On:** seus padrões de tráfego do aplicativo são monitorados 24 horas por dia, 7 dias por semana, procurando indicadores de ataques de DDoS. A mitigação é realizada quando as políticas de proteção são excedidas.
- **Ajuste adaptável:** Criação de perfil de tráfego inteligente aprende o tráfego do seu aplicativo ao longo do tempo e seleciona e atualiza o perfil que é o mais adequado para seu serviço. O perfil se ajusta conforme o tráfego é alterado ao longo do tempo.
- **Proteção em várias camadas:** fornece proteção contra DDoS de pilha completa, quando usado com um firewall do aplicativo Web.
- **Escala de mitigação ampla:** mais de 60 tipos de ataques diferentes podem ser atenuados, com capacidade global, para proteger contra os maiores ataques de DDoS conhecidos.
- **Análise de ataque:** Obtenha relatórios detalhados em incrementos de cinco minutos durante um ataque, além de um resumo completo após o término dele. Transmita logs do fluxo de mitigação por streaming para um sistema de SIEM (gerenciamento de evento e informações de segurança) offline para monitoramento quase em tempo real durante um ataque.
- **Métricas de ataque:** as métricas resumidas de cada ataque são acessíveis por meio do Azure Monitor.
- **Alerta de ataque:** Alertas podem ser configurados no início e interrupção de um ataque e ao longo da duração do ataque, usando métricas de ataque internas. Alertas são integrados em seu software operacional como logs do Microsoft Azure Monitor, Splunk, armazenamento do Azure, Email e o portal do Azure.
- **Garantia de custo:** créditos de serviço de expansão de aplicativo para ataques de DDoS documentados.

## <a name="ddos-protection-standard-mitigation"></a>Mitigação da Proteção contra DDoS do Azure Standard

A Proteção contra DDoS Standard monitora a utilização de tráfego real e compara-a constantemente com os limites definidos na Política de DDoS. Quando esse limite de tráfego for excedido, a mitigação de DDoS será iniciada automaticamente. Quando o tráfego retorna para baixo do limite, a mitigação é removida.

![Atenuação](./media/ddos-protection-overview/mitigation.png)

Durante a mitigação, o tráfego enviado para o recurso protegido é redirecionado pelo serviço de Proteção contra DDoS e diversas verificações são executadas, incluindo as seguintes:

- Assegure que os pacotes estejam em conformidade com as especificações de Internet e não sejam malformados.
- Interagir com o cliente para determinar se o tráfego é potencialmente um pacote adulterado (por exemplo: Autenticação de SYN ou Cookie SYN ou removendo um pacote para a fonte o retransmita).
- Estabelecer limites de taxa para pacotes se nenhum outro método de imposição puder ser executado.

A Proteção contra DDoS bloqueia o tráfego e encaminha o tráfego restante para seu destino pretendido. Em poucos minutos de detecção de ataque, você será notificado pelo uso de métricas no Azure Monitor. Configurando o registro em log na telemetria da Proteção contra DDoS Standard, você pode gravar os logs de opções disponíveis para análise futura. Os dados de métrica no Azure Monitor para a Proteção contra DDoS Standard são mantidos por 30 dias.

A Microsoft firmou parceria com a [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para criar uma interface em que você pode gerar tráfego contra Endereços IP Públicos com a Proteção contra DDoS habilitada para fins de simulação. A simulação do BreakPoint Cloud permite que você:

- Valide como a Proteção contra DDoS Standard do Microsoft Azure protege seus recursos do Azure contra ataques de DDoS
- Otimize o processo de resposta a incidentes durante ataques de DDoS
- Documente a conformidade de DDoS
- Treine suas equipes de segurança de rede

## <a name="next-steps"></a>Próximas etapas

- [Configurar a Proteção contra DDoS Standard](manage-ddos-protection.md)
