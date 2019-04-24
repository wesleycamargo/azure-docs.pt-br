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
ms.date: 03/13/2019
ms.author: magoedte
ms.openlocfilehash: a60413ee6614b638db58748ee2c0aea5d7ea32ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60199895"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>O que é o Azure Monitor para VMs (versão prévia)?

O Azure Monitor para VMs monitora as VMs (máquinas virtuais) e os conjuntos de dimensionamento de máquinas virtuais do Azure em escala. Ele analisa o desempenho e a integridade das VMs do Windows e do Linux e monitora os processos e as dependências de outros recursos e processos externos. 

Como solução, o Azure Monitor para VMs inclui suporte para monitorar o desempenho e as dependências de aplicativos das VMs hospedadas localmente ou em outro provedor de nuvem. Três principais recursos fornecem insights detalhados:

* **Componentes lógicos de VMs do Azure que executam o Windows e o Linux**: São medidos em relação aos critérios de integridade pré-configurados e alertam quando a condição avaliada é atendida.  

* **Gráficos de desempenho mais populares predefinido**: Exibem métricas básicas de desempenho no sistema operacional da VM convidada.

* **Mapa de dependências**: Exibe os componentes interconectados com a VM de vários grupos de recursos e assinaturas.  

Os recursos são organizados em três perspectivas:

* Saúde
* Desempenho
* Mapear

>[!NOTE]
>Atualmente, o recurso de integridade é oferecido somente para máquinas virtuais do Azure. Desempenho e recursos de mapa de suportam a VMs do Azure, conjuntos de dimensionamento de VM do Azure e máquinas virtuais que são hospedadas em seu ambiente ou outro provedor de nuvem.

A integração com os logs do Azure Monitor oferece uma agregação e uma filtragem eficientes, e pode analisar as tendências de dados ao longo do tempo. Esse monitoramento abrangente da carga de trabalho não pode ser obtido sozinho com o Azure Monitor ou o Mapa do Serviço.  

Você pode exibir esses dados em uma VM individual na máquina virtual diretamente ou pode usar o Azure Monitor para fornecer uma exibição agregada das VMs. Essa exibição baseia-se na perspectiva de cada recurso:

* **Integridade**: As VMs estão relacionadas a um grupo de recursos.
* **Mapa** e **Desempenho**: As VMs são configuradas para serem subordinadas a um espaço de trabalho do Log Analytics específico.

![Perspectiva de insights de máquina virtual no portal do Azure](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

O Azure Monitor para VMs pode fornecer desempenho e disponibilidade previsíveis de aplicativos vitais. Ele identifica problemas de rede, gargalos de desempenho e eventos críticos do sistema operacional. O Azure Monitor para VMs pode ajudar você a entender se um problema está relacionado a outras dependências.  

## <a name="data-usage"></a>Uso de dados 

Quando você implanta o Azure Monitor para VMs, os dados coletados pelas VMs são ingeridos e armazenados no Azure Monitor. As métricas de critérios de integridade são armazenadas no Azure Monitor em um banco de dados de série temporal, desempenho e dependência de dados coletados são armazenados em um espaço de trabalho do Log Analytics. Com base nos preços publicados na [página de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/), o Azure Monitor para VMs é cobrado pelo seguinte:

* Os dados ingeridos e armazenados.
* O número de séries temporais de métrica de critérios de integridade monitorados.
* As regras de alerta criadas.
* As notificações enviadas. 

Varia de acordo com o tamanho do log com os comprimentos de cadeia de caracteres de contadores de desempenho, e ele pode aumentar com o número de discos lógicos e adaptadores de rede alocados para a VM. Se você já tiver um workspace e estiver coletando esses contadores, nenhum encargo duplicado será aplicado. Se você já estiver usando o Mapa do Serviço, a única alteração que você verá serão os dados de conexão adicionais enviados ao Azure Monitor.

## <a name="next-steps"></a>Próximas etapas
Para entender os requisitos e os métodos que ajudam você a monitorar suas máquinas virtuais, examine [Implantar o Azure Monitor para VMs](vminsights-onboard.md).
