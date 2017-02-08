---
title: Fontes de dados no Log Analytics | Microsoft Docs
description: "As fontes de dados definem os dados que o Log Analytics coleta de agentes e outras fontes conectadas.  Este artigo descreve o conceito de como o Log Analytics usa fontes de dados, explica os detalhes de como configurá-las e fornece um resumo das diferentes fontes de dados disponíveis."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 57df4ab0b2a1df6631eb6e67a90f69cebb1dfe75
ms.openlocfilehash: ad9cc8765f1a8b83c9dbf5caca573811c6e7f10e


---
# <a name="data-sources-in-log-analytics"></a>Fontes de dados no Log Analytics
O Log Analytics coleta dados das fontes conectadas em seu espaço de trabalho do OMS e os armazena no repositório do OMS.  Os dados que são coletados de cada uma são definidos pelas fontes de dados que você configurar.  Os dados no repositório do OMS são armazenados como um conjunto de registros.  Cada fonte de dados cria registros de um determinado tipo com cada tipo de tendo seu próprio conjunto de propriedades.

![Coleta de dados do Log Analytics](./media/log-analytics-data-sources/overview.png)

As fontes de dados são diferentes de soluções do OMS, que também coletam dados das fontes conectadas e criam registros no repositório do OMS.  As soluções podem ser adicionadas ao espaço de trabalho da Galeria de Soluções e geralmente fornecerão ferramentas de análise adicionais no portal do OMS.  

## <a name="summary-of-data-sources"></a>Resumo das fontes de dados
As fontes de dados que estão disponíveis no momento no Log Analytics são listadas na tabela a seguir.  Cada uma tem um link para um artigo à parte que fornece detalhes para aquela fonte de dados.

| Fonte de dados | Tipo de evento | Descrição |
|:--- |:--- |:--- |
| [Logs personalizados](log-analytics-data-sources-custom-logs.md) |\<LogName\>_CL |Arquivos de texto em agentes do Windows ou Linux que contenham informações de log. |
| [Logs de eventos do Windows](log-analytics-data-sources-windows-events.md) |Evento |Os eventos coletados do log de eventos em computadores Windows. |
| [Contadores de desempenho do Windows](log-analytics-data-sources-performance-counters.md) |Perf |Contadores de desempenho coletados de computadores Windows. |
| [Contadores de desempenho do Linux](log-analytics-data-sources-performance-counters.md) |Perf |Contadores de desempenho coletados de computadores Linux. |
| [Logs do IIS](log-analytics-data-sources-iis-logs.md) |W3CIISLog |Serviços de Informações da Internet em formato W3C. |
| [Syslog](log-analytics-data-sources-syslog.md) |syslog |Eventos de syslog em computadores Windows ou Linux. |

## <a name="configuring-data-sources"></a>Configurando fontes de dados
As fontes de dados são configuradas do menu **Dados** nas **Configurações** do Log Analytics.  Toda configuração é enviada para todas as fontes conectadas em seu espaço de trabalho do OMS.  No momento, você não pode excluir nenhum agente dessa configuração.

![Configurar eventos do Windows](./media/log-analytics-data-sources/configure-events.png)

1. No console do OMS, selecione o bloco **Configurações** .
2. Selecione **Dados**.
3. Clique na fonte de dados para configurar.
4. Siga o link para a documentação para cada fonte de dados na tabela acima para obter detalhes sobre sua configuração.

## <a name="data-collection"></a>Coleta de dados
As configurações de fonte de dados são entregues aos agentes que estão diretamente conectados ao OMS dentro de alguns minutos.  Os dados especificados são coletados do agente e entregues diretamente para o Log Analytics em intervalos específicos para cada fonte de dados.  Consulte a documentação para cada fonte de dados para encontrar essas especificações.

Para agentes do SCOM (System Center Operations Manager) em um grupo de gerenciamento conectado, as configurações de fonte de dados são convertidas em pacotes de gerenciamento e entregues ao grupo de gerenciamento a cada cinco minutos por padrão.  O agente baixa o pacote de gerenciamento como qualquer outro e coleta os dados especificados. Dependendo da fonte de dados, os dados serão enviados para um servidor de gerenciamento que os encaminha para o Log Analytics ou o agente enviará os dados para o Log Analytics sem passar pelo servidor de gerenciamento. Consulte os [detalhes da coleta de dados para recursos e soluções do OMS](log-analytics-add-solutions.md#data-collection-details) para obter mais detalhes.  Você pode ler sobre os detalhes da conexão do SCOM e do OMS e a modificação da frequência com que a configuração é entregue em [Configure Integration with System Center Operations Manager](log-analytics-om-agents.md)(Configurar a integração com o System Center Operations Manager).

## <a name="log-analytics-records"></a>Registros do Log Analytics
Todos os dados coletados pelo Log Analytics são armazenados no repositório do OMS como registros.  Registros coletados por diferentes fontes de dados terão seu próprio conjunto de propriedades e serão identificados por sua propriedade **Type** .  Consulte a documentação para cada fonte de dados e a solução para obter detalhes sobre cada tipo de registro.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [soluções](log-analytics-add-solutions.md) que adicionam funcionalidades ao Log Analytics e também coletam dados para o repositório do OMS.
* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para analisar os dados coletados de fontes de dados e soluções.  
* Configure [alertas](log-analytics-alerts.md) para notificá-lo proativamente sobre dados críticos coletados de fontes de dados e soluções.



<!--HONumber=Dec16_HO1-->


