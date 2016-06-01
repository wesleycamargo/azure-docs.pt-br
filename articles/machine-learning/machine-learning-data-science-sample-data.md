<properties 
	pageTitle="Amostra de dados em contêineres de blob do Azure, SQL Server e tabelas Hive | Microsoft Azure" 
	description="Como explorar dados armazenados em vários ambientes do Azure." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/10/2016" 
	ms.author="fashah;garye;bradsev" />

#<a name="heading"></a>Amostra de dados em contêineres de blob do Azure, SQL Server e tabelas Hive

Este documento leva a tópicos que abordam como obter amostras de dados armazenados em um dos três diferentes locais do Azure:

- A amostragem dos **dados no contêiner de blobs do Azure** é realizada baixando-os programaticamente e realizando a amostragem com um exemplo de código Python.
- A amostragem dos **dados do SQL Server** é realizada usando SQL e a linguagem de programação Python. 
- A amostragem dos **dados da tabela do Hive** é realizada usando consultas de Hive.

O **menu** abaixo leva a tópicos que descrevem como obter amostras de dados de cada um desses ambientes de armazenamento do Azure.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

A tarefa de amostragem é uma etapa do [CAP (Processo do Cortana Analytics)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## Por que fazer a amostragem de dados?

Se o conjunto de dados que você deseja analisar é grande, geralmente é uma boa ideia reduzir a amostra de dados para um tamanho menor, mas representativo e mais gerenciável. Isso facilita a compreensão de dados, exploração e engenharia de recursos. Sua função no Processo de Análise do Cortana é habilitar a rápida criação de protótipos de funções de processamento de dados e modelos de aprendizado de máquina.

<!---HONumber=AcomDC_0518_2016-->