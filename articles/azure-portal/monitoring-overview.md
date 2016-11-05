---
title: Visão Geral do Monitoramento no Microsoft Azure | Microsoft Docs
description: Visão geral de nível superior do monitoramento e diagnóstico no Microsoft Azure, incluindo alertas, webhooks, dimensionamento automático e muito mais.
author: rboucher
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: robb

---
# Visão Geral do Monitoramento no Microsoft Azure
Este artigo fornece uma visão geral conceitual do monitoramento de recursos do Azure e links para mais informações sobre os tipos específicos de recursos. Para obter informações de alto nível sobre como monitorar seu aplicativo do ponto de vista não do Azure, consulte [Diretrizes de monitoramento e diagnóstico](../best-practices-monitoring.md).

Os aplicativos em nuvem são complexos com muitas partes móveis. O monitoramento fornece dados para garantir que seu aplicativo permaneça ativo e em execução em um estado íntegro. Ele também ajuda a afastar os problemas potenciais ou solucionar problemas antigos. Além disso, você pode usar os dados de monitoramento para obter mais informações sobre seu aplicativo. Essas informações podem ajudar você a melhorar seu desempenho ou capacidade de manutenção, ou a automatizar algumas ações que normalmente exigiriam a intervenção manual.

O diagrama a seguir mostra uma exibição conceitual do monitoramento do Azure, incluindo o tipo de logs que você pode coletar e o que você pode fazer com os dados.

![Modelo Lógico para o monitoramento e diagnóstico dos recursos não de computação](./media/monitoring-overview/monitoring-azure-resources-non-compute.png)

Figura 1: modelo conceitual para o monitoramento e diagnóstico dos recursos que não são de computação

<br/>

![Modelo Lógico para o monitoramento e diagnóstico dos recursos de computação](./media/monitoring-overview/monitoring-azure-resources-compute.png)

Figura 2: modelo conceitual para o monitoramento e diagnóstico dos recursos de computação

## Fontes de Monitoramento
### Logs de Atividade da Infraestrutura
Você pode pesquisar os Logs de auditoria (às vezes chamados Logs operacionais ou de Atividade) em busca de informações sobre seus recursos, como visto pela infraestrutura do Azure. O log contém informações como os horários quando os recursos são criados ou destruídos.

### VM de Host
**Computação Somente**

Atualmente, não é possível coletar a métrica sobre a VM de Host, mas ela é listada aqui para ter integridade.

Alguns recursos de computação, como os Serviços de Nuvem, Máquinas Virtuais e Service Fabric, têm uma VM de Host dedicada com a qual eles interagem. A VM de Host é o equivalente da VM Raiz no modelo de hipervisor Hyper-V.

Para os outros serviços do Azure, não há necessariamente um mapeamento 1:1 entre o recurso e uma determinada VM de Host.

### Recursos - Métricas e Logs de Diagnóstico
As métricas colecionáveis variam com base no tipo de recurso. Por exemplo, máquinas virtuais fornecem estatísticas sobre a E/S de disco e Porcentagem da CPU. Mas essas estatísticas não existem para uma fila do Barramento de Serviço, que, em vez disso, fornece métricas como tamanho da fila e taxa de transferência de mensagem.

Para os recursos de computação, você pode obter métricas sobre o SO convidado e os módulos de diagnóstico, como o Diagnóstico do Azure. O Diagnóstico do Azure ajuda a coletar e rotear dados de diagnóstico para outros locais, incluindo o armazenamento do Azure.

### Aplicativo - Logs de Diagnóstico, Logs de Aplicativo e Métrica
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

## Usos dos Dados de Monitoramento
### Visualizar
Visualizar os dados de monitoramento nos gráficos ajuda a localizar as tendências muito mais rapidamente do que examinar os dados em si.

Alguns métodos de visualização incluem:

* Use o Portal do Azure
* Rotear os dados para Application Insights do Azure
* Rotear os dados para o Microsoft PowerBI
* Rotear os dados para uma ferramenta de visualização de terceiros usando a transmissão ao vivo ou com a ferramenta de leitura de um arquivo no armazenamento do Azure

