<properties 
	pageTitle="Criar fluxos de trabalho de codificação avançada com o Designer de Fluxo de Trabalho" 
	description="Saiba mais sobre como criar fluxos de trabalho de codificação avançada com o Designer de Fluxo de Trabalho." 
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
	ms.date="09/07/2015"
	ms.author="juliako"/>


#Criar fluxos de trabalho de codificação avançada com o Designer de Fluxo de Trabalho

##Visão geral
O **Designer de Fluxo de Trabalho** é uma ferramenta de área de trabalho do Windows usada para projetar e criar fluxos de trabalho personalizados para codificação com **Fluxo de Trabalho do Media Encoder Premium**.

Essa ferramenta também pode ser usada para modificar [fluxos de trabalho existentes](media-services-workflow-designer.md#existing_workflows).

>[AZURE.NOTE]Para obter uma cópia da ferramenta de Designer de Fluxo de Trabalho, entre em contato com mepd@microsoft.com.


Quando um arquivo de fluxo de trabalho é criado, ele pode ser carregado como um Ativo e, em seguida, ser usado para codificar arquivos de mídia. Para obter informações sobre como codificar com o **Fluxo de Trabalho do Media Encoder Premium** usando **.NET**, consulte [Codificação avançada com Fluxo de Trabalho do Media Encoder Premium](media-services-encode-with-premium-workflow.md).

##<a id="existing_workflows"></a>Modificar fluxos de trabalho existentes

Os arquivos de fluxo de trabalho padrão podem ser modificados usando a ferramenta de designer. Você pode obter arquivos de fluxo de trabalho padrão [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). A pasta também contém a descrição desses arquivos.

Os vídeos a seguir mostram como usar o designer.

###Dia 1 - Introdução

O vídeo do Dia 1 abrange:

- Visão geral do designer
- Fluxos de trabalho básicos – "Hello World"
- Criar vários arquivos MP4 de saída para uso com o streaming dos Serviços de Mídia do Azure

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-1]

###Dia 2

O vídeo do Dia 2 abrange:

- Diversos cenários de arquivo de origem – a manipulação do áudio
- Fluxos de trabalho com lógica avançada
- Estágios de gráfico

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-2]

###Dia 3

O vídeo do Dia 3 abrange:

- Scripts em fluxos de trabalho/plantas
- Restrições com o codificador atual
- Perguntas e respostas
 
> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-3]



##Roteiros de aprendizagem dos Serviços de Mídia

Você pode exibir os roteiros de aprendizagem do AMS aqui:

- [Fluxo de trabalho do streaming ao vivo do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Fluxo de trabalho do streaming sob demanda do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


##Consulte também

[Vídeos de treinamento de Designer de Fluxo de Trabalho do Azure Premium Encoder](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

<!---HONumber=Oct15_HO2-->