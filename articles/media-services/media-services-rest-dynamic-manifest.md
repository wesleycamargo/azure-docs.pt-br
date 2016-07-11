<properties 
	pageTitle="Criando filtros com a API REST dos Serviços de Mídia do Azure" 
	description="Este tópico descreve como criar filtros para que seu cliente possa usá-los na transmissão de seções específicas de um fluxo. Os Serviços de Mídia criam manifestos dinâmicos para atingir esse streaming seletivo."
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="06/22/2016"  
	ms.author="juliako;cenkdin"/>

#Criando filtros com a API REST dos Serviços de Mídia do Azure

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-dynamic-manifest.md)
- [REST](media-services-rest-dynamic-manifest.md)


A partir da versão 2.11, os Serviços de Mídia permitem definir filtros para seus ativos. Esses filtros são regras do lado do servidor que permitirão aos clientes optar por realizar ações como: reproduzir apenas uma seção de um vídeo (em vez de reproduzir o vídeo inteiro) ou especificar apenas um subconjunto de representações de áudio e vídeo com o qual o dispositivo do cliente pode lidar (em vez de todas as representações que estão associadas ao ativo). A filtragem de ativos é arquivada por meio de **Manifestos Dinâmicos** criados mediante solicitação do cliente para transmitir um vídeo com base em filtros especificados.

Para obter mais informações relacionadas a filtros e ao Manifesto Dinâmico, consulte [Visão geral de manifestos dinâmicos](media-services-dynamic-manifest-overview.md).

Este tópico mostra como usar APIs REST para criar, atualizar e excluir filtros.

##Tipos usados para criar filtros

Os tipos a seguir são usados durante a criação de filtros:

