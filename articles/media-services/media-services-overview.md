<properties 
	pageTitle="Visão geral e cenários comuns do Serviços de Mídia do Azure" 
	description="Este tópico oferece uma visão geral dos Serviços de Mídia do Azure" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako,anilmur" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/12/2016"
	ms.author="juliako"/>

#Visão geral e cenários comuns do Serviços de Mídia do Azure

Os Serviços de Mídia do Microsoft Azure tratam-se de uma plataforma extensível baseada em nuvem que permite aos desenvolvedores compilar aplicativos de gerenciamento e entrega de mídia escalonável. Os serviços de mídia se baseiam em APIs REST que permitem que você carregue com segurança, armazene, codifique e empacote o conteúdo de áudio ou vídeo para entrega de streaming sob demanda e ao vivo para vários clientes (por exemplo, TV, PCs e dispositivos móveis).

Você pode compilar fluxos de trabalho de ponta a ponta usando totalmente os serviços de mídia. Você também pode optar por usar componentes de terceiros para algumas partes do seu fluxo de trabalho. Por exemplo, codifique usando um codificador de terceiros. Em seguida, carregue, proteja, empacote e entregue usando os serviços de mídia.

Você pode optar por transmitir seu conteúdo ao vivo ou fornecer conteúdo sob demanda. Este tópico mostra cenários comuns de entrega de conteúdo [ao vivo](media-services-overview.md#live_scenarios) ou [sob demanda](media-services-overview.md#vod_scenarios). O tópico também está vinculado a outros tópicos relevantes.

## SDKs e ferramentas 

Para compilar soluções de serviços de mídia, você pode usar:

- [API REST dos Serviços de Mídia](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Um dos SDKs de cliente disponíveis: [SDK dos Serviços de Mídia do Azure para .NET](https://github.com/Azure/azure-sdk-for-media-services), [SDK do Azure para Java](https://github.com/Azure/azure-sdk-for-java), [Serviços de Mídia do Azure para Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) ou [SDK do PHP do Azure](https://github.com/Azure/azure-sdk-for-php)
- Ferramentas existentes: [Portal Clássico do Azure](http://manage.windowsazure.com/) ou [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer).


##Roteiros de aprendizagem dos Serviços de Mídia

Você pode exibir os roteiros de aprendizagem do AMS aqui:

- [Fluxo de trabalho do streaming ao vivo do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Fluxo de trabalho do streaming sob demanda do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##Cartaz


[Aqui](http://azure.microsoft.com/documentation/infographics/media-services/) é possível exibir o cartaz dos Serviços de Mídia do Azure que representa os fluxos de trabalho do AMS, desde a criação de mídia até o consumo.

##Pré-requisitos

Para começar a usar o Azure Media Services, você deve possuir o seguinte:
 
3. Uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](azure.microsoft.com).
2. Uma conta de Serviços de Mídia do Azure. Use o Portal Clássico do Azure, o .NET ou a API REST para criar a conta dos Serviços de Mídia do Azure. Para obter mais informações, veja [Criar conta](media-services-create-account.md).
3. (Opcional) Configure o ambiente de desenvolvimento. Escolha .NET ou API REST para seu ambiente de desenvolvimento. Para obter mais informações, veja [Configurar ambiente](media-services-dotnet-how-to-use.md). 

	Além disso, saiba como conectar de forma programática [Conectar](media-services-dotnet-connect_programmatically.md).
4. (Recomendado) Aloque uma ou mais unidades de escala. É recomendável alocar uma ou mais unidades de escala para aplicativos no ambiente de produção. Para obter mais informações, veja [Gerenciando pontos de extremidade de transmissão](media-services-manage-origins.md).

##Conceitos

Para obter mais informações, veja [Conceitos](media-services-concepts.md).


##<a id="vod_scenarios"></a>Fornecendo Mídia sob Demanda com os Serviços de Mídia do Azure: cenários e tarefas comuns

Esta seção descreve cenários comuns e fornece links para tópicos relevantes. O diagrama a seguir mostra as partes principais da plataforma de serviços de mídia que estão envolvidas em fornecer conteúdo sob demanda.

![Fluxo de trabalho VoD][vod-overview]


###Proteja o conteúdo no armazenamento e forneça mídia de streaming sem proteção (não criptografada)

1. Carregar um arquivo mezzanine de alta qualidade em um ativo.
	
	É recomendável aplicar a opção de criptografia de armazenamento a seu ativo para proteger o conteúdo durante o carregamento e enquanto ele estiver em repouso no armazenamento.
 
1. Codifique para um conjunto de arquivos MP4 com taxa de bits adaptável.

	É recomendável aplicar a opção de criptografia de armazenamento ao ativo de saída para proteger o conteúdo em repouso.
	
1. Configure a política de entrega de ativos (usada pelo empacotamento dinâmico).
	
	Se seu ativo tiver o armazenamento criptografado, você **deverá** configurar a política de entrega de ativos.

1. Publicar o ativo criando um localizador OnDemand.

	Verifique se você tem pelo menos uma unidade reservada de streaming no ponto de extremidade de streaming do qual você deseja transmitir conteúdo.

1. Fluxo de conteúdo publicado.

###Proteger o conteúdo no armazenamento, fornecer mídia de streaming criptografada dinamicamente  

Para poder usar criptografia dinâmica, primeiro é necessário obter pelo menos uma unidade reservada de streaming no ponto de extremidade de streaming do qual você deseja transmitir conteúdo criptografado.

1. Carregue um arquivo mezanino de alta qualidade em um ativo. Aplique a opção de criptografia de armazenamento ao ativo.
1. Codifique para um conjunto de arquivos MP4 com taxa de bits adaptável. Aplique a opção de criptografia de armazenamento ao ativo de saída.
1. Crie uma chave de conteúdo de criptografia para o ativo que você quer que seja criptografado dinamicamente durante a reprodução.
2. Configure a política de autorização de chave de conteúdo.
1. Configure a política de entrega de ativos (usada pelo empacotamento dinâmico e criptografia dinâmica).
1. Publique o ativo, criando um localizador OnDemand.
1. Fluxo de conteúdo publicado. 

###Conteúdo do índice

1. Carregar um arquivo mezzanine de alta qualidade em um Ativo.
1. Conteúdo do índice.

	O trabalho de indexação gera arquivos que podem ser usados como CC (legenda oculta) na reprodução de vídeo. Ele também gera arquivos que o habilitam a fazer pesquisa em vídeo e saltar para o local exato do vídeo.

1. Consumir conteúdo indexado.


###Entregar o download progressivo 

1. Carregar um arquivo mezzanine de alta qualidade em um ativo.
1. Codificar em um único arquivo MP4.
1. Publicar o ativo criando um localizador OnDemand ou SAS

	Se estiver usando o localizador OnDemand, verifique se você tem pelo menos uma unidade reservada de streaming no ponto de extremidade de streaming do qual você planeja baixar conteúdo de forma progressiva.

	Se você estiver usando o localizador de SAS, o conteúdo será baixado do armazenamento de blob do Azure. Nesse caso, não é necessário ter unidades reservadas de streaming.
  
1. Download progressivo de conteúdo.

###Consulte também

- [Como: carregar conteúdo](media-services-manage-content.md#upload)
- [Como obter um processador de mídia](media-services-get-media-processor.md)
- [Como codificar conteúdo](media-services-manage-content.md#encode)
- [Como monitorar trabalhos](media-services-portal-check-job-progress.md)
- [Como indexar conteúdo](media-services-manage-content.md#index)
- [Como proteger conteúdo](media-services-manage-content.md#encrypt)
- [Como proteger publicação](media-services-manage-content.md#publish)
- [Como dimensionar a codificação](media-services-portal-encoding-units.md)

##<a id="live_scenarios"></a>Trabalhando com Eventos de Live Streaming com os Serviços de Mídia do Azure

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
	
		
Os **Serviços de Mídia do Microsoft Azure** (AMS) fornecem a capacidade de incluir, codificar, visualizar, armazenar e fornecer o conteúdo de transmissão ao vivo.

Ao fornecer conteúdo aos clientes, sua meta é fornecer um vídeo de alta qualidade para vários dispositivos em condições de rede diferentes. Para tratar da qualidade e das condições de rede, use os codificadores ao vivo para codificar seu fluxo para transmissão de vídeo com múltiplas taxas de bits (taxa de bits adaptável). Para lidar com streaming em diferentes dispositivos, use o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md) dos Serviços de Mídia para reempacotar dinamicamente seu fluxo para diferentes protocolos. Os serviços de mídia oferecem suporte ao fornecimento das seguintes tecnologias de streaming com taxa de bits adaptável: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS (apenas para licenciados do Adobe PrimeTime/Access).

Nos Serviços de Mídia do Azure, **Canais**, **Programas** e **StreamingEndpoints** tratam de todas as funcionalidades de transmissão ao vivo, incluindo ingestão, formatação, DVR, segurança, escalabilidade e redundância.

Um **Canal** representa um pipeline para o processamento de conteúdo de transmissão ao vivo. Atualmente, um Canal pode receber fluxos de entrada ao vivo da seguinte maneira:


- Um codificador ao vivo local envia um fluxo de taxa de bits adaptável única para o Canal que é habilitado para realizar a codificação ao vico com os serviços de mídia em um dos seguintes formatos: RTP (MPEG-TS), RTMP oi Smooth Streaming (MP4 fragmentado). O Canal então realiza a codificação ao vivo do fluxo de entrada com taxa de bits única em um fluxo de vídeo (adaptável) de múltiplas taxas de bits. Quando solicitado, os Serviços de Mídia transmitem o fluxo aos clientes.

	A codificação de um fluxo ao vivo com os Serviços de Mídia está no modo **Visualização**.
- Um codificador ativo local envia um **RTMP** ou **Smooth Streaming** (MP4 fragmentado) com múltiplas taxas de bits para o Canal. Você pode usar os codificadores ao vivo a seguir, que produz Smooth Streaming com múltiplas taxas de bits: Elemental, Envivio, Cisco. Os codificadores ao vivo a seguir produzem RTMP: transcodificadores Adobe Flash Live, Telestream Wirecast e Tricaster. Os fluxos ingeridos passam pelos **Canais**sem nenhum processamento adicional. Seu codificador ao vivo também pode enviar um fluxo de taxa de bits única para um canal que não está habilitado para codificação ao vivo, mas que não é recomendado. Quando solicitado, os Serviços de Mídia transmitem o fluxo aos clientes.


###Trabalhando com canais habilitados a executar codificação ao vivo com os Serviços de Mídia do Azure


O diagrama a seguir mostra as partes principais da plataforma AMS envolvidas no fluxo de trabalho de transmissão ao vivo em que um canal está habilitado para executar a codificação ao vivo com os serviços de mídia.

![Fluxo de trabalho ao vivo][live-overview1]

Para obter mais informações, consulte [Trabalhando com canais habilitados para executar codificação ao vivo com os Serviços de Mídia do Azure](media-services-manage-live-encoder-enabled-channels.md).


###Trabalhando com Canais que recebam transmissão ao vivo de múltiplas taxas de bits de codificadores locais


O diagrama a seguir mostra as partes principais da plataforma AMS que estão envolvidas no fluxo de trabalho de transmissão ao vivo.

![Fluxo de trabalho ao vivo][live-overview2]

Para obter mais informações, veja [Trabalhando com canais que recebem a transmissão ao vivo de múltiplas taxas de bits de codificadores locais](media-services-manage-channels-overview.md).

##Consumo de conteúdo

Os Serviços de Mídia do Azure fornecem as ferramentas necessárias para criar aplicativos de player do cliente sofisticados e dinâmicos para a maioria das plataformas, incluindo: dispositivos iOS, dispositivos Android, Windows, Windows Phone, Xbox e decodificadores de sinais. O tópico a seguir fornece links para SDKs e estruturas de Player que você pode usar para desenvolver seus próprios aplicativos de cliente que podem consumir mídia de streaming dos Serviços de Mídia.

[Desenvolvendo aplicativos de player de vídeo](media-services-develop-video-players.md)

##Habilitando o CDN do Azure

Os Serviços de Mídia dão suporte à integração com o CDN do Azure. Para obter informações sobre como habilitar o CDN do Azure, consulte [Como gerenciar pontos de extremidade de Streaming em uma conta de Serviços de Mídia](media-services-manage-origins.md#enable_cdn).

##Dimensionamento de uma conta de serviços de mídia

Você pode dimensionar os **Serviços de Mídia** especificando o número de **Unidades Reservadas para Streaming** e **Unidades Reservadas para Codificação** com as quais você deseja provisionar sua conta.

Você também pode dimensionar sua conta dos Serviços de Mídia adicionando contas de armazenamento a ela. Cada conta de armazenamento é limitada a 500 TB. Para expandir o armazenamento além das limitações padrão, você pode optar por anexar diversas contas de armazenamento a uma única conta de serviços de mídia.

[Este](media-services-how-to-scale.md) tópico fornece links para tópicos relevantes.

##Suporte

O [Suporte do Azure](http://azure.microsoft.com/support/options/) fornece opções de suporte do Azure, incluindo os Serviços de Mídia.

##Padrões e práticas de orientação

[Orientação sobre padrões e práticas](https://wamsg.codeplex.com/) [Documentação online](https://msdn.microsoft.com/library/dn735912.aspx) [Livro eletrônico para download](https://www.microsoft.com/download/details.aspx?id=42629)


##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Contrato de nível de serviço (SLA)

- Para a Codificação dos Serviços de mídia, garantimos a disponibilidade de 99,9% de transações de API REST.
- Para streaming, atenderemos com êxito a solicitações com uma garantia de disponibilidade de 99,9% para conteúdos de mídia existentes em casos que pelo menos uma unidade para streaming é adquirida.
- Para canais ao vivo, garantimos que os canais em execução terão conectividade externa em, no mínimo, 99,9% do tempo.
- Para proteção de conteúdo, garantimos que atenderemos com êxito a solicitações de chave em, no mínimo, 99,9% do tempo.
- Para o indexador, podemos atenderemos com êxito às solicitações de tarefa do indexador processadas com uma unidade reservada para codificação em 99,9% do tempo.

	Para obter mais informações, veja [SLA do Microsoft Azure](http://azure.microsoft.com/support/legal/sla/).

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 

<!---HONumber=AcomDC_0114_2016-->