---
title: Logs de eventos do Windows no Log Analytics | Microsoft Docs
description: Os logs de eventos do Windows são uma das fontes de dados mais comuns usadas pelo Log Analytics.  Este artigo descreve como configurar a coleta de logs de eventos do Windows e os detalhes dos registros que eles criam no repositório do OMS.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn

ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: bwren

---
# <a name="windows-event-log-data-sources-in-log-analytics"></a>Fontes de dados de log de eventos do Windows no Log Analytics
Os logs de eventos do Windows são uma das [fontes de dados](log-analytics-data-sources.md) mais comuns usadas para agentes do Windows, pois esse é o método usado pela maioria dos aplicativos para registrar informações e erros.  Você pode coletar eventos de logs padrão como do sistema e aplicativo além de especificar todos os logs personalizados criados por aplicativos que você precisa monitorar.

![Eventos do Windows](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Configurando os logs de eventos do Windows
Configure os logs de Eventos do Windows no [menu Dados nas Configurações do Log Analytics](log-analytics-data-sources.md#configuring-data-sources).

O Log Analytics coletará apenas os eventos dos logs de eventos do Windows que são especificados nas configurações.  Você pode adicionar um novo log digitando o nome do log e clicando em **+**.  Para cada log, somente eventos com as severidades selecionadas serão coletados.  Marque as severidades para o log específico que você deseja coletar.  Você não pode fornecer quaisquer critérios adicionais para filtrar eventos.

![Configurar eventos do Windows](media/log-analytics-data-sources-windows-events/configure.png)

## <a name="data-collection"></a>Coleta de dados
O Log Analytics coletará cada evento que corresponde a uma severidade selecionada de um log de evento monitorado conforme o evento é criado.  O agente registrará seu lugar em cada log de eventos do qual ele realiza a coleta.  Se o agente ficar offline por um período de tempo, o Log Analytics coletará os eventos de onde ele parou, mesmo que os eventos tenham sido criados enquanto o agente estava offline.

## <a name="windows-event-records-properties"></a>Propriedades de registros de eventos do Windows
Os registros de eventos do Windows têm um tipo de **Evento** e têm as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Nome do computador do qual o evento foi coletado. |
| EventCategory |Categoria do evento. |
| EventData |Todos os dados de evento em formato bruto. |
| EventID |Número do evento. |
| EventLevel |Severidade do evento em formato numérico. |
| EventLevelName |Severidade do evento em formato de texto. |
| EventLog |Nome do log de eventos do qual o evento foi coletado. |
| ParameterXml |Valores de parâmetro de evento em formato XML. |
| ManagementGroupName |Nome do grupo de gerenciamento de agentes do SCOM.  Para outros agentes, ele é AOI-<workspace ID> |
| RenderedDescription |Descrição do evento com valores de parâmetro |
| Fonte |Fonte do evento. |
| SourceSystem |Tipo de agente do qual o evento foi coletado. <br> OpsManager - agente do Windows, conexão direta ou SCOM <br>  Linux: todos os agentes do Linux  <br>  AzureStorage: Diagnóstico do Azure |
| TimeGenerated |Data e hora em que o evento foi criado no Windows. |
| UserName |Nome de usuário da conta que registrou o evento. |

## <a name="log-searches-with-windows-events"></a>Pesquisas de log com eventos do Windows
A tabela a seguir fornece diferentes exemplos de pesquisas de log que recuperam registros de eventos do Windows.

| Consultar | Descrição |
|:--- |:--- |
| Type=Event |Todos os eventos do Windows. |
| Type=Event EventLevelName=error |Todos os eventos do Windows com severidade de erro. |
| Type=Event &#124; Measure count() by Source |Contagem de eventos do Windows por fonte. |
| Type=Event EventLevelName=error &#124; Measure count() by Source |Contagem de eventos de erro do Windows por fonte. |

## <a name="next-steps"></a>Próximas etapas
* Configure o Log Analytics para coletar outras [fontes de dados](log-analytics-data-sources.md) para análise.
* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para analisar os dados coletados de fontes de dados e soluções.  
* Use [campos personalizados](log-analytics-custom-fields.md) para analisar os registros de eventos em campos individuais.
* Configure a [coleta de contadores de desempenho](log-analytics-data-sources-performance-counters.md) de seus agentes do Windows.

<!--HONumber=Oct16_HO2-->


