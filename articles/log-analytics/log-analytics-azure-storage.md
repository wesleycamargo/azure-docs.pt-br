---
title: "Visão geral da coleta de dados de armazenamento do Azure no Log Analytics | Microsoft Docs"
description: "Os recursos do Azure podem gravar logs e métricas em uma conta de armazenamento do Azure usando, muitas vezes, o Diagnóstico do Azure. O Log Analytics pode indexar esses dados e torná-los pesquisáveis."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2a160030ab51799199fc6df08133f811d4987feb


---
# <a name="collecting-azure-storage-data-in-log-analytics-overview"></a>Coletando dados de armazenamento do Azure na visão geral de Log Analytics
Muitos recursos do Azure conseguem gravar logs e métricas em uma Conta de Armazenamento do Azure. O Log Analytics pode consumir esses dados e facilitar o monitoramento dos recursos do Azure.

Para gravar no armazenamento do Azure, um recurso pode usar o diagnóstico do Azure ou ter sua própria maneira de gravar dados. Esses dados podem ser gravados em vários formatos em um dos seguintes locais:

* Tabela do Azure
* Blob do Azure
* EventHub

O Log Analytics dá suporte a serviços do Azure que gravam dados usando [logs de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Além disso, o Log Analytics oferece suporte a outros serviços que a saída de logs e as métricas de diferentes formatos e locais.  

> [!NOTE]
> São cobradas taxas de dados do Azure normais para armazenamento e transações quando você envia diagnósticos para uma conta de armazenamento e quando o Log Analytics lê os dados de sua conta de armazenamento.
> 
> 

![Diagrama de armazenamento do Azure](media/log-analytics-azure-storage/azure-storage-diagram.png)

## <a name="supported-azure-resources"></a>Recursos do Azure suportados
O Log Analytics pode coletar dados para os seguintes recursos do Azure:

| Tipo de recurso | Logs (Categorias de diagnóstico) | Solução do Log Analytics |
| --- | --- | --- |
| Application Insights |Disponibilidade <br> Eventos personalizados <br> Exceções <br> Solicitações <br> |Application Insights (Visualização) |
| Automação <br> Microsoft.Automation/AutomationAccounts |JobLogs <br> JobStreams |AzureAutomation (Visualização) |
| Cofre da Chave <br> Microsoft.KeyVault/Vaults |AuditEvent |KeyVault (Visualização) |
| Gateway de Aplicativo <br> Microsoft.Network/ApplicationGateways |ApplicationGatewayAccessLog <br> ApplicationGatewayPerformanceLog |AzureNetworking (Visualização) |
| Grupo de Segurança de Rede <br> Microsoft.Network/NetworkSecurityGroups |NetworkSecurityGroupEvent <br> NetworkSecurityGroupRuleCounter |AzureNetworking (Visualização) |
| Service Fabric |ETWEvent <br> Evento operacional <br> Evento de ator confiável <br> Evento de serviço confiável |ServiceFabric (Visualização) |
| Máquinas Virtuais |Linux Syslog <br> Evento do Windows <br> Log do IIS <br> Windows ETWEvent |*nenhum* |
| Funções web <br> Funções de trabalho |Linux Syslog <br> Evento do Windows <br> Log do IIS <br> Windows ETWEvent |*nenhum* |

> [!NOTE]
> Para o monitoramento de máquinas virtuais do Azure (Linux e Windows), é recomendável instalar a [extensão de VM do Log Analytics](log-analytics-azure-vm-extension.md). O agente fornece insights mais abrangentes sobre suas máquinas virtuais do que os fornecidos pelo diagnóstico gravado no armazenamento.
> 
> 

Você pode nos ajudar a priorizar logs adicionais para os OMS para análise votando em nossa [página de comentários](http://feedback.azure.com/forums/267889-azure-log-analytics/category/88086-log-management-and-log-collection-policy).

* Consulte [Analisar logs de diagnóstico do Azure usando Log Analytics](log-analytics-azure-storage-json.md) para saber mais sobre como o Log Analytics pode ler os logs de serviços do Azure que oferecem suporte a [logs de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md):
  * Cofre da Chave do Azure
  * Automação do Azure
  * Gateway de Aplicativo
  * Grupos de segurança de rede
* Consulte [usar armazenamento de blobs para IIS e armazenamento de tabelas para eventos](log-analytics-azure-storage-iis-table.md) para saber mais sobre como o Log Analytics pode ler os logs de serviços do Azure que gravam diagnósticos em armazenamento de tabelas ou de logs do IIS gravados para armazenamento de blob, incluindo:
  * Service Fabric
  * Funções web
  * Funções de trabalho
  * Máquinas Virtuais

## <a name="next-steps"></a>Próximas etapas
* [Analisar logs de diagnóstico do Azure usando o Log Analytics](log-analytics-azure-storage-json.md) para ler os logs de serviços do Azure que gravam diagnósticos no armazenamento de blobs no formato JSON.
* [Use o armazenamento de blobs para IIS e armazenamento de tabelas para eventos](log-analytics-azure-storage-iis-table.md) para ler os logs de serviços do Azure que gravam diagnósticos em armazenamento de tabelas ou de logs do IIS gravados para armazenamento de blobs.
* [Habilitar Soluções](log-analytics-add-solutions.md) para fornecer informações sobre os dados.
* [Usar consultas de pesquisa](log-analytics-log-searches.md) para analisar os dados.




<!--HONumber=Nov16_HO3-->


