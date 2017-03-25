---
title: "Visão geral das métricas no Microsoft Azure | Microsoft Docs"
description: "Visão geral das métricas e seus usos no Microsoft Azure"
author: kamathashwin
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 405ec51c-0946-4ec9-b535-60f65c4a5bd1
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: ashwink
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 86e025f9211a1d7ed07e831b7ce4c21be351513b
ms.lasthandoff: 03/09/2017

---

# <a name="overview-of-metrics-in-microsoft-azure"></a>Visão geral das métricas no Microsoft Azure
Este artigo descreve o que são as métricas no Microsoft Azure, seus benefícios e como começar a usá-las.  

## <a name="what-are-metrics"></a>O que são métricas?
O Azure Monitor permite consumir a telemetria para ter visibilidade do desempenho e da integridade de suas cargas de trabalho no Azure. Os tipos de dados de telemetria do Azure mais importantes são as métricas (também chamadas de contadores de desempenho) emitidas pela maioria dos recursos do Azure. O Azure Monitor fornece várias maneiras de configurar e consumir essas métricas para monitorar e solucionar problemas.

## <a name="what-can-you-do-with-metrics"></a>O que você pode fazer com as métricas?
As métricas são uma fonte valiosa de telemetria e permitem que você realize as seguintes tarefas:

* **Controlar o desempenho** do recurso (como uma VM, um site ou um aplicativo lógico) plotando as métricas em um gráfico do portal e fixando o gráfico em um painel.
* **Ser notificado sobre um problema** que afeta o desempenho do recurso quando uma métrica cruza determinado limite.
* **Configurar ações automatizadas**, como dimensionar automaticamente um recurso ou disparar um runbook quando uma métrica cruza certo limite.
* **Executar análises avançadas** ou gerar relatórios sobre as tendências de desempenho ou uso do recurso.
* **Arquive** o histórico de desempenho ou integridade do recurso para **auditoria/conformidade**.

## <a name="what-are-the-characteristics-of-metrics"></a>Quais são as características das métricas?
As métricas têm as seguintes características:

* Todas as métricas têm uma **frequência de um minuto**. Você recebe um valor da métrica a cada minuto de seu recurso, fornecendo uma visibilidade quase em tempo real do estado e da integridade do recurso.
* As métricas são **disponibilizadas imediatamente**. Isso significa que você não precisa aceitar nem configurar diagnósticos adicionais.
* Você pode acessar **30 dias do histórico** para cada métrica. Você pode examinar rapidamente as tendências recentes e mensais no desempenho ou na integridade do recurso.

Você também pode:

* Configurar uma **regra de alerta da métrica que envie uma notificação ou tome uma ação automatizada** quando a métrica cruzar o limite definido. O dimensionamento automático é uma ação automatizada especial que permite escalar horizontalmente seu recurso para atender às solicitações de entrada, às cargas em seu site da Web ou aos recursos de computação. Você pode configurar uma regra de configuração do Dimensionamento automático para escalar verticalmente/horizontalmente com base em uma métrica que cruza um limite.

* **Roteie** todas as métricas para o Application Insights ou Log Analytics (OMS) a fim de habilitar a análise instantânea, a pesquisa e os alertas personalizados sobre os dados das métricas dos recursos. Você também pode transmitir as métricas para um Hub de Eventos, o que permite a você roteá-las para o Stream Analytics do Azure ou para aplicativos personalizados para análise quase em tempo real. Você configura o Hub de Eventos usando as configurações de diagnóstico de streaming.

* **Arquive as métricas para armazenamento** para um maior tempo de retenção ou use-as para relatórios offline. Você pode rotear suas métricas para o Armazenamento de Blobs do Azure quando configura as definições de diagnóstico para o recurso.

* Descubra, acesse e **exiba todas as métricas** com facilidade usando o portal do Azure ao escolher um recurso e plotar as métricas em um gráfico.

* **Consuma** as métricas por meio das novas APIs REST do Azure Monitor.

* Métricas de **consulta** usando os cmdlets do PowerShell ou a API REST de Plataforma Cruzada.

  ![Roteamento das Métricas no Azure Monitor](./media/monitoring-overview-metrics/Metrics_Overview_v4.png)

## <a name="access-metrics-via-the-portal"></a>Acessar métricas pelo portal
Veja a seguir um rápido passo a passo de como criar um gráfico de métricas usando o portal do Azure.

### <a name="to-view-metrics-after-creating-a-resource"></a>Para exibir métricas após a criação de um recurso
1. Abra o portal do Azure.
2. Crie um site do Serviço de Aplicativo do Azure.
3. Após a criação de um site, vá para a folha **Visão geral** do site.
4. É possível exibir novas métricas como um bloco **Monitoramento**. Você pode editar o bloco e escolher mais métricas.

   ![Métricas em um recurso no Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview1.png)

### <a name="to-access-all-metrics-in-a-single-place"></a>Para acessar todas as métricas em único lugar
1. Abra o portal do Azure.
2. Navegue até a nova guia **Monitor** e escolha a opção **Métricas** abaixo dela.
3. Escolha a assinatura, o grupo de recursos e o nome do recurso na lista suspensa.
4. Exiba a lista de métricas disponíveis. Escolha a métrica em que está interessado e plote-a.
5. Você pode fixá-la no painel clicando no pino no canto superior direito.

   ![Acessar todas as métricas em um único lugar no Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview2.png)

