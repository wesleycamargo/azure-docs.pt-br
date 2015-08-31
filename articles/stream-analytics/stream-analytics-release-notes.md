<properties 
	pageTitle="Notas de versão do Stream Analytics | Microsoft Azure" 
	description="Notas de versão do Stream Analytics GA" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="08/20/2015" 
	ms.author="jeffstok"/>

#Notas de versão do Stream Analytic da Microsoft

## Notas de versão de 20/08/2015 do Stream Analytics ##

Esta versão contém as atualizações a seguir.

Title|Descrição
---|---
Função LAST adicionada |A função [LAST](http://msdn.microsoft.com/library/mt421186.aspx) agora está disponível em trabalhos do Stream Analytics, permitindo que você recupere o evento mais recente em um fluxo de eventos em um determinado período de tempo.
Novas funções de matriz|As funções de matriz [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) e [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) agora estão disponíveis.
Novas funções de registro|As funções de registro [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) e [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) agora estão disponíveis.

## Notas de versão de 30/07/2015 do Stream Analytics ##

Esta versão contém as atualizações a seguir.

Title|Descrição
---|---
Id da Org Power BI dissociada da Id do Azure|Esse recurso habilita a [saída do Power BI](stream-analytics-power-bi-dashboard.md) para trabalhos ASA em qualquer tipo de conta do Azure (Live Id ou Id da Org). Além disso, você pode ter uma Id da Org para sua conta do Azure e usar uma diferente para autorizar a saída do Power BI.
Suporte para saída de filas do barramento de serviço|As saídas das [Filas do Barramento de Serviço](stream-analytics-connect-data-event-outputs.md#service-bus-queues) agora estão disponíveis em trabalhos do Stream Analytics.
Suporte para saída de filas de tópicos do Barramento de Serviço|As saídas dos [Tópicos do Barramento de Serviço](stream-analytics-connect-data-event-outputs.md#service-bus-topics) agora estão disponíveis em trabalhos do Stream Analytics.

## Notas de versão de 09/07/2015 do Stream Analytics ##

Esta versão contém as atualizações a seguir.


Title|Descrição
---|---
Particionamento de saída de blob personalizado|Saídas de armazenamento de blob agora permitem uma opção para especificar a frequência em que os blobs de saída são gravados e a estrutura e o formato da pasta do caminho de saída dados. 

## Notas de versão de 03/05/2015 do Stream Analytics ##

Esta versão contém as atualizações a seguir.


Title|Descrição
---|---
Maior valor máximo aumentado da janela de tolerância inoperante|O tamanho máximo da janela de tolerância inoperante agora é 59:59 (MM:SS)
Formato de saída JSON: linha separada ou matriz|Agora há uma opção para gerar a armazenamento de blob ou hub de evento de saída como uma matriz de objetos JSON ou separando os objetos JSON com uma nova linha. 

## Notas de versão de 16/04/2015 do Stream Analytics ##


Title|Descrição
---|---
Atraso na configuração da conta de Armazenamento do Azure|Ao criar um trabalho do Stream Analytics em uma região pela primeira vez, você precisará criar uma nova conta de Armazenamento ou especifique uma conta existente para monitorar trabalhos do Stream Analytics nessa região. Devido à latência na configuração de monitoramento, criar outro trabalho do Stream Analytics na mesma região dentro de 30 minutos solicitará a especificação de uma segunda conta de armazenamento em vez de mostrar aquela recentemente configurada no menu suspenso de Conta de Monitoramento de Armazenamento. Para evitar a criação de uma conta de Armazenamento desnecessária, aguarde 30 minutos após a criação de um trabalho em uma região pela primeira vez antes de provisionar trabalhos adicionais nessa região.
Atualização de trabalho|Atualmente, o Stream Analytics não dá suporte a edições dinâmicas para a definição ou a configuração de um trabalho em execução. Para alterar a entrada, saída, consulta, escala ou configuração de um trabalho em execução, você deve primeiro interromper o trabalho.
Tipos de dados inferidos da fonte de entrada|Se uma instrução CREATE TABLE não for usada, o tipo de entrada será derivado do formato de entrada, por exemplo, todos os campos do arquivo CSV são cadeias de caracteres. Os campos precisam ser explicitamente convertidos no tipo certo usando a função CAST para evitar erros de incompatibilidade de tipo.
Os campos ausentes são enviados como valores nulos|Fazer referência a um campo que não esteja presente na fonte de entrada resultará em valores nulos no evento de saída.
As instruções WITH devem preceder as instruções SELECT|Na consulta, as instruções SELECT devem seguir subconsultas definidas em instruções WITH.
Problema de memória insuficiente|Trabalhos do Stream Analytics com uma grande tolerância para eventos de problemas verificação e/ou consultas complexas com uma grande quantidade de estado podem causar insuficiência de memória para o trabalho, resultando no reinício do trabalho. As operações de iniciar e parar ficarão visíveis nos logs de operação do trabalho. Para evitar esse comportamento, dimensione a consulta por várias partições. Em uma versão futura, essa limitação será tratada por meio da redução do desempenho nos trabalhos afetados em vez de reiniciá-los.
Grandes entradas de blob sem carimbo de data/hora de carga podem causar o problema de memória insuficiente|Consumir grandes arquivos de um armazenamento Blob pode fazer com que trabalhos do Stream Analytics falhem caso um campo de carimbo de data/hora não seja especificado via TIMESTAMP BY. Para evitar esse problema, não permita que os blobs tenham mais de 10 MB de tamanho.
Limitação de volume de eventos de Banco de Dados SQL|Quando o Banco de Dados SQL for usado como um destino de saída, volumes muito grandes de dados de saída poderão fazer com que o trabalho do Stream Analytics atinja o tempo limite. Para resolver esse problema, reduza o volume de saída usando agregações ou operadores de filtro, ou escolha o armazenamento Blob ou Eventos de Hubs do Azure como um destino de saída.
Os conjuntos de dados do PowerBI só podem conter uma tabela|O PowerBI não oferece suporte a mais de uma tabela em um determinado conjunto de dados.

## Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/pt-br/home?forum=AzureStreamAnalytics)

## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](../stream.analytics.get.started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=August15_HO8-->