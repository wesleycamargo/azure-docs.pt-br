<properties 
	pageTitle="Carregar dados em ambientes de armazenamento para análise | Microsoft Azure" 
	description="Mover dados de e para o Armazenamento de Blobs do Azure" 
	services="machine-learning,storage" 
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
	ms.date="02/08/2016" 
	ms.author="bradsev" />

# Carregar dados em ambientes de armazenamento para análise

## Introdução

O Processo de Análise do Cortana (CAP) requer que os dados sejam ingeridos ou carregados em uma variedade de ambientes de armazenamento diferentes para ser processado ou analisado da maneira mais apropriada em cada estágio do processo. Destinos de dados usados para processamento por CAP incluem armazenamento de Blob Azure, bancos de dados do SQL Azure, SQL Server na VM do Azure, HDInsight (Hadoop) e aprendizado de máquina do Azure.

Esse **menu** liga os tópicos que descrevem como ingerir dados em ambientes de destino nos quais os dados podem ser armazenados e processados.

[AZURE.INCLUDE [seletor de dados de ingestão de limite](../../includes/cap-ingest-data-selector.md)]

Necessidades comerciais e técnicos, bem como o local inicial, formato e tamanho de seus dados que determinarão os ambientes de destino no qual os dados precisam ser incluídos para atingir as metas de sua análise. Não é incomum para um cenário exigir dados a serem movidos entre vários ambientes para atingir a variedade de tarefas necessárias para construir um modelo de previsão. Essa sequência de tarefas pode incluir, por exemplo, a exploração de dados, pré-processamento, limpar, redução da resolução e treinamento de modelo.

<!---HONumber=AcomDC_0211_2016-->