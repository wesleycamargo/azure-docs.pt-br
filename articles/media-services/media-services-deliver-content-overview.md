<properties 
	pageTitle="Visão geral do fornecimento de conteúdo a clientes" 
	description="Este tópico fornece uma visão geral do que está envolvido no fornecimento de conteúdo com os Serviços de Mídia do Azure." 
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
	ms.date="04/08/2015" 
	ms.author="juliako"/>


#Visão geral do fornecimento de conteúdo a clientes

##Visão geral

Ao se trabalhar com os Serviços de Mídia, um dos cenários comuns é:

1. Carregar um arquivo de entrada (chamado de arquivo mezzanine) em um ativo. Por exemplo, H.264, MP4 ou WMV.
1. Codifique o ativo em um conjunto de MP4s de taxa de bits adaptável.
1. Publique o ativo. 
2. Use o [empacotamento dinâmico](http://msdn.microsoft.com/library/azure/jj889436.aspx) para fornecer o conteúdo a seus clientes em qualquer um dos seguintes formatos: MPEG DASH, Apple HLS ou Smooth Streaming. 

Este tópico fornece uma visão geral dos principais [conceitos](media-services-fornecer-content.md #concepts) e links para tópicos que mostram como executar [tarefas](../media-services-deliver-content.md#tasks) relacionadas ao fornecimento de conteúdo.

##<a id="concepts"></a>Conceitos

A lista a seguir descreve conceitos e terminologia úteis para o fornecimento de mídia.

###Localizadores

Para fornecer a seus usuários uma URL que pode ser usada para transmitir ou baixar seu conteúdo, primeiro você precisa "publicar" o ativo criando um localizador.  Os localizadores fornecem um ponto de entrada para acessar os arquivos contidos em um ativo. Os Serviços de Mídia oferecem suporte a dois tipos de localizadores: 

- Os localizadores **OnDemandOrigin**, usados para transmitir mídia (por exemplo, MPEG DASH, HLS ou Smooth Streaming) ou baixar arquivos progressivamente.
-  Os localizadores de URL **SAS** (assinatura de acesso), usados para baixar arquivos de mídia para seu computador local.

Uma **política de acesso** é usada para definir as permissões (como leitura, gravação e lista) e a duração pela qual um cliente tem acesso a determinado ativo. Observe que a permissão de lista (Accesspermissions) não deve ser usada ao criar um localizador OrDemandOrigin.

Os localizadores têm uma data de validade. Quando você usa o portal para publicar seus ativos, são criados localizadores com uma data de validade de 100 anos. 

>[AZURE.NOTE] Se você usou o portal para criar localizadores antes de março de 2015, foram criados localizadores com uma data de validade de dois anos.  

Para atualizar a data de validade em um localizador, use as APIs [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) ou [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Observe que, quando você atualiza a data de validade de um localizador SAS, a URL é alterada. 
 
Os localizadores não foram desenvolvidos para gerenciar o controle de acesso por usuário. Para conceder direitos de acesso diferentes para usuários individuais, use as soluções de gerenciamento de direitos digitais (DRM). Para obter mais informações, consulte [Protegendo mídia](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Observe que, quando você cria um localizador, pode haver um atraso de 30 segundos devido a processos de armazenamento e propagação necessários no Armazenamento do Azure.


###Streaming adaptável 

Tecnologias de taxa de bits adaptável permitem que os aplicativos de player de vídeo determinem as condições da rede e selecionem entre várias taxas de bits. Quando a comunicação da rede degrada, o cliente pode selecionar uma taxa de bits inferior, permitindo que o player continue a reproduzir o vídeo com uma qualidade de vídeo inferior. Como melhorarem as condições de rede cliente pode alternar para uma taxa de bits mais alta com melhor qualidade de vídeo. Os Serviços de Mídia do Azure dão suporte às seguintes tecnologias de taxa de bits adaptável: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS.

Para fornecer aos usuários URLs de streaming, você deve primeiro criar um localizador OnDemandOrigin. Criar o localizador oferece a você o caminho base para o ativo que contém o conteúdo que você deseja transmitir. No entanto, para poder transmitir este conteúdo você precisa modificar esse caminho ainda mais. Para construir uma URL completa para o arquivo de manifesto de streaming, você deve concatenar o valor do caminho do localizador e o nome de arquivo de manifesto (ISM). Em seguida, anexe um formato apropriado (se necessário) ao caminho do localizador. 

Você também pode transmitir seu conteúdo por uma conexão SSL. Para fazer isso, certifique-se de que suas URLs de streaming começam com HTTPS. 

Observe que você só pode transmitir por SSL se o ponto de extremidade de streaming por meio do qual você pode distribuir o conteúdo tiver sido criado depois de 10 de setembro de 2014. Se suas URLs de streaming baseiam-se nos pontos de extremidade de streaming após 10 de setembro, a URL contém "streaming.mediaservices.windows.net" (o novo formato). URLs de streaming que contêm "origin.mediaservices.windows.net" (o formato antigo) não dão suporte a SSL. Se sua URL está no formato antigo e você deseja ser capaz de transmitir por SSL, crie um novo ponto de extremidade de streaming. Use URLs criadas com base no novo ponto de extremidade de streaming para transmitir seu conteúdo por SSL. 


####Formatos de URL de streaming:

**Formato Smooth Streaming**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Exemplo:

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest


**Formato MPEG DASH**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf) 

Exemplo

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

**Formato Apple HTTP Live Streaming (HLS) V4**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

**Formato Apple HTTP Live Streaming (HLS) V3**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)
	
	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

**HDS (apenas para licenciados do Adobe PrimeTime/Access)**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)


###Empacotamento dinâmico

Os Serviços de Mídia fornecem empacotamento dinâmico, que permite a você distribuir o conteúdo de taxa de bits adaptável MP4 ou Smooth Streaming codificado em formatos de streaming suportados pelo Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) sem a necessidade de empacotar novamente nesses formatos de fluxo contínuo. 

