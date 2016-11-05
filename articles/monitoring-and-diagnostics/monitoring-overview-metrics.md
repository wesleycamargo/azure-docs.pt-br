---
title: Visão geral das métricas no Microsoft Azure | Microsoft Docs
description: Visão geral das métricas e seus usos no Microsoft Azure
author: kamathashwin
manager: carolz
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: ashwink

---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Visão geral das métricas no Microsoft Azure
Este artigo descreve o que são as métricas no Microsoft Azure, seus benefícios e como começar a usá-las.  

## <a name="what-are-metrics?"></a>O que são métricas?
O Azure Monitor permite consumir a telemetria para ter visibilidade do desempenho e da integridade de suas cargas de trabalho no Azure. O tipo de dados de telemetria do Azure mais importantes são as métricas (também chamadas de contadores de desempenho) emitidas pela maioria dos recursos do Azure. O Azure Monitor fornece várias maneiras de configurar e consumir essas métricas para monitorar e solucionar problemas.

## <a name="what-can-you-do-with-metrics?"></a>O que você pode fazer com as Métricas?
As métricas são uma fonte valiosa de telemetria e permitem que você faça o seguinte:

* **Controlar o desempenho** do recurso (como uma VM, site ou Aplicativo Lógico) plotando as métricas em um gráfico do portal e fixando o gráfico em um painel.
* **Ser notificado sobre um problema** que afeta o desempenho do recurso quando uma métrica cruza certo limite.
* **Configurar ações automatizadas**, como dimensionar automaticamente um recurso ou disparar um runbook quando uma métrica cruza certo limite.
* **Executar análises avançadas** ou gerar relatórios sobre as tendências de desempenho ou uso do(s) recurso(s).
* **Arquivar** o histórico de desempenho ou integridade do recurso para a **auditoria/conformidade**.

## <a name="metric-characteristics"></a>Características da Métrica
As métricas têm as seguintes características:

* Todas as métricas têm uma **frequência de 1 minuto**. Você recebe um valor da métrica a cada minuto de seu recurso, fornecendo uma visibilidade quase em tempo real do estado e da integridade do recurso.
* As métricas estão **disponíveis prontamente sem a necessidade de aceitar** ou configurar diagnósticos adicionais.
* Você pode acessar **30 dias do histórico** para cada métrica. Você pode examinar rapidamente as tendências recentes e mensais no desempenho ou na integridade do recurso.

Você pode:

* Descubra facilmente, acesse e **exiba todas as métricas** por meio do portal do Azure quando você selecionar um recurso e plotá-lo em um gráfico. 
* Configure uma **regra de alerta da métrica que envia uma notificação ou toma uma ação automatizada** quando a métrica cruza o limite definido. O dimensionamento automático é uma ação automatizada especial que permite escalar horizontalmente seu recurso para atender às solicitações de entrada, carga em seu site da Web ou recursos de computação. Você pode configurar uma regra de configuração do Dimensionamento automático para escalar verticalmente/horizontalmente com base em uma métrica cruzando um limite.
* **arquivamento** para mais tempo ou use-as para relatórios offline. Você pode rotear suas métricas para o armazenamento de blobs quando configura as definições de diagnóstico para o recurso.
* **transmissão** metrics to an Event Hub, enabling you to then route them to Azure transmissão Analytics or custom apps for near-real time analysis. Você pode fazer isso usando as configurações de diagnóstico.
* **Roteia** todas as métricas para o Log Analytics do OMS para desbloquear a análise instantânea, pesquisa e alertas personalizados sobre os dados das métricas a partir dos recursos.
* **Consome** as métricas por meio das novas APIs REST do Azure Monitor.
* **consulta** usando os Cmdlets do PowerShell ou a API REST de Plataforma Cruzada.
  
  ![Roteamento das Métricas no Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview0.png)

## <a name="access-metrics-via-portal"></a>Métricas de acesso via portal
Aqui está uma rápida explicação passo a passo da criação de um gráfico de métricas via portal do Azure

### <a name="view-metrics-after-creating-a-resource"></a>Visualizar as métricas depois de criar um recurso
1. Abrir o portal do Azure
2. Crie um novo Serviço de Aplicativo - site da Web.
3. Depois de criar um site, vá para a folha Visão Geral do site.
4. Você pode exibir as novas métricas como um bloco 'Monitorando'. É possível editar o bloco e selecionar mais métricas
   
   ![Métricas em um recurso no Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview1.png)    

### <a name="access-all-metrics-in-a-single-place"></a>Acessar todas as métricas em um único lugar
1. Abrir o portal do Azure 
2. Navegue até a nova guia Monitor e selecione a opção Métricas abaixo 
3. Você pode selecionar sua assinatura, grupo de recursos e o nome do recurso na lista suspensa. 
4. Agora, você pode exibir a lista de métricas disponíveis. 
5. Selecione a métrica na qual está interessado e plote. 
6. Você pode fixá-la no painel clicando no pino no canto superior direito.
   
   ![Acessar todas as métricas em um único lugar no Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview2.png) 

