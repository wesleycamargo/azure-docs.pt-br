---
title: "Explorar a nova experiência de Alertas (Versão Prévia) no Azure Monitor | Microsoft Docs"
description: "Entenda como a nova experiência de alertas simples e escalonáveis no Azure possibilita criar, exibir e gerenciar alertas mais facilmente"
author: manishsm-msft
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: mamit
ms.custom: 
ms.openlocfilehash: 316dcd53509897a6efc387749ca6f9ec268cb7ac
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="explore-the-new-alerts-preview-experience-in-azure-monitor"></a>Explorar a nova experiência de Alertas (Versão Prévia) no Azure Monitor

## <a name="overview"></a>Visão geral
 A experiência de Alertas no Azure tem uma nova aparência e funções atualizadas. Essa nova experiência está disponível na guia **Alertas (Versão Prévia)** no Azure Monitor. Abaixo estão algumas das vantagens de se usar a nova experiência de Alertas (Versão Prévia):

 - **Separação de Alertas Disparados e Regras de Alerta** – na experiência de Alertas(Versão Prévia), as Regras de Alerta (a definição da condição que dispara um alerta) e os Alertas Disparados (uma instância do gatilho de regra de alerta) são diferenciados e, portanto, as exibições operacional e de configuração são diferentes.
 - **Uma experiência de criação unificada para alertas de métrica e de log** – a nova experiência de Alertas (Versão Prévia) orienta o usuário durante o processo de configurar uma regra de alerta, o que simplifica a percepção do que deve ser monitorado.
 - **Exibir alertas disparados do Log Analytics no portal do Azure** – na experiência de Alertas (Versão Prévia), agora você pode também consultar alertas do Log Analytics disparados na sua assinatura.  
 - **Experiência de criação unificada para alertas de Log de Atividades** – Agora você pode criar alertas de Log de atividades diretamente de **Monitor** > **Alertas (Versão Prévia)**. Anteriormente você poderia criá-los somente por meio de **Monitor** > **Log de atividades**.

As seções a seguir descrevem mais detalhadamente como funciona a nova experiência.

## <a name="taxonomy"></a>Taxonomia
A experiência de Alertas(versão prévia) usa os conceitos a seguir para separar os objetos de Regra de Alerta e Alerta Disparado enquanto unifica a experiência de criação em diferentes tipos de alerta.

- **Recurso de Destino** – um destino pode ser um recurso do Azure. O Recurso de Destino define o escopo e os sinais disponíveis para o alerta. Destinos de exemplo: uma máquina virtual, uma conta de armazenamento, um conjunto de dimensionamento de máquinas virtuais, um espaço de trabalho do Log Analytics ou uma solução.

- **Critérios** – critérios é a combinação de sinal e lógica aplicada a um recurso de destino. Exemplos: porcentagem da CPU > 70%, tempo de resposta do servidor > 4 ms, contagem de resultados de um log de consulta > 100, etc. 

- **Sinal** – sinais são emitidos pelo recurso de destino e podem ser de vários tipos. Esta versão prévia oferece suporte a **Métrica**, **Log de atividades**, **Application Insights** e **Log** como tipos de Sinais.

- **Lógica** – lógica definida pelo usuário para verificar se o sinal está dentro dos valores/ intervalo esperado.  
 
- **Ação** – uma chamada específica enviada a um destinatário de uma notificação (por exemplo, enviando por email um endereço ou publicando em uma URL de webhook). As notificações normalmente podem disparar várias ações. Os tipos de alerta com suporte nesta visualização dão suporte a grupos de ação.  
 
- **Regra de alerta** – a definição de uma condição que irá disparar o alerta. Nesta visualização, a Regra de alerta captura o destino e os critérios para o alerta. A Regra de alerta pode estar em estado Habilitado ou Desabilitado.
 
- **Alerta Disparado** – criado quando uma Regra de alerta habilitada é disparada. O objeto de alerta disparado pode ser Disparado ou Não Resolvido.

    > [!NOTE]
    > Isso é diferente da experiência de alertas atual, em que o alerta representa a regra e um alerta disparado e, portanto, pode estar em um dos estados de Aviso, Ativo ou Desabilitado.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>Mesmo lugar para exibir e gerenciar alertas
O objetivo da experiência de Alertas (Versão Prévia) é ter o único lugar para exibir e gerenciar todos os alertas do Azure. As subseções a seguir descrevem as funções de cada tela individual da nova experiência.

### <a name="alerts-preview-overview-page"></a>Página de visão geral de Alertas (Versão Prévia)
A página de visão geral **Monitor - Alertas (Versão Prévia)** mostra o resumo agregado de todos os alertas disparados e as regras de alerta configuradas/habilitadas. Ela também mostra uma lista de todos os alertas disparados. A mudança dos parâmetros de filtro ou assinaturas atualiza as agregações e as listas de alertas disparados.

