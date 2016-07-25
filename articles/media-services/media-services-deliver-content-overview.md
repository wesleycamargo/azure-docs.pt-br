<properties 
	pageTitle="Visão geral do fornecimento de conteúdo a clientes" 
	description="Este tópico fornece uma visão geral do que está envolvido no fornecimento de conteúdo com os Serviços de Mídia do Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2016"
	ms.author="juliako"/>


#Visão geral do fornecimento de conteúdo a clientes

##Visão geral

Ao fornecer conteúdo aos clientes (eventos de transmissão ao vivo ou vídeo sob demanda) sua meta é fornecer um vídeo de alta qualidade para vários dispositivos em condições de rede diferentes.

Para atingir esse objetivo:

- codifique seu fluxo para múltiplas taxas de bits (taxa de bits adaptável) transmissão de vídeo (isso também tratará das condições de rede e de qualidade) e
- use o [Empacotamento dinâmico](media-services-dynamic-packaging-overview.md) dos serviços de mídia para reempacotar dinamicamente seu fluxo em protocolos diferentes (isso se encarregará da transmissão em dispositivos diferentes). Os serviços de mídia oferecem suporte ao fornecimento das seguintes tecnologias de streaming com taxa de bits adaptável: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS (apenas para licenciados do Adobe PrimeTime/Access).

Este tópico apresenta uma visão geral dos conceitos importantes de fornecimento de conteúdo.

Para verificar os problemas conhecidos, confira [esta](media-services-deliver-content-overview.md#known-issues) seção.

##Empacotamento dinâmico


Os Serviços de Mídia fornecem empacotamento dinâmico, que permite a você distribuir o conteúdo de taxa de bits adaptável MP4 ou Smooth Streaming codificado em formatos de streaming suportados pelo Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) sem a necessidade de empacotar novamente nesses formatos de fluxo contínuo. É recomendável usar empacotamento dinâmico para fornecer seu conteúdo.

Para aproveitar os benefícios do empacotamento dinâmico, você precisa fazer o seguinte:

