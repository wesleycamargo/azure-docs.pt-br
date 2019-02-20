---
title: Visão geral de alertas e monitoramento de notificações do Azure
description: Visão geral de alertas no Azure. Alertas, alertas clássicos, a interface de alertas.
author: rboucher
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/28/2018
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: 6fb49baf8ab58ae6cfe7639cedcc4466810c8b96
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003305"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Visão geral dos alertas no Microsoft Azure 

Este artigo descreve o que são alertas, seus benefícios e como começar a usá-los.  




## <a name="what-are-alerts-in-microsoft-azure"></a>O que são os alertas no Microsoft Azure?
Os alertas trabalham de forma proativa, mandando notificações quando encontram condições importante em seus dados de monitoramento. Eles permitem que você identifique e resolva problemas antes que os usuários do seu sistema os percebam. 

Este artigo discute a experiência unificada de alerta no Azure Monitor, que agora inclui alertas que eram gerenciados pelo Log Analytics e pelo Application Insights. A [experiência anterior de alerta](alerts-classic.overview.md) e os tipos de alerta são chamados de **alertas clássicos**. Você pode ver a experiência mais antiga e o tipo mais antigo de alerta clicando em **Exibir alertas clássicos** na parte superior da página de alerta. 

## <a name="overview"></a>Visão geral

O diagrama a seguir representa o fluxo de alertas. 

![Fluxo de alertas](media/alerts-overview/Azure-Monitor-Alerts.svg)

As regras de alerta são separadas dos alertas e das ações executadas quando um alerta é disparado. 

**Regra de alerta**: a regra de alerta captura o destino e os critérios para o alerta. A regra de alerta pode estar em estado habilitado ou desabilitado. Os alertas disparam apenas quando estão habilitados. 

Os principais atributos de uma regra de alerta são:

**Recurso de Destino** - Define o escopo e os sinais disponíveis para o alerta. Um destino pode ser um recurso do Azure. Destinos de exemplo: uma máquina virtual, uma conta de armazenamento, um conjunto de dimensionamento de máquinas virtuais, um workspace do Log Analytics ou um recurso do Application Insights. Para determinados recursos (por exemplo, Máquinas Virtuais), é possível especificar vários recursos como o destino da regra de alerta.

**Sinal**: os sinais são emitidos pelo recurso de destino e podem ser de vários tipos. Métrica, Log de atividades, Application Insights e Log.

**Critérios** – critérios é a combinação de sinal e lógica aplicada a um recurso de destino. Exemplos: 
   - Porcentagem de CPU > 70%
   - Tempo de resposta do servidor > 4 ms 
   - Contagem de resultados de uma consulta de log > 100

**Nome do Alerta**: um nome específico para a regra de alerta configurado pelo usuário

**Descrição do Alerta**: uma descrição para a regra de alerta configurada pelo usuário

**Gravidade**: a gravidade do alerta quando os critérios especificados na regra de alerta são atendidos. A gravidade pode variar de 0 a 4.

**Ação** - Uma ação específica executada quando o alerta é disparado. Para obter mais informações, consulte [Grupos de Ações](../../azure-monitor/platform/action-groups.md).

## <a name="what-you-can-alert-on"></a>Sobre o que você pode alertar

Você pode alertar sobre métricas e logs, conforme descrito em [fontes de dados de monitoramento](../../azure-monitor/platform/data-sources-reference.md). Elas incluem, mas sem limitação:
- Valores métricos
- Consultas da pesquisa de logs
- Eventos do Log de Atividades
- Integridade da plataforma subjacente do Azure
- Testes de disponibilidade do site

Antes, as métricas do Azure Monitor, o Application Insights, o Log Analytics e a Integridade do Serviço tinham recursos de alerta separados. Com o tempo, o Azure aprimorou e combinou a interface de usuário e os diferentes métodos de alerta. Essa consolidação ainda está em processo. Como resultado, ainda há alguns recursos de alerta que não estão no novo sistema de alertas.  

