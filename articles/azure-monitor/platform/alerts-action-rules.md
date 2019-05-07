---
title: Regras de ação para alertas do Azure Monitor
description: Noções básicas sobre quais regras de ação são e como configurar e gerenciá-los.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: e5d04fd136848684e866fae9768b252e3b6ca77f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138121"
---
# <a name="action-rules-preview"></a>Regras de ação (visualização)

Este artigo descreve quais regras de ação são e como configurar e gerenciá-los.

## <a name="what-are-action-rules"></a>Quais são as regras de ação?

Regras de ação permitem que você defina ações (ou supressão de ações) em qualquer escopo do Gerenciador de recursos (assinatura, grupo de recursos ou recurso). Eles têm uma variedade de filtros que permitem que você para restringi-lo para o subconjunto específico de instâncias do alerta que você deseja agir. 

Com as regras de ação, você pode:

* Suprima notificações e ações, se você tem janelas de manutenção planejadas ou para os fim de semana/feriados, em vez de precisar desabilitar cada regra de alerta individualmente.
* Defina a ações e notificações em escala: Em vez de ter que definir um grupo de ação individualmente para cada regra de alerta, agora você pode definir um grupo de ação para disparar alertas gerados em qualquer escopo. Por exemplo, eu pode optar por ter um gatilho de 'ContosoActionGroup' de grupo de ação para cada alerta gerado em minha assinatura.

## <a name="configuring-an-action-rule"></a>Configurar uma regra de ação

Você pode acessar o recurso selecionando **Gerenciar ações** dos alertas de página de aterrissagem no Azure Monitor. Em seguida, selecione **regras de ação (visualização)**. Você pode acessá-los, selecionando **regras de ação (visualização)** do painel da página de aterrissagem para alertas.

![Regras de ação da página de aterrissagem do Azure Monitor](media/alerts-action-rules/action-rules-landing-page.png)

Selecione **+ nova regra de ação**. 

![Adicionar nova regra de ação](media/alerts-action-rules/action-rules-new-rule.png)

Como alternativa, você também pode optar por criar uma regra de ação ao configurar uma regra de alerta.

![Adicionar nova regra de ação](media/alerts-action-rules/action-rules-alert-rule.png)

Agora você deve ver o fluxo de criação de regra de ação abrir. Configure os seguintes elementos: 

