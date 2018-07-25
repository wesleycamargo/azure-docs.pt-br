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
ms.openlocfilehash: c4c8279a1d4638a1c5d889b53e2d9e89e458cc37
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39117166"
---
# <a name="unified-alerts-in-azure-monitor"></a>Alertas unificados no Azure Monitor

## <a name="overview"></a>Visão geral

> [!NOTE]
>  Uma nova experiência de alerta unificada que permite gerenciar alertas de várias assinaturas e apresenta estados de alerta e grupos inteligentes está disponível atualmente na visualização pública. Consulte a última seção deste artigo para obter uma descrição dessa experiência aprimorada e do processo para habilitá-la.


Este artigo descreve a experiência de alerta unificada no Azure Monitor. A [experiência de alerta anterior](monitoring-overview-alerts.md) está disponível na opção **Alertas (Clássico)** no menu do Azure Monitor. 

## <a name="features-of-the-unified-alert-experience"></a>Recursos da experiência de alerta unificada

A experiência unificada tem os seguintes benefícios em relação à experiência clássica:

-   **Melhor sistema de notificação**: [grupos de ações](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) são grupos nomeados de notificações e ações que podem ser reutilizados em vários alertas. 
- **Experiência de criação unificada**: alertas e regras de alerta para métricas, logs e logs de atividades no Azure Monitor, Log Analytics e Application Insights podem ser gerenciados em um único lugar. 
- **Exibir alertas acionados do Log Analytics no portal do Azure**: os alertas do Log Analytics agora podem ser exibidos com alertas de outras fontes no portal do Azure. Anteriormente, alertas de outras fontes estavam em um portal separado.
- **Separação de alertas acionados e regras de alerta**: as regras de alerta agora são diferenciadas dos alertas. Uma regra de alerta é a definição de uma condição que dispara um alerta. Um alerta é uma instância de um disparo de regra de alerta.
- **Melhor fluxo de trabalho**: a experiência de criação de alertas unificados orienta-o no processo de configuração de uma regra de alerta.
 
Os alertas de métrica têm os seguintes aprimoramentos em relação aos alertas de métrica clássicos:

-   **Latência melhorada**: os alertas métricos podem ser executados com a frequência de um a cada minuto. Os alertas métricos clássicos sempre são executados em uma frequência de uma vez a cada 5 minutos. Alertas do log ainda têm um atraso maior que um minuto devido ao tempo necessário para ingerir os logs. 
-   **Suporte para métricas multidimensionais**: você pode alertar sobre métricas dimensionais, o que significa que é possível monitorar uma instância específica da métrica.
-   **Mais controle sobre as condições de métrica**: você pode definir regras de alerta mais sofisticadas que dão suporte ao monitoramento dos valores máximo, mínimo, médio e total de métricas.
-   **Monitoramento combinado de várias métricas**: é possível monitorar até duas métricas com uma única regra. Um alerta é disparado quando ambas as métricas violam seus respectivos limites para o período especificado.
-   **Métricas dos logs** (visualização pública limitada): alguns dados de log que entram no Log Analytics agora podem ser extraídos e convertidos em métricas do Azure Monitor e, em seguida, são alertados da mesma forma que outras métricas. 


## <a name="alert-rules"></a>Regras de alerta
A experiência de alertas unificada usa os seguintes conceitos para separar as regras de alerta dos alertas ao unificar a experiência de criação entre diferentes tipos de alerta.

| item | Definição |
|:---|:---|
| Regra de alerta | Definição da condição para criar um alerta. Uma regra de alerta é composta de um _recurso de destino_, _sinal_, _critérios_ e _lógica_. Uma regra de alerta estará ativa apenas se estiver com o estado _habilitado_.
| Recurso de destino | Define os recursos e sinais específicos que estão disponíveis para alerta. Um destino pode ser um recurso do Azure.<br><br>Exemplos: máquina virtual, conta de armazenamento, conjunto de dimensionamento de máquinas virtuais, espaço de trabalho do Log Analytics ou recurso do Application Insights |
| Sinal | Fonte de dados emitidos pelo recurso de destino. Os tipos de sinal compatíveis são *Métrica*, *Log de atividades*, *Application Insights* e *Log*. |
| Critérios | A combinação de _sinal_ e _lógica_ aplicada a um recurso de destino.<br><br>Exemplos: Porcentagem de CPU > 70%, Tempo de Resposta do Servidor > 4 ms, Contagem de resultados de uma consulta de log > 100, e assim por diante |
| Lógica | Lógica definida pelo usuário para verificar se o sinal está dentro do intervalo/valores esperados. |
| Ação | Ação a ser executada quando o alerta é disparado. Várias ações podem ocorrer quando um alerta é acionado. Esses alertas dão suporte a grupos de ação.<br><br>Exemplos: envio por email para o endereço de email, chamada de uma URL do webhook |
| Monitorar condição | Indica se a condição que criou um alerta de métrica foi resolvida. As regras de alerta de métrica criam uma amostra de uma métrica específica em intervalos regulares. Se os critérios na regra de alerta forem atendidos, um novo alerta será criado com uma condição de "acionado."  Quando a métrica é amostrada novamente, se os critérios ainda forem atendidos, nada acontecerá.  Se os critérios não forem atendidos, a condição do alerta será alterada para "resolvida." Na próxima vez que os critérios forem atendidos, outro alerta será criado com uma condição de "acionado." |