| **Monitor de origem** | **Tipo de sinal**  | **Descrição** | 
|-------------|----------------|-------------|
| Integridade do serviço | Log de atividades  | Sem suporte. Consulte [Criar alertas do log de atividades em notificações de serviço](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Testes de disponibilidade na Web | Sem suporte. Consulte [Alertas de teste da Web](../../azure-monitor/app/monitor-web-app-availability.md). Disponível para qualquer site que seja instrumentado para enviar dados ao Application Insights. Receba uma notificação quando a disponibilidade ou capacidade de resposta de um site estiver abaixo das expectativas. |

## <a name="manage-alerts"></a>Gerenciar alertas
Você pode definir o estado de um alerta para especificar onde ele está no processo de resolução. Quando os critérios especificados na regra de alerta são atendidos, um alerta é criado ou disparado com um status de *Novo*. É possível alterar o status ao reconhecer um alerta e ao fechá-lo. Todas as alterações de estado são armazenadas no histórico do alerta.

Os seguintes estados de alerta são compatíveis.

| Estado | DESCRIÇÃO |
|:---|:---|
| Novo | O problema acaba de ser detectado e ainda não foi analisado. |
| Confirmado | Um administrador examinou o alerta e começou a trabalhar nele. |
| Fechado | O problema foi resolvido. Depois que um alerta for fechado, será possível reabri-lo, alterando-o para outro estado. |

O **estado de alerta** é diferente e independente da **condição do monitor**. O estado de alerta é definido pelo usuário. A condição do monitor é definida pelo sistema. Quando um alerta é acionado, a condição do monitor do alerta é definida como *acionado*. Quando a condição subjacente que causou o acionamento do alerta desaparece, a condição do monitor é definida como *resolvida*. O estado de alerta não é alterado até que o usuário o altere. Saiba [como alterar o estado dos seus alertas e grupos inteligentes](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Grupos inteligentes 
Os grupos inteligentes estão em versão prévia. 

Grupos inteligentes são agregações de alertas com base em algoritmos de aprendizado de máquina, que podem ajudar a reduzir o ruído de alerta e auxiliam na solução de problemas. [Saiba mais sobre Grupos inteligentes](https://aka.ms/smart-groups) e [como gerenciar seus grupos inteligentes](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Experiência de alertas 
A página Alertas padrão fornece um resumo dos alertas que são criados dentro de uma janela de tempo específica. Ela exibe o total de alertas para cada gravidade com colunas que identificam o número total de alertas em cada estado para cada gravidade. Selecione qualquer uma das gravidades para abrir a página [Todos os Alertas](#all-alerts-page) filtrada por tal gravidade.

Isso não mostra ou controla [alertas clássicos](#classic-alerts) mais antigos. Você pode alterar as assinaturas ou parâmetros para atualizar a página. 

![Página de alertas](media/alerts-overview/alerts-page.png)

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


## <a name="manage-alert-rules"></a>Gerenciar regras de alerta
Clique em **Gerenciar regras de alerta** para mostrar a página **Regras**. **Regras** é um local único para gerenciar todas as regras de alerta nas assinaturas do Azure. Ela lista todas as regras de alerta e estas podem ser classificadas com base em grupos de recursos, recursos de destino, nome da regra ou status. As regras de alerta também podem ser editadas, habilitadas ou desabilitadas nessa página.  

 ![alerts-rules](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Criar uma regra de alerta
Os alertas podem ser criados de forma consistente independentemente do serviço de monitoramento ou do tipo de sinal. Todos os alertas disparados e os detalhes relacionados ficam disponíveis em uma mesma página.
 
Você pode criar uma nova regra de alerta com estas três etapas:
1. Escolha o _destino_ para o alerta.
1. Selecione o _sinal_ entre os sinais disponíveis para o destino.
1. Especifique a _lógica_ a ser aplicada aos dados do sinal.
 
Esse processo de criação simplificado não exige mais que você conheça a origem de monitoramento ou sinais que tenham suporte antes de selecionar um recurso do Azure. A lista de sinais disponíveis é filtrada automaticamente com base no recurso de destino selecionado. Também com base nesse destino, você será guiado pela definição da lógica da regra de alerta automaticamente.  

Saiba mais sobre como criar regras de alerta em [Criar, exibir e gerenciar alertas usando o Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

Os alertas estão disponíveis em vários serviços de monitoramento do Azure. Para obter informações sobre como e quando usar cada um desses serviços, consulte [Monitorar aplicativos e recursos do Azure](../../azure-monitor/overview.md). 


## <a name="all-alerts-page"></a>Página Todos os alertas 
Clique em Total de Alertas para ver a página com todos os alertas. Nela, é possível exibir uma lista de alertas que foram criados dentro da janela de tempo selecionada. É possível exibir uma lista dos alertas individuais ou uma lista dos grupos inteligentes que contêm os alertas. Selecione a faixa na parte superior da página para alternar entre as exibições.

![Página Todos os Alertas](media/alerts-overview/all-alerts-page.png)

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

## <a name="alert-detail-page"></a>Página Detalhes do alerta
A página de detalhes do Alerta é exibida ao selecionar um alerta. Ela fornece detalhes do alerta e permite alterar o estado.

![Detalhes do alerta](media/alerts-overview/alert-detail2.png)

A página de detalhes do alerta inclui as seções a seguir.

| Seção | DESCRIÇÃO |
|:---|:---|
| Conceitos básicos | Exibe as propriedades e outras informações significativas sobre o alerta. |
| Histórico | Lista cada ação realizada pelo alerta e todas as alterações feitas no alerta. Atualmente limitado a alterações de estado. |
| Grupo inteligente | Informações sobre o grupo inteligente no qual o alerta está incluído. A *contagem de alerta* refere-se ao número de alertas incluídos no grupo inteligente. Inclui outros alertas no mesmo grupo inteligente que foram criados nos últimos 30 dias, independentemente do filtro de tempo na página da lista de alertas. Selecione um alerta para exibir os detalhes. |
| Mais detalhes | Exibe mais informações contextuais para o alerta que normalmente são específicas para o tipo de origem que criou o alerta. |


## <a name="classic-alerts"></a>Alertas clássicos 

O recurso de alerta do log de atividades e de métricas do Azure Monitor anterior a junho de 2018 é chamado de "Alertas (clássico)". 

Para saber mais, confira [Alertas clássicos](./../../azure-monitor/platform/alerts-classic.overview.md)


## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre os grupos inteligentes](https://aka.ms/smart-groups)
- [Saiba mais sobre grupos de ação](../../azure-monitor/platform/action-groups.md)
- [Gerenciar suas instâncias de alertas no Azure](https://aka.ms/managing-alert-instances)
- [Gerenciar grupos inteligentes](https://aka.ms/managing-smart-groups)






