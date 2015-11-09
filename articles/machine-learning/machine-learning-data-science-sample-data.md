<properties 
	pageTitle="Amostragem de dados no Processo de Análise da Cortana" 
	description="Como explorar dados em vários ambientes de armazenamento." 
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
	ms.date="10/20/2015" 
	ms.author="fashah;garye;bradsev" />

#<a name="heading"></a>Amostragem de dados no Processo de Análise da Cortana

Os links neste **menu** levam você até os tópicos que descrevem como obter amostras de dados de vários ambientes de armazenamento. Esta tarefa é uma etapa no Processo de Análise da Cortana (CAP).

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

## Introdução

Este documento aborda os dados de amostragem armazenados no Armazenamento do Blobs do Azure, baixando-os por meio de programação e realizando amostragem com um exemplo de código Python. As etapas para isso são os seguintes:

Este documento aborda como realizar a amostragem de dados armazenados em três locais normalmente usados no Processo de Análise da Cortana:

- A amostragem dos **dados no contêiner de blobs do Azure** é realizada baixando-os programaticamente e realizando a amostragem com um exemplo de código Python.
- A amostragem dos **dados do SQL Server** é realizada usando SQL e a linguagem de programação Python. 
- A amostragem dos **dados da tabela do Hive** é realizada usando consultas de Hive.

<!---HONumber=Nov15_HO1-->