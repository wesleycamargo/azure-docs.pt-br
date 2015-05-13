<properties 
	pageTitle="Notas de versão do Stream Analytics | Azure" 
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
	ms.date="04/24/2015" 
	ms.author="jeffstok"/>

#Atraso na configuração da conta de Armazenamento do Azure

Ao criar um trabalho do Stream Analytics em uma região pela primeira vez, você precisará criar uma nova conta de Armazenamento ou especifique uma conta existente para monitorar trabalhos do Stream Analytics nessa região. Devido à latência na configuração de monitoramento, criar outro trabalho do Stream Analytics na mesma região dentro de 30 minutos solicitará a especificação de uma segunda conta de armazenamento em vez de mostrar aquela recentemente configurada no menu suspenso de Conta de Monitoramento de Armazenamento. Para evitar a criação de uma conta de Armazenamento desnecessária, aguarde 30 minutos após a criação de um trabalho em uma região pela primeira vez antes de provisionar trabalhos adicionais nessa região.

##Atualização de trabalho

Atualmente, o Stream Analytics não dá suporte a edições dinâmicas para a definição ou a configuração de um trabalho em execução. Para alterar a entrada, saída, consulta, escala ou configuração de um trabalho em execução, você deve primeiro interromper o trabalho.

##Tipos de dados inferidos da fonte de entrada

Se uma instrução **CREATE TABLE** não for usada, o tipo de entrada será derivado do formato de entrada, por exemplo, todos os campos do arquivo CSV são cadeias de caracteres. Os campos precisam ser explicitamente convertidos no tipo certo usando a função CAST para evitar erros de incompatibilidade de tipo.

##Os campos ausentes são enviados como valores nulos

Fazer referência a um campo que não esteja presente na fonte de entrada resultará em valores nulos no evento de saída.

##As instruções WITH devem preceder as instruções SELECT

Na consulta, as instruções SELECT devem seguir subconsultas definidas em instruções WITH.

##Problema de memória insuficiente

Trabalhos do Stream Analytics com uma grande tolerância para eventos de problemas verificação e/ou consultas complexas com uma grande quantidade de estado podem causar insuficiência de memória para o trabalho, resultando no reinício do trabalho. As operações de iniciar e parar ficarão visíveis nos logs de operação do trabalho. Para evitar esse comportamento, dimensione a consulta por várias partições. Em uma versão futura, essa limitação será tratada por meio da redução do desempenho nos trabalhos afetados em vez de reiniciá-los.

##Grandes entradas de blob sem carimbo de data/hora de carga podem causar o problema de memória insuficiente

Consumir grandes arquivos de um armazenamento Blob pode fazer com que trabalhos do Stream Analytics falhem caso um campo de carimbo de data/hora não seja especificado via TIMESTAMP BY. Para evitar esse problema, não permita que os blobs tenham mais de 10 MB de tamanho.

##Limitação de volume de eventos de Banco de Dados SQL

Quando o Banco de Dados SQL for usado como um destino de saída, volumes muito grandes de dados de saída poderão fazer com que o trabalho do Stream Analytics atinja o tempo limite. Para resolver esse problema, reduza o volume de saída usando agregações ou operadores de filtro, ou escolha o armazenamento Blob ou Eventos de Hubs do Azure como um destino de saída.

##Os conjuntos de dados do PowerBI só podem conter uma tabela

O PowerBI não oferece suporte a mais de uma tabela em um determinado conjunto de dados.

## Obter ajuda
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](stream.analytics.get.started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--HONumber=52-->
