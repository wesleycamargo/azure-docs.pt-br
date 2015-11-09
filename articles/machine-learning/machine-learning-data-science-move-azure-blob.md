<properties 
	pageTitle="Mover dados de e para o Armazenamento de Blobs do Azure | Microsoft Azure" 
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
	ms.date="10/12/2015" 
	ms.author="bradsev;sunliangms;sachouks;mohabib" />

# Mover dados de e para o Armazenamento de Blobs do Azure

[AZURE.INCLUDE [seletor de dados de ingestão de limite](../../includes/cap-ingest-data-selector.md)]

## Introdução

Orientações sobre as tecnologias usadas para mover dados para e/ou do armazenamento de blobs do Azure estão vinculadas aqui:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

O melhor método para você depende de seu cenário. O artigo [Cenários para ADAPT (Processo e Tecnologia de Análise Avançada) no Aprendizado de Máquina do Azure](../machine-learning-data-science-plan-sample-scenarios.md) o ajudará a determinar os recursos necessários para uma variedade de fluxos de trabalho de ciência de dados usados no processo de análise avançada.

> [AZURE.NOTE]Para obter uma introdução completa ao armazenamento de Blobs do Azure, consulte [Noções básicas do Serviço Blob do Azure](../storage-dotnet-how-to-use-blobs.md) e [Serviço de Blob do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

> [AZURE.TIP]Como alternativa, você pode usar o [Azure Data Factory](https://azure.microsoft.com/pt-BR/services/data-factory/) para criar e agendar um pipeline que baixará os dados do armazenamento de Blob do Azure, os passará para um serviço Web publicado do Aprendizado de Máquina do Azure, receberá os resultados de análise preditiva e carregará os resultados no armazenamento. Para obter mais informações, consulte [Criar pipelines preditivos usando o Azure Data Factory e o Aprendizado de Máquina do Azure](../data-factory/data-factory-create-predictive-pipelines.md).

## Pré-requisitos

Este documento pressupõe que você tenha uma assinatura, uma conta de armazenamento do Azure e a chave de armazenamento correspondente dessa conta. Antes de carregar/baixar os dados, você deve conhecer o nome e a chave da sua conta do Armazenamento do Azure.

- Para configurar uma assinatura do Azure, consulte [Avaliação gratuita de um mês](https://azure.microsoft.com/pt-BR/pricing/free-trial/).
- Para obter instruções sobre como criar uma conta de armazenamento e para obter informações sobre conta e chave, consulte [Sobre contas do armazenamento do Azure](../storage-create-storage-account.md).




 

<!---HONumber=Nov15_HO1-->