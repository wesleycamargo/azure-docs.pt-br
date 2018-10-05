---
title: O que é o Azure Monitor para VMs? | Microsoft Docs
description: O Azure Monitor para as VMs é um recurso do Azure Monitor que combina o monitoramento de integridade e de desempenho do sistema operacional da VM do Azure, e também a descoberta automática de componentes e dependências de aplicativos em outros recursos, e mapeia a comunicação entre eles. Este artigo fornece uma visão geral.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: 26fcc3eb78af53360cca57382b4c06b017f36c0e
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063261"
---
# <a name="what-is-azure-monitor-for-vms"></a>O que é o Azure Monitor para VMs?

O Azure Monitor para VMs monitora suas VMs (máquina virtuais) do Azure em escala analisando o desempenho e a integridade das VMs do Windows e do Linux, incluindo seus diferentes processos e dependências interconectados a outros recursos e processos externos. A solução inclui suporte para monitorar o desempenho e as dependências de aplicativos nas VMs hospedadas localmente ou em outro provedor de nuvem.  Ela inclui três recursos principais para fornecer esse insight detalhado:

* Os componentes lógicos de VMs do Azure executando o sistema operacional Windows e Linux são medidos com base em um conjunto de critérios e alertas de integridade pré-configurados quando a condição avaliada é atendida.  
* As principais métricas de desempenho de processador, memória, disco e adaptador de rede do sistema operacional convidado da VM são coletadas e apresentadas em gráficos de desempenho de tendências predefinidos.
* Mapa de dependências que mostra os componentes interconectados descobertos com essa VM de vários grupos de recursos e assinaturas.  

Esses recursos são organizados em três perspectivas:

* Integridade
* Desempenho
* Mapa

>[!NOTE]
>Atualmente, o recurso de integridade é oferecido apenas para máquinas virtuais do Azure.
>

A integração com o Log Analytics oferece filtragem e agregação altamente eficientes e a capacidade de executar análise de tendência dos dados ao longo do tempo. O monitoramento abrangente de suas cargas de trabalho não pode ser obtido sozinho com o Azure Monitor, o Mapa do Serviço ou o Log Analytics.  

Você pode exibir esses dados no contexto de única VM diretamente na máquina virtual ou com o Azure Monitor, que oferece uma exibição agregada das suas VMs com base na seguinte perspectiva para cada recurso:

* Integridade – VMs relacionadas a um grupo de recursos
* Mapa e o desempenho – as VMs configuradas para relatar a um workspace do Log Analytics específico

![Perspectiva de insights de máquina virtual do portal](./media/monitoring-vminsights-overview/vminsights-azmon-directvm-01.png)

DevOps pode fornecer desempenho e disponibilidade previsíveis de aplicativos vitais, com eficiência, identificando eventos críticos e gargalos de desempenho do sistema operacional, problemas de rede e se um problema está relacionado a outras dependências.  

## <a name="data-usage"></a>Uso de dados 

Assim que você integra o Azure Monitor para VMs, os dados coletados pelas VMs passam a ser ingeridos e armazenados no Azure Monitor.  O Azure Monitor para VMs é cobrado pelos dados ingeridos e mantidos, pelo número de séries temporais de métricas dos critérios de integridade monitoradas, pelas regras de alerta criadas e pelas notificações enviadas, de acordo com o preço publicado na [página de preços](https://azure.microsoft.com/pricing/details/monitor/) do Azure Monitor

O tamanho do log varia de acordo com o tamanho das cadeias de caracteres de contadores e pode aumentar com o número de discos lógicos e de adaptadores de rede.  Se você já tiver um workspace e estiver coletando esses contadores, não será aplicado nenhum encargo duplicado.  Se você já estiver usando o Mapa do Serviço, a única alteração será que os dados de conexão adicionais passarão a ser enviados ao Azure Monitor.

## <a name="next-steps"></a>Próximas etapas
Examine [Integrar o Azure Monitor para VMs](monitoring-vminsights-onboard.md) para entender os requisitos e os métodos usados para habilitar o monitoramento das máquinas virtuais.
