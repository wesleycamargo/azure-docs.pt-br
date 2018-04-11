---
title: Explorar a nova experiência de Alertas no Azure Monitor | Microsoft Docs
description: Entenda como a nova experiência de alertas simples e escalonáveis no Azure possibilita criar, exibir e gerenciar alertas mais facilmente
author: manishsm-msft
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: mamit
ms.custom: ''
ms.openlocfilehash: 16e0fc493a257504e2708336e05c30b36d4bea15
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="the-new-alerts-experience-in-azure-monitor"></a>Explorar a nova experiência de alertas no Azure Monitor

## <a name="overview"></a>Visão geral

> [!NOTE]
> Este artigo descreve os alertas mais recentes. Os alertas clássicos mais antigos do Azure Monitor são descritos na [Visão geral de alertas clássicos](monitoring-overview-alerts.md). 
>
>

Alertas têm nova experiência. A experiência mais antiga de alertas agora está na guia Alertas (clássico). A nova experiência de alertas tem os seguintes benefícios em relação à experiência de Alertas (clássico):

-   **Melhor sistema de notificação**: todos os alertas mais recentes usam [grupos de ações]( https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups), que são grupos nomeados de notificações e ações que podem ser reutilizados em vários alertas.  Alertas de métrica clássicos e alertas antigos do Log Analytics não usam grupos de ações. 
- **Uma experiência de criação unificada** - Toda criação de alerta para métricas, logs e log de atividades no Azure Monitor, no Log Analytics e no Application Insights está em um lugar. 
- **Exibir alertas disparados do Log Analytics no portal do Azure** – Você pode agora também consultar alertas do Log Analytics disparados na sua assinatura. Anteriormente, eles estavam em um portal separado. 
- **Separação de Alertas Disparados e Regras de Alerta** – Regras de Alerta (a definição da condição que dispara um alerta) e os Alertas Disparados (uma instância do gatilho de regra de alerta) são diferenciados e, portanto, as exibições operacional e de configuração são diferentes.
- **Fluxo de trabalho melhor** – A nova experiência de criação de alertas orienta o usuário durante o processo de configurar uma regra de alerta, o que simplifica a percepção do que deve ser monitorado.
 
Os alertas de métrica mais recentes têm especificamente os aprimoramentos a seguir:
-   **Latência melhorada**: alertas de métrica mais recentes podem ser executados com uma frequência mínima a cada um minuto. Os alertas de métrica antigos são sempre executados em uma frequência de 5 minutos. Alertas do log ainda têm um atraso de mais de 1 minuto devido ao tempo necessário para ingerir os logs. 
-   **Suporte para métricas multidimensionais**: você pode alertar sobre métricas dimensionais, permitindo que você monitore um segmento interessante da métrica.
-   **Mais controle sobre as condições de métrica**: é possível definir regras de alerta mais avançadas. Os alertas mais recentes dão suporte ao monitoramento dos valores máximo, mínimo, média e total das métricas.
-   **Monitoramento combinado de várias métricas**: é possível monitorar várias métricas (atualmente, até duas métricas) com uma única regra. Um alerta é disparado quando ambas as métricas violam seus respectivos limites para o período especificado.
-   **Métricas dos Logs** (visualização pública limitada): alguns dados do log transferidos para o Log Analytics agora podem ser extraídos e convertidos em métricas do Azure Monitor e, em seguida, alertados da mesma forma que outras métricas. 



As seções a seguir descrevem mais detalhadamente como funciona a nova experiência.

## <a name="alert-rules-terminology"></a>Terminologia de regras de alerta
A nova experiência de alertas usa os conceitos a seguir para separar os objetos de Regra de Alerta e Alerta Disparado enquanto unifica a experiência de criação em diferentes tipos de alerta.

- **Recurso de Destino** – um destino pode ser um recurso do Azure. O Recurso de Destino define o escopo e os sinais disponíveis para o alerta. Destinos de exemplo: uma máquina virtual, uma conta de armazenamento, um conjunto de dimensionamento de máquinas virtuais, um espaço de trabalho do Log Analytics ou um recurso do Application Insights.

- **Critérios** – critérios é a combinação de sinal e lógica aplicada a um recurso de destino. Exemplos: porcentagem da CPU > 70%, tempo de resposta do servidor > 4 ms, contagem de resultados de um log de consulta > 100, etc. 

- **Sinal** – sinais são emitidos pelo recurso de destino e podem ser de vários tipos. **Métrica**, **Log de atividades**, **Application Insights** e **Log** são tipos de sinais suportados.

- **Lógica** – lógica definida pelo usuário para verificar se o sinal está dentro dos valores/ intervalo esperado.  
 
- **Ação** - Uma ação específica executada quando o alerta é disparado. Por exemplo, enviar um endereço de email ou chamar uma URL do webhook. Várias ações podem ocorrer quando um alerta é disparado. Esses alertas dão suporte a grupos de ação.  
 
- **Regra de alerta** – A condição que irá disparar o alerta. A regra de alerta captura o destino e os critérios para o alerta. A Regra de alerta pode estar em estado Habilitado ou Desabilitado.
 
    > [!NOTE]
    > Isso é diferente da experiência de Alertas (Clássico), em que o alerta representa a regra e um alerta disparado e, portanto, pode estar em um dos estados de Aviso, Ativo ou Desabilitado.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>Mesmo lugar para exibir e gerenciar alertas
O objetivo da experiência de Alertas é ter o único lugar para exibir e gerenciar todos os alertas do Azure. As subseções a seguir descrevem as funções de cada tela individual da nova experiência.

