---
title: Visão geral da extensão do Diagnóstico do Azure
description: Usar o diagnóstico do Azure para depurar, medir o desempenho, monitorar e analisar o tráfego em serviços de nuvem, em máquinas virtuais e no Service Fabric
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/13/2018
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: b00d774ec59755288b8660d238c7b8dfc9a89eab
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089886"
---
# <a name="what-is-azure-diagnostics-extension"></a>O que é a extensão Diagnóstico do Azure
A extensão Diagnóstico do Azure é um agente no Azure que permite a coleta de dados de diagnóstico em um aplicativo implantado. Você pode usar a extensão de diagnóstico de várias fontes diferentes. As que têm suporte no momento são as Funções de Trabalho ou Web do Serviço de Nuvem do Azure (clássico), as Máquinas Virtuais, os conjuntos de dimensionamento de Máquinas Virtuais e o Service Fabric. Outros serviços do Azure têm métodos diferentes de diagnósticos. Consulte [Visão geral do monitoramento no Azure](monitoring-overview.md). 

## <a name="linux-agent"></a>Agente do Linux
Uma [versão do Linux da extensão](../virtual-machines/linux/diagnostic-extension.md) está disponível para Máquinas Virtuais que executam o Linux. As estatísticas coletadas e o comportamento variam conforme a versão do Windows. 

## <a name="data-you-can-collect"></a>Dados que você pode coletar
A extensão Diagnóstico do Azure pode coletar os seguintes tipos de dados:

| Fonte de dados | DESCRIÇÃO |
| --- | --- |
| contadores de desempenho |Contadores de desempenho personalizados e do Sistema Operacional |
| Logs de aplicativo |Rastreio de mensagens gravadas pelo seu aplicativo |
| Logs de Eventos do Windows |Informações enviadas ao sistema de log de eventos do Windows |
| Fonte de evento do .NET |Eventos de gravação de código usando a classe [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) do .NET |
| Logs IIS |Informações sobre sites do IIS |
| Manifesto com base no ETW |Rastreamento de Eventos para eventos do Windows gerados por qualquer processo.(1) |
| Despejos de falhas |Informações sobre o estado do processo no caso de uma falha do aplicativo |
| Logs de erros personalizados |Logs criados por seu aplicativo ou serviço |
| Logs de infraestrutura do Diagnóstico do Azure |Informações sobre o próprio Diagnóstico |

(1) para obter uma lista de provedores ETW, execute `c:\Windows\System32\logman.exe query providers` em uma janela de console no computador do qual você gostaria de coletar informações. 

## <a name="data-storage"></a>Armazenamento de dados
A extensão armazena seus dados em uma [conta do Armazenamento do Azure](azure-diagnostics-storage.md) especificada. 

Você também pode enviá-los para o [Application Insights](../application-insights/app-insights-cloudservices.md). Outra opção é transmiti-los para o [Hub de Eventos](../event-hubs/event-hubs-what-is-event-hubs.md), que permite o envio dos dados para serviços de monitoramento que não fazem parte do Azure. 


## <a name="versioning-and-configuration-schema"></a>Controle de versão e esquema de configuração
Confira [Histórico de versão e esquema do Diagnóstico do Azure](azure-diagnostics-versioning-history.md).


## <a name="next-steps"></a>Próximas etapas
Escolha de qual serviço você está tentando coletar diagnósticos e use os seguintes artigos para começar. Use os links gerais de diagnóstico do Azure para obter referências de tarefas específicas.

## <a name="cloud-services-using-azure-diagnostics"></a>Serviços de Nuvem que usam o Diagnóstico do Azure
* Se estiver usando o Visual Studio, veja [Usar o Visual Studio para rastrear um aplicativo dos Serviços de Nuvem](../vs-azure-tools-debug-cloud-services-virtual-machines.md) para começar. Caso contrário, veja
* [Como monitorar os Serviços de Nuvem que usam o Diagnóstico do Azure](../cloud-services/cloud-services-how-to-monitor.md)
* [Configurar o Diagnóstico do Azure em um Aplicativo dos Serviços de Nuvem](../cloud-services/cloud-services-dotnet-diagnostics.md)

Para tópicos mais avançados, veja

* [Uso dos Diagnósticos do Azure com o Application Insights para Serviços de Nuvem](../application-insights/app-insights-cloudservices.md)
* [Rastrear o fluxo de um aplicativo de Serviços de Nuvem com o Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Usar o PowerShell para configurar o diagnóstico nos Serviços de Nuvem](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Máquinas Virtuais
* Se estiver usando o Visual Studio, veja [Usar o Visual Studio para rastrear Máquinas Virtuais do Azure](../vs-azure-tools-debug-cloud-services-virtual-machines.md) para começar. Caso contrário, veja
* [Configurar o Diagnóstico do Azure em uma Máquina Virtual do Azure](../virtual-machines-dotnet-diagnostics.md)

Para tópicos mais avançados, veja

* [Usar o PowerShell para configurar o diagnóstico nas Máquinas Virtuais do Azure](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Criar uma máquina virtual do Windows com monitoramento e diagnóstico usando o modelo do Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Comece em [Monitorar um aplicativo do Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Muitos outros artigos de diagnóstico do Service Fabric estarão disponíveis na árvore de navegação à esquerda depois que você acessar este artigo.

## <a name="general-articles"></a>Artigos gerais
* Saiba como [usar os Contadores de Desempenho no Diagnóstico do Azure](../cloud-services/diagnostics-performance-counters.md).
* Caso tenha problemas com o início do diagnóstico ou a localização de seus dados nas tabelas de armazenamento do Azure, confira [Solução de problemas do Diagnóstico do Azure](azure-diagnostics-troubleshooting.md)
