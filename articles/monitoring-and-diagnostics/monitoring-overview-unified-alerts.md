---
title: Alertas unificados no Azure Monitor
description: Descrição de alertas unificados no Azure que permitem que você gerencie alertas e regras de alerta entre os serviços do Azure.
author: manishsm-msft
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: mamit
ms.component: alerts
ms.openlocfilehash: ff2650ec7d4c2c1fffd57176327b56199335fa9d
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264809"
---
# <a name="unified-alerts-in-azure-monitor"></a>Alertas unificados no Azure Monitor

## <a name="overview"></a>Visão geral

> [!NOTE]
>  Uma nova experiência de alerta unificada que permite que você gerencie alertas de várias assinaturas e apresenta estados de alerta e grupos inteligentes está disponível em versão prévia pública no momento. Consulte a [última seção deste artigo](#enhanced-unified-alerts-experience-public-preview) para encontrar uma descrição desta experiência aprimorada e do processo para habilitá-la.


Este artigo descreve a experiência de alerta unificada no Azure Monitor. A [experiência de alerta anterior](monitoring-overview-alerts.md) está disponível na opção **Alertas (Clássico)** no menu do Azure Monitor. 

## <a name="features-of-the-unified-alert-experience"></a>Recursos da experiência de alerta unificada

A experiência unificada tem os seguintes benefícios em relação à experiência clássica:

-   **Melhor sistema de notificação**: os alertas unificados usam [grupos de ações]( https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups), que são grupos nomeados de notificações e ações que podem ser reutilizados em vários alertas. 
- **Experiência de criação unificada**: você pode gerenciar alertas e regras de alerta para métricas, logs e log de atividades entre o Azure Monitor, o Log Analytics e o Application Insights em um único lugar. 
- **Exibição de alertas do Log Analytics disparados no portal do Azure**: exiba alertas do Log Analytics com outros alertas de outras fontes no portal do Azure. Anteriormente, eles estavam em um portal separado.
- **Separação alertas disparados e regras de alerta**: as regras de alerta agora são diferenciadas dos alertas. Uma regra de alerta consiste nas definições de uma condição que dispara um alerta. Um alerta é uma instância de um disparo de regra de alerta.
- **Fluxo de trabalho melhor**: a experiência de criação de alerta unificada orienta você pelo processo de configuração de uma regra de alerta.
 
Os alertas de métrica têm os seguintes aprimoramentos em relação aos alertas de métrica clássicos:

-   **Latência melhorada**: os alertas de métrica podem ser executados com uma frequência mínima a cada um minuto. Os alertas de métrica clássicos são sempre executados em uma frequência de cinco minutos. Alertas de log ainda têm um atraso de mais de um minuto devido ao tempo necessário para ingerir os logs. 
-   **Suporte para métricas multidimensionais**: você pode alertar sobre métricas dimensionais, permitindo monitorar uma instância específica da métrica.
-   **Mais controle sobre as condições de métrica**: você pode definir regras de alerta mais sofisticadas que dão suporte ao monitoramento dos valores máximo, mínimo, médio e total de métricas.
-   **Monitoramento combinado de várias métricas**: é possível monitorar até duas métricas com uma única regra. Um alerta é disparado quando ambas as métricas violam seus respectivos limites para o período especificado.
-   **Métricas dos Logs** (visualização pública limitada): alguns dados do log transferidos para o Log Analytics agora podem ser extraídos e convertidos em métricas do Azure Monitor e, em seguida, alertados da mesma forma que outras métricas. 


## <a name="alert-rules"></a>Regras de alerta
A experiência de alertas unificada usa os seguintes conceitos para separar as regras de alerta dos alertas ao unificar a experiência de criação entre diferentes tipos de alerta.

| item | Definição |
|:---|:---|
| Regra de alerta | Definição da condição para criar um alerta. Composto de um _recurso de destino_, _sinal_, _critérios_ e _lógica_. Uma regra de alerta estará ativa apenas se estiver com o estado _habilitado_.
| Recurso de destino | Define os recursos específicos e sinaliza disponível para alertas. Um destino pode ser um recurso do Azure.<br>Exemplos: máquina virtual, conta de armazenamento, conjunto de dimensionamento de máquinas virtuais, espaço de trabalho do Log Analytics ou recurso do Application Insights |
| Sinal | Fonte de dados emitidos pelo recurso de destino. Os tipos de sinal compatíveis são *Métrica*, *Log de atividades*, *Application Insights* e *Log*. |
| Critérios | A combinação de _sinal_ e _lógica_ aplicada a um recurso de destino.<br>Exemplos: porcentagem da CPU > 70%, tempo de resposta do servidor > 4 ms, contagem de resultados de um log de consulta > 100, etc. |
| Lógica | Lógica definida pelo usuário para verificar se o sinal está dentro dos valores/intervalo esperado. |
| Ação | Ação a ser executada quando o alerta é disparado. Várias ações podem ocorrer quando um alerta é disparado. Esses alertas dão suporte a grupos de ação.<br>Exemplos: enviar um endereço de email, chamar uma URL do webhook. |
| Monitorar condição | Indica se a condição que criou um alerta de métrica subsequentemente foi resolvida. As regras de alerta de métrica criam uma amostra de uma métrica específica em intervalos regulares. Se os critérios na regra de alerta são atendidos, um novo alerta é criado com uma condição de acionado.  Quando a métrica for amostrada novamente, se os critérios ainda tiverem sido atendidos, não acontecerá nada.  Se os critérios que não são atendidos, a condição do alerta é alterada como resolvido. Na próxima vez que os critérios forem atendidos, um outro alerta será criado com uma condição de acionado. |


## <a name="alert-pages"></a>Páginas de alertas
Os alertas unificados fornecem um único local para exibir e gerenciar todos os alertas do Azure. As seções a seguir descrevem as funções de cada página individual da experiência unificada.

### <a name="alerts-overview-page"></a>Página de visão geral de alertas
A página de visão geral **Alertas** mostra o resumo agregado de todos os alertas disparados e as regras de alerta habilitadas. A mudança dos parâmetros de filtro ou assinaturas atualiza as agregações e as listas de alertas disparados.

 ![alerts-overview](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>Gerenciamento de regras de alerta
**Regras** é uma página única para gerenciar todas as regras de alerta das suas assinaturas do Azure. Ela lista todas as regras de alerta e estas podem ser classificadas com base em grupos de recursos, recursos de destino, nome da regra ou status. As regras de alerta também podem ser habilitadas, desabilitadas ou editadas nessa página.

 ![alerts-rules](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="creating-an-alert-rule"></a>Criar uma regra de alerta
Os alertas podem ser criados de forma consistente independentemente do serviço de monitoramento ou do tipo de sinal. Todos os alertas disparados e os detalhes relacionados ficam disponíveis em uma mesma página.
 
Você pode criar uma nova regra de alerta com estas três etapas:
1. Escolha o _destino_ para o alerta.
1. Selecione o _sinal_ entre os sinais disponíveis para o destino.
1. Especifique a _lógica_ a ser aplicada aos dados do sinal.
 
Esse processo simplificado de criação não exige que o usuário conheça a origem do monitoramento ou os sinais com suporte antes de selecionar um recurso do Azure. A lista de sinais disponíveis é filtrada automaticamente com base no recurso de destino selecionado e orienta você na definição da lógica da regra de alerta.

Você pode aprender mais sobre como criar regras de alerta no [Criar, exibir e gerenciar alertas usando o Azure Monitor](monitor-alerts-unified-usage.md).

Os alertas estão disponíveis em vários serviços de monitoramento do Azure. Para obter informações sobre como e quando usar cada um desses serviços, consulte [Monitorando aplicativos e recursos do Azure](./monitoring-overview.md). A tabela a seguir fornece uma lista dos tipos de regras de alerta disponíveis no Azure e o que é compatível atualmente com a experiência unificada de alerta.

| **Fonte do Monitor** | **Tipo de sinal**  | **Descrição** | 
|-------------|----------------|-------------|
| Azure Monitor | Métrica  | Também chamados [alertas de métrica quase em tempo real](monitoring-near-real-time-metric-alerts.md), dão suporte à avaliação de condições de métrica com frequência de 1 minuto e permitem regras de métrica multidimensionais e várias métricas. Uma lista de tipos de recursos compatíveis está disponível em [Alertas de métrica mais recentes para os serviços do Azure no portal do Azure](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).<br>[Alertas de métrica clássicos](monitoring-overview-alerts.md) não têm suporte na nova experiência de alertas. É possível localizá-los em Alertas (Clássico) no Portal do Azure. Os alertas clássicos dão suporte a alguns tipos de métricas que ainda não foram movidos para os alertas mais recentes. Para uma lista completa, consulte [métrica compatíveis](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics). |
| Log Analytics | Logs  | Receber notificações ou executar ações automatizadas quando uma Consulta de pesquisa de logs atender a certos critérios. Os alertas no Log Analytics estão [sendo copiados para a nova experiência](monitoring-alerts-extend.md). Uma [versão prévia dos *Logs do Log Analytics como métrica*](monitoring-alerts-extend-tool.md) está disponível. A versão prévia permite que você pegue alguns tipos de logs e converta-os em métrica, onde será possível alertá-los usando a nova experiência de alerta. A versão prévia será útil se você tiver logs não Azure e deseja obter em conjunto com métrica do Azure Monitor nativa. |
| Logs de atividade | Log de Atividade | Contém os registros de todas as ações de criar, atualizar e excluir criados pelo destino selecionado. |
| Integridade do Serviço | Log de Atividade  | Não é compatível em alertas unificados. Consulte [Criar alertas do log de atividades em notificações de serviço](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Application Insights | Logs  | Contém logs com os detalhes de desempenho do aplicativo. Usando a consulta de análise, você pode definir as condições para as ações a serem tomada – com base em dados do aplicativo. |
| Application Insights | Métrica | Não é compatível em alertas unificados. Consulte [Metric alerts].(../application-insights/app-insights-alerts.md) |
| Application Insights | Testes de disponibilidade na Web | Não é compatível em alertas unificados.  Consulte [Alertas de teste da Web](../application-insights/app-insights-monitor-web-app-availability.md). Disponível para qualquer site instrumentado para enviar dados ao Application Insights. Receba uma notificação quando a disponibilidade ou capacidade de resposta de um site estiver abaixo das expectativas. |

## <a name="enhanced-unified-alerts-public-preview"></a>Alertas unificados aprimorados (Versão prévia pública)
> [!NOTE]
>  A funcionalidade nesta seção estará disponível em breve. Ele pode não aparecer em sua versão do portal ainda. 

Uma experiência de alertas unificada aprimorada foi lançada na versão prévia pública para o Azure Monitor em 1º de junho de 2018. Essa experiência amplia os benefícios dos [alertas unificados](#overview) lançados em março de 2018 e fornece a capacidade de gerenciar e agregar alertas individuais além de modificar o estado de alerta. Esta seção descreve os novos recursos e como navegar nas novas páginas de alertas no portal do Azure.

### <a name="features-enhanced-unified-alerts"></a>Recursos aprimorados nos alertas unificados

A nova experiência fornece os seguintes recursos que não estão disponíveis na experiência unificada clássica:

- **Exibir alertas entre assinaturas**: agora você pode exibir e gerenciar instâncias individuais de alertas entre várias assinaturas em uma única exibição.
- **Gerenciar o estado de alertas**: agora os alertas têm um estado que indica se foi confirmado para fechamento.
- **Organizar os alertas com Grupos Inteligentes**: os Grupos Inteligentes automaticamente agrupam os alertas relacionados, portanto, você pode gerenciá-los como um conjunto em vez de individualmente.

### <a name="enable-enhanced-unified-alerts"></a>Habilitar alertas unificados aprimorados
Habilite a nova experiência de alerta unificada clicando na faixa na parte superior da página Alertas. Esse processo cria um repositório de alertas que inclui os últimos 30 dias de alertas acionados em serviços compatíveis. Depois que a nova experiência estiver habilitada, você poderá alternar entre a experiência de nova e a antiga clicando na faixa.

> [!NOTE]
>  Pode levar alguns minutos para que a nova experiência seja inicialmente habilitada.

![Faixa](media/monitoring-overview-unified-alerts/opt-in-banner.png)

Todas as assinaturas às quais você tem acesso serão registradas ao habilitar a nova experiência. Embora a assinatura inteira esteja habilitada, somente os usuários que selecionaram a nova experiência poderão exibi-la. Outros usuários com acesso à assinatura devem habilitar a experiência separadamente.

Habilitar a nova experiência de alerta não afeta a configuração de grupos de ação ou notificações em suas regras de alerta. Isso apenas altera a maneira que você exibe e gerencia as instâncias acionadas dos alertas no portal do Azure.

### <a name="smart-groups"></a>Grupos Inteligentes
Grupos inteligentes reduzem o ruído, permitindo que você gerencie alertas relacionados como uma única unidade, em vez de gerenciar alertas individuais. Você pode exibir os detalhes dos grupos inteligentes e definir o estado semelhante a um alerta. Cada alerta é um membro de apenas um grupo inteligente.

Os grupos inteligentes são criados automaticamente usando o aprendizado de máquina para combinar os alertas relacionados que representam um único problema. Quando um alerta é criado, o algoritmo o adiciona a um novo grupo inteligente ou a um grupo inteligente existente com base em informações como padrões históricos, similaridade de propriedades e similaridade da estrutura. Atualmente, o algoritmo considera apenas alertas do mesmo serviço de monitor em uma assinatura. Os grupos inteligentes podem reduzir até 99% do ruído de alerta por meio dessa consolidação. Você pode exibir o motivo que os alertas foram incluídos em um grupo na página Detalhes do Grupo Inteligente.

O nome de um grupo inteligente é o nome do seu primeiro alerta. Você não pode criar ou renomear um grupo inteligente.


### <a name="alert-states"></a>Estados de alerta
Os alertas unificados avançados introduzem o conceito de estado de alerta. Você pode definir o estado de um alerta para especificar onde ele está no processo de resolução.  Quando um alerta é criado, ele tem um status de *Novo*. Você pode alterar o status quando já confirmou um alerta e quando o fechou. Qualquer alteração de estado é armazenada no histórico do alerta.

Os seguintes estados de alerta são compatíveis.

| Estado | DESCRIÇÃO |
|:---|:---|
| Novo | O problema acaba de ser detectado e ainda não foi examinado. |
| Confirmado | Um administrador examinou o alerta e começou a trabalhar nele. |
| Fechado | O problema foi resolvido. Depois que um alerta foi fechado, você poderá reabri-lo alterando-o para outro estado. |

O estado de um alerta é diferente da condição do monitor. As regras de alerta de métricas podem definir um alerta para uma condição de _resolvido_ quando a condição de erro não for mais atendida. O estado de alerta é definido pelo usuário e é independente da condição do monitor. Mesmo que o sistema possa definir a condição do monitor como resolvido, o estado de alerta não será alterado até que o usuário o altere.

#### <a name="changing-the-state-of-an-alert-or-smart-group"></a>Alterar o estado de um alerta ou grupo inteligente
Você pode alterar o estado de um alerta individual ou gerenciar vários alertas em conjunto definindo o estado de um grupo inteligente.

Altere o estado de um alerta clicando em **Alterar o estado de alerta** na exibição de detalhes do alerta ou altere o estado de um grupo inteligente clicando em **Alterar o estado do grupo inteligente** na exibição de detalhes. Você pode alterar o estado de vários itens ao mesmo tempo selecionando-os em uma exibição de lista e clicando em **Alterar Estado** na parte superior da página. Em ambos os casos, selecione um novo estado na lista suspensa e, opcionalmente, forneça um comentário. Se você está alterando um único item, também tem uma opção de aplicar as mesmas alterações a todos os alertas no grupo inteligente.

![Alterar estado](media/monitoring-overview-unified-alerts/change-tate.png)

### <a name="alerts-page"></a>Página de alertas
A página Alertas padrão fornece um resumo dos alertas que são criados dentro de uma janela de tempo específica. Ela exibe o total de alertas de cada gravidade com colunas que identificam o número total de alertas em cada estado para cada gravidade. Clique em qualquer severidade para abrir a página [Todos os Alertas](#all-alerts-page) filtrada por tal gravidade.

![Página de alertas](media/monitoring-overview-unified-alerts/alerts-page.png)

Você pode filtrar essa exibição selecionando valores nas listas suspensas na parte superior da página.

| Coluna | DESCRIÇÃO |
|:---|:---|
| Assinatura | Selecione até cinco assinaturas do Azure. Somente os alertas nas assinaturas selecionadas são incluídos na exibição. |
| Grupo de recursos | Selecione um único grupo de recursos. Somente alertas com destinos no grupo de recursos selecionado são incluídos na exibição. |
| Intervalo de tempo | Somente os alertas disparados dentro da janela de tempo selecionada serão incluídos na exibição. Os valores compatíveis são a última hora, as últimas 24 horas, os últimos 7 dias e os últimos 30 dias. |

Clique nos seguintes valores na parte superior da página Alertas para abrir outra página.

| Valor | DESCRIÇÃO |
|:---|:---|
| Total de Alertas | Número total de alertas que correspondem aos critérios selecionados. Clique nesse valor para abrir a exibição Todos os Alertas sem filtro. |
| Grupos Inteligentes | O número total de grupos inteligentes criados a partir de alertas que correspondem aos critérios selecionados. Clique nesse valor para abrir a lista Grupos Inteligentes na exibição Todos os Alertas.
| Total de Regras de Alerta | Número total de regras de alerta no grupo de recursos e na assinatura selecionados. Clique nesse valor para abrir a exibição Regras filtrada no grupo de recursos e assinaturas selecionados.


### <a name="all-alerts-page"></a>Página Todos os Alertas 
A página Todos os Alertas permite exibir uma lista de alertas que foram criados dentro da janela de tempo selecionada. Você pode exibir uma lista de alertas individuais ou uma lista dos grupos inteligentes contendo os alertas. Clique na faixa na parte superior da página para alternar entre as exibições.

![Página Todos os Alertas](media/monitoring-overview-unified-alerts/all-alerts-page.png)

Você pode filtrar a exibição selecionando os seguintes valores nas listas suspensas na parte superior da página.

| Coluna | DESCRIÇÃO |
|:---|:---|
| Assinatura | Selecione até cinco assinaturas do Azure. Somente os alertas nas assinaturas selecionadas são incluídos na exibição. |
| Grupo de recursos | Selecione um único grupo de recursos. Somente alertas com destinos no grupo de recursos selecionado são incluídos na exibição. |
| Tipo de recurso | Selecione um ou mais tipos de recurso. Somente alertas com destinos do tipo selecionado são incluídos na exibição. Essa coluna só estará disponível depois que um grupo de recursos for especificado. |
| Recurso | Selecione um recurso. Somente alertas com recursos como um destino são incluídos na exibição. Essa coluna só estará disponível depois que um tipo de recurso for especificado. |
| Severity | Selecione a gravidade do alerta ou selecione *Tudo* para incluir alertas de todas as gravidades. |
| Monitorar condição | Selecione uma condição do monitor ou *Tudo* para incluir alertas de condições. |
| Estado do Alerta | Selecione um estado de alerta ou selecione *Tudo* para incluir alertas de estados. |
| Monitorar Serviço | Selecione um serviço ou selecione *Tudo* para incluir todos os serviços. Somente os alertas criados por regras usando esse serviço como um destino são incluídos. |
| Intervalo de tempo | Somente os alertas disparados dentro da janela de tempo selecionada serão incluídos na exibição. Os valores compatíveis são a última hora, as últimas 24 horas, os últimos 7 dias e os últimos 30 dias. |

Clique em **Colunas** na parte superior da página para selecionar quais colunas exibir. Você pode remover qualquer coluna exceto 

### <a name="alert-detail-page"></a>Página Detalhes do alerta
A página Detalhes do Alerta é exibida quando você clica em um alerta. Ela fornece detalhes do alerta e permite que você altere seu estado.

![Detalhes do Alerta](media/monitoring-overview-unified-alerts/alert-detail.png)

A página Detalhes do Alerta inclui as seções a seguir.

| Seção | DESCRIÇÃO |
|:---|:---|
| Conceitos básicos | Exibe as propriedades e outras informações significativas sobre o alerta. |
| Histórico | Lista cada ação realizada pelo alerta e todas as alterações feitas no alerta. Isso é atualmente limitado a alterações de estado. |
| Grupo Inteligente | Informações sobre o grupo inteligente no qual o alerta está incluído. A **Contagem de Alertas** refere-se ao número de alertas incluídos no grupo inteligente. Isso inclui os outros alertas que estão incluídos no mesmo grupo mesmo inteligente e que foram criados nos últimos 30 dias.  Isso ocorre independentemente do filtro de tempo na página da lista de alertas. Clique em um alerta para exibir seus detalhes. |
| Mais detalhes | Exibe mais informações contextuais para o alerta que normalmente são específicas para o tipo de origem que criou o alerta. |


### <a name="smart-group-detail-page"></a>Página Detalhes do Grupo Inteligente
A página Detalhes do Grupo Inteligente é exibida quando você clica em um grupo inteligente. Ela fornece detalhes do grupo inteligente, incluindo o raciocínio usado para criar o grupo e permite que você altere seu estado.
 
![Detalhes do Grupo Inteligente](media/monitoring-overview-unified-alerts/smart-group-detail.png)


A página Detalhes do Grupo Inteligente inclui as seções a seguir.

| Seção | DESCRIÇÃO |
|:---|:---|
| Alertas | Lista os alertas individuais que estão incluídos no grupo inteligente. Clique em um alerta para abrir a página Detalhes do Alerta dele. |
| Histórico | Lista cada ação realizada pelo grupo inteligente e todas as alterações feitas nele. Atualmente, isso se limita a alterações de estado e alterações de associação do alerta. |

## <a name="next-steps"></a>Próximas etapas
- [Saiba como usar a nova experiência de Alertas para criar, exibir e gerenciar alertas](monitor-alerts-unified-usage.md)
- [Saiba mais sobre os alertas de log na experiência de Alertas](monitor-alerts-unified-log.md)
- [Saiba mais sobre os alertas de métrica na experiência de Alertas](monitoring-near-real-time-metric-alerts.md)
- [Saiba mais sobre os alertas de log de atividades na experiência de Alertas](monitoring-activity-log-alerts-new-experience.md)
