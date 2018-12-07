---
title: Analisar o uso de dados no Log Analytics | Microsoft Docs
description: Use o painel Uso e custo estimado no Log Analytics para avaliar a quantidade de dados que é enviada para o Log Analytics e identificar o que pode causar o aumento imprevisto.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: e702e1f5eb1816b007317765e4c9a9f88bb99bfd
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635417"
---
# <a name="analyze-data-usage-in-log-analytics"></a>Analisar o uso de dados no Log Analytics

> [!NOTE]
> Este artigo descreve como analisar o uso de dados no Log Analytics.  Consulte os artigos a seguir para informações relacionadas.
> - [Gerenciar custos controlando o volume e a retenção de dados no Log Analytics](log-analytics-manage-cost-storage.md) descreve como controlar seus custos alterando o período de retenção de dados.
> - [Monitorando o uso e os custos estimados](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) descreve como visualizar o uso e os custos estimados em vários recursos de monitoramento do Azure para diferentes modelos de preços. Também descreve como alterar seu modelo de preços.

## <a name="understand-usage"></a>Reconhecer o uso

Use **Uso do Log Analytics e Custos Estimados** para revisar e analisar o uso de dados. A mostra quantos dados são coletados por cada solução, quantos dados estão sendo retidos e uma estimativa de seus custos com base na quantidade de dados ingeridos e em qualquer retenção adicional além do valor incluído.

![Uso e custos estimados](media/log-analytics-usage/usage-estimated-cost-dashboard-01.png)<br>

Para explorar seus dados com mais detalhes, clique no ícone no canto superior direito de qualquer um dos gráficos na página **Uso e custos estimados**. Agora você pode trabalhar com essa consulta para explorar mais detalhes do seu uso.  

![Exibir logs](media/log-analytics-usage/logs.png)<br>

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Solução de problemas por uso acima do esperado
O uso aumenta devido a uma ou mais das seguintes causas:
- Os dados enviados para o Log Analytics estão acima do esperado
- Mais nós do que o esperado enviando dados para o Log Analytics ou alguns nós estão enviando mais dados do que o normal

Vamos dar uma olhada em como podemos aprender mais sobre essas duas causas. 

> [!NOTE]
> Alguns dos campos do tipo de dados Uso, ainda no esquema, foram reprovados e seus valores não são mais preenchidos. Estes são **Computador**, bem como campos relacionados à ingestão (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** e **AverageProcessingTimeMs**.

### <a name="data-volume"></a>Volume de dados 
Na página **Uso e custos estimados**, o gráfico *Ingestão de dados por solução* mostra o volume total de dados enviados e quanto está sendo enviado por cada solução. Isso permite determinar tendências, como se o uso geral de dados (ou uso por uma solução específica) está crescendo, permanecendo estável ou diminuindo. É a consulta usada para gerar isso

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

Observe que a cláusula "onde IsBillable = verdadeiro" filtra os tipos de dados de determinadas soluções para o qual não há nenhuma taxa de ingestão. 

Você pode detalhar mais para ver tendências de dados para tipos de dados específicos, por exemplo, se você quiser estudar os dados devido aos logs do IIS:

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

### <a name="nodes-sending-data"></a>Nós enviando dados

Para reconhecer o número de nós que relatam dados no último mês, use

`Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1d)    
| render timechart`

Para ver a contagem de eventos ingeridos por computador, use

`union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last`

Use essa consulta com moderação, pois é caro executar. Para ver a contagem de eventos faturáveis ingeridos por computador, use 

`union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last`

Se você quiser ver quais tipos de dados faturáveis estão enviando dados para um computador específico, use:

`union withsource = tt *
| where Computer == "*computer name*"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last `

Para aprofundar a fonte de dados de um determinado tipo de dados, aqui estão algumas consultas de exemplo úteis:

+ Solução de **Segurança**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Solução do **Gerenciamento de log**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Tipo de dados de **Desempenho**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Tipo de dados de **Evento**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Tipo de dados **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Tipo de dados **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Dicas para reduzir o volume de dados

Algumas sugestões para reduzir o volume de logs coletados incluem:

| Origem do alto volume de dados | Como reduzir o volume de dados |
| -------------------------- | ------------------------- |
| Eventos de segurança            | Selecione [eventos de segurança mínima ou comuns](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/) <br> Alterar a política de auditoria de segurança para coletar somente eventos necessários. Em particular, examine a necessidade para coletar eventos para <br> - [auditoria de plataforma de filtragem](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [auditoria de registro](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [auditoria de sistema de arquivos](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [auditoria de objeto kernel](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [auditoria de manipulação de identificador](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - auditoria de armazenamento removível |
| contadores de desempenho       | Altere a [configuração do contador de desempenho](log-analytics-data-sources-performance-counters.md) para: <br> - Reduzir a frequência de coleta <br> - Reduzir o número de contadores de desempenho |
| Logs de eventos                 | Altere a [configuração de log de eventos](log-analytics-data-sources-windows-events.md) para: <br> - Reduzir o número de logs de eventos coletados <br> - Coletar somente níveis de eventos necessários. Por exemplo, não colete eventos de nível *informações* |
| syslog                     | Altere a [configuração do syslog](log-analytics-data-sources-syslog.md) para: <br> - Reduzir o número de instalações coletadas <br> - Coletar somente níveis de eventos necessários. Por exemplo, não coletar eventos de nível *Informações* e *Depurar* |
| AzureDiagnostics           | Altere a coleção de logs do recurso para: <br> - Reduzir o número de logs de envio de recursos para o Log Analytics <br> - Coletar somente os logs necessários |
| Dados da solução de computadores que não precisam da solução | Use [direcionamento de solução](../azure-monitor/insights/solution-targeting.md) para coletar dados somente dos grupos de computadores necessários. |

### <a name="getting-node-counts"></a>Ao obter contagens de nó 

Se você estiver no nível de preço "Por nó (OMS)", será cobrado com base no número de nós e soluções usados, o número de nós do Insights e do Analytics para os quais você está sendo faturado será mostrado na tabela do **Uso e custo estimado**.  

Para ver o número de nós de segurança distintos, você pode usar a consulta:

`union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count`

Para ver o número de nós de automação distintos, use a consulta:

` ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc`

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Criar um alerta quando a coleta de dados for maior que a esperada
Este artigo descreve como criar um alerta quando:
- O volume de dados excede uma quantidade definida.
- A previsão do volume de dados excede uma quantidade definida.

Os alertas do Azure oferecem suporte a [alertas de log](../monitoring-and-diagnostics/monitor-alerts-unified-log.md) que usam consultas de pesquisa. 

A consulta abaixo tem um resultado quando há mais de 100 GB de dados coletados nas últimas 24 horas:

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

A consulta abaixo usa uma fórmula simples para prever quando mais de 100 GB de dados serão enviados em um mesmo dia: 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Para receber um alerta em caso de volume de dados diferente, altere o número 100 nas consultas para o número de GB que deve disparar um alerta.

Use as etapas descritas em [criar um novo alerta de log](../monitoring-and-diagnostics/alert-metric.md) para ser notificado quando a coleta de dados for maior que a esperada.

Ao criar o alerta para a primeira consulta, quando há mais de 100 GB de dados em 24 horas, defina:  

- **Definir condição de alerta** especifica seu workspace do Log Analytics como o destino do recurso.
- **Critérios de alerta** especificam o seguinte:
   - **Nome do sinal** seleciona **Pesquisa de registro personalizada**
   - **Consulta de pesquisa** como `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - **Lógica de alerta** é **Baseada em** *número de resultados* e **Condição** é *maior* que um **Limite**  de *0*
   - **Período de tempo** de *1440* minutos e **Frequência de alerta** a cada *60* minutos uma vez que os dados de uso são atualizados somente uma vez por hora.
- **Definir os detalhes do alerta** especifica o seguinte:
   - O **Nome** como *Volume de dados maior que 100 GB em 24 horas*
   - A **Gravidade** como *Aviso*

Especifique um já existente ou crie um novo [Grupo de ação](../monitoring-and-diagnostics/monitoring-action-groups.md) para que, quando o alerta de log corresponder aos critérios, você seja notificado.

Ao criar o alerta para a segunda consulta, quando existe a previsão de que haverá mais de 100 GB de dados em 24 horas, defina:

- **Definir condição de alerta** especifica seu workspace do Log Analytics como o destino do recurso.
- **Critérios de alerta** especificam o seguinte:
   - **Nome do sinal** seleciona **Pesquisa de registro personalizada**
   - **Consulta de pesquisa** como `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - **Lógica de alerta** é **Baseada em** *número de resultados* e **Condição** é *maior* que um **Limite**  de *0*
   - **Período de tempo** de *180* minutos e **Frequência de alerta** a cada *60* minutos uma vez que os dados de uso são atualizados somente uma vez por hora.
- **Definir os detalhes do alerta** especifica o seguinte:
   - O **Nome** como *Previsão de volume de dados maior que 100 GB em 24 horas*
   - A **Gravidade** como *Aviso*

Especifique um já existente ou crie um novo [Grupo de ação](../monitoring-and-diagnostics/monitoring-action-groups.md) para que, quando o alerta de log corresponder aos critérios, você seja notificado.

Ao receber um alerta, use as etapas na seção a seguir para solucionar problemas relativos ao uso acima do esperado.

## <a name="next-steps"></a>Próximas etapas
* Confira [Pesquisas de log no Log Analytics](log-analytics-queries.md) para aprender a usar a linguagem de pesquisa. Você pode usar consultas de pesquisa para executar outras análises nos dados de uso.
* Use as etapas descritas em [criar um alerta de log](../monitoring-and-diagnostics/alert-metric.md) para ser notificado quando um critério de pesquisa for atendido.
* Use [direcionamento de solução](../azure-monitor/insights/solution-targeting.md) para coletar dados somente dos grupos de computadores necessários.
* Para configurar uma política de coleta de eventos efetivamente segura, analise [Política de filtragem da Central de Segurança do Azure](../security-center/security-center-enable-data-collection.md).
* Altere a [configuração do contador de desempenho](log-analytics-data-sources-performance-counters.md).
* Para modificar as configurações de coleta de eventos, analise [configuração de log de eventos](log-analytics-data-sources-windows-events.md).
* Para modificar as configurações de coleta de syslog, analise [configuração de syslog](log-analytics-data-sources-syslog.md).
