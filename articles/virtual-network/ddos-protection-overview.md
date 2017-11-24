---
title: "Visão geral de Proteção contra DDoS do Azure Standard | Microsoft Docs"
description: "Saiba mais sobre o serviço de Proteção contra DDoS do Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 6b26108b000bac56fe7d49a3a634f2be9d7543a8
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="azure-ddos-protection-standard-overview"></a>Visão geral de Proteção contra DDoS do Azure Standard

Ataques de DDoS (negação de serviço distribuído) são uma das maiores preocupações de disponibilidade e de segurança enfrentadas pelos clientes que estão migrando seus aplicativos para a nuvem. Um ataque de DDoS tenta esgotar os recursos de um aplicativo, fazendo com que o aplicativo não fique disponível para usuários legítimos. Ataques de DDoS podem ser direcionadas a qualquer ponto de extremidade publicamente acessível pela Internet.

A Proteção contra DDoS do Azure, combinada com as melhores práticas de design de aplicativo, fornece proteção contra ataques de DDoS. A Proteção contra DDoS do Azure fornece as seguintes camadas de serviço: 

- **Proteção contra DDoS do Azure Básica**: habilitada automaticamente como parte da plataforma Azure, sem custo adicional. A mitigação em tempo real de ataques comuns no nível de rede e o monitoramento de tráfego Always On fornecem os mesmos tipos de proteção utilizados pelos serviços online da Microsoft. A escala inteira da rede global do Azure pode ser usada para distribuir e reduzir o tráfego de ataques entre regiões. A proteção é fornecida para [endereços IP públicos](virtual-network-public-ip-address.md) IPv4 e IPv6 do Azure.
- **Proteção contra DDoS do Azure Standard** Fornece funcionalidades de mitigação adicionais ajustadas especificamente para os recursos de Rede Virtual do Azure. É simples de habilitar e não exige nenhuma alteração no aplicativo. As políticas de proteção são ajustadas por meio do monitoramento de tráfego dedicado e dos algoritmos de aprendizado de máquina e aplicadas a endereços IP públicos associados a recursos implantados em redes virtuais, como instâncias do Azure Load Balancer, Gateway de Aplicativo do Azure e Azure Service Fabric. A telemetria em tempo real está disponível por meio de exibições do Azure Monitor durante um ataque e para o histórico. Proteções de camada de aplicativo podem ser adicionadas por meio do [Firewall do Aplicativo Web do Gateway de Aplicativo](https://azure.microsoft.com/services/application-gateway). A proteção é fornecida para [endereços IP públicos](virtual-network-public-ip-address.md) IPv4 do Azure. 

![Proteção contra DDoS do Azure Standard](./media/ddos-protection-overview/ddos-protection-overview-fig2.png)

> [!IMPORTANT]
> A Proteção contra DDoS do Azure Standard está atualmente em versão prévia. A proteção é fornecida para qualquer recurso do Azure que tem um endereço IP público do Azure associado a ele, como máquinas virtuais, balanceadores de carga e gateways de aplicativo. Você precisa se [registrar](http://aka.ms/ddosprotection) no serviço antes de habilitar a Proteção contra DDoS Standard em sua assinatura. Após o registro, a equipe de DDoS do Azure entrará em contato com você e o orientará pelo processo de habilitação. A Proteção contra DDoS Standard está disponível apenas nas regiões Leste dos EUA, Leste dos EUA 2, Oeste dos EUA, Centro-oeste dos EUA, Europa Setentrional, Europa Ocidental, Oeste do Japão, Leste do Japão, Ásia Oriental e Sudeste Asiático. Durante a versão prévia, você não será cobrado pelo uso do serviço.

Recomendamos que você experimente a Proteção contra DDoS Standard nos ambientes de desenvolvimento, teste ou produção. Use os seguintes recursos para fornecer comentários sobre suas experiências:
- [Proteção contra DDoS do Azure no Fórum do Microsoft Azure](https://feedback.azure.com/forums/905032-azure-ddos-protection). 
- [Proteção contra DDoS do Azure no Fórum do MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azureddosprotection)
- [Proteção contra DDoS do Azure no Stack Overflow](https://stackoverflow.com/tags/azure-ddos/info)

Para problemas de suporte, você pode [abrir um tíquete de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md). Enquanto a Proteção contra DDoS Standard está em versão prévia, o suporte é fornecido como melhor esforço.

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Tipos de ataques de DDoS mitigados pela Proteção contra DDoS Standard

A Proteção contra DDoS Standard pode mitigar estes tipos de ataques:

- **Ataques volumétricos**: a meta do ataque é inundar a camada de rede com uma quantidade significativa de tráfego aparentemente legítimo. Eles incluem inundações de UDP, inundações de amplificação e outras inundações de pacotes falsificados. A Proteção contra DDoS Standard atenua esses ataques potenciais de vários gigabytes absorvendo-os e depurando-os, utilizando a escala de rede global do Azure automaticamente. 
- **Ataques de protocolo**: esses ataques renderizam um destino inacessível explorando uma vulnerabilidade na pilha de protocolos das camadas 3 e 4. Eles incluem ataques de inundação SYN, ataques de reflexão e outros ataques de protocolo. A Proteção contra DDoS Standard atenua esses ataques diferenciando entre o tráfego mal-intencionado e o legítimo, interagindo com o cliente e bloqueando o tráfego mal-intencionado. 
- **Ataques de camada de aplicativo**: esses ataques são direcionados a pacotes de aplicativo Web para interromper a transmissão de dados entre os hosts. Eles incluem violações de protocolo HTTP, injeção de SQL, scripts entre sites e outros ataques de camada 7. Use o [firewall do aplicativo Web do Gateway de Aplicativo](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure com a Proteção contra DDoS Standard para fornecer proteção contra esses ataques. 

A Proteção contra DDoS Standard protege os recursos em uma rede virtual, incluindo endereços IP públicos associados a máquinas virtuais, balanceadores de carga e gateways de aplicativo. Quando combinada com o firewall do aplicativo Web do Gateway de Aplicativo, a Proteção contra DDoS Standard pode fornecer uma funcionalidade de mitigação completa da camada 3 a 7.

## <a name="ddos-protection-standard-features"></a>Recursos da Proteção contra DDoS do Azure Standard

![Funcionalidade de DDoS](./media/ddos-protection-overview/ddos-overview-fig1.png)

Os recursos da Proteção contra DDoS do Azure Standard: 

- **Integração de plataforma nativa:** nativamente integrada ao Azure e inclui configuração por meio do portal do Azure e do PowerShell. A Proteção contra DDoS Standard compreende seus recursos e a respectiva configuração.
- **Monitoramento de tráfego Always On:** seus padrões de tráfego do aplicativo são monitorados 24 horas por dia, 7 dias por semana, procurando indicadores de ataques de DDoS. A mitigação é realizada quando as políticas de proteção são excedidas.
- **Proteção imediata:** a configuração simplificada protege de maneira imediata todos os recursos em uma rede virtual assim que a Proteção contra DDoS Standard é habilitada. Nenhuma definição ou intervenção do usuário é necessária. A Proteção contra DDoS Standard atenua o ataque de maneira instantânea e automática, assim que ele é detectado.
- **Ajuste adaptável:** a criação de perfil de tráfego inteligente aprende sobre o tráfego do seu aplicativo ao longo do tempo e seleciona e atualiza o perfil é o mais adequado para seu serviço. O perfil se ajusta conforme o tráfego é alterado ao longo do tempo.
- **Proteção da camada 3 a 7:** fornece proteção completa contra DDoS de pilha, quando usado com um gateway de aplicativo.
- **Escala de mitigação ampla:** mais de 60 tipos de ataques diferentes podem ser atenuados, com capacidade global, para proteger contra os maiores ataques de DDoS conhecidos. 
- **Métricas de ataque:** métricas resumidas de cada ataque são acessíveis por meio do Azure Monitor.
- **Alerta de ataque:** alertas podem ser configurados no início, durante e após a interrupção de um ataque, usando métricas de ataque internas. Os alertas são integrados em seu software operacional como o Microsoft Operations Management Suite, Splunk, Armazenamento do Azure, Email e o portal do Azure.
- **Garantia de custo:** créditos de serviço de expansão de aplicativo para ataques de DDoS documentados.

## <a name="ddos-protection-standard-mitigation"></a>Mitigação da Proteção contra DDoS do Azure Standard

O serviço de Proteção contra DDoS da Microsoft monitora a utilização de tráfego real e compara-a constantemente aos limites definidos na Política de DDoS. Quando esse limite de tráfego for excedido, a mitigação de DDoS será iniciada automaticamente. Quando o tráfego retorna para baixo do limite, a mitigação é removida.

Durante a mitigação, o tráfego enviado para o recurso protegido é redirecionado pelo serviço de Proteção contra DDoS e diversas verificações são executadas. Geralmente, essas verificações desempenham as seguintes funções:

- Assegurar que os pacotes estejam em conformidade com as especificações de Internet e não sejam malformados.
- Interagir com o cliente para determinar se o tráfego é potencialmente um pacote adulterado (por exemplo: Aut. SYN ou Cookie SYN ou removendo um pacote para que a fonte o retransmita).
- Estabelecer limites de taxa para pacotes se nenhum outro método de imposição puder ser executado.

A Proteção contra DDoS bloqueia o tráfego de ataque e encaminha o tráfego restante para seu destino pretendido. Em poucos minutos de detecção de ataque, você será notificado pelo uso de métricas no Azure Monitor. Configurando o registro em log na telemetria da Proteção contra DDoS Standard, você pode gravar os logs de opções disponíveis para análise futura. Atualmente, dados de métrica no Azure Monitor para a Proteção contra DDoS Standard são mantidos por 30 dias.

Não é aconselhável que clientes simulem seus próprios ataques de DDoS. Em vez disso, os clientes podem usar o canal de suporte para solicitar que uma simulação de ataque de DDoS seja realizada pela Rede do Azure. Um engenheiro entrará em contato com você para organizar os detalhes do ataque de DDoS (portas, protocolos, IPs de destino) e combinar um horário para agendar o teste.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como gerenciar a Proteção contra DDoS Standard usando o [Azure PowerShell](ddos-protection-manage-ps.md) ou o [Portal do Azure](ddos-protection-manage-portal.md).