## <a name="alert-pages"></a>Páginas de alertas
Os alertas unificados fornecem um único local para exibir e gerenciar todos os alertas do Azure. As seções a seguir descrevem as funções de cada página individual da experiência unificada.

### <a name="alerts-overview-page"></a>Página de visão geral de alertas
Os **Alertas** mostram um resumo agregado de todos os alertas acionados e o número total de regras de alerta habilitadas. A mudança dos parâmetros de filtro ou assinaturas atualiza as agregações e as listas de alertas disparados.

 ![alerts-overview](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>Gerenciamento de regras de alerta
**Regras** é uma única página para gerenciar todas as regras de alerta nas assinaturas do Azure. Ela lista todas as regras de alerta e estas podem ser classificadas com base em grupos de recursos, recursos de destino, nome da regra ou status. As regras de alerta também podem ser editadas, habilitadas ou desabilitadas nessa página.

 ![alerts-rules](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Criar uma regra de alerta
Os alertas podem ser criados de forma consistente independentemente do serviço de monitoramento ou do tipo de sinal. Todos os alertas disparados e os detalhes relacionados ficam disponíveis em uma mesma página.
 
Você pode criar uma nova regra de alerta com estas três etapas:
1. Escolha o _destino_ para o alerta.
1. Selecione o _sinal_ entre os sinais disponíveis para o destino.
1. Especifique a _lógica_ a ser aplicada aos dados do sinal.
 
Esse processo de criação simplificado não exige mais que você conheça a origem de monitoramento ou sinais que tenham suporte antes de selecionar um recurso do Azure. A lista de sinais disponíveis é filtrada automaticamente com base no recurso de destino selecionado e orienta-o na definição da lógica da regra de alerta.

Saiba mais sobre como criar regras de alerta em [Criar, exibir e gerenciar alertas usando o Azure Monitor](monitor-alerts-unified-usage.md).

Os alertas estão disponíveis em vários serviços de monitoramento do Azure. Para obter informações sobre como e quando usar cada um desses serviços, consulte [Monitorar aplicativos e recursos do Azure](./monitoring-overview.md). A tabela a seguir fornece uma listagem dos tipos de regras de alerta disponíveis no Azure. Ele também lista o que atualmente tem suporte pela experiência de alerta unificada.

| **Monitor de origem** | **Tipo de sinal**  | **Descrição** | 
|-------------|----------------|-------------|
| Azure Monitor | Métrica  | Também chamados de [alertas de métrica quase em tempo real](monitoring-near-real-time-metric-alerts.md), eles dão suporte à avaliação de condições de métrica com a frequência de um minuto e permitem regras de métrica multidimensionais e multimétricas. Uma lista de tipos de recursos compatíveis está disponível em [Alertas de métrica mais recentes para os serviços do Azure no portal do Azure](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).<br>[Alertas de métrica clássicos](monitoring-overview-alerts.md) não têm suporte na nova experiência de alertas. É possível localizá-los em Alertas (Clássico) no Portal do Azure. Os alertas clássicos dão suporte a alguns tipos de métrica que ainda não foram movidos para os alertas mais recentes. Para uma lista completa, consulte [métrica compatíveis](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics). |
| Log Analytics | Logs  | Receber notificações ou executar ações automatizadas quando uma consulta de pesquisa de logs atender a determinados critérios. Os alertas no Log Analytics estão [sendo copiados para a nova experiência](monitoring-alerts-extend.md). Uma [versão prévia dos *Logs do Log Analytics como métrica*](monitoring-alerts-extend-tool.md) está disponível. A versão prévia permite a você pegar alguns tipos de logs e convertê-los em métricas, onde será possível alertá-los usando a nova experiência de alerta. A versão prévia é útil se você tiver logs que não sejam do Azure e quer obter junto com as métricas nativas do Azure Monitor. |
| Logs de atividade | Log de atividades | Contém os registros de todas as ações de criação, atualização e exclusão criadas pelo destino selecionado. |
| Integridade do serviço | Log de atividades  | Não é compatível em alertas unificados. Consulte [Criar alertas do log de atividades em notificações de serviço](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Application Insights | Logs  | Contém logs com os detalhes de desempenho do aplicativo. Usando a consulta analítica, é possível definir as condições para as ações a serem tomadas com base nos dados do aplicativo. |
| Application Insights | Métrica | Não é compatível em alertas unificados. Consulte [Alertas de métrica](../application-insights/app-insights-alerts.md). |
| Application Insights | Testes de disponibilidade na Web | Não é compatível em alertas unificados.  Consulte [Alertas de teste da Web](../application-insights/app-insights-monitor-web-app-availability.md). Disponível para qualquer site que seja instrumentado para enviar dados ao Application Insights. Receba uma notificação quando a disponibilidade ou capacidade de resposta de um site estiver abaixo das expectativas. |

## <a name="enhanced-unified-alerts-public-preview"></a>Alertas unificados aprimorados (visualização pública)

Uma experiência de alertas unificada aprimorada foi lançada na versão prévia pública para o Azure Monitor em 1º de junho de 2018. Essa experiência baseia-se nos benefícios de [alertas unificados](#overview), lançados em março de 2018 e que fornecem a capacidade de gerenciar e agregar alertas individuais e modificar o estado de alerta. Esta seção descreve os novos recursos e como navegar nas novas páginas de alertas no portal do Azure.

### <a name="enhanced-unified-alerts"></a>Alertas unificados aprimorados

A nova experiência fornece os seguintes recursos que não estão disponíveis na experiência unificada clássica:

- **Exibir alertas entre assinaturas**: agora é possível exibir e gerenciar instâncias individuais de alertas entre várias assinaturas em uma única exibição.
- **Gerenciar o estado dos alertas**: os alertas agora têm um estado que indica se foram confirmados como fechados.
- **Organize alertas com grupos inteligentes**: os grupos inteligentes agrupam automaticamente os alertas relacionados para que seja possível gerenciá-los como um conjunto, em vez de individualmente.

### <a name="enable-enhanced-unified-alerts"></a>Habilitar alertas unificados aprimorados
Habilite a nova experiência de alertas unificados, selecionando a faixa na parte superior da página Alertas. Esse processo cria um repositório de alertas que inclui os últimos 30 dias de alertas acionados em serviços compatíveis. Depois que a nova experiência for habilitada, será possível alternar entre a nova e a antiga experiência, selecionando essa faixa.

> [!NOTE]
>  Pode demorar alguns minutos para que a nova experiência seja habilitada inicialmente.

![Faixa](media/monitoring-overview-unified-alerts/opt-in-banner.png)

Todas as assinaturas que você tiver acesso serão registradas ao habilitar a nova experiência. Embora a assinatura inteira esteja habilitada, somente usuários que selecionam a nova experiência poderão exibi-la. Outros usuários com acesso à assinatura devem habilitar a experiência separadamente.

Habilitar a nova experiência de alerta não afeta a configuração de grupos de ação ou notificações em suas regras de alerta. Isso apenas altera a maneira que você exibe e gerencia as instâncias acionadas dos alertas no portal do Azure.

### <a name="smart-groups"></a>Grupos inteligentes
Os grupos inteligentes reduzem o ruído, permitindo que você gerencie alertas relacionados como uma única unidade, e não como alertas individuais. Você pode exibir os detalhes dos grupos inteligentes e definir o estado, de forma semelhante como é possível com alertas. Cada alerta é um membro de apenas um grupo inteligente.

Grupos inteligentes são criados automaticamente usando aprendizado de máquina para combinar alertas relacionados que representam um único problema. Quando um alerta é criado, o algoritmo o adiciona a um novo grupo inteligente ou a um grupo inteligente existente com base em informações, como padrões históricos, propriedades semelhantes e estrutura semelhante. 

Atualmente, o algoritmo considera apenas alertas do mesmo serviço de monitor em uma assinatura. Os grupos inteligentes podem reduzir até 99% do ruído de alerta por meio dessa consolidação. É possível ver o motivo pelo qual os alertas foram incluídos em um grupo na página de detalhes do grupo inteligente.

O nome de um grupo inteligente é o nome do seu primeiro alerta. Você não pode criar ou renomear um grupo inteligente.


### <a name="alert-states"></a>Estados de alerta
Os alertas unificados avançados introduzem o conceito de estado de alerta. Você pode definir o estado de um alerta para especificar onde ele está no processo de resolução. Quando um alerta é criado, ele tem um status de *Novo*. É possível alterar o status ao reconhecer um alerta e ao fechá-lo. Todas as alterações de estado são armazenadas no histórico do alerta.

Os seguintes estados de alerta são compatíveis.

| Estado | DESCRIÇÃO |
|:---|:---|
| Novo | O problema acaba de ser detectado e ainda não foi analisado. |
| Confirmado | Um administrador examinou o alerta e começou a trabalhar nele. |
| Fechado | O problema foi resolvido. Depois que um alerta for fechado, será possível reabri-lo, alterando-o para outro estado. |

O estado de um alerta é diferente da condição do monitor. As regras de alerta de métricas podem definir um alerta para uma condição de _resolvido_ quando a condição de erro não for mais atendida. O estado de alerta é definido pelo usuário e é independente da condição do monitor. Embora o sistema possa definir a condição do monitor como "resolvida", o estado de alerta não é alterado até que o usuário o altere.

#### <a name="change-the-state-of-an-alert-or-smart-group"></a>Alterar o estado de um alerta ou grupo inteligente
Você pode alterar o estado de um alerta individual ou gerenciar vários alertas em conjunto definindo o estado de um grupo inteligente.

Altere o estado de um alerta, selecionando **Alterar estado de alerta** na exibição detalhada do alerta. Ou altere o estado de um grupo inteligente, selecionando **Alterar o estado do grupo inteligente** na exibição detalhada. Altere o estado de vários itens de uma só vez, selecionando-os primeiro em uma exibição de lista e selecionando **Alterar estado** na parte superior da página. 

Nos dois casos, selecione um novo estado no menu suspenso. Em seguida, forneça um comentário opcional. Se você estiver alterando um único item, também terá a opção de aplicar as mesmas alterações a todos os alertas no grupo inteligente.

![Alterar estado](media/monitoring-overview-unified-alerts/change-tate.png)

### <a name="alerts-page"></a>Página de alertas
A página Alertas padrão fornece um resumo dos alertas que são criados dentro de uma janela de tempo específica. Ela exibe o total de alertas para cada gravidade com colunas que identificam o número total de alertas em cada estado para cada gravidade. Selecione qualquer uma das gravidades para abrir a página [Todos os Alertas](#all-alerts-page) filtrada por tal gravidade.

![Página de alertas](media/monitoring-overview-unified-alerts/alerts-page.png)

É possível filtrar essa exibição, selecionando valores nos menus suspensos na parte superior da página.

| Coluna | DESCRIÇÃO |
|:---|:---|
| Assinatura | Selecione até cinco assinaturas do Azure. Somente os alertas nas assinaturas selecionadas são incluídos na exibição. |
| Grupo de recursos | Selecione um único grupo de recursos. Somente alertas com destinos no grupo de recursos selecionado são incluídos na exibição. |
| Intervalo de tempo | Apenas alertas acionados dentro da janela de tempo selecionada são incluídos na exibição. Os valores com suporte são a última hora, as últimas 24 horas, os últimos 7 dias e os últimos 30 dias. |

Selecione os valores a seguir na parte superior da página Alertas para abrir outra página.

| Valor | DESCRIÇÃO |
|:---|:---|
| Total de alertas | O número total de alertas que correspondem aos critérios selecionados. Selecione esse valor para abrir a exibição Todos os Alertas sem filtro. |
| Grupos inteligentes | O número total de grupos inteligentes que foram criados a partir dos alertas que correspondem aos critérios selecionados. Selecione esse valor para abrir a lista de grupos inteligentes na exibição Todos os Alertas.
| Total de regras de alerta | O número total de regras de alerta na assinatura e no grupo de recursos selecionados. Selecione esse valor para abrir a exibição Regras filtrada na assinatura e no grupo de recursos selecionados.


### <a name="all-alerts-page"></a>Página Todos os Alertas 
Ao usar a página Todos os Alertas, é possível exibir uma lista de alertas que foram criados dentro da janela de tempo selecionada. É possível exibir uma lista dos alertas individuais ou uma lista dos grupos inteligentes que contêm os alertas. Selecione a faixa na parte superior da página para alternar entre as exibições.

![Página Todos os Alertas](media/monitoring-overview-unified-alerts/all-alerts-page.png)

É possível filtrar a exibição, selecionando os valores a seguir nos menus suspensos na parte superior da página.

| Coluna | DESCRIÇÃO |
|:---|:---|
| Assinatura | Selecione até cinco assinaturas do Azure. Somente os alertas nas assinaturas selecionadas são incluídos na exibição. |
| Grupo de recursos | Selecione um único grupo de recursos. Somente alertas com destinos no grupo de recursos selecionado são incluídos na exibição. |
| Tipo de recurso | Selecione um ou mais tipos de recurso. Somente alertas com destinos do tipo selecionado são incluídos na exibição. Essa coluna somente estará disponível depois que um grupo de recursos for especificado. |
| Recurso | Selecione um recurso. Apenas alertas com esse recurso como um destino são incluídos na exibição. Essa coluna somente estará disponível depois que um tipo de recurso for especificado. |
| Severity | Selecione uma gravidade de alerta ou selecione *Todos* para incluir alertas de todas as gravidades. |
| Monitorar condição | Selecione uma condição de monitor ou selecione *Todos* incluir alertas de condições. |
| Estado de alerta | Selecione um estado de alerta ou selecione *Todos* para incluir alertas de estados. |
| Monitorar serviço | Selecione um serviço ou selecione *Todos* para incluir todos os serviços. Apenas alertas criados por regras que usam o serviço como um destino são incluídos. |
| Intervalo de tempo | Apenas alertas acionados dentro da janela de tempo selecionada são incluídos na exibição. Os valores com suporte são a última hora, as últimas 24 horas, os últimos 7 dias e os últimos 30 dias. |

Selecione **Colunas** na parte superior da página para selecionar quais colunas exibir. 

### <a name="alert-detail-page"></a>Página Detalhes do alerta
A página de detalhes do Alerta é exibida ao selecionar um alerta. Ela fornece detalhes do alerta e permite alterar o estado.

![Detalhes do alerta](media/monitoring-overview-unified-alerts/alert-detail.png)

A página de detalhes do alerta inclui as seções a seguir.

| Seção | DESCRIÇÃO |
|:---|:---|
| Conceitos básicos | Exibe as propriedades e outras informações significativas sobre o alerta. |
| Histórico | Lista cada ação realizada pelo alerta e todas as alterações feitas no alerta. Isso é atualmente limitado a alterações de estado. |
| Grupo inteligente | Informações sobre o grupo inteligente no qual o alerta está incluído. A *contagem de alerta* refere-se ao número de alertas incluídos no grupo inteligente. Isso inclui os outros alertas no mesmo grupo inteligente que foram criados nos últimos 30 dias.  Isso ocorre independentemente do filtro de tempo na página da lista de alertas. Selecione um alerta para exibir os detalhes. |
| Mais detalhes | Exibe mais informações contextuais para o alerta que normalmente são específicas para o tipo de origem que criou o alerta. |


### <a name="smart-group-detail-page"></a>Página de detalhes do grupo inteligente
A página de detalhes do grupo inteligente é exibida aos selecionar um grupo inteligente. Ela fornece detalhes sobre o grupo inteligente, incluindo o motivo usado para criar o grupo e permite alterar o estado.
 
![Detalhe do grupo inteligente](media/monitoring-overview-unified-alerts/smart-group-detail.png)


A página de detalhes do grupo inteligente inclui as seções a seguir.

| Seção | DESCRIÇÃO |
|:---|:---|
| Alertas | Lista os alertas individuais que estão incluídos no grupo inteligente. Selecione um alerta para abrir a página de detalhes do alerta. |
| Histórico | Lista cada ação tomada pelo grupo inteligente e quaisquer alterações feitas no grupo. Atualmente, isso se limita a alterações de estado e alterações de associação do alerta. |

## <a name="next-steps"></a>Próximas etapas
- [Saiba como usar a nova experiência de Alertas para criar, exibir e gerenciar alertas](monitor-alerts-unified-usage.md)
- [Saiba mais sobre alertas de log na experiência de Alertas](monitor-alerts-unified-log.md)
- [Saiba mais sobre os alertas de métrica na experiência de Alertas](monitoring-near-real-time-metric-alerts.md)
- [Saiba mais sobre alertas de Log de atividades na experiência de Alertas](monitoring-activity-log-alerts-new-experience.md)