![Novo fluxo de criação de regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Scope

Primeiro, escolha o escopo, ou seja, o recurso de destino, grupo de recursos ou assinatura. Você também tem a capacidade de fazer seleção múltipla em uma combinação de qualquer uma das opções acima (em uma única assinatura). 

![Escopo da ação de regra](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>critérios de filtro

Além disso, você pode definir filtros para restringir ainda mais para baixo até um subconjunto específico de alertas sobre o escopo definido. 

Os filtros disponíveis são: 

* **Gravidade**: Selecione uma ou mais gravidades de alerta. Severidade = gravidade 1 significa que a regra de ação é aplicável a todos os alertas com severidade como gravidade 1.
* **Monitorar serviço**: Filtre com base no serviço de monitoramento de origem. Isso também é seleção múltipla. Por exemplo, o Monitor de serviço = "Application Insights" significa que a regra de ação é aplicável a todos os "Application Insights" com base em alertas.
* **Tipo de recurso**: Filtre com base em um tipo de recurso específico. Isso também é seleção múltipla. Por exemplo, o tipo de recurso = significa "Máquinas virtuais" que a regra de ação é aplicável a todas as máquinas virtuais.
* **ID da regra de alerta**: Permite que você filtre para regras de alerta específicas usando a ID da regra de alerta do Gerenciador de recursos.
* **Monitorar a condição**: Filtro de instâncias de alerta com "Disparado" ou "Resolvido" como a condição do monitor.
* **Descrição**: Regex de correspondência dentro a descrição definida como parte da regra de alerta.
* **O contexto de alerta (carga)**: Correspondência de RegEx dentro de [contexto de alerta](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) campos de uma instância de alerta.

Esses filtros são aplicados em conjunto um ao outro. Por exemplo, se eu definir 'Tipo de recurso' = 'Máquinas virtuais' e 'Gravidade' = 'Sev0', em seguida, posso ter filtrado para todos os alertas de 'Sev0' somente as minhas VMs. 

![Filtros de regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Configuração do grupo de ação ou de supressão

Em seguida, configure a regra de ação para a supressão de alerta ou suporte de grupo de ação. Você não pode escolher ambas. A configuração funciona em todas as instâncias de alerta correspondentes aos filtros e escopo definido anteriormente.

#### <a name="suppression"></a>Supressão

Se você selecionar **supressão**, configurar a duração para a supressão de ações e notificações. Escolha uma das seguintes opções:
* **A partir de agora (sempre)**: Suprime todas as notificações por tempo indeterminado.
* **Em um horário agendado**: Suprima notificações dentro de uma duração limitada.
* **Com uma recorrência**: Suprima em uma agenda de recorrência, que pode ser diária, semanal ou mensal.

![Supressão da regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Grupo de ações

Se você selecionar **grupo de ação** no botão de alternância, adicione um grupo de ação existente ou crie um novo. 

> [!NOTE]
> Você pode associar apenas um grupo de ação com uma regra de ação.

![Grupo de ação de regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Detalhes da regra de ação

Por fim, configure os seguintes detalhes para a regra de ação
* NOME
* Grupo de recursos no qual ele será salvo
* DESCRIÇÃO 

## <a name="example-scenarios"></a>Cenários de exemplo

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Cenário 1: Supressão de alertas com base na gravidade

A Contoso deseja suprimir as notificações para todos os alertas de Sev4 em todas as VMs em sua assinatura 'ContosoSub' cada semana.

**Solução:** Crie uma regra de ação com
* Scope = 'ContosoSub'
* Filtros
    * Severidade = 'Sev4'
    * Tipo de recurso = 'Máquinas virtuais'
* Supressão de recorrência é definido para semanal, e 'Sábado' e 'Domingo' verificada

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Cenário 2: Supressão de alertas com base no contexto do alerta (carga)

A Contoso deseja suprimir notificações para todos os log de alertas gerados por 'Computador-01' em 'ContosoSub' indefinidamente como ele está passando por manutenção.

**Solução:** Crie uma regra de ação com
* Scope = 'ContosoSub'
* Filtros
    * Monitorar serviço = a análise de Log
    * O contexto de alerta (carga) contém 'Computador-01'
* Supressão definido como ' a partir de agora (sempre)'

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Cenário 3: Grupo de ações definido em um grupo de recursos

A Contoso definiu [um alerta de métrica no nível da assinatura](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor), mas quer definem as ações que disparam alertas separadamente de seu grupo de recursos 'ContosoRG'.

**Solução:** Crie uma regra de ação com
* Scope = 'ContosoRG'
* Nenhum filtro
* Grupo de ação definido como 'ContosoActionGroup'

## <a name="managing-your-action-rules"></a>Gerenciar suas regras de ação

Você pode exibir e gerenciar suas regras de ação da exibição de lista, conforme mostrado abaixo.

![Exibição de lista de regras de ação](media/alerts-action-rules/action-rules-list-view.png)

A partir daqui, você pode regras de ação de habilitar/desabilitar/excluir em grande escala, selecionando a caixa de seleção ao lado deles. Clicar em qualquer regra de ação abre a página de configuração, permitindo que você atualize sua definição e habilitar/desabilitá-lo.

## <a name="best-practices"></a>Práticas recomendadas

Log de alertas criados com o ['número de resultados'](https://docs.microsoft.com/azure-monitor/platform/alerts-unified-log) opção Gerar **uma única instância de alerta** usando o resultado de pesquisa inteiro (o que poderia ser em vários computadores por exemplo). Nesse cenário, se uma regra de ação usa o filtro de "Contexto de alerta (carga)", ele atuará na instância do alerta enquanto houver uma correspondência. No cenário 2 conforme descrito anteriormente, se os resultados da pesquisa para o alerta de log gerados contêm 'Computador-01' e 'Computador-02', a notificação inteira será suprimida (ou seja, não há nenhuma notificação gerada para 'Computador-02' em todos os).

![Regras de ação e alertas do log (número de resultados)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Para alertas do log de aproveitar melhor com as regras de ação, aconselhamos que você criar alertas do log com o ['medição métrica'](https://docs.microsoft.com/azure-monitor/platform/alerts-unified-log) opção. Usando essa opção, instâncias separadas de alertas são geradas com base no campo de grupo definida. Em seguida, no cenário 2, instâncias separadas de alertas são geradas para 'Computador-01' e 'Computador-02'. Com a regra de ação descrita no cenário, somente a notificação para 'Computador-01' poderia ser suprimida enquanto a notificação para 'Computador-02' continuará a ser acionado como normal.

![Regras de ação e alertas do log (número de resultados)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Perguntas frequentes

* P. Ao configurar uma regra de ação, eu gostaria de ver todos os possíveis sobreposição de ação de regras para que posso evitar notificações duplicadas. É possível fazer isso?

    a. Depois que você definir o escopo ao configurar uma regra de ação, você pode ver uma lista de regras de ação que se sobrepõem no mesmo escopo (se houver). Essa sobreposição pode ser uma das seguintes opções:
    * Uma correspondência exata: Por exemplo, a regra de ação que você está definindo e a regra de ação sobrepostos são na mesma assinatura.
    * Um subconjunto: Por exemplo, a regra de ação que você está definindo está em uma assinatura, e a regra de ação sobreposição está em um grupo de recursos dentro da assinatura.
    * Um superconjunto: Por exemplo, a regra de ação que você está definindo está em um grupo de recursos e a regra de ação sobrepostos está na assinatura que contém o grupo de recursos.
    * Uma interseção: Por exemplo, a regra de ação que você está definindo está na 'VM1' e 'VM2' e a regra de ação de sobreposição está em 'VM2' e 'VM3'.

    ![Sobreposição de regras de ação](media/alerts-action-rules/action-rules-overlapping.png)

* P. Enquanto Configurando uma regra de alerta, é possível saber se já houver regras de ação definido que pode agir sobre a regra de alerta que estou definindo?

    a. Depois de definir o recurso de destino para a regra de alerta, você pode ver a lista de regras de ação que atuam no mesmo escopo (se houver), clicando no 'Modo de exibição configurado ações' na seção 'Ações'. Esta lista é preenchida com base nos seguintes cenários para o escopo:
    * Uma correspondência exata: Por exemplo, a regra de alerta que você está definindo e a regra de ação estão na mesma assinatura.
    * Um subconjunto: Por exemplo, a regra de alerta que você está definindo está em uma assinatura, e a regra de ação está em um grupo de recursos dentro da assinatura.
    * Um superconjunto: Por exemplo, a regra de alerta que você está definindo está em um grupo de recursos e a regra de ação está na assinatura que contém o grupo de recursos.
    * Uma interseção: Por exemplo, a regra de alerta que você está definindo está em 'VM1' e 'VM2' e a regra de ação está em 'VM2' e 'VM3'.
    
    ![Sobreposição de regras de ação](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

* P. Posso ver os alertas que foram suprimidos por uma regra de ação?

    a. No [página de lista de alertas](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), há uma coluna adicional que pode ser escolhida chamado 'supressão de Status'. Se a notificação para uma instância de alerta foi suprimida, ele seria mostram o status na lista.

    ![Instâncias de alerta suprimidas](media/alerts-action-rules/action-rules-suppressed-alerts.png)

* P. Se houver uma regra de ação com um grupo de ações e outro com supressão Active Directory no mesmo escopo, o que acontece?

    a. **Supressão sempre tem precedência no mesmo escopo**.

* P. O que acontece se eu tiver um recurso monitorado em duas regras de ação separada? Eu recebo uma ou duas notificações? Por exemplo 'VM2' neste cenário:

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1'
      action rule 'AR2' defined for 'VM2' and 'VM3' with action group 'AG1'

    a. Para cada alerta em 'VM1' e 'VM3', 'AG1' do grupo de ação deverá ser disparado uma vez. Para cada alerta de 'VM2', 'AG1' do grupo de ação deverá ser disparado duas vezes (**regras de ação não eliminar a duplicação ações**). 

* P. O que acontece se eu tiver um recurso monitorado em duas regras de ação separada e um chama para ação, enquanto o outro para supressão? Por exemplo, VM2 neste cenário:

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1' 
      action rule 'AR2' defined for 'VM2' and 'VM3' with suppression

    a. Para cada alerta na 'VM1', 'AG1' do grupo de ação deverá ser disparado uma vez. Ações e notificações para cada alerta em 'VM2' e 'VM3' serão suprimidas. 

* P. O que acontece se eu tiver uma regra de alerta e uma regra de ação definidos para o mesmo recurso de grupos de ações diferentes de chamada? Por exemplo, VM1 neste cenário:

      alert rule  'rule1' on          'VM1' with action group 'AG2'
      action rule 'AR1'   defined for 'VM1' with action group 'AG1' 
 
    a. Para cada alerta na 'VM1', 'AG1' do grupo de ação deverá ser disparado uma vez. Sempre que a regra de alerta 'rule1' for disparada, ele também disparará 'AG2' Além disso. **Grupos de ações definidas dentro de regras de ação e as regras de alerta operam de forma independente, com a eliminação de duplicação**. 

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre alertas no Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
