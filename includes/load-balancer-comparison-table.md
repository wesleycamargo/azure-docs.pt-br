---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 12/11/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 61af77897de0ad860eb01ee309bbeedf939e466b
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53326484"
---
| | SKU Standard | SKU Básico |
| --- | --- | --- |
| Tamanho do pool de back-end | Compatível com até 1000 instâncias | Compatível com até 100 instâncias |
| Pontos de extremidade de pool de back-end | Qualquer máquina virtual em uma rede virtual única, incluindo a mistura de máquinas virtuais, conjuntos de disponibilidade, conjuntos de dimensionamento de máquina virtual. | Máquinas virtuais em um conjunto de disponibilidade ou conjunto de dimensionamento da máquina virtual. |
| [Investigações de integridade](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Comportamento de investigação de integridade inoperante](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | As conexões TCP permanecem ativas com investigação inoperante __e__ todas as investigações inoperantes. | As conexões TCP permanecem ativas com investigação de instância inoperante. Todas as conexões TCP terminam com todas as investigações estão inoperantes. |
| Zonas de Disponibilidades | No SKU Standard, os front-ends com redundância de zona e por zona para entrada e saída e os mapeamentos de fluxos de saída sobrevivem à falha de zona, balanceamento de carga entre zonas. | Não disponível |
| Diagnósticos | Azure Monitor, métricas multidimensionais incluindo contadores byte e pacote, status de investigação de integridade, tentativas de conexão (TCP SYN), integridade de conexão de saída (SNAT bem sucedido e fluxos com falha), medidas de plano de dados ativo | Log Analytics do Azure apenas para o balanceador de carga público, alerta de exaustão SNAT, conta de integridade de pool back-end. |
| Portas de alta disponibilidade | Balanceador de Carga Interno | Não disponível |
| Segurança por padrão | A menos que incluído na lista de permissões por um grupo de segurança de rede, entrada não é permitida para pontos de extremidade de IP público e do Load Balancer (públicos e internos). | Aberto por padrão, Grupo de Segurança de Rede opcional. |
| [Conexões de saída](../articles/load-balancer/load-balancer-outbound-connections.md) | Você pode definir explicitamente com base no pool de NAT de saída com [regras de saída](../articles/load-balancer/load-balancer-outbound-rules-overview.md). Você pode usar vários front-ends com recusa de regra de balanceamento de carga. Um cenário de saída _deve_ ser explicitamente criado para a máquina virtual poder usar a conectividade de saída.  Os Pontos de Extremidade de Serviço da Rede Virtual podem ser acessados sem conectividade de saída e não são considerados dados processados.  Quaisquer endereços de IP públicos, incluindo serviços de PaaS do Azure não estão disponíveis como pontos de extremidade de VNet, devem ser acessados por meio de conectividade de saída e contam para dados processados. Quando apenas um Load Balancer interno estiver atendendo uma máquina virtual, as conexões de saída via SNAT padrão não estarão disponíveis. Nesse caso, use as [regras de saída](../articles/load-balancer/load-balancer-outbound-rules-overview.md). A programação de saída SNAT é o protocolo de transporte específicos com base no protocolo da regra de balanceamento de carga de entrada. | Único front-end, selecionado aleatoriamente quando vários front-ends estiverem presentes.  Quando apenas o Azure Load Balancer interno estiver atendendo a uma máquina virtual, o padrão SNAT é usado. |
| [Regras de saída](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Configuração de NAT de saída declarativa usando endereços IP públicos ou prefixos IP públicos ou ambos, tempo limite de ociosidade de saída configurável, alocação de porta de SNAT personalizada | Não disponível |
|  [Redefinição de TCP quando ocioso](../articles/load-balancer/load-balancer-tcp-reset.md) | Habilitar a Redefinição de TCP (TCP RST) quando o tempo limite de ociosidade for atingido em qualquer regra | Não disponível |
| [Vários front-ends](../articles/load-balancer/load-balancer-multivip-overview.md) | Entrada e [saída](../articles/load-balancer/load-balancer-outbound-connections.md) | Somente entrada |
| Operações de Gerenciamento | Maioria das operações < 30 segundos | Normalmente, 60 a 90 segundos ou mais. |
| Contrato de Nível de Serviço | 99,99% para o caminho de dados com duas máquinas virtuais íntegras. | [Implícito no SLA de VM](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/). | 
| Preços | Cobrado com base no número de regras, dados processados de entrada e saída associados ao recurso.  | Sem encargos |
|  |  |  |
