<properties 
	pageTitle="Fornecendo mídia sob demanda com os Serviços de Mídia do Azure" 
	description="Este tópico fornece uma visão geral dos principais componentes envolvidos na transmissão ao vivo." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/28/2015"  
	ms.author="juliako"/>


#Trabalhando com Eventos de Live Streaming com os Serviços de Mídia do Azure

##Visão geral

Ao trabalhar com a transmissão ao vivo, normalmente os seguintes componentes estão envolvidos:

- Uma câmera é usada para transmitir um evento.
- Um codificador de vídeo ao vivo que converte os sinais da câmera para fluxos que são enviados a um serviço de transmissão ao vivo. 
  
	Opcionalmente, vários codificadores ao vivo. Para determinados eventos ao vivo críticos que demandam disponibilidade e qualidade de experiência muito altas, é recomendável utilizar codificadores redundantes ativo-ativo para atingir um failover contínuo sem perda de dados.
- Um serviço de streaming ao vivo que permite que você faça o seguinte: 
	- inclusão de conteúdo ao vivo usando diversos protocolos de transmissão ao vivo (por exemplo RTMP ou Smooth Streaming) 
	- codificação de seu fluxo no fluxo de taxa de bits adaptável
	- visualização de sua transmissão ao vivo,
	- armazenamento do conteúdo incluído para ser transmitido posteriormente (vídeo sob demanda)
	- fornecimento do conteúdo por meio de protocolos de transmissão comuns (por exemplo, MPEG DASH, Smooth, HLS, HDS) diretamente aos seus clientes ou para uma CDN (Rede de Distribuição de Conteúdo) para a distribuição posterior. 
	
		
O **AMS** (Serviços de Mídia do Microsoft Azure) fornece a capacidade de ingerir, codificar, visualizar, armazenar e entregar seu conteúdo de transmissão ao vivo.

Ao fornecer conteúdo aos clientes, sua meta é fornecer um vídeo de alta qualidade para vários dispositivos em condições de rede diferentes. Para tratar da qualidade e das condições de rede, use os codificadores ao vivo para codificar seu fluxo para transmissão de vídeo com múltiplas taxas de bits (taxa de bits adaptável). Para lidar com a transmissão em dispositivos diferentes, use o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md) dos Serviços de Mídia para reempacotar dinamicamente sua transmissão para diferentes protocolos. Os serviços de mídia oferecem suporte ao fornecimento das seguintes tecnologias de streaming com taxa de bits adaptável: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS (apenas para licenciados do Adobe PrimeTime/Access).

Nos Serviços de Mídia do Azure, **Canais**, **Programas** e **StreamingEndpoints** tratam de todas as funcionalidades de transmissão ao vivo, incluindo ingestão, formatação, DVR, segurança, escalabilidade e redundância.

Um **Canal** representa um pipeline para o processamento de conteúdo de transmissão ao vivo. Atualmente, um Canal pode receber fluxos de entrada ao vivo da seguinte maneira:


- Um codificador ao vivo local envia um fluxo de taxa de bits adaptável única para o Canal que é habilitado para realizar a codificação ao vico com os serviços de mídia em um dos seguintes formatos: RTP (MPEG-TS), RTMP oi Smooth Streaming (MP4 fragmentado). O Canal então realiza a codificação ao vivo do fluxo de entrada com taxa de bits única em um fluxo de vídeo (adaptável) de múltiplas taxas de bits. Quando solicitado, os Serviços de Mídia transmitem o fluxo aos clientes.

- Um codificador ativo local envia um **RTMP** ou **Smooth Streaming** (MP4 fragmentado) com múltiplas taxas de bits para o Canal. Você pode usar os codificadores ao vivo a seguir, que produz Smooth Streaming com múltiplas taxas de bits: Elemental, Envivio, Cisco. Os codificadores ao vivo a seguir produzem RTMP: transcodificadores Adobe Flash Live, Telestream Wirecast e Tricaster. Os fluxos ingeridos passam pelos **Canais**sem nenhum processamento adicional. Seu codificador ao vivo também pode enviar um fluxo de taxa de bits única para um canal que não está habilitado para codificação ao vivo, mas que não é recomendado. Quando solicitado, os Serviços de Mídia transmitem o fluxo aos clientes.


##Trabalhando com canais habilitados a executar codificação ao vivo com os Serviços de Mídia do Azure


O diagrama a seguir mostra as partes principais da plataforma AMS envolvidas no fluxo de trabalho de transmissão ao vivo em que um canal está habilitado para executar a codificação ao vivo com os serviços de mídia.

![Fluxo de trabalho ao vivo][live-overview1]

Para obter mais informações, consulte [Trabalhando com canais habilitados para executar codificação ao vivo com os Serviços de Mídia do Azure](media-services-manage-live-encoder-enabled-channels.md).


##Trabalhando com Canais que recebam transmissão ao vivo de múltiplas taxas de bits de codificadores locais


O diagrama a seguir mostra as partes principais da plataforma AMS que estão envolvidas no fluxo de trabalho de transmissão ao vivo.

![Fluxo de trabalho ao vivo][live-overview2]

Para obter mais informações, veja [Trabalhando com canais que recebem a transmissão ao vivo de múltiplas taxas de bits de codificadores locais](media-services-manage-channels-overview.md).



##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Tópicos relacionados

[Conceitos de Serviços de Mídia](media-services-concepts.md)

[Especificação de ingestão dinâmica de MP4 fragmentado dos Serviços de Mídia do Azure](media-services-fmp4-live-ingest-overview.md)





[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png

[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 

<!---HONumber=Nov15_HO3-->