### <a name="alerts-overview-page"></a>Página de visão geral de alertas
A página de visão geral **Monitor - Alertas** mostra o resumo agregado de todos os alertas disparados e as regras de alerta configuradas/habilitadas. Ela também mostra uma lista de todos os alertas disparados. A mudança dos parâmetros de filtro ou assinaturas atualiza as agregações e as listas de alertas disparados.

> [!NOTE]
> Os Alertas Disparados mostrados em Alertas são limitados alertas de métrica e de log de atividade com suporte; a Visão Geral do Azure Monitor mostra a contagem de alertas disparados, inclusive os dos Alertas do Azure mais antigos

 ![alerts-overview](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>Gerenciamento de regras de alerta
**Monitor - Alertas>Regras** é uma página única para gerenciar todas as regras de alerta das suas assinaturas do Azure. Ela lista todas as regras de alerta (habilitadas ou desabilitadas) e estas podem ser classificadas com base em grupos de recursos, recursos de destino, nome da regra ou status. As regras de alerta também podem ser habilitadas/desabilitadas ou editadas nessa página.  

 ![alerts-rules](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>Uma única experiência de criação de alertas em todas as fontes de monitoramento
Na nova experiência de Alertas, os alertas podem ser criados de forma consistente independentemente do serviço de monitoramento ou do tipo de sinal. Todos os alertas disparados e os detalhes relacionados ficam disponíveis em uma mesma página.  
 
A criação de um alerta é uma tarefa de três etapas, em que o usuário primeiro escolhe um destino para o alerta, seleciona o sinal correto e especifica a lógica que será aplicada ao sinal como parte da regra de alerta. Esse processo simplificado de criação não exige que o usuário conheça a origem do monitoramento ou os sinais com suporte antes de selecionar um recurso do Azure. A experiência de criação comum filtra a lista de sinais disponíveis automaticamente com base no recurso de destino selecionado e orienta a criação da lógica de alerta

Você pode aprender mais sobre como criar os tipos de alerta a seguir [aqui](monitor-alerts-unified-usage.md).
- Alertas de Métricas
- Alertas de log (Log Analytics)
- Alertas do log (Log de atividades)
- Alertas de log (Application Insights)

 
## <a name="alerts-supported-in-new-experience"></a>Alertas com suporte em nova experiência
Os alertas estão disponíveis em vários serviços de monitoramento do Azure. Para saber mais sobre como e quando usar esses serviços, [confira este artigo](./monitoring-overview.md). A seguir, um detalhamento dos tipos de alertas disponíveis no Azure e que atualmente têm suporte pela nova experiência de alertas. 


| **Tipo de sinal** | **Fonte do Monitor** | **Descrição** | 
|-------------|----------------|-------------|
| Métrica | Azure Monitor | Também chamados [alertas de métrica quase em tempo real](monitoring-near-real-time-metric-alerts.md), dão suporte à avaliação de condições de métrica com frequência de 1 minuto e permitem regras de métrica multidimensionais e várias métricas. Uma lista de tipos de recursos com suporte está disponível [aqui](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported). |
| Métrica | Azure Monitor | [Alertas de métrica clássicos mais antigos](monitoring-overview-alerts.md) não têm suporte na nova experiência de alertas. É possível localizá-los em Alertas (Clássico) no Portal do Azure. Os alertas clássicos dão suporte a alguns tipos de métricas que ainda não foram movidos para os alertas mais recentes. Para uma lista completa, consulte [métrica com suporte](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-supported-metrics)
| Logs  | Log Analytics | Receber notificações ou executar ações automatizadas quando uma Consulta de pesquisa de log sobre dados de métrica e/ou de evento atender a certos critérios. Os alertas mais antigos do Log Analytics ainda estão disponíveis, mas estão [sendo copiados para a nova experiência](monitoring-alerts-extend.md). Além disso, uma [versão prévia dos *Logs do Log Analytics como métrica*](monitoring-alerts-extend-tool.md)está disponível. A versão prévia permite que você pegue alguns tipos de logs e converta-os em métrica, onde será possível alertá-los usando a nova experiência de alerta. A versão prévia será útil se você tiver logs não Azure e deseja obter em conjunto com métrica do Azure Monitor nativa. |
| Log de Atividade | Logs de atividades (geral) | Contém os registros de todas as ações Criar, Atualizar e Excluir realizadas por meio do destino selecionado (recurso/grupo de recursos/assinatura). |
| Log de Atividade  | Integridade do Serviço | Sem suporte na nova experiência de alertas. Consulte [Criar alertas do log de atividades em notificações de serviço](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Logs  | Application Insights | Contém logs com os detalhes de desempenho do aplicativo. Usando a consulta de análise, você pode definir as condições para as ações a serem tomada – com base em dados do aplicativo. |
| Métrica | Application Insights | Sem suporte na nova experiência de alertas. Consulte [Alertas de métrica](../application-insights/app-insights-alerts.md) |
| Testes de disponibilidade na Web | Application Insights | Sem suporte na experiência de Alertas.  Consulte [Alertas de teste da Web](../application-insights/app-insights-monitor-web-app-availability.md). Disponível para qualquer site instrumentado para enviar dados ao Application Insights. Receba uma notificação quando a disponibilidade ou capacidade de resposta de um site estiver abaixo das expectativas. |




## <a name="next-steps"></a>Próximas etapas
- [Saiba como usar a nova experiência de Alertas para criar, exibir e gerenciar alertas](monitor-alerts-unified-usage.md)
- [Saiba mais sobre os alertas de log na experiência de Alertas](monitor-alerts-unified-log.md)
- [Saiba mais sobre os alertas de métrica na experiência de Alertas](monitoring-near-real-time-metric-alerts.md)
- [Saiba mais sobre os alertas de log de atividades na experiência de Alertas](monitoring-activity-log-alerts-new-experience.md)