Para aproveitar os benefícios do empacotamento dinâmico, você precisa fazer o seguinte:

- Codifique seu arquivo mezzanine (de origem) em um conjunto de arquivos MP4 de taxa de bits adaptável ou arquivos Smooth Streaming de taxa de bits adaptável,
- Obter pelo menos uma unidade de streaming sob demanda para o ponto de extremidade de streaming por meio do qual você planeja fornecer seu conteúdo. Para obter mais informações, consulte [Como dimensionar unidades reservadas para Streaming sob demanda](media-services-manage-origins.md#scale_streaming_endpoints/).

Com o empacotamento dinâmico, você só precisa armazenar e pagar pelos arquivos em um único formato de armazenamento, e os Serviços de Mídia criarão e fornecerão a resposta apropriada com base nas solicitações de um cliente. 

Observe que, além de poder usar os recursos de empacotamento dinâmico, unidades reservadas de streaming sob demanda oferecem capacidade de saída dedicada que pode ser comprada em incrementos de 200 Mbps. Por padrão, o streaming por demanda é configurado em um modelo de instância compartilhada para a qual os recursos do servidor (por exemplo, computação, capacidade de egresso etc.) são compartilhados com todos os outros usuários. Para melhorar a taxa de transferência de um streaming por demanda, é recomendável adquirir unidades reservadas para Streaming por Demanda.

###Download progressivo 

O download progressivo permite iniciar a reprodução da mídia antes do arquivo inteiro ter sido baixado. Você não pode baixar progressivamente arquivos  .ism* (ismv, isma, ismt, ismc). 

Para baixar conteúdo progressivamente, use o tipo de localizador OnDemandOrigin. O exemplo a seguir mostra a URL que é baseada no tipo de localizador OnDemandOrigin:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

A seguinte consideração é aplicável:

- É necessário descriptografar qualquer ativo criptografado em armazenamento que você deseje transmitir do serviço de origem para download progressivo.


###Baixar

Para baixar o conteúdo em um dispositivo de cliente, você deve criar um localizador SAS. O localizador SAS lhe dá acesso ao contêiner do Armazenamento do Azure em que o arquivo está localizado. Para criar a URL de download, você deve inserir o nome do arquivo entre o host e a assinatura SAS. 

O seguinte exemplo mostra a URL que se baseia no localizador SAS:

	https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

As seguintes considerações se aplicam:

- É necessário descriptografar qualquer ativo criptografado em armazenamento que você deseje transmitir do serviço de origem para download progressivo.
- Um download que não foi concluído em 12 horas falhará.



###Ponto de extremidade de streaming

Um **Ponto de Extremidade de Streaming** representa um serviço de streaming que pode entregar conteúdo diretamente a um aplicativo de player do cliente ou a uma CDN (Rede de Entrega de Conteúdo) para a distribuição posterior. O fluxo de saída de um serviço de ponto de extremidade de streaming pode ser uma transmissão ao vivo ou um ativo de vídeo sob demanda em sua conta dos Serviços de Mídia. Além disso, você pode controlar a capacidade do serviço de ponto de extremidade de streaming para lidar com necessidades crescentes de largura de banda ajustando as unidades reservadas de streaming. Você deve alocar pelo menos uma unidade reservada para aplicativos em um ambiente de produção. Para obter mais informações, consulte [Como dimensionar um serviço de mídia](media-services-manage-origins.md#scale_streaming_endpoints).

##<a id="tasks"></a>Tarefas relacionadas ao fornecimento de ativos

###Configurando a política de fornecimento de ativos

Configurar a política de fornecimento de ativos usando **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

###Publicando ativos

Publicar ativos (ao criar localizadores) usando o **Portal de Gerenciamento do Azure** ou **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


##Tópicos relacionados

[Atualizar localizadores dos Serviços de Mídia depois de implantar chaves de armazenamento](media-services-roll-storage-access-keys.md)


<!--HONumber=52--> 