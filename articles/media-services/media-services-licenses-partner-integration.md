<properties 
	pageTitle="Usando parceiros para fornecer licenças Widevine para os Serviços de Mídia do Azure" 
	description="Este artigo descreve como é possível usar os serviços de mídia do Azure (AMS) para oferecer um fluxo dinamicamente é criptografado pelo AMS com os DRMs do PlayReady e Widevine. A licença do PlayReady vem do servidor de licenças do PlayReady dos serviços de mídia e a licença do Widevine é fornecida pelo servidor de licenças do castLabs." 
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
	ms.date="10/07/2015"  
	ms.author="juliako"/>

#Usando parceiros para fornecer licenças Widevine para os Serviços de Mídia do Azure

##Visão geral

Os Serviços de Mídia do Microsoft Azure permitem o fornecimento de MPEG-DASH protegido por DRM Widevine, que é criptografado de acordo com a especificação de Criptografia Comum (CENC).

A partir da versão 3.5.2 do SDK do .NET dos Serviços de Mídia, os Serviços de Mídia permitem que você configure um modelo de licença do Widevine e obtenha licenças do Widevine. Você também pode usar os seguintes parceiros do AMS para ajudá-lo a fornecer licenças do Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) e [castLabs](http://castlabs.com/company/partners/azure/).

##castLabs

Você pode usar [castLabs](http://castlabs.com/company/partners/azure/) para fornecer licenças Widevine. Para saber mais, consulte [Usando o castLabs para fornecer licenças DRM aos Serviços de Mídia do Azure](media-services-castlabs-integration.md)

##Axinom

Você pode usar [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) para fornecer licenças Widevine. Para saber mais, consulte [Usando o Axinom para fornecer licenças DRM aos Serviços de Mídia do Azure](media-services-axinom-integration.md)


##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Consulte também

[Usando a PlayReady e/ou a criptografia comum dinâmica Widevine.](media-services-protect-with-drm.md)

[Blog do Mingfei](https://azure.microsoft.com/pt-BR/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

<!---HONumber=Nov15_HO4-->