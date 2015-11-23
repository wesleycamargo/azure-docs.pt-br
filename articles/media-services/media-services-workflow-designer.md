<properties 
	pageTitle="Criar fluxos de trabalho de codificação avançada com o Designer de Fluxo de Trabalho" 
	description="Saiba mais sobre como criar fluxos de trabalho de codificação avançada com o Designer de Fluxo de Trabalho." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako,johndeu,anilmur" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/16/2015"  
	ms.author="juliako"/>


#Criar fluxos de trabalho de codificação avançada com o Designer de Fluxo de Trabalho

##Visão geral

O **Designer de Fluxo de Trabalho** é uma ferramenta de área de trabalho do Windows usada para projetar e criar fluxos de trabalho personalizados para codificação com **Fluxo de Trabalho do Media Encoder Premium**. Usando o poder da ferramenta de designer de fluxo de trabalho, você pode projetar e criar fluxos de trabalho complexos que serão executados no **Media Encoder Premium**.

Os fluxos de trabalho podem incluir a lógica de decisão do cliente e a ramificação com base nas propriedades do arquivo de origem de entrada. Você pode criar fluxos de trabalho com propriedades substituíveis e valores dinâmicos para tornar até mesmo as tarefas de codificação mais complexas fáceis de repetir e de personalizar na nuvem.

Os fluxos de trabalho de exemplo que você pode criar incluem:

- Os fluxos de trabalho baseados em decisão que inspecionam o conteúdo de origem para resolução e que codificam somente as faixas de saída desejadas. Isso será útil para eliminar as faixas descartadas que seriam geradas pela expansão inadvertida do conteúdo de origem.
- Vários arquivos de entrada podem ser usados para oferecer suporte a legendas, sobreposições e montagens de conteúdo. 

Essa ferramenta também pode ser usada para modificar qualquer um dos [fluxos de trabalho publicados](media-services-workflow-designer.md#existing_workflows).

>[AZURE.NOTE]Para obter uma cópia da ferramenta Designer de Fluxo de Trabalho, entre em contato com mepd@microsoft.com.


Quando um arquivo de fluxo de trabalho é criado, ele pode ser carregado como um Ativo e, em seguida, ser usado para codificar arquivos de mídia. Para saber mais sobre como codificar com o **Fluxo de Trabalho do Media Encoder Premium** usando **.NET**, consulte [Codificação avançada com Fluxo de Trabalho do Media Encoder Premium](media-services-encode-with-premium-workflow.md).

##<a id="existing_workflows"></a>Modificar fluxos de trabalho existentes

Os [fluxos de trabalho publicados](media-services-workflow-designer.md#existing_workflows) padrão podem ser modificados usando a ferramenta de designer. Você pode obter arquivos de fluxo de trabalho padrão [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). A pasta também contém a descrição desses arquivos.

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

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


Se precisar de suporte ou se tiver perguntas sobre a criação de fluxos de trabalho personalizados na ferramenta Designer de Fluxo de Trabalho, envie um email para mepd@microsoft.com.

##Veja também

[Vídeos de treinamento de Designer de Fluxo de Trabalho do Azure Premium Encoder](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

<!---HONumber=Nov15_HO3-->