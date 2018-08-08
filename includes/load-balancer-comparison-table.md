---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 7/26/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: fc98206b5940ce75ddf26cfd905b1e0a89e2d85c
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39326237"
---
| | SKU Standard | SKU Básico |
| --- | --- | --- |
| Tamanho do pool de back-end | Compatível com até 1000 instâncias | Compatível com até 100 instâncias |
| Pontos de extremidade de pool de back-end | Qualquer máquina virtual em uma rede virtual única, incluindo a mistura de máquinas virtuais, conjuntos de disponibilidade, conjuntos de dimensionamento de máquina virtual. | Máquinas virtuais em um conjunto de disponibilidade ou conjunto de dimensionamento da máquina virtual. |
| Zonas de Disponibilidades | Em SKU básico, front-ends com redundância de zona e por zona para entrada e saída, o mapeamento de fluxos de saída sobrevivem à falha de zona, balanceamento de carga entre zonas. | n/d|
| Diagnósticos | Azure Monitor, métricas multidimensionais incluindo contadores byte e pacote, status de investigação de integridade, tentativas de conexão (TCP SYN), integridade de conexão de saída (SNAT bem sucedido e fluxos com falha), medidas de plano de dados ativo | Log Analytics do Azure apenas para o balanceador de carga público, alerta de exaustão SNAT, conta de integridade de pool back-end. |
| Portas de alta disponibilidade | Balanceador de Carga Interno | n/d |
| Segurança por padrão | Padrão fechado para IP público e pontos de extremidade de Azure Load Balancer e um grupo de segurança de rede deve ser usado para indicar explicitamente a permissão para o tráfego fluir. | Aberto por padrão, Grupo de Segurança de Rede opcional. |
| [Conexões de saída](../articles/load-balancer/load-balancer-outbound-connections.md) | Vários front-ends com recusa por regra de balanceamento de carga. Um cenário de saída _deve_ ser explicitamente criado para a máquina virtual poder usar a conectividade de saída.  Pontos de extremidade da Rede Virtual do Microsoft Azure podem ser acessados sem conectividade de saída e não são considerados dados processados.  Quaisquer endereços de IP públicos, incluindo serviços de PaaS do Azure não estão disponíveis como pontos de extremidade de VNet, devem ser acessados por meio de conectividade de saída e contam para dados processados. Quando apenas um Azure Load Balancer interno estiver atendendo uma máquina virtual, as conexões de saída via SNAT padrão não estão disponíveis. A programação de saída SNAT é o protocolo de transporte específicos com base no protocolo da regra de balanceamento de carga de entrada. | Único front-end, selecionado aleatoriamente quando vários front-ends estiverem presentes.  Quando apenas o Azure Load Balancer interno estiver atendendo a uma máquina virtual, o padrão SNAT é usado. |
| [Vários front-ends](../articles/load-balancer/load-balancer-multivip-overview.md) | Entrada e [saída](../articles/load-balancer/load-balancer-outbound-connections.md) | Somente entrada |
| [Comportamento de investigação de integridade inoperante](../articles/load-balancer/load-balancer-custom-probe-overview.md) | As conexões TCP permanecem ativas com investigação inoperante __e__ todas as investigações inoperantes. | As conexões TCP permanecem ativas com investigação de instância inoperante. Todas as conexões TCP terminam com todas as investigações inoperantes. |
| Operações de Gerenciamento | Maioria das operações < 30 segundos | Normalmente, 60 a 90 segundos ou mais. |
| Contrato de Nível de Serviço | 99,99% para o caminho de dados com duas máquinas virtuais íntegras. | Implícito no SLA de VM. | 
| Preços | Cobrado com base no número de regras, dados processados de entrada ou saída associados ao recurso.  | Sem encargos |
|  |  |  |