> [!NOTE]
> Você pode acessar as métricas no nível do host a partir das VMs (com base no Azure Resource Manager) e Conjuntos de Dimensionamento da VM sem nenhuma configuração de diagnóstico adicional. Essas novas métricas no nível do host estão disponíveis para as instâncias do Windows e do Linux. Essas métricas não devem ser confundidas com as métricas no nível do SO Convidado, às quais você tem acesso quando ativa o Diagnóstico do Azure em suas VMs ou VMSS. Para saber mais sobre como configurar o Diagnóstico do Azure, consulte [O Que é Diagnóstico do Microsoft Azure](../azure-diagnostics.md).
> 
> 

## <a name="access-metrics-via-rest-api"></a>Métricas de acesso via API REST
As Métricas do Azure podem ser acessadas via APIs do Azure Monitor. Há duas APIs que ajudam você a descobrir e acessar as métricas. Use: 

* [API REST de definições da Métrica do Azure Monitor](https://msdn.microsoft.com/library/mt743621.aspx) para acessar a lista de métricas disponíveis para um serviço.
* [API REST de Métricas do Azure Monitor](https://msdn.microsoft.com/library/mt743622.aspx) para acessar os dados da métrica reais

> [!NOTE]
> Este artigo aborda as métricas por meio da [nova API para métricas](https://msdn.microsoft.com/library/dn931930.aspx) dos recursos do Azure. A versão da API para a nova API de definições da métrica é 2016-03-01 e a versão para a API das métricas é 2016-09-01. As definições das métricas de herança e as métricas podem ser acessadas com a versão da api 2014-04-01.
> 
> 

Para obter uma explicação mais detalhada usando as APIs REST do Azure Monitor, consulte [Passo a Passo da API REST do Azure Monitor](monitoring-rest-api-walkthrough.md).

## <a name="export-options-for-metrics"></a>Opções de exportação das métricas
Você pode ir para a folha Logs de Diagnóstico na guia Monitor e exibir as opções de exportação das métricas. Você pode selecionar as métricas (e logs de diagnóstico) a serem roteadas para o Armazenamento de Blobs, Hubs de Eventos ou Log Analytics do OMS para os casos de uso mencionados anteriormente neste artigo. 

 ![Opções de exportação das métricas no Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview3.png)   

Você pode configurar isso por meio dos modelos do Resource Manager, [PowerShell](insights-powershell-samples.md), [CLI](insights-cli-samples.md) ou [APIs REST](https://msdn.microsoft.com/library/dn931943.aspx). 

## <a name="take-action-on-metrics"></a>Tome uma Ação nas Métricas
Você pode receber notificações ou tomar ações automatizadas nos dados da métrica. Você pode configurar regras de Alerta ou configurações do Dimensionamento automático para fazer isso.

### <a name="alert-rules"></a>Regras de Alerta
Você pode configurar regras de alerta sobre as métricas. Essas regras de alerta podem verificar se uma métrica cruzou certo limite e notificá-lo por email ou acionar um webhook que pode ser usado para executar qualquer script personalizado. Você também pode usar o webhook para configurar integrações de produtos de terceiros.

 ![Métricas e regras de alerta no Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview4.png)

### <a name="autoscale"></a>Autoscale
Alguns recursos do Azure oferecem suporte a várias instâncias para escalar horizontalmente ou verticalmente e lidar com as cargas de trabalho. O dimensionamento automático aplica-se aos Serviços de Aplicativos (aplicativos Web), Conjuntos de Dimensionamento da Máquina Virtual (VMSS) e Serviços de Nuvem clássicos. Você pode configurar regras de dimensionamento automático para escalar horizontalmente ou verticalmente quando certa métrica, que afeta sua carga de trabalho, cruza um limite especificado. Para obter mais informações, consulte [Visão geral do dimensionamento automático](monitoring-overview-autoscale.md).

 ![Métricas e dimensionamento automático no Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="supported-services-and-metrics"></a>Serviços e Métricas com Suporte
O Azure Monitor é uma nova infraestrutura das métricas. Ele oferece suporte para os seguintes serviços do Azure no portal do Azure e a nova versão da API do Azure Monitor:

* VMs (com base no Azure Resource Manager)
* Conjuntos de Dimensionamento da VM
* Batch
* Namespace do Hub de Eventos 
* Namespace do Barramento de Serviço (SKU premium somente)
* SQL (versão 12)
* Pool SQL Elástico
* Sites
* Farms do Servidor Web
* Aplicativos Lógicos
* Hubs IoT
* Cache Redis
* Rede: Gateways do Aplicativo
* Pesquisar

Você pode exibir um uma lista detalhada de todos os serviços com suporte e suas métricas em [Métricas do Azure Monitor - métricas suportadas por tipo de recurso](monitoring-supported-metrics.md). 

## <a name="next-steps"></a>Próximas etapas
Consulte os links neste artigo. Além disso, saiba mais:  

* sobre as [métricas comuns para o dimensionamento automático](insights-autoscale-common-metrics.md)
* como [criar regras de alerta](insights-alerts-portal.md)

<!--HONumber=Oct16_HO2-->