- Codifique seu arquivo mezzanine (de origem) em um conjunto de arquivos MP4 de taxa de bits adaptável ou arquivos Smooth Streaming de taxa de bits adaptável,
- Obter pelo menos uma unidade de streaming sob demanda para o ponto de extremidade de streaming por meio do qual você planeja fornecer seu conteúdo. Para saber mais, consulte [Como dimensionar unidades reservadas para streaming sob demanda](media-services-manage-origins.md#scale_streaming_endpoints).

Com o empacotamento dinâmico, você só precisa armazenar e pagar pelos arquivos em um único formato de armazenamento, e os Serviços de Mídia criarão e fornecerão a resposta apropriada com base nas solicitações de um cliente.

Observe que, além de poder usar os recursos de empacotamento dinâmico, unidades reservadas de streaming sob demanda oferecem capacidade de saída dedicada que pode ser comprada em incrementos de 200 Mbps. Por padrão, o streaming por demanda é configurado em um modelo de instância compartilhada para a qual os recursos do servidor (por exemplo, computação, capacidade de egresso etc.) são compartilhados com todos os outros usuários. Para melhorar a taxa de transferência de um streaming por demanda, é recomendável adquirir unidades reservadas para Streaming por Demanda.

Para saber mais, consulte [Empacotamento dinâmico](media-services-dynamic-packaging-overview.md).

##Filtros e manifestos dinâmicos

Os Serviços de Mídia permitem definir filtros para seus ativos. Esses filtros são regras do lado do servidor que permitirão aos clientes optar por realizar ações como: reproduzir apenas uma seção de um vídeo (em vez de reproduzir o vídeo inteiro) ou especificar apenas um subconjunto de representações de áudio e vídeo com o qual o dispositivo do cliente pode lidar (em vez de todas as representações que estão associadas ao ativo). A filtragem de ativos é obtida por meio de **Manifestos Dinâmicos** criados mediante solicitação do cliente para transmitir um vídeo com base em filtros especificados.

Para obter mais informações, consulte [Filtros e manifestos dinâmicos](media-services-dynamic-manifest-overview.md).

##Localizadores

Para fornecer a seus usuários uma URL que pode ser usada para transmitir ou baixar seu conteúdo, primeiro você precisa "publicar" o ativo criando um localizador. Os localizadores fornecem um ponto de entrada para acessar os arquivos contidos em um ativo. Os Serviços de Mídia oferecem suporte a dois tipos de localizadores:

- Os localizadores **OnDemandOrigin**, usados para transmitir mídia (por exemplo, MPEG DASH, HLS ou Smooth Streaming) ou baixar arquivos progressivamente.
-  Os localizadores de URL **SAS** (assinatura de acesso), usados para baixar arquivos de mídia para seu computador local.

Uma **política de acesso** é usada para definir as permissões (como leitura, gravação e lista) e a duração pela qual um cliente tem acesso a determinado ativo. Observe que a permissão de lista (AccessPermissions.List) não deve ser usada ao criar um localizador OrDemandOrigin.

Os localizadores têm uma data de validade. Quando você usa o portal para publicar seus ativos, são criados localizadores com uma data de validade de 100 anos.

>[AZURE.NOTE] Se você usou o portal para criar localizadores antes de março de 2015, foram criados localizadores com uma data de validade de dois anos.

Para atualizar a data de validade em um localizador, use as APIs [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) ou [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Observe que, quando você atualiza a data de validade de um localizador SAS, a URL é alterada.
 
Os localizadores não foram desenvolvidos para gerenciar o controle de acesso por usuário. Para conceder direitos de acesso diferentes para usuários individuais, use as soluções de gerenciamento de direitos digitais (DRM). Para obter mais informações, consulte [Protegendo mídia](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Observe que, quando você cria um localizador, pode haver um atraso de 30 segundos devido a processos de armazenamento e propagação necessários no Armazenamento do Azure.


##Streaming adaptável 

Tecnologias de taxa de bits adaptável permitem que os aplicativos de player de vídeo determinem as condições da rede e selecionem entre várias taxas de bits. Quando a comunicação da rede degrada, o cliente pode selecionar uma taxa de bits inferior, permitindo que o player continue a reproduzir o vídeo com uma qualidade de vídeo inferior. Como melhorarem as condições de rede cliente pode alternar para uma taxa de bits mais alta com melhor qualidade de vídeo. Os Serviços de Mídia do Azure dão suporte às seguintes tecnologias com taxa de bits adaptável: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS.

Para fornecer aos usuários URLs de streaming, você deve primeiro criar um localizador OnDemandOrigin. Criar o localizador oferece a você o caminho base para o ativo que contém o conteúdo que você deseja transmitir. No entanto, para poder transmitir este conteúdo você precisa modificar esse caminho ainda mais. Para construir uma URL completa para o arquivo de manifesto de streaming, você deve concatenar o valor do caminho do localizador e o nome de arquivo de manifesto (ISM). Em seguida, anexe um formato apropriado (se necessário) ao caminho do localizador.

>[AZURE.NOTE]Você também pode transmitir seu conteúdo por uma conexão SSL. Para fazer isso, certifique-se de que suas URLs de streaming começam com HTTPS.

Observe que você só pode transmitir por SSL se o ponto de extremidade de streaming por meio do qual você pode distribuir o conteúdo tiver sido criado depois de 10 de setembro de 2014. Se suas URLs de streaming baseiam-se nos pontos de extremidade de streaming após 10 de setembro, a URL contém "streaming.mediaservices.windows.net" (o novo formato). URLs de streaming que contêm "origin.mediaservices.windows.net" (o formato antigo) não dão suporte a SSL. Se sua URL está no formato antigo e você deseja ser capaz de transmitir por SSL, crie um novo ponto de extremidade de streaming. Use URLs criadas com base no novo ponto de extremidade de streaming para transmitir seu conteúdo por SSL.


##Formatos de URL de streaming

###Formato MPEG DASH

{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=mpd-time-csf)

Exemplo

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)



###Formato Apple HTTP Live Streaming (HLS) V4

{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=m3u8-aapl)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

###Formato Apple HTTP Live Streaming (HLS) V3

{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=m3u8-aapl-v3)
	
	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

###Formato HLS (Apple HTTP Live Streaming) com filtro somente áudio

Por padrão, faixas somente áudio são incluídas no manifesto do HLS. Isso é necessário para a certificação da Apple Store para redes de celular. Nesse caso, se um cliente não tiver largura de banda suficiente ou conectada por uma conexão 2G, ele alternará para a reprodução somente áudio. Isso ajuda a manter a transmissão contínua sem buffer, mas com uma desvantagem de nenhuma exibição de vídeo. No entanto, em alguns cenários, o buffer do player pode ser preferível em relação ao somente áudio. Se desejar remover a faixa somente áudio, é possível adicionar (somente áudio = false) à URL e removê-la.

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

Para saber mais, confira [este](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.


###Formato Smooth Streaming

{nome do ponto de extremidade de streaming - nome de conta do dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arqui}.ism/Manifest

Exemplo:

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

###<a id="fmp4_v20"></a>Manifesto do Smooth Streaming 2.0 (manifesto herdado)

Por padrão o formato de manifesto Smooth Streaming contém a marca de repetição (r-tag). No entanto, alguns jogadores não dão suporte à r-tag. Esses clientes podem usar o formato que desabilita a r-tag:

{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=fmp4-v20)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

###HDS (apenas para licenciados do Adobe PrimeTime/Access)

{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=f4m-f4f)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)

##Download progressivo 

O download progressivo permite iniciar a reprodução da mídia antes do arquivo inteiro ter sido baixado. Você não pode baixar progressivamente arquivos .ism* (ismv, isma, ismt, ismc).

Para baixar conteúdo progressivamente, use o tipo de localizador OnDemandOrigin. O exemplo a seguir mostra a URL que é baseada no tipo de localizador OnDemandOrigin:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

A seguinte consideração é aplicável:

- É necessário descriptografar qualquer ativo criptografado em armazenamento que você deseje transmitir do serviço de origem para download progressivo.

##Baixar

Para baixar o conteúdo em um dispositivo de cliente, você deve criar um localizador SAS. O localizador SAS lhe dá acesso ao contêiner do Armazenamento do Azure em que o arquivo está localizado. Para criar a URL de download, você deve inserir o nome do arquivo entre o host e a assinatura SAS.

O seguinte exemplo mostra a URL que se baseia no localizador SAS:

	https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

As seguintes considerações se aplicam:

- É necessário descriptografar qualquer ativo criptografado em armazenamento que você deseje transmitir do serviço de origem para download progressivo.
- Um download que não foi concluído em 12 horas falhará.

##Ponto de extremidade de streaming

Um **Ponto de Extremidade de Transmissão** representa um serviço de transmissão que pode entregar conteúdo diretamente a um aplicativo de player do cliente ou a uma CDN (Rede de Distribuição de Conteúdo) para distribuição posterior. O fluxo de saída de um serviço de ponto de extremidade de streaming pode ser uma transmissão ao vivo ou um ativo de vídeo sob demanda em sua conta dos Serviços de Mídia. Além disso, você pode controlar a capacidade do serviço de ponto de extremidade de streaming para lidar com necessidades crescentes de largura de banda ajustando as unidades reservadas de streaming. Você deve alocar pelo menos uma unidade reservada para aplicativos em um ambiente de produção. Para obter mais informações, consulte [Como dimensionar um serviço de mídia](media-services-manage-origins.md#scale_streaming_endpoints).

##Problemas conhecidos

### Alterações na versão do manifesto do Smooth Streaming

Antes da liberação do serviço de julho de 2016, quando os Ativos produzidos pelo Codificador de Mídia Padrão, Fluxo de Trabalho Premium do Codificador de Mídia ou Codificador de Mídia do Azure herdado eram transmitidos com o Empacotamento Dinâmico, o manifesto do Smooth Streaming retornado estava em conformidade com a versão 2.0, em que as durações de fragmentos não usam as chamadas marcações de repetição (“r”). Por exemplo:

	<?xml version="1.0" encoding="UTF-8"?>
	<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
		<StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
			<QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
			<c t="0" d="2000" n="0" />
			<c d="2000" />
			<c d="2000" />
			<c d="2000" />
		</StreamIndex>
	</SmoothStreamingMedia>

Após a liberação do serviço de julho de 2016, o manifesto do Smooth Streaming gerado está em conformidade com a versão 2.2, com durações de fragmentos que usam marcações de repetição. Por exemplo:

	<?xml version="1.0" encoding="UTF-8"?>
	<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
		<StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
			<QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
			<c t="0" d="2000" r="4" />
		</StreamIndex>
	</SmoothStreamingMedia>

Alguns dos clientes herdados do Smooth Streaming podem não dar suporte às marcações de repetição e falhar durante o carregamento do manifesto. Para atenuar esse problema, use o parâmetro de formato do manifesto herdado **(format=fmp4-v20)** (para obter mais informações, confira [esta](media-services-deliver-content-overview.md#fmp4_v20) seção) ou atualize o cliente para a versão mais recente que dá suporte a marcações de repetição.

##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Tópicos relacionados

[Atualizar localizadores dos Serviços de Mídia depois de implantar chaves de armazenamento](media-services-roll-storage-access-keys.md)
 

<!---HONumber=AcomDC_0713_2016-->