> [!NOTE]
> Você pode acessar as métricas no nível do host nas VMs (baseadas no Azure Resource Manager) e nos conjuntos de dimensionamento de máquinas virtuais sem nenhuma configuração de diagnóstico adicional. Essas novas métricas no nível do host estão disponíveis para as instâncias do Windows e do Linux. Essas métricas não devem ser confundidas com as métricas no nível do SO Convidado, às quais você tem acesso quando ativa o Diagnóstico do Azure em suas VMs ou nos conjuntos de dimensionamento de máquinas virtuais. Para saber mais sobre como configurar o Diagnóstico, confira [What is Microsoft Azure Diagnostics](../azure-diagnostics.md) (O que é o Diagnóstico do Microsoft Azure).
>
>

## <a name="access-metrics-via-the-rest-api"></a>Acessar métricas pela API REST
As Métricas do Azure podem ser acessadas pelas APIs do Azure Monitor. Há duas APIs que ajudam você a descobrir e acessar as métricas:

* Use a [API REST de definições da Métrica do Azure Monitor](https://msdn.microsoft.com/library/mt743621.aspx) para acessar a lista de métricas disponíveis para um serviço.
* Use a [API REST de Métricas do Azure Monitor](https://msdn.microsoft.com/library/mt743622.aspx) para acessar os dados de métricas reais.

> [!NOTE]
> Este artigo aborda as métricas por meio da [nova API para métricas](https://msdn.microsoft.com/library/dn931930.aspx) dos recursos do Azure. A versão da API para a nova API de definições da métrica é 2016-03-01 e a versão para a API das métricas é 2016-09-01. As definições de métrica herdadas e as métricas podem ser acessadas com a versão da 2014-04-01 da API.
>
>

Para obter uma explicação mais detalhada de como usar as APIs REST do Azure Monitor, confira [Passo a passo da API REST do Azure Monitor](monitoring-rest-api-walkthrough.md).

## <a name="export-metrics"></a>Exportar métricas
Você pode ir para a folha **Configurações de diagnóstico** na guia **Monitor** e exibir as opções de exportação das métricas. É possível escolher métricas (e logs de diagnóstico) a serem roteadas para o Armazenamento de Blobs, os Hubs de Eventos do Azure ou o OMS para os casos de uso mencionados anteriormente neste artigo.

 ![Opções de exportação das métricas no Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview3.png)

Você pode configurar isso usando os modelos do Resource Manager, o [PowerShell](insights-powershell-samples.md), a [CLI do Azure](insights-cli-samples.md) ou as [APIs REST](https://msdn.microsoft.com/library/dn931943.aspx).

## <a name="take-action-on-metrics"></a>Executar uma ação com base nas métricas
Para receber notificações ou executar ações automatizadas com base nos dados de métrica, você pode definir regras de alerta ou configurações de dimensionamento automático.

### <a name="configure-alert-rules"></a>Configurar regras de alerta
Você pode configurar regras de alerta sobre as métricas. Essas regras de alerta podem verificar se uma métrica ultrapassou um determinado limite. Desse modo, elas podem notificar você por email ou disparar um webhook que pode ser usado para executar qualquer script personalizado. Também é possível usar o webhook para configurar integrações de produtos de terceiros.

 ![Métricas e regras de alerta no Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview4.png)

### <a name="autoscale-your-azure-resources"></a>Dimensionar automaticamente os recursos do Azure
Alguns recursos do Azure aceitam o dimensionamento vertical ou horizontal de várias instâncias para tratamento das cargas de trabalho. O dimensionamento automático aplica-se aos Serviços de Aplicativo (Aplicativos Web), aos conjuntos de dimensionamento de máquinas virtuais e aos Serviços de Nuvem clássicos do Azure. Você pode configurar regras de dimensionamento automático para escalar horizontal ou verticalmente quando uma determinada métrica que afeta sua carga de trabalho cruza um limite especificado. Para obter mais informações, consulte [Visão geral do dimensionamento automático](monitoring-overview-autoscale.md).

 ![Métricas e dimensionamento automático no Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="learn-about-supported-services-and-metrics"></a>Conheça os serviços e as métricas compatíveis
O Azure Monitor é uma nova infraestrutura das métricas. Ele oferece suporte para os seguintes serviços do Azure no portal do Azure e a nova versão da API do Azure Monitor:

* VMs (baseadas no Azure Resource Manager)
* conjuntos de escala de máquina virtual
* Batch
* Namespace do Hubs de Eventos
* Namespace do Barramento de Serviço (SKU premium somente)
* Banco de Dados SQL (versão 12)
* Pool SQL Elástico
* Sites
* Farms do servidor Web
* Aplicativos Lógicos
* Hubs IoT
* Cache Redis
* Rede: gateways de aplicativo
* Pesquisar

Você pode exibir um uma lista detalhada de todos os serviços compatíveis e suas métricas em [Métricas compatíveis com o Azure Monitor](monitoring-supported-metrics.md).

## <a name="next-steps"></a>Próximas etapas
Consulte os links neste artigo. Além disso, saiba mais sobre:  

* [Métricas comuns para dimensionamento automático](insights-autoscale-common-metrics.md)
* [Como criar regras de alerta](insights-alerts-portal.md)
* [Analisar logs do Armazenamento do Azure com o Log Analytics](../log-analytics/log-analytics-azure-storage.md)

