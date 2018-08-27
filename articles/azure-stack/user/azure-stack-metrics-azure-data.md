---
title: O Azure monitor no Azure Stack | Microsoft Docs
description: Saiba mais sobre o Azure monitor no Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: mabrigg
ms.openlocfilehash: 5663d6fbf6272e46a1d9b75d84ee90a3f46c1d30
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889955"
---
# <a name="azure-monitor-on-azure-stack"></a>O Azure Monitor no Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Este artigo fornece uma visão geral do serviço do Azure Monitor no Azure Stack. Ele aborda a operação do Azure Monitor e informações adicionais sobre como usar o Azure Monitor no Azure Stack. 

Para obter uma introdução, visão geral e como começar com o Azure Monitor, consulte o artigo do Azure global [Introdução ao Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

![Folha de Monitor de pilha do Azure](./media/azure-stack-metrics-azure-data/azs-monitor.png)

O Azure Monitor é o serviço de plataforma que fornece uma única fonte para monitorar os recursos do Azure. Com o Azure Monitor, você pode visualizar, consultar, rotear, arquivar e também tomar medidas relacionadas às métricas e aos logs provenientes dos recursos do Azure. Você pode trabalhar com esses dados usando o portal de administração do Azure Stack, Cmdlets do PowerShell do Monitor, da CLI de plataforma cruzada ou APIs REST do Azure Monitor. Para a conectividade específica com suporte do Azure Stack, consulte [como consumir dados de monitoramento do Azure Stack](azure-stack-metrics-monitor.md)

> [!Note]  
As métricas e logs de diagnóstico não estão disponíveis para o Kit de desenvolvimento do Azure Stack.

## <a name="prerequisites"></a>Pré-requisitos

Registre-se a **Microsoft. Insights** provedor de recursos em configurações de provedores de recurso de oferta da sua assinatura. Você pode verificar que o provedor de recursos está disponível em sua oferta associada à sua assinatura:

1. Abra o portal de administração do Azure Stack.
2. Selecione **oferece**.
3. Selecione a oferta associada à assinatura.
4. Selecione **provedores de recursos** sob **configurações.** 
5. Encontre **Microsoft. Insights** na lista e verifique se o status é **registrado.**.

## <a name="overview"></a>Visão geral

Como o Azure Monitor no Azure, o Azure Monitor no Azure Stack fornece logs e métricas de infraestrutura de nível básico para a maioria dos serviços.

## <a name="azure-monitor-sources-compute-subset"></a>Fontes do Azure monitor: subconjunto de computação

![O Azure monitor fontes - subconjunto de computação](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.png)

O **Microsoft. Compute** inclui o provedor de recursos no Azure Stack:
 - Máquinas Virtuais 
 - Conjuntos de dimensionamento de Máquinas Virtuais

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Aplicativo - logs de diagnóstico, logs de aplicativo e métricas

Aplicativos podem ser executados no sistema operacional de uma VM em execução com o **Microsoft. Compute** provedor de recursos. Esses aplicativos e VMs emitem seu próprio conjunto de métricas e logs. O Azure Monitor se baseia na extensão de diagnóstico do Azure (Windows ou Linux) para coletar a maioria dos logs e métricas de nível do aplicativo. 

Os tipos de medidas incluem:
 - contadores de desempenho
 - Logs de aplicativo
 - Logs de eventos do Windows
 - Fonte de evento do .NET
 - Logs IIS
 - ETW baseado em manifesto
 - Despejos de falhas
 - Logs de erro do cliente

> [!Note]  
> Não há suporte para a extensão de diagnóstico do Linux no Azure Stack.

### <a name="host-and-guest-vm-metrics"></a>Métricas de VM host e convidada

Os recursos de computação listados anteriormente têm uma VM host dedicada e SO convidado. A VM host e o sistema operacional convidado são equivalentes a VM raiz e o convidado de VM no hipervisor Hyper-V. Você pode coletar métricas para a VM do host e o sistema operacional convidado. Além disso, você pode coletar logs de diagnóstico para o sistema operacional convidado. Uma lista de métricas colecionáveis para métricas de Host e convidado de VM no Azure Stack estão disponíveis em [métricas com suporte no Azure Monitor no Azure Stack](azure-stack-metrics-supported.md). 

### <a name="activity-log"></a>Log de atividades

Você pode pesquisar os logs de atividade para obter informações sobre os recursos de computação, como visto pela infraestrutura do Azure Stack. O log contém informações como os horários quando os recursos são criados ou destruídos. Os logs de atividade no Azure Stack é consistente com o Azure. Para obter mais informações, consulte a descrição da [visão geral do log de atividades no Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). 


## <a name="azure-monitor-sources-everything-else"></a>Fontes do Azure monitor: todo o resto

![Código-fonte do Azure monitor - todo o resto](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.png)

### <a name="resources---metrics-and-diagnostics-logs"></a>Logs de recursos - métricas e diagnósticos

Logs de diagnóstico e métricas colecionáveis variam com base no tipo de recurso. Uma lista de métricas colecionáveis para cada recurso no Azure Stack está disponível em métricas com suporte. Para obter mais informações, consulte [métricas com suporte no Azure Monitor no Azure Stack](azure-stack-metrics-supported.md).

### <a name="activity-log"></a>Log de atividades

O log de atividades é que o mesmo para os recursos de computação. 

### <a name="uses-for-monitoring-data"></a>Usos para os dados de monitoramento

**Store e arquivamento**  

Alguns dados de monitoramento já ficam armazenados e disponíveis no Azure Monitor por um período determinado. 
 - As métricas são armazenadas por 90 dias. 
 - As entradas do log de atividades são armazenadas por 90 dias. 
 - Logs de diagnóstico não são armazenados.
 - Arquive os dados em uma conta de armazenamento para retenção maior.

**Consulta**  

Você pode usar a API REST do Azure Monitor, comandos de Interface de linha de comando (CLI) de plataforma cruzada, cmdlets do PowerShell ou o SDK do .NET para acessar os dados no sistema ou no armazenamento do Azure. 

**Visualização**

Visualizar os dados de monitoramento em grafos ajuda a localizar tendências mais rapidamente do que examinar os dados em si. 

Alguns métodos de visualização incluem:
 - Usar o portal de administrador e usuário do Azure Stack
 - Dados de rota para o Microsoft Power BI
 - Rotear os dados para uma ferramenta de visualização de terceiros usando a transmissão ao vivo ou com a ferramenta de leitura de um arquivo no armazenamento do Azure

## <a name="methods-of-accessing-azure-monitor-on-azure-stack"></a>Métodos para acessar o Azure monitoram na pilha do Azure

Em geral, você pode manipular o controle, roteamento e recuperação dos dados usando um dos métodos a seguir. Nem todos os métodos estão disponíveis para todas as ações ou tipos de dados.

 - [Portal do Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-use-portal)
 - [PowerShell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [Interface(CLI) de linha de comando de plataforma cruzada](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [API REST](https://docs.microsoft.com/rest/api/monitor)
 - [SDK .NET](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as opções do consumo de dados de monitoramento no Azure Stack no artigo [consumir dados de monitoramento do Azure Stack](azure-stack-metrics-monitor.md).
