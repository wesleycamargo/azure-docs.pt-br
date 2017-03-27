---
title: "Visão Geral do Azure Monitor| Microsoft Docs"
description: "O Azure Monitor coleta estatísticas para uso em alertas, webhooks, dimensionamento automático e automação. O artigo também lista outras opções de monitoramento da Microsoft."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: ca55f1c295d99ff92abaef1a919d4c90d3c33b8a
ms.lasthandoff: 03/09/2017


---

# <a name="overview-of-azure-monitor"></a>Visão geral do Azure Monitor
Este artigo fornece uma visão geral conceitual dos recursos de monitoramento do Azure. Ele fornece links para informações sobre tipos específicos de recursos.  Para obter informações de alto nível sobre como monitorar seu aplicativo do ponto de vista não do Azure, consulte [Diretrizes de monitoramento e diagnóstico](../best-practices-monitoring.md).

Um vídeo passo a passo do Azure Monitor está disponível em  
[Introdução ao Azure Monitor](https://channel9.msdn.com/Blogs/Azure-Monitoring/Get-Started-with-Azure-Monitor). Um vídeo adicional explicando um cenário no qual você pode usar o Azure Monitor está disponível em [Explore o monitoramento e diagnósticos no Microsoft Azure](https://channel9.msdn.com/events/Ignite/2016/BRK2234).  

Os aplicativos em nuvem são complexos com muitas partes móveis. O monitoramento fornece dados para garantir que seu aplicativo permaneça ativo e em execução em um estado íntegro. Ele também ajuda a afastar os problemas potenciais ou solucionar problemas antigos. Além disso, você pode usar os dados de monitoramento para obter mais informações sobre seu aplicativo. Esse conhecimento pode ajudá-lo a melhorar o desempenho ou a capacidade de manutenção do aplicativo ou automatizar ações que normalmente exigiriam intervenção manual.

O diagrama a seguir mostra uma exibição conceitual do monitoramento do Azure, incluindo o tipo de logs que você pode coletar e o que você pode fazer com os dados.   

![Modelo para o monitoramento e diagnóstico dos recursos não de computação](./media/monitoring-overview/Monitoring_Azure_Resources-compute_v4.png)

Figura 1: modelo conceitual para o monitoramento e diagnóstico dos recursos que não são de computação

<br/>

![Modelo para o monitoramento e diagnóstico dos recursos de computação](./media/monitoring-overview/Monitoring_Azure_Resources-non-compute_v4.png)

Figura 2: modelo conceitual para o monitoramento e diagnóstico dos recursos de computação

## <a name="monitoring-sources"></a>Fontes de Monitoramento
### <a name="activity-logs"></a>Logs de atividade
Você pode pesquisar o Log de atividade (anteriormente chamado de Logs de auditoria ou operacionais) para obter informações sobre o recurso como visto pela infraestrutura do Azure. O log contém informações como os horários quando os recursos são criados ou destruídos.  

### <a name="host-vm"></a>VM de Host
**Computação Somente**

Alguns recursos de computação, como os Serviços de Nuvem, Máquinas Virtuais e Service Fabric, têm uma VM de Host dedicada com a qual eles interagem. A VM de Host é o equivalente da VM Raiz no modelo de hipervisor Hyper-V. Nesse caso, você pode coletar métricas apenas sobre a VM de Host além do SO convidado.  

Para os outros serviços do Azure, não há necessariamente um mapeamento 1:1 entre o recurso e uma determinada VM de Host, portanto, métricas da VM de Host não estão disponíveis.

### <a name="resource---metrics-and-diagnostics-logs"></a>Recursos - Métricas e Logs de Diagnóstico
As métricas colecionáveis variam com base no tipo de recurso. Por exemplo, máquinas virtuais fornecem estatísticas sobre a E/S de disco e Porcentagem da CPU. Mas essas estatísticas não existem para uma fila do Barramento de Serviço, que, em vez disso, fornece métricas como tamanho da fila e taxa de transferência de mensagem.

Para os recursos de computação, você pode obter métricas sobre o SO convidado e os módulos de diagnóstico, como o Diagnóstico do Azure. O Diagnóstico do Azure ajuda a coletar e rotear dados de diagnóstico para outros locais, incluindo o armazenamento do Azure.

Uma lista de métricas colecionáveis atualmente está disponível em [métricas com suporte](monitoring-supported-metrics.md).

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Aplicativo - Logs de Diagnóstico, Logs de Aplicativo e Métrica
**Computação Somente**

Os aplicativos podem ser executados sobre o SO convidado no modelo de computação. Eles emitem seu próprio conjunto de métricas e logs.

Os tipos de métrica incluem

* Contadores de desempenho
* Logs de aplicativo
* Logs de Eventos do Windows
* Fonte de evento do .NET
* Logs IIS
* Manifesto com base no ETW
* Despejos de Falha
* Logs de Erro do Cliente

## <a name="uses-for-monitoring-data"></a>Usos dos Dados de Monitoramento
### <a name="visualize"></a>Visualizar
Visualizar os dados de monitoramento nos gráficos ajuda a localizar as tendências muito mais rapidamente do que examinar os dados em si.  

Alguns métodos de visualização incluem:

* Use o Portal do Azure
* Rotear os dados para Application Insights do Azure
* Rotear os dados para o Microsoft PowerBI
* Rotear os dados para uma ferramenta de visualização de terceiros usando a transmissão ao vivo ou com a ferramenta de leitura de um arquivo no armazenamento do Azure

### <a name="archive"></a>Arquivo
Os dados de monitoramento normalmente são gravados no armazenamento do Azure e mantidos lá até você excluí-los.

Algumas formas de usar esses dados:

* Uma vez gravados, você pode ter outras ferramentas dentro ou fora do Azure para lê-los e processá-los.
* Você baixa os dados localmente para um arquivo local ou altera a política de retenção na nuvem para manter os dados por longos períodos de tempo.  
* <a name="you-leave-the-data-in-azure-storage-indefinitely-though-you-have-to-pay-for-azure-storage-based-on-the-amount-of-data-you-keep"></a>Você deixa os dados no armazenamento do Azure por tempo indefinido, embora precise pagar pelo armazenamento do Azure com base na quantidade de dados que mantém.
  -

### <a name="query"></a>Consultar
Você pode usar a API REST do Azure Monitor, comandos de CLI (interface de linha de comando) entre plataformas, cmdlets do PowerShell ou o SDK do .NET para acessar os dados no sistema ou no armazenamento do Azure

Alguns exemplos incluem: 

* Obtendo dados para um aplicativo de monitoramento personalizado escrito por você
* Criando consultas personalizadas e enviando esses dados para um aplicativo de terceiros.

### <a name="route"></a>Rota
Você pode transmitir os dados de monitoramento para outros locais em tempo real.

Os exemplos incluem:

* Envie para o Application Insights para que você possa usar as ferramentas de visualização lá.
* Envie para os Hubs de Eventos para que você possa rotear para as ferramentas de terceiros para executar a análise em tempo real.

### <a name="automate"></a>Automatizar
Você pode usar os dados de monitoramento para disparar alertas ou até processos inteiros.
Os exemplos incluem:

* Use os dados para dimensionar automaticamente as instâncias de computação com base na carga do aplicativo.
* Envie emails quando uma métrica passar de um limite predeterminado.
* Chame uma URL da Web (webhook) para executar uma ação em um sistema fora do Azure
* Inicie um runbook na automação do Azure para executar quaisquer tarefas

## <a name="methods-of-use"></a>Métodos de Uso
Em geral, você pode manipular o controle, roteamento e recuperação dos dados usando um dos métodos a seguir. Nem todos os métodos estão disponíveis para todas as ações ou tipos de dados.

* [Portal do Azure](https://portal.azure.com)
* [PowerShell](insights-powershell-samples.md)  
* [CLI (Interface de linha de comando) de plataforma cruzada](insights-cli-samples.md)
* [API REST](https://msdn.microsoft.com/library/dn931943.aspx)
* [SDK .NET](https://msdn.microsoft.com/library/dn802153.aspx)

## <a name="azures-monitoring-offerings"></a>Ofertas de Monitoramento do Azure
O Azure tem ofertas disponíveis para monitorar os serviços da infraestrutura sem sistema operacional para a telemetria do aplicativo. A melhor estratégia de monitoramento combina o uso de todas as três para obter uma visão abrangente e detalhada da integridade dos seus serviços.

* [Azure Monitor](http://aka.ms/azmondocs) – Oferece a visualização, consulta, roteamento, alertas, dimensionamento automático e automação nos dados da infraestrutura do Azure (Logs de Atividade) e de cada recurso do Azure individual (Logs de Diagnóstico). Este artigo faz parte da documentação do Azure Monitor. O nome Azure Monitor foi lançado em 25 de setembro no Ignite 2016.  O nome anterior era "Azure Insights".  
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) – fornece uma detecção avançada e diagnóstico para os problemas na camada do aplicativo de seu serviço, bem integrada nos dados do Monitoramento do Azure. É a plataforma de diagnóstico padrão para aplicativos Web do Serviço de Aplicativo.  Você pode rotear os dados de outros serviços para ele.  
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) parte do [Operations Management Suite](https://www.microsoft.com/oms/) – Fornece uma solução abrangente de gerenciamento de TI para infraestrutura local e em nuvens de terceiros (como o AWS), além dos recursos do Azure.  Os dados do Azure Monitor podem ser roteados diretamente para o Log Analytics para que você possa ver métricas e logs de todo o ambiente em um único lugar.     

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre

* [Azure Monitor em um vídeo do Ignite 2016](https://myignite.microsoft.com/videos/4977)
* [Introdução ao Azure Monitor](monitoring-get-started.md)
* [Diagnóstico do Azure](../azure-diagnostics.md) se você estiver tentando diagnosticar problemas em seu Serviço de Nuvem, Máquina Virtual ou aplicativo Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) se você tiver problemas de diagnóstico em seu aplicativo Web do Serviço de Aplicativo.
* [Solucionar de problemas no Armazenamento do Azure](../storage/storage-e2e-troubleshooting.md) ao usar os Blobs, Tabelas ou Filas de Armazenamento
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) e [Operations Management Suite](https://www.microsoft.com/oms/)

