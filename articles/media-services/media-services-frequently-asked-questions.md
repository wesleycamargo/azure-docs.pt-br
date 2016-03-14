<properties 
	pageTitle="Perguntas frequentes" 
	description="Perguntas frequentes (FAQs)" 
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
 	ms.date="03/01/2016"  
	ms.author="juliako"/>


#Perguntas frequentes  

##Visão geral

P: Como você dimensiona indexação?

R: As unidades reservadas são as mesmas para tarefas de codificação e indexação. Siga as instruções em [Como dimensionar unidades reservadas de codificação](media-services-how-to-scale.md). **Observação** o desempenho do indexador não é afetado pelo tipo de unidade reservada.

P: Carreguei, codifiquei e publiquei um vídeo. Qual seria o motivo pelo qual o vídeo não é reproduzido quando tento transmiti-lo?

R: Um dos motivos mais comuns é que você não tem pelo menos uma unidade de streaming reservada alocada no ponto de extremidade de streaming do qual está tentando reproduzir. Siga as instruções em [Como dimensionar unidades reservadas de streaming](media-services-how-to-scale.md).

P: Posso fazer a composição em um fluxo ao vivo?

R: A composição de fluxos ao vivo atualmente não é oferecida nos Serviços de Mídia do Azure. Portanto, seria necessário realizar a pré-composição em seu computador.

P: Pode usar o CDN do Azure com a transmissão ao vivo?

R: Os Serviços de Mídia dão suporte à integração com o Azure CDN (para obter mais informações, consulte [Como gerenciar pontos de extremidade de streaming em uma conta dos Serviços de Mídia](media-services-manage-origins.md#enable_cdn)). Você pode usar a transmissão ao vivo o com CDN. Os Serviços de Mídia do Azure fornecem saídas de Smooth Streaming, HLS e MPEG-DASH. Todos esses formatos usam HTTP para transferir dados e obtêm os benefícios do cache de HTTP. Na transmissão ao vivo real, os dados de áudio/vídeo são divididos em fragmentos, e esses fragmentos individuais são armazenados em cache na CDN. Os únicos dados que precisam ser atualizados são os dados de manifesto. A CDN atualiza periodicamente os dados de manifesto.

P: Os Serviços de Mídia do Azure dão suporte ao armazenamento de imagens?

R: Se quiser apenas armazenar imagens JPEG ou PNG, você deverá mantê-las no Armazenamento de Blob do Azure. Não há benefício em colocá-las em sua conta dos Serviços de Mídia, a menos que deseje mantê-las associadas a seus ativos de vídeo ou áudio. Ou então, se talvez você precisar usar as imagens como sobreposições no codificador de vídeo. O codificador dos Serviços de Mídia dá suporte à sobreposição de imagens sobre vídeos, e é por isso que ele lista JPEG e PNG como formatos de entrada com suporte. Para obter mais informações, consulte [Criando sobreposições](https://msdn.microsoft.com/library/azure/dn640496.aspx).

P: Como posso copiar ativos de uma conta de serviços de mídia para outra.

R: Para copiar ativos de uma conta de serviços de mídia para outra, use o método de extensão [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) disponível no repositório de [Extensões do SDK do .NET dos Serviços de Mídia do Azure](https://github.com/Azure/azure-sdk-for-media-services-extensions/). Para obter mais informações, consulte o thread [deste](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) fórum.

P: Como é possível girar um vídeo durante o processo de codificação.

R: o [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) dá suporte à rotação em ângulos de 90/180/270. O comportamento padrão é "Auto", em que ele tentará detectar os metadados de rotação no arquivo MP4/MOV de entrada e compensá-lo. Inclua o seguinte elemento de **Fontes** em uma das predefinições de json definidas [aqui](http://msdn.microsoft.com/library/azure/mt269960.aspx):
	
	"Version": 1.0,
	"Sources": [
	{
	  "Streams": [],
	  "Filters": {
	    "Rotation": "90"
	  }
	}
	],
	"Codecs": [
	
	...

##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0302_2016-->