> [!NOTE]
> Os Alertas Disparados mostrados em Alertas (Versão Prévia) são limitados a alarmes de métrica e de log com suporte; o Azure Monitor mostra a contagem de alertas de incêndio, inclusive os do Alertas do Azure mais antigos

 ![alertas-versão-prévia-visão-geral](./media/monitoring-overview-unified/alerts-preview-overview.png) 

### <a name="alert-rules-management"></a>Gerenciamento de regras de alerta
**Monitor - Regras de Alertas (Versão Prévia)>** é uma página única para gerenciar todas as regras de alerta das suas assinaturas do Azure. Ela lista todas as regras de alerta (habilitadas ou desabilitadas) e estas podem ser classificadas com base em grupos de recursos, recursos de destino, nome da regra ou status. As regras de alerta também podem ser habilitadas/desabilitadas ou editadas nessa página.  

 ![alertas-versão-prévia-regras](./media/monitoring-overview-unified/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>Uma única experiência de criação de alertas em todas as fontes de monitoramento
Na experiência Alertas (Versão Prévia), os alertas podem ser criados de forma consistente independentemente do serviço de monitoramento ou do tipo de sinal. Todos os alertas disparados e os detalhes relacionados ficam disponíveis em uma mesma página.  
 
A criação de um alerta é uma tarefa de três etapas, em que o usuário primeiro escolhe um destino para o alerta, seleciona o sinal correto e especifica a lógica que será aplicada ao sinal como parte da regra de alerta. Esse processo simplificado de criação não exige que o usuário conheça a origem do monitoramento ou os sinais com suporte antes de selecionar um recurso do Azure. A experiência de criação comum filtra a lista de sinais disponíveis automaticamente com base no recurso de destino selecionado e orienta a criação da lógica de alerta

Você pode aprender mais sobre como criar os tipos de alerta a seguir [aqui](monitor-alerts-unified-usage.md).
- Alertas de métrica (chamados de Alertas de Métrica em Tempo Quase Real na experiência atual)
- Alertas de log (Log Analytics)
- Alertas do log (Log de atividades)
- Alertas de log (Application Insights)

 

## <a name="alert-types-supported-in-this-preview"></a>Tipos de alertas com suporte nesta versão prévia


| **Tipo de sinal** | **Fonte do Monitor** | **Descrição** | 
|-------------|----------------|-------------|
| Métrica | Azure Monitor | Chamado [ **alertas de Métrica em Tempo Quase Real** ](monitoring-near-real-time-metric-alerts.md) na experiência atual, esses alertas de métricas dão suporte à avaliação de condições de métrica com frequência de até 1 minuto e permitem regras com várias métricas. Uma lista de tipos de recursos com suporte está disponível [aqui](monitoring-near-real-time-metric-alerts.md#what-resources-can-i-create-near-real-time-metric-alerts-for). Outros alertas de métrica definidos [aqui](monitoring-overview-alerts.md#alerts-in-different-azure-services) não têm suporte na experiência de Alertas (versão prévia).|
| Logs  | Log Analytics | Receber notificações ou executar ações automatizadas quando uma Consulta de pesquisa de log sobre dados de métrica e/ou de evento atender a certos critérios.|
| Logs  | Logs de atividade | Esta categoria contém os registros de todas as ações Criar, Atualizar e Excluir realizadas por meio do destino selecionado (recurso/grupo de recursos/assinatura). |
| Logs  | Logs de Integridade do Serviço | Sem suporte na experiência Alertas (versão prévia).   |
| Logs  | Application Insights | Esta categoria contém logs com os detalhes de desempenho do seu aplicativo. Usando a consulta de análise, você pode definir as condições para as ações a serem tomada – com base em dados do aplicativo. |
| Métrica | Application Insights | Sem suporte na experiência Alertas (versão prévia). |
| Testes de disponibilidade | Application Insights | Sem suporte na experiência Alertas (versão prévia). |


## <a name="next-steps"></a>Próximas etapas
- [Saiba como usar a nova experiência Alertas (Versão Prévia) para criar, exibir e gerenciar alertas](monitor-alerts-unified-usage.md)
- [Saiba mais sobre os alertas de log na experiência Alertas (Versão Prévia)](monitor-alerts-unified-log.md)
- [Saiba mais sobre os alertas de métrica na experiência Alertas (Versão Prévia)](monitoring-near-real-time-metric-alerts.md)
- [Saiba mais sobre os alertas do Log de atividades na experiência Alertas (Versão Prévia)](monitoring-activity-log-alerts-new-experience.md)
