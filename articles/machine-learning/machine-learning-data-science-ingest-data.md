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
	ms.date="05/10/2016" 
	ms.author="bradsev" />

# Carregar dados em ambientes de armazenamento para análise

O Processo do Cortana Analytics exige que os dados sejam ingeridos ou carregados em uma variedade de ambientes de armazenamento diferentes para serem processados ou analisados da maneira mais apropriada em cada estágio do processo. Os destinos de dados geralmente usados para processamento incluem Armazenamento de Blobs do Azure, bancos de dados do SQL Azure, SQL Server na VM do Azure, HDInsight (Hadoop) e Aprendizado de Máquina do Azure.

[AZURE.INCLUDE [seletor de dados de ingestão de limite](../../includes/cap-ingest-data-selector.md)]

Esse **menu** liga os tópicos que descrevem como ingerir dados em ambientes de destino nos quais os dados podem ser armazenados e processados.

Necessidades comerciais e técnicos, bem como o local inicial, formato e tamanho de seus dados que determinarão os ambientes de destino no qual os dados precisam ser incluídos para atingir as metas de sua análise. Não é incomum para um cenário exigir dados a serem movidos entre vários ambientes para atingir a variedade de tarefas necessárias para construir um modelo de previsão. Essa sequência de tarefas pode incluir, por exemplo, a exploração de dados, pré-processamento, limpar, redução da resolução e treinamento de modelo.

<!---HONumber=AcomDC_0518_2016-->