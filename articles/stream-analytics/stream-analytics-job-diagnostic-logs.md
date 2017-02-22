---
title: "Logs de diagnóstico do Stream Analytics do Azure | Microsoft Docs"
description: "Aprenda a analisar os logs de diagnóstico de trabalhos do Stream Analytics no Microsoft Azure."
keywords: 
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: f0292efd50721ef58028df778052eb0ed6fcda84
ms.openlocfilehash: 724eba50b7428b0012e8f062e264ce057e2a5287


---
# <a name="job-diagnostic-logs"></a>Logs de diagnóstico do trabalho

## <a name="introduction"></a>Introdução
O Stream Analytics expõe dois tipos de logs: 
* [Logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) que estão sempre habilitados e fornecem informações sobre as operações realizadas em trabalhos;
* [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) que são configuráveis pelo usuário e fornecem informações mais avançadas sobre tudo o que acontece com o trabalho desde quando ele é criado, atualizado, enquanto ele está em execução e até ser excluído;

## <a name="how-to-enable-diagnostic-logs"></a>Como habilitar os logs de diagnóstico
Os logs de diagnóstico estão **desligados** por padrão. Para habilitá-los, siga estas etapas:

Entre no Portal do Azure e navegue até a folha de trabalho de streaming e use a folha de "Logs de diagnóstico" em "Monitoramento".

![navegação de folha para logs de diagnóstico](./media/stream-analytics-job-diagnostic-logs/image1.png)  

Em seguida, clique no link "Ativar diagnóstico"

![ativar logs de diagnóstico](./media/stream-analytics-job-diagnostic-logs/image2.png)

Nos diagnósticos abertos, altere o status para "Ligado".

![alterar logs de diagnóstico de status](./media/stream-analytics-job-diagnostic-logs/image3.png)

Configure o destino de arquivamento desejado (conta de armazenamento, hub de eventos, Log Analytics) e selecione as categorias de logs que você deseja coletar (Execução, Criação). Em seguida, salve a nova configuração de diagnóstico.

Uma vez salva, a configuração levará cerca de 10 minutos para entrar em vigor e após isso os logs começarão a aparecer no destino de arquivamento configurado, que pode ser visto na folha “Logs de diagnóstico”:

![navegação de folha para logs de diagnóstico](./media/stream-analytics-job-diagnostic-logs/image4.png)

Há mais informações sobre a configuração de diagnóstico disponíveis na página [logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostic-logs-categories"></a>Categorias dos logs de diagnóstico
Há duas categorias de logs de diagnóstico que capturamos no momento:

* **Criação:** capture os logs relacionados a operações de criação de trabalho: criação, adição e exclusão de entradas e saídas, adição e atualização da consulta, início e parada do trabalho.
* **Execução:** capture o que está ocorrendo durante a execução do trabalho.
    * Erros de conectividade;
    * Erros de processamento de dados;
        * Eventos que não estão em conformidade com a definição de consulta (valores e tipos de campo incompatíveis, campos ausentes etc.);
        * Erros de avaliação da expressão;
    * Etc.

## <a name="diagnostic-logs-schema"></a>Esquema de logs de diagnóstico
Todos os logs são armazenados no formato JSON e cada entrada tem os seguintes campos de cadeia de caracteres comuns:

Nome | Descrição
------- | -------
tempo real | O carimbo de data/hora (em UTC) do log
resourceId | A ID do recurso em que a operação ocorreu, em maiúsculas. Inclui a ID de assinatura, o grupo de recursos e o nome do trabalho. Por exemplo, `/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB`
categoria | A categoria do log, `Execution` ou `Authoring`
operationName | Nome da operação que está registrada. Por exemplo, `Send Events: SQL Output write failure to mysqloutput`
status | O status da operação. Por exemplo: `Failed, Succeeded`.
level | Nível do log. Por exemplo, `Error, Warning, Informational`
propriedades | detalhes específicos de entrada de log, serializada como cadeia de caracteres JSON, consulte abaixo para obter mais detalhes

### <a name="execution-logs-properties-schema"></a>Esquema de propriedades de logs de execução
Os logs de execução contêm informações sobre eventos que ocorreram durante a execução do trabalho do Stream Analytics.
Dependendo do tipo de eventos, as propriedades terão esquemas diferentes. No momento, temos os seguintes tipos:

### <a name="data-errors"></a>Erros de dados
Qualquer erro nos dados de processamento se enquadrará nessa categoria de logs. Produzido principalmente durante a operação de leitura, serialização e gravação de dados. Ele não inclui erros de conectividade que são tratados como eventos genéricos.

Nome | Descrição
------- | -------
Fonte | Nome da entrada ou saída do trabalho em que o erro ocorreu.
Mensagem | Mensagem associada ao erro.
Tipo | O tipo de erro. Por exemplo, `DataConversionError, CsvParserError, ServiceBusPropertyColumnMissingError` etc.
Dados | Contém dados úteis para localizar com precisão a origem do erro. Sujeito a truncamento dependendo do tamanho.

Dependendo do valor de **operationName**, os erros de dados terão o esquema a seguir:
* **Serializar eventos** – acontecendo durante operações de leitura de evento quando os dados na entrada não atendem ao esquema de consulta:
    * Tipos incompatíveis durante a (des)serialização do evento: campo causando o erro.
    * Não é possível ler um evento, serialização inválida: informações sobre o local nos dados de entrada em que o erro ocorreu: nome do blob para entrada de blob, deslocamento e um exemplo dos dados.
* **Enviar eventos** – operações de gravação: evento de streaming que causou o erro.

### <a name="generic-events"></a>Eventos genéricos
Todo o resto

Nome | Descrição
-------- | --------
Erro | (opcional) Informações de erro, geralmente informações de exceção se disponíveis.
Mensagem| Mensagem de log.
Tipo | Tipo de mensagem, mapeia para categorização interna de erros: por exemplo, JobValidationError, BlobOutputAdapterInitializationFailure etc.
ID de Correlação | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) que identifica exclusivamente a execução do trabalho. Todas as entradas de log de execução produzidas desde que o trabalho foi iniciado até ele parar terão a mesma “ID de Correlação”.



## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Feb17_HO1-->


