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
   ms.date="07/25/2016"
   ms.author="jeffstok" />

| Identificador de limite | Limite | Comentários |
|----------------- | ------------|--------- |
| Número máximo de Unidades de Streaming por assinatura, por região | 50 | É possível fazer uma solicitação para aumentar as unidades de streaming de sua assinatura para além de 50. Para isso, basta contatar o [Suporte da Microsoft](https://support.microsoft.com/pt-BR). |
| Taxa de transferência máxima de uma Unidade de Streaming | 1MB/s* | A taxa de transferência máxima por SU depende do cenário. A taxa de transferência real pode ser menor e depende do particionamento e da complexidade da consulta. Detalhes adicionais podem ser encontrados no artigo [Escalar trabalhos do Stream Analytics do Azure para aumentar a taxa de transferência](../articles/stream-analytics/stream-analytics-scale-jobs.md). |
| Número máximo de entradas por trabalho | 60 | Há um limite rígido de 60 entradas por trabalho do Stream Analytics. |
| Número máximo de saídas por trabalho | 60 | Há um limite rígido de 60 saídas por trabalho do Stream Analytics. |
| Número máximo de funções por trabalho | 60 | Há um limite rígido de 60 funções por trabalho do Stream Analytics. |
| Número máximo de trabalhos por região | 1500 | Cada assinatura pode ter até 1500 trabalhos por região geográfica. |

<!---HONumber=AcomDC_0727_2016-->