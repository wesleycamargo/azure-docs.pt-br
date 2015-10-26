<properties 
   pageTitle="Tabela de limites do Stream Analytics"
   description="Descreve os limites do sistema e tamanhos recomendados para componentes e conexões do Stream Analytics."
   services="stream-analytics"
   documentationCenter="NA"
   authors="jeffstokes72"
   manager="paulettm"
   editor="cgronlun" />
<tags 
   ms.service="stream-analytics"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-data"
   ms.date="07/13/2015"
   ms.author="jeffstok" />

| Identificador de limite | Limite | Comentários |
|----------------- | ------------|--------- |
| Número máximo de Unidades de Streaming por assinatura, por região | 50 | É possível fazer uma solicitação para aumentar as unidades de streaming de sua assinatura para além de 50. Para isso, basta contatar o [Suporte da Microsoft](https://support.microsoft.com/pt-BR). |
| Taxa de transferência máxima de uma Unidade de Streaming | 1MB/s* | A taxa de transferência máxima por SU depende do cenário. A taxa de transferência real pode ser menor e depende do particionamento e da complexidade da consulta. Detalhes adicionais podem ser encontrados no artigo [Escalar trabalhos do Stream Analytics do Azure para aumentar a taxa de transferência](../articles/stream-analytics/stream-analytics-scale-jobs.md). |
| Limitação de consulta da instrução SELECT | 5 saídas por consulta | Esse limite pode ser aumentado no futuro. |
| Limitação de subconsulta da instrução SELECT | 14 agregações por subconsulta | Esse limite pode ser aumentado no futuro. |

<!---HONumber=Oct15_HO3-->