- [Filter](http://msdn.microsoft.com/library/azure/mt149056.aspx)
- [AssetFilter](http://msdn.microsoft.com/library/azure/mt149053.aspx)
- [PresentationTimeRange](http://msdn.microsoft.com/library/azure/mt149052.aspx)
- [FilterTrackSelect e FilterTrackPropertyCondition](http://msdn.microsoft.com/library/azure/mt149055.aspx)



>[AZURE.NOTE] Ao trabalhar com a API REST dos serviços de mídia, as seguintes considerações se aplicam:
>
>Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos nas suas solicitações HTTP. Para obter mais informações, consulte [Configuração para desenvolvimento da API REST dos Serviços de Mídia](media-services-rest-how-to-use.md).

>Depois de se conectar com êxito a https://media.windows.net, você receberá um redirecionamento 301 especificando outro URI dos Serviços de Mídia. Você deve fazer chamadas subsequentes para o novo URI, conforme descrito em [Conectando-se aos Serviços de Mídia usando a API REST](media-services-rest-connect-programmatically.md).


##Criar filtros

###Criar filtros globais

Para criar um filtro global, use as seguintes solicitações HTTP:

####Solicitação HTTP

Cabeçalhos de solicitação

	POST https://media.windows.net/API/Filters HTTP/1.1 
	DataServiceVersion:3.0 
	MaxDataServiceVersion: 3.0 
	Content-Type: application/json 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host:media.windows.net 

Corpo da solicitação

	{  
	   "Name":"GlobalFilter",
	   "PresentationTimeRange":{  
	      "StartTimestamp":"0",
	      "EndTimestamp":"9223372036854775807",
	      "PresentationWindowDuration":"12000000000",
	      "LiveBackoffDuration":"0",
	      "Timescale":"10000000"
	   },
	   "Tracks":[  
	      {  
	         "PropertyConditions":
	              [  
	            {  
	               "Property":"Type",
	               "Value":"audio",
	               "Operator":"Equal"
	            },
	            {  
	               "Property":"Bitrate",
	               "Value":"0-2147483647",
	               "Operator":"Equal"
	            }
	         ]
	      }
	   ]
	}




####Resposta HTTP
	
	HTTP/1.1 201 Created 

###Criar AssetFilters local

Para criar um AssetFilter local, use as seguintes solicitações HTTP:

####Solicitação HTTP

Cabeçalhos de solicitação

	POST https://media.windows.net/API/AssetFilters HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Content-Type: application/json 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host: media.windows.net  

Corpo da solicitação

	{   
	   "Name":"AssetFilter", 
	   "ParentAssetId":"nb:cid:UUID:536e555d-1500-80c3-92dc-f1e4fdc6c592", 
	   "PresentationTimeRange":{   
	      "StartTimestamp":"0", 
	      "EndTimestamp":"9223372036854775807", 
	      "PresentationWindowDuration":"12000000000", 
	      "LiveBackoffDuration":"0", 
	      "Timescale":"10000000" 
	   }, 
	   "Tracks":[   
	      {   
	         "PropertyConditions": 
	              [   
	            {   
	               "Property":"Type", 
	               "Value":"audio", 
	               "Operator":"Equal" 
	            }, 
	            {   
	               "Property":"Bitrate", 
	               "Value":"0-2147483647", 
	               "Operator":"Equal" 
	            } 
	         ] 
	      } 
	   ] 
	} 

####Resposta HTTP 

	HTTP/1.1 201 Created 
	. . . 

##Listar filtros

###Obter todos os **filtros** globais na conta AMS

Para listar filtros, use as seguintes solicitações HTTP:

####Solicitação HTTP
	 
	GET https://media.windows.net/API/Filters HTTP/1.1 
	DataServiceVersion:3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	Host: media.windows.net 
	
### Obter **AssetFilter**s associados a um ativo

####Solicitação HTTP

	GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host: media.windows.net 

###Obter um **AssetFilter** com base em sua ID

####Solicitação HTTP

	GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000


##Atualizar filtros
 
Use o PATCH, PUT ou MERGE para atualizar um filtro com novos valores de propriedade. Para obter mais informações sobre essas operações, consulte [PATCH, PUT, MERGE](http://msdn.microsoft.com/library/dd541276.aspx).
 
Ao atualizar um filtro, talvez sejam necessários até 2 minutos para que o ponto de extremidade do streaming atualize as regras. Se o conteúdo foi servido usando esse filtro (e armazenado em cache nos proxies e caches CDN), atualizar esse filtro pode resultar em falhas do player. É recomendável limpar o cache depois de atualizar o filtro. Se essa opção não for possível, considere usar um filtro diferente.
 
###Atualizar filtros globais

Para atualizar um filtro global, use as seguintes solicitações HTTP:

####Solicitação HTTP
 
Cabeçalhos de solicitação:

	MERGE https://media.windows.net/API/Filters('filterName') HTTP/1.1 
	DataServiceVersion:3.0 
	MaxDataServiceVersion: 3.0 
	Content-Type: application/json 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host: media.windows.net 
	Content-Length: 384
	
Corpo da solicitação:
	
	{ 
	   "Tracks":[   
	      {   
	         "PropertyConditions": 
	         [   
	            {   
	               "Property":"Type", 
	               "Value":"audio", 
	               "Operator":"Equal" 
	            }, 
	            {   
	               "Property":"Bitrate", 
	               "Value":"0-2147483647", 
	               "Operator":"Equal" 
	            } 
	         ] 
	      } 
	   ] 
	} 

###Atualizar AssetFilters local

Para atualizar um filtro local, use as seguintes solicitações HTTP:

####Solicitação HTTP

Cabeçalhos de solicitação:

	MERGE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter')  HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Content-Type: application/json 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
	Host: media.windows.net 
	
Corpo da solicitação:
	
	{ 
	   "Tracks":[   
	      {   
	         "PropertyConditions": 
	         [   
	            {   
	               "Property":"Type", 
	               "Value":"audio", 
	               "Operator":"Equal" 
	            }, 
	            {   
	               "Property":"Bitrate", 
	               "Value":"0-2147483647", 
	               "Operator":"Equal" 
	            } 
	         ] 
	      } 
	   ] 
	} 


##Excluir filtros


###Excluir filtros globais

Para excluir um filtro global, use as seguintes solicitações HTTP:
	
####Solicitação HTTP

	DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
	DataServiceVersion:3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	Host: media.windows.net 


###Excluir AssetFilters local

Para excluir um AssetFilter local, use as seguintes solicitações HTTP:

####Solicitação HTTP

	DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
	DataServiceVersion: 3.0 
	MaxDataServiceVersion: 3.0 
	Accept: application/json 
	Accept-Charset: UTF-8 
	Authorization: Bearer <token value> 
	x-ms-version: 2.11 
	Host: media.windows.net 

##Criar URLs de streaming que usam filtros

Para obter informações sobre como publicar e distribuir seus ativos, consulte [Visão geral do fornecimento de conteúdo a clientes](media-services-deliver-content-overview.md).


Os exemplos a seguir mostram como adicionar filtros às URLs de streaming.

**MPEG DASH**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Streaming Suave**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


**HDS**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f, filter=MyFilter)


##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Consulte também 

[Visão geral de manifestos dinâmicos](media-services-dynamic-manifest-overview.md)
 

 

<!---HONumber=AcomDC_0629_2016-->