<properties
	pageTitle="Como cortar um vídeo | Microsoft Azure"
	description="Este artigo mostra como cortar vídeos com o Codificador de Mídia Padrão."
	services="media-services"
	documentationCenter=""
	authors="anilmur"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/11/2016"  
	ms.author="anilmur;juliako;"/>

# Cortar vídeos com o Codificador de Mídia Padrão

Você pode usar o MES (Codificador de Mídia Padrão) para cortar a entrada de seu vídeo. Corte é o processo de seleção de uma janela retangular no quadro de vídeo e codificação apenas dos pixels nessa janela. O diagrama a seguir ajuda a ilustrar o processo.

![Cortar um vídeo](./media/media-services-crop-video/media-services-crop-video01.png)

Suponha que você tenha como entrada um vídeo que tem uma resolução de 1920 x 1080 pixels (taxa de proporção de 16:9), mas tem barras pretas (formatos pillarbox) à esquerda e direita e, portanto, apenas uma janela de 4:3 ou 1440 x 1080 pixels contém vídeo ativo. Você pode usar o MES para cortar ou editar as barras pretas e codificar a região 1440 x 1080.

O corte no MES é uma fase de pré-processamento e, portanto, os parâmetros de corte na predefinição de codificação se aplicam ao vídeo de entrada original. A codificação é um estágio posterior, e as configurações de largura/altura se aplicam ao vídeo *pré-processado*, não ao vídeo original. Ao projetar a predefinição, você precisa fazer o seguinte: (a) selecionar os parâmetros de corte com base no vídeo de entrada original e (b) selecionar as configurações de codificação com base no vídeo cortado. Se as configurações de codificação não forem correspondentes ao vídeo cortado, a saída não será como esperado.

O tópico [a seguir](media-services-advanced-encoding-with-mes.md#encoding_with_dotnet) mostra como criar um trabalho de codificação com o MES e especificar uma predefinição personalizada para a tarefa de codificação.

## Criando uma predefinição personalizada

No exemplo mostrado no diagrama:

1. A entrada original é 1920 x 1080
1. Ela precisa ser recortada para uma saída de 1440 x 1080, que é centralizada no quadro de entrada
1. Isso significa um deslocamento de X de (1920 – 1440)/2 = 240 e um deslocamento de Y de zero
1. A Largura e a Altura do retângulo de Corte são 1440 e 1080, respectivamente
1. No estágio de codificação, a tarefa é produzir três camadas com as resoluções 1440 x 1080, 960 x 720 e 480 x 360, respectivamente

###Predefinição JSON


	{
	  "Version": 1.0,
	  "Sources": [
	    {
	      "Streams": [],
	      "Filters": {
	        "Crop": {
	            "X": 240,
	            "Y": 0,
	            "Width": 1440,
	            "Height": 1080
	        }
	      },
	      "Pad": true
	    }
	  ],
	  "Codecs": [
	    {
	      "KeyFrameInterval": "00:00:02",
	      "H264Layers": [
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 3400,
	          "MaxBitrate": 3400,
	          "BufferWindow": "00:00:05",
	          "Width": 1440,
	          "Height": 1080,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        },
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 2250,
	          "MaxBitrate": 2250,
	          "BufferWindow": "00:00:05",
	          "Width": 960,
	          "Height": 720,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        },
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 1250,
	          "MaxBitrate": 1250,
	          "BufferWindow": "00:00:05",
	          "Width": 480,
	          "Height": 360,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        }
	      ],
	      "Type": "H264Video"
	    },
	    {
	      "Profile": "AACLC",
	      "Channels": 2,
	      "SamplingRate": 48000,
	      "Bitrate": 128,
	      "Type": "AACAudio"
	    }
	  ],
	  "Outputs": [
	    {
	      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
	      "Format": {
	        "Type": "MP4Format"
	      }
	    }
	  ]
	}


##Restrições de corte

O recurso de corte deve ser manual. Você precisa carregar o vídeo de entrada em uma ferramenta de edição adequada que permite selecionar quadros de interesse, posicionar o cursor para determinar os deslocamentos do retângulo de corte, determinar a predefinição de codificação ajustada para esse vídeo específico, etc. Esse recurso não se destina a habilitar opções como: detecção automática e remoção de bordas pretas no formato letterbox/pillarbox no vídeo de entrada.

As restrições a seguir se aplicam ao recurso de corte. Se elas não forem atendidas, a Tarefa de codificação poderá falhar ou produzir uma saída inesperada.

1. As coordenadas e o tamanho do retângulo de Corte deverão se ajustar ao vídeo de entrada
1. Conforme mencionado acima, a Largura e a Altura nas configurações de codificação precisam corresponder ao vídeo cortado
1. O corte se aplica aos vídeos capturados no modo paisagem (ou seja, não se aplica aos vídeos gravados com um smartphone mantido verticalmente ou no modo retrato)
1. Funciona melhor com vídeo progressivo capturado com pixels quadrados

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Próxima etapa
 
Veja os roteiros de aprendizagem dos Serviços de Mídia do Azure para ajudá-lo a saber mais sobre os excelentes recursos oferecidos pelo AMS.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

<!---HONumber=AcomDC_0713_2016-->