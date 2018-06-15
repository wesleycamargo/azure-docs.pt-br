---
title: Configurar fontes de dados no Azure Log Analytics | Microsoft Docs
description: As fontes de dados definem os dados que o Log Analytics coleta de agentes e outras fontes conectadas.  Este artigo descreve o conceito de como o Log Analytics usa fontes de dados, explica os detalhes de como configurá-las e fornece um resumo das diferentes fontes de dados disponíveis.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2018
ms.author: bwren
ms.openlocfilehash: 5201d02b4f70f964f39b4fe135e4715732b9741a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792541"
---
# <a name="data-sources-in-log-analytics"></a>Fontes de dados no Log Analytics
O Log Analytics coleta dados de suas fontes conectadas e armazena-os no seu espaço de trabalho do Log Analytics.  Os dados que são coletados de cada uma são definidos pelas fontes de dados que você configurar.  Os dados no Log Analytics são armazenados como um conjunto de registros.  Cada fonte de dados cria registros de um determinado tipo com cada tipo de tendo seu próprio conjunto de propriedades.

![Coleta de dados do Log Analytics](./media/log-analytics-data-sources/overview.png)

As fontes de dados são diferentes das [soluções de gerenciamento](log-analytics-add-solutions.md), que também coletam dados das fontes conectadas e criam registros no Log Analytics.  Além de coletar dados, as soluções geralmente incluem pesquisas de log e exibições para ajudá-lo a analisar a operação de um determinado aplicativo ou serviço.


## <a name="summary-of-data-sources"></a>Resumo das fontes de dados
A tabela a seguir lista as fontes de dados atualmente disponíveis no Log Analytics.  Cada uma tem um link para um artigo à parte que fornece detalhes para aquela fonte de dados.   Fornece também informações sobre o método e a frequência de coleta de dados no Log Analytics.  É possível usar as informações deste artigo para identificar as diferentes soluções disponíveis e compreender o fluxo de dados e os requisitos de conexão para diferentes soluções de gerenciamento. Para obter explicações sobre as colunas, consulte [Detalhes da coleta de dados para soluções de gerenciamento no Azure](../monitoring/monitoring-solutions-inventory.md).


| Fonte de dados | Plataforma | Agente de monitoramento da Microsoft | Agente do Operations Manager | Armazenamento do Azure | Operations Manager necessário? | Dados de agente do Operations Manager enviados por meio do grupo de gerenciamento | Frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [Logs personalizados](log-analytics-data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | na chegada |
| [Logs personalizados](log-analytics-data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | na chegada |
| [Logs do IIS](log-analytics-data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |5 minutos |
| [Contadores de desempenho](log-analytics-data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |conforme agendado, mínimo de 10 segundos |
| [Contadores de desempenho](log-analytics-data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |conforme agendado, mínimo de 10 segundos |
| [Syslog](log-analytics-data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |do armazenamento do Azure: 10 minutos; do agente: na chegada |
| [Logs de eventos do Windows](log-analytics-data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | na chegada |


## <a name="configuring-data-sources"></a>Configurando fontes de dados
Configure fontes de dados no menu **Dados** nas **Configurações Avançadas** do Log Analytics.  Todas as configurações são entregues a todas as fontes conectadas em seu espaço de trabalho.  No momento, você não pode excluir nenhum agente dessa configuração.

![Configurar eventos do Windows](./media/log-analytics-data-sources/configure-events.png)

1. No portal do Azure, selecione **Log Analytics** > seu espaço de trabalho > **Configurações Avançadas**.
2. Selecione **Dados**.
3. Clique na fonte de dados que você deseja configurar.
4. Siga o link para a documentação para cada fonte de dados na tabela acima para obter detalhes sobre sua configuração.


## <a name="data-collection"></a>Coleta de dados
As configurações de fonte de dados são entregues aos agentes que estão diretamente conectados ao Log Analytics dentro de alguns minutos.  Os dados especificados são coletados do agente e entregues diretamente para o Log Analytics em intervalos específicos para cada fonte de dados.  Consulte a documentação para cada fonte de dados para encontrar essas especificações.

Para agentes do System Center Operations Manager em um grupo de gerenciamento conectado, as configurações de fonte de dados são convertidas em pacotes de gerenciamento e entregues ao grupo de gerenciamento a cada cinco minutos por padrão.  O agente baixa o pacote de gerenciamento como qualquer outro e coleta os dados especificados. Dependendo da fonte de dados, os dados serão enviados para um servidor de gerenciamento que os encaminhará para o Log Analytics ou o agente enviará os dados para o Log Analytics sem passar pelo servidor de gerenciamento. Consulte [detalhes da coleta de dados](log-analytics-add-solutions.md#data-collection-details) para obter detalhes.  Você pode ler sobre os detalhes da conexão do Operations Manager e do Log Analytics e a modificação da frequência em que a configuração é entregue em [Configure Integration with System Center Operations Manager](log-analytics-om-agents.md)(Configurar a integração com o System Center Operations Manager).

Se o agente não pode se conectar ao Log Analytics ou Operations Manager, ele continuará a coletar dados que fornecerá ao estabelecer uma conexão.  Dados podem ser perdidos se a quantidade de dados atingir o tamanho máximo do cache do cliente, ou se o agente não for capaz de estabelecer uma conexão dentro de 24 horas.

## <a name="log-analytics-records"></a>Registros do Log Analytics
Todos os dados coletados pelo Log Analytics são armazenados no espaço de trabalho como registros.  Registros coletados por diferentes fontes de dados terão seu próprio conjunto de propriedades e serão identificados por sua propriedade **Type** .  Consulte a documentação para cada fonte de dados e a solução para obter detalhes sobre cada tipo de registro.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [soluções](log-analytics-add-solutions.md) que adicionam funcionalidades ao Log Analytics e também coletam dados no espaço de trabalho.
* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para analisar os dados coletados de fontes de dados e soluções.  
* Configure [alertas](log-analytics-alerts.md) para notificá-lo proativamente sobre dados críticos coletados de fontes de dados e soluções.
