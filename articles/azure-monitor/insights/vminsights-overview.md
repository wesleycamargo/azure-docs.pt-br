---
title: O que é o Azure Monitor para VMs (versão prévia)? | Microsoft Docs
description: O Azure Monitor para as VMs é um recurso do Azure Monitor que combina o monitoramento de integridade e de desempenho do sistema operacional da VM do Azure, e também a descoberta automática de componentes e dependências de aplicativos em outros recursos, e mapeia a comunicação entre eles. Este artigo fornece uma visão geral.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2019
ms.author: magoedte
ms.openlocfilehash: 0733b4179793e88759ca1c3330880546650fbe09
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856360"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>O que é o Azure Monitor para VMs (versão prévia)?

O Azure Monitor para VMs monitora as VMs (máquinas virtuais) e os conjuntos de dimensionamento de máquinas virtuais do Azure em escala. O serviço analisa o desempenho e a integridade das VMs do Windows e do Linux, monitorando os processos e as dependências em outros recursos e processos externos. 

Como solução, o Azure Monitor para VMs inclui suporte para monitorar o desempenho e as dependências de aplicativos das VMs hospedadas localmente ou em outro provedor de nuvem. Três principais recursos fornecem insights detalhados:

* **Componentes lógicos de VMs do Azure que executam o Windows e o Linux**: São medidos em relação aos critérios de integridade pré-configurados e alertam quando a condição avaliada é atendida.  

* **Gráficos de desempenho de tendência predefinidos**: Exibem métricas básicas de desempenho no sistema operacional da VM convidada.

* **Mapa de dependências**: Exibe os componentes interconectados com a VM de vários grupos de recursos e assinaturas.  

Os recursos são organizados em três perspectivas:

* Integridade
* Desempenho
* Mapa

>[!NOTE]
>Atualmente, o recurso Integridade é oferecido apenas para máquinas virtuais do Azure e conjuntos de dimensionamento de máquinas virtuais. Os recursos Desempenho e Mapa dão suporte a VMs do Azure e a máquinas virtuais hospedadas no ambiente ou em outro provedor de nuvem.

A integração com o Log Analytics oferece agregação e filtragem eficientes e pode analisar tendências de dados ao longo do tempo. Esse monitoramento abrangente de carga de trabalho não pode ser obtido sozinho com o Azure Monitor, o Mapa do Serviço ou o Log Analytics.  

Você pode exibir esses dados em uma VM individual na máquina virtual diretamente ou pode usar o Azure Monitor para fornecer uma exibição agregada das VMs. Essa exibição baseia-se na perspectiva de cada recurso:

* **Integridade**: As VMs estão relacionadas a um grupo de recursos.
* **Mapa** e **Desempenho**: As VMs são configuradas para serem subordinadas a um workspace específico do Log Analytics.

![Perspectiva de insights de máquina virtual no portal do Azure](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

O Azure Monitor para VMs pode fornecer desempenho e disponibilidade previsíveis de aplicativos vitais. Ele identifica problemas de rede, gargalos de desempenho e eventos críticos do sistema operacional. O Azure Monitor para VMs pode ajudar você a entender se um problema está relacionado a outras dependências.  

## <a name="data-usage"></a>Uso de dados 

Quando você implanta o Azure Monitor para VMs, os dados coletados pelas VMs são ingeridos e armazenados no Azure Monitor. Com base nos preços publicados na [página de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/), o Azure Monitor para VMs é cobrado pelo seguinte:
* Os dados ingeridos e armazenados.
* O número de séries temporais de métrica de critérios de integridade monitorados.
* As regras de alerta criadas.
* As notificações enviadas. 

O tamanho do log varia de acordo com o tamanho das cadeias de caracteres de contadores e pode aumentar com o número de discos lógicos e de adaptadores de rede. Se você já tiver um workspace e estiver coletando esses contadores, nenhum encargo duplicado será aplicado. Se você já estiver usando o Mapa do Serviço, a única alteração que você verá serão os dados de conexão adicionais enviados ao Azure Monitor.

## <a name="next-steps"></a>Próximas etapas
Para entender os requisitos e os métodos que ajudam você a monitorar suas máquinas virtuais, examine [Implantar o Azure Monitor para VMs](vminsights-onboard.md).