### Arquivo
Os dados de monitoramento normalmente são gravados no armazenamento do Azure e mantidos lá até você excluí-los.

Algumas formas de usar esses dados:

* Uma vez gravados, você pode ter outras ferramentas dentro ou fora do Azure para lê-los e processá-los.
* Você baixa os dados localmente para um arquivo local ou altera a política de retenção na nuvem para manter os dados por longos períodos de tempo.
* Deixe os dados no armazenamento do Azure indefinidamente. Observe que é necessário pagar pelo armazenamento do Azure com base na quantidade de dados que você mantém.

### Consultar
Você pode usar a API REST do Insights, comandos CLI (Interface de Linguagem Comum), cmdlets do PowerShell ou o SDK do .NET para acessar os dados no sistema ou no armazenamento do Azure

Os exemplos incluem:

* Obtendo dados para um aplicativo de monitoramento personalizado escrito por você
* Criando consultas personalizadas e enviando esses dados para um aplicativo de terceiros.

### Rota
Você pode transmitir os dados de monitoramento para outros locais em tempo real.

Os exemplos incluem:

* Envie para o Application Insights para que você possa usar as ferramentas de visualização lá.
* Envie para os Hubs de Eventos para que você possa rotear para as ferramentas de terceiros para executar a análise em tempo real.

### Automatizar
Você pode usar os dados de monitoramento para disparar eventos ou até processos inteiros. Os exemplos incluem:

* Use os dados para dimensionar automaticamente as instâncias de computação com base na carga do aplicativo.
* Envie emails quando uma métrica passar de um limite predeterminado.
* Chame uma URL da Web (webhook) para executar uma ação em um sistema fora do Azure
* Inicie um runbook na automação do Azure para executar quaisquer tarefas

## Métodos de Uso
Em geral, você pode manipular o controle, roteamento e recuperação dos dados usando um dos métodos a seguir. Nem todos os métodos estão disponíveis para todas as ações ou tipos de dados.

* [Portal do Azure](https://portal.azure.com)
* [PowerShell](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [CLI (Interface de linha de comando) de plataforma cruzada](../monitoring-and-diagnostics/insights-cli-samples.md)
* [API REST](https://msdn.microsoft.com/library/dn931943.aspx)
* [SDK .NET](https://msdn.microsoft.com/library/dn802153.aspx)

## Ofertas de Monitoramento do Azure
O Azure tem ofertas disponíveis para monitorar os serviços da infraestrutura sem sistema operacional para a telemetria do aplicativo. A melhor estratégia de monitoramento combina o uso de todas as três para obter uma visão abrangente e detalhada da integridade dos seus serviços.

* [Monitoramento do Azure Insights (Plataforma)](http://aka.ms/azmondocs) – Oferece a visualização, consulta, roteamento, alertas, dimensionamento automático e automação nos dados da infraestrutura do Azure (Logs de Atividade) e de cada recurso do Azure individual (Logs de Diagnóstico). Este artigo faz parte da documentação do Azure Insights.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) – fornece uma detecção avançada e diagnóstico para os problemas na camada do aplicativo de seu serviço, bem integrada nos dados do Monitoramento do Azure.
* Parte do [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) do [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) – fornece uma solução de gerenciamento de TI abrangente para os locais e a infraestrutura baseada em nuvem, inclusive a pesquisa de log e a análise dos recursos do Azure.

## Próximas etapas
Saiba mais sobre

* [Azure Insights](http://aka.ms/azmondocs) explorando os links no Sumário à esquerda e os vídeos neste link.
* [Diagnóstico do Azure](../azure-diagnostics.md) se você estiver tentando diagnosticar problemas em seu Serviço de Nuvem, Máquina Virtual ou aplicativo Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) se você tiver problemas de diagnóstico em seu aplicativo Web do Serviço de Aplicativo.
* [Solucionar de problemas no Armazenamento do Azure](../storage/storage-e2e-troubleshooting.md) ao usar os Blobs, Tabelas ou Filas de Armazenamento
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) e [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite)

<!---HONumber=AcomDC_0907_2016-->