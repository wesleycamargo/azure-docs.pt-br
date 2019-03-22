---
title: Solucionar problemas do Stream Analytics do Azure usando logs de diagnóstico
description: Este artigo descreve como analisar logs de diagnóstico no Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/19/2019
ms.custom: seodec18
ms.openlocfilehash: cc62a6b9f03bdd6dc8671a6cf96113a2234fc092
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247147"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Solucionar problemas do Stream Analytics do Azure usando logs de diagnóstico

Ocasionalmente, um trabalho do Stream Analytics do Azure interrompe o processamento de forma inesperada. É importante conseguir resolver esse tipo de evento. As falhas podem ser causadas por um resultado de consulta inesperado, pela conexão com dispositivos ou por uma interrupção inesperada do serviço. Os logs de diagnóstico do Stream Analytics podem ajudá-lo a identificar a causa dos problemas assim que eles ocorrem e reduzir o tempo de recuperação.

## <a name="log-types"></a>Tipos de logs

O Stream Analytics oferece dois tipos de logs:

* [Logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (Always On), que fornecem informações sobre as operações realizadas nos trabalhos.

* [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (configuráveis), que fornecem informações mais avançadas sobre tudo o que acontece com um trabalho. Os logs de diagnóstico são iniciados quando o trabalho é criado e terminam quando o trabalho é excluído. Eles abrangem eventos quando o trabalho é atualizado e durante sua execução.

> [!NOTE]
> Você pode usar serviços como o armazenamento do Azure, Hubs de eventos do Azure e logs do Azure Monitor para analisar os dados não estão em conformidade. Você é cobrado com base no modelo de preço desses serviços.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Depurando com o uso de logs de atividades

Os logs de atividades são ativados por padrão e fornecem insights de alto nível sobre as operações realizadas pelo trabalho do Stream Analytics. As informações presentes nos logs de atividades podem ajudar a encontrar a causa raiz dos problemas que afetam seu trabalho. Execute as seguintes etapas para usar os logs de atividades no Stream Analytics:

1. Entre no portal do Azure e selecione **Log de atividades** em **Visão Geral**.

   ![Log de atividades do Stream Analytics](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Você pode ver uma lista das operações que foram executadas. As operações que causaram a falha do trabalho têm uma bolha de informações em vermelho.

3. Clique em uma operação para exibir o resumo. As informações fornecidas aqui geralmente são limitadas. Para obter mais detalhes sobre a operação, clique no **JSON**.

   ![Resumo de operações do log de atividades do Stream Analytics](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Role para baixo até a seção **Propriedades** do JSON, que fornece os detalhes do erro que causou a operação com falha. Neste exemplo, a falha ocorreu devido a um erro de tempo de execução de valores de latitude fora do limite.

   ![Detalhes do erro JSON](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Você pode realizar ações corretivas com base na mensagem de erro no JSON. Neste exemplo, será necessário adicionar verificações do valor de latitude entre -90 graus e 90 graus à consulta.

6. Se a mensagem de erro nos logs de atividade não é útil para identificar a causa raiz, habilitar logs de diagnóstico e usar os logs do Azure Monitor.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Enviar diagnósticos para logs do Azure Monitor

Ativar os logs de diagnóstico e enviá-los aos logs do Azure Monitor é altamente recomendável. Os logs de diagnóstico estão **desativados** por padrão. Para ativar os logs de diagnóstico, realize estas etapas:

1.  Entre no portal do Azure e navegue até o trabalho do Stream Analytics. Em **Monitoramento**, selecione **Logs de diagnóstico**. Em seguida, selecione **ativar o diagnóstico**.

    ![Navegação na folha para os logs de diagnóstico](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Crie um **Nome** em **Configurações de diagnóstico** e marque a caixa ao lado de **Enviar para o Log Analytics**. Em seguida, adicione um **workspace do Log Analytics** existente ou crie um. Marque as caixas de **Execução** e **Criação** em **LOG**, e **AllMetrics** em **MÉTRICA**. Clique em **Salvar**.

    ![Configurações para os logs de diagnóstico](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Quando o trabalho do Stream Analytics é iniciado, os logs de diagnóstico são roteados para o seu workspace do Log Analytics. Navegue até o workspace do Log Analytics e escolha **Logs** na seção **Geral**.

   ![Logs de Monitor do Azure, na seção geral](./media/stream-analytics-job-diagnostic-logs/log-analytics-logs.png)

4. Você pode [escrever sua própria consulta](../azure-monitor/log-query/get-started-portal.md) para pesquisar termos, identificar tendências, analisar padrões e fornecer insights com base nos seus dados. Por exemplo, você pode escrever uma consulta para filtrar apenas os logs de diagnóstico com a mensagem "Falha no trabalho de streaming". Os logs de diagnóstico do Azure Stream Analytics são armazenados na tabela **AzureDiagnostics**.

   ![Resultados e consulta de diagnóstico](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-query.png)

5. Quando você tiver uma consulta que está procurando os registros certos, salve-a selecionando **Salvar** e forneça um Nome e uma Categoria. Você pode criar um alerta selecionando **Nova regra de alerta**. Em seguida, especifique a condição de alerta. Selecione **Condição** e insira o valor de limite e a frequência em que essa pesquisa de log personalizada é avaliada.  

   ![Consulta de pesquisa de log de diagnóstico](./media/stream-analytics-job-diagnostic-logs/search-query.png)

6. Escolha o grupo de ações e especifique detalhes do alerta, como nome e descrição, antes de criar a regra de alerta. Você pode rotear os logs de diagnóstico de vários trabalhos para o mesmo workspace do Log Analytics. Isso permite que você configure alertas uma vez para funcionarem em todos os trabalhos.  

## <a name="diagnostics-log-categories"></a>Categorias de logs de diagnóstico

Atualmente, capturamos duas categorias de logs de diagnóstico:

* **Criação**: Captura eventos de log relacionados a operações de criação de trabalho, como criação de trabalho, adição e exclusão de entradas e saídas, adição e atualização da consulta e início e parada do trabalho.

* **Execução**: Captura eventos que ocorrem durante a execução do trabalho.
    * Erros de conectividade
    * Erros de processamento de dados, incluindo:
        * Eventos que não estão em conformidade com a definição de consulta (valores e tipos de campo sem correspondência, campos ausentes e assim por diante)
        * Erros de avaliação da expressão
    * Outros erros e eventos

## <a name="diagnostics-logs-schema"></a>Esquema dos logs de diagnóstico

Todos os logs são armazenados no formato JSON. Cada entrada tem os seguintes campos de cadeia de caracteres comuns:

NOME | DESCRIÇÃO
------- | -------
tempo real | Carimbo de data/hora (em UTC) do log.
ResourceId | ID do recurso em que a operação ocorreu, em maiúsculas. Inclui a ID da assinatura, o grupo de recursos e o nome do trabalho. Por exemplo, **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
categoria | Categoria do log, **Execução** ou **Criação**.
operationName | Nome da operação que está registrada. Por exemplo, **Enviar eventos: falha na gravação da Saída do SQL em mysqloutput**.
status | Status da operação. Por exemplo, **Com Falha** ou **Com Êxito**.
level | Nível do log. Por exemplo, **Erro**, **Aviso** ou **Informativo**.
propriedades | Detalhes específicos à entrada de log, serializados como uma cadeia de caracteres JSON. Para obter mais informações, consulte as próximas seções deste artigo.

### <a name="execution-log-properties-schema"></a>Esquema de propriedades do log de execução

Os logs de execução trazem informações sobre eventos que ocorreram durante a execução do trabalho do Stream Analytics. O esquema de propriedades varia, dependendo do tipo de evento. No momento, temos os seguintes tipos de logs de execução:

### <a name="data-errors"></a>Erros de dados

Qualquer erro ocorrido enquanto o trabalho processa os dados está nessa categoria de logs. Esses logs costumam ser criados durante operações de leitura, serialização e gravação de dados. Esses logs não incluem erros de conectividade. Os erros de conectividade são tratados como eventos genéricos.

NOME | DESCRIÇÃO
------- | -------
Fonte | Nome da entrada ou saída do trabalho em que ocorreu o erro.
Mensagem | Mensagem associada ao erro.
Type | Tipo de erro. Por exemplo, **DataConversionError**, **CsvParserError** ou **ServiceBusPropertyColumnMissingError**.
Dados | Contém dados que são úteis para localizar com precisão a origem do erro. Sujeito a truncamento, dependendo do tamanho.

Dependendo do valor de **operationName**, os erros de dados terão o seguinte esquema:
* **Serializar eventos**. Serializar os eventos ocorridos durante operações de leitura de eventos. Eles ocorrem quando os dados na entrada não atendem ao esquema de consulta por um destes motivos:
    * *Tipos incompatíveis durante a (des)serialização do evento*: identifica o campo que está causando o erro.
    * *Não é possível ler um evento, serialização inválida*: lista informações sobre a localização nos dados de entrada em que ocorreu o erro. Inclui o nome do blob para a entrada do blob, o deslocamento e uma amostra dos dados.
* **Enviar eventos**. Envia eventos ocorridos durante operações de gravação. Ele identifica o evento de streaming que causou o erro.

### <a name="generic-events"></a>Eventos genéricos

Os eventos genéricos abrangem todo o resto.

NOME | DESCRIÇÃO
-------- | --------
Erro | (opcional) Informações sobre erros. Normalmente, essas são informações de exceção, se estiverem disponíveis.
Mensagem| Mensagem de log.
Type | Tipo de mensagem. É mapeado para a categorização interna de erros. Por exemplo, **JobValidationError** ou **BlobOutputAdapterInitializationFailure**.
ID de Correlação | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) que identifica exclusivamente a execução do trabalho. Todas as entradas do log de execução desde a hora em que o trabalho é iniciado até ele ser interrompido têm o mesmo valor de **ID de Correlação**.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência da linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST de gerenciamento do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
