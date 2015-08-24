<properties 
	pageTitle="Trabalhando com trabalhos do Serviços de Mídia do Azure" 
	description="Este tópico fornece uma visão geral de como gerenciar trabalhos de serviços de mídia do Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015" 
	ms.author="juliako"/>

#Trabalhando com trabalhos do Serviços de Mídia do Azure

Um **Trabalho** contém metadados sobre o processamento a ser realizado. Cada **Trabalho** contém uma ou mais **Tarefas** que especificam uma tarefa de processamento atômica, seus ativos de entrada e ativos de saída, um processador de mídia e suas configurações associadas. Para saber mais sobre as configurações do codificador, consulte guias do codificador e esquemas do codificador.

Um trabalho de codificação é geralmente combinado com outro processamento, por exemplo, empacotamento ou criptografia do ativo ou ativos gerados pelo codificador. Tarefas em um trabalho podem ser encadeadas, em que o ativo de saída de uma tarefa é determinado como o ativo de entrada para a próxima tarefa. Dessa forma, um trabalho pode conter todo o processamento necessário para uma apresentação de mídia.

Esta seção fornece links para tarefas comuns que você executaria ao trabalhar com Jobs\\Tasks.

>[AZURE.NOTE]Atualmente, há um limite de 30 tarefas por trabalho. Se você precisa encadear mais de 30 tarefas, crie mais de um trabalho para conter as tarefas.


##Obtendo o processador de mídia

Obtenha o processador de mídia com **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../../includes/media-services-selector-get-media-processor.md)]

##Criando trabalhos 

Um trabalho é uma entidade que contém metadados sobre um conjunto de tarefas (por exemplo, codificação ou indexação). Cada tarefa executa uma operação atômica nos ativos de entrada. Por exemplo, sobre como criar trabalhos de codificação, consulte:

[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

##Indexação

[AZURE.INCLUDE [media-services-selector-index-content](../../includes/media-services-selector-index-content.md)]

##Codificação 

Codifique-os com o **Codificador de Mídia do Azure** usando o **Portal de Gerenciamento**, o **.NET** ou **API REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

##Monitorando o progresso do trabalho

Monitore o progresso do trabalho usando o **Portal de Gerenciamento do Azure**, **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-job-progress](../../includes/media-services-selector-job-progress.md)]

##Links relacionados

[Cotas e limitações](media-services-quotas-and-limitations.md)– Descreve as cotas usadas e limitações do Codificador dos Serviços de Mídia
 

<!---HONumber=August15_HO7-->