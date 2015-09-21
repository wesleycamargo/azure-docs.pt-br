<properties 
	pageTitle="Desenvolver aplicativos de player de vídeo" 
	description="O tópico fornece links para estruturas de Player e plug-ins que você pode usar para desenvolver seus próprios aplicativos de cliente que podem consumir mídia de streaming dos Serviços de Mídia." 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015" 
	ms.author="juliako"/>


#Desenvolver aplicativos de player de vídeo

##Visão geral

Os Serviços de Mídia do Azure fornecem as ferramentas necessárias para criar aplicativos de player do cliente sofisticados e dinâmicos para a maioria das plataformas, incluindo: dispositivos iOS, dispositivos Android, Windows, Windows Phone, Xbox e decodificadores de sinais. Este tópico também fornece links para SDKs e estruturas de Player que você pode usar para desenvolver seus próprios aplicativos de cliente que podem consumir mídia de streaming dos Serviços de Mídia do Azure.


##Azure Media Player

O [Azure Media Player](http://aka.ms/ampinfo) é um player de vídeo da Web criado para reproduzir conteúdo de mídia dos Serviços de Mídia do Microsoft Azure em uma grande variedade de navegadores e dispositivos. O Azure Media Player usa padrões do setor, como HTML5, MSE (Media Source Extensions) e EME (Encrypted Media Extensions) para fornecer uma experiência avançada de streaming adaptável. Quando esses padrões não estão disponíveis em um dispositivo ou em um navegador, o Azure Media Player usa Flash e Silverlight como tecnologias de fallback. Independentemente da tecnologia de reprodução usada, os desenvolvedores terão uma interface unificada do JavaScript para acessar APIs. Isso permite que o conteúdo fornecido pelos Serviços de Mídia do Azure seja executado em vários dispositivos e navegadores sem esforço adicional.

Os Serviços de Mídia do Microsoft Azure permitem que o conteúdo seja fornecido com os formatos de streaming DASH, Smooth Streaming e HLS para reprodução de conteúdo. O Azure Media Player leva em conta esses vários formatos e executa automaticamente o melhor link com base nos recursos da plataforma/navegador. Os Serviços de Mídia do Microsoft Azure também permitem a criptografia dinâmica de ativos com criptografia PlayReady ou criptografia de envelope de 128 bits AES. O Azure Media Player permite a descriptografia do conteúdo criptografado com PlayReady e AES de 128 bits, quando configurado adequadamente.

Para mais informações:

- [Azure Media Player](http://aka.ms/ampinfo)
- [Documentação do Azure Media Player](http://aka.ms/ampdocs) 
- [Blog de Introdução do Azure Media Player](http://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player)
- [Inscreva-se para se manter atualizado com a versão mais recente do Azure Media Player](http://aka.ms/ampsignup)
- [Adicionar novas solicitações de recurso, ideias, comentários](http://feedback.azure.com/forums/293565-azure-media-player/filters/my_feedback) 


##Outras ferramentas para criar aplicativos de player

Você também pode usar qualquer um dos seguintes SDKs:

- [SDK do cliente de Smooth Streaming](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [Aplicativo de Smooth Streaming da Windows Store](media-services-build-smooth-streaming-apps.md)
- [Plataforma de Mídia da Microsoft: Player Framework](http://playerframework.codeplex.com/) 
- [Documentação da estrutura de player HTML5](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [Plug-in Microsoft Smooth Streaming para OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
- [Licenciamento do kit de portabilidade de cliente do Microsoft® Smooth Streaming](https://www.microsoft.com/mediaplatform/sspk.aspx) 
- [Desenvolvimento de aplicativos de vídeo do XBOX](http://xbox.create.msdn.com/) 
 

##Publicidade

Os Serviços de Mídia do Azure dão suporte à inserção de anúncios por meio da Plataforma de Mídia do Windows: Player Frameworks. As estruturas de player com suporte a anúncios estão disponíveis para dispositivos com Windows 8, Silverlight, Windows Phone 8 e iOS. Cada estrutura de player contém código de exemplo que mostra como implementar um aplicativo de player. Há três tipos diferentes de anúncios que você pode inserir em sua mídia:

Lineares – anúncios em tela cheia que pausam o vídeo principal

Não lineares – anúncios de sobreposição que são exibidos quando o vídeo principal está sendo reproduzido, geralmente um logotipo ou outra imagem estática colocada no player.

Complementares – anúncios que são exibidos fora do player

Os anúncios podem ser inseridos em qualquer ponto na linha do tempo do vídeo principal. Você deve indicar ao player quando reproduzir o anúncio e quais anúncios devem ser reproduzidos. Isso é feito usando um conjunto de arquivos padrão baseados em XML: VAST (Video Ad Service Template), VMAP (Digital Video Multiple Ad Playlist), MAST (Media Abstract Sequencing Template) e VPAID (Digital Video Player Ad Interface Definition). Os arquivos VAST especificam quais anúncios devem ser exibidos. Os arquivos VMAP especificam quando reproduzir diversos anúncios e contêm XML VAST. Os arquivos MAST são outra maneira de sequenciar anúncios que também podem conter XML VAST. Os arquivos VPAID definem uma interface entre o player de vídeo e o anúncio ou o servidor de anúncios. Para saber mais, confira [Inserindo anúncios](https://msdn.microsoft.com/library/dn387398.aspx).

Para obter informações sobre o suporte à legendagem oculta e a anúncios em vídeos de Streaming ao vivo, confira [Padrões de legendagem oculta e inserção de anúncios compatíveis](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).


##Roteiros de aprendizagem dos Serviços de Mídia

Você pode exibir os roteiros de aprendizagem do AMS aqui:

- [Fluxo de trabalho do streaming ao vivo do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Fluxo de trabalho do streaming sob demanda do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


##Consulte também

[Inserindo um vídeo de streaming adaptável MPEG-DASH em um aplicativo HTML5 com DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[Repositório do dash.js do GitHub](https://github.com/Dash-Industry-Forum/dash.js)
 

<!---HONumber=Sept15_HO2-->