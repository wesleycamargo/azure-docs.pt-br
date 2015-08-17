<properties 
	pageTitle="Visão geral dos Serviços de Mídia do Azure" 
	description="Este tópico oferece uma visão geral dos Serviços de Mídia do Azure" 
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
	ms.date="05/26/2015" 
	ms.author="juliako"/>

#Visão geral dos Serviços de Mídia do Azure

Os Serviços de Mídia do Microsoft Azure tratam-se de uma plataforma extensível baseada em nuvem que permite aos desenvolvedores compilar aplicativos de gerenciamento e entrega de mídia escalonável. Os serviços de mídia se baseiam em APIs REST que permitem que você carregue com segurança, armazene, codifique e empacote o conteúdo de áudio ou vídeo para entrega de streaming sob demanda e ao vivo para vários clientes (por exemplo, TV, PCs e dispositivos móveis).

Você pode compilar fluxos de trabalho de ponta a ponta usando totalmente os serviços de mídia. Você também pode optar por usar componentes de terceiros para algumas partes do seu fluxo de trabalho. Por exemplo, codifique usando um codificador de terceiros. Em seguida, carregue, proteja, empacote e entregue usando os serviços de mídia.

Para compilar soluções de serviços de mídia, você pode usar:

- [API REST dos Serviços de Mídia](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Um dos SDKs de cliente disponíveis: [SDK dos Serviços de Mídia do Azure para .NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure SDK para Java](https://github.com/Azure/azure-sdk-for-java), [Serviços de Mídia do Azure para Node.js](https://github.com/fritzy/node-azure-media), [SDK do PHP do Azure](https://github.com/Azure/azure-sdk-for-php)
- Ferramentas existentes: [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/) ou [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer).


Pôster do seguinte descreve os fluxos de trabalho dos Serviços de Mídia do Azure, desde a criação de mídia por meio de consumo. Você pode baixar o pôster aqui: [Pôster do Mídia Services do Azure](http://www.microsoft.com/download/details.aspx?id=38195).

![Visão geral][overview]

**Contrato de nível de serviço (SLA)**:

- Para a Codificação dos Serviços de mídia, garantimos a disponibilidade de 99,9% de transações de API REST.
- Para streaming, atenderemos com êxito a solicitações com uma garantia de disponibilidade de 99,9% para conteúdos de mídia existentes em casos que pelo menos uma unidade para streaming é adquirida.
- Para canais ao vivo, garantimos que os canais em execução terão conectividade externa em, no mínimo, 99,9% do tempo.
- Para proteção de conteúdo, garantimos que atenderemos com êxito a solicitações de chave em, no mínimo, 99,9% do tempo.
- Para o indexador, podemos atenderemos com êxito às solicitações de tarefa do indexador processadas com uma unidade reservada para codificação em 99,9% do tempo.

	Para obter mais informações, consulte [SLA do Microsoft Azure](http://azure.microsoft.com/support/legal/sla/).

##Conceitos

Para obter mais informações, consulte [Conceitos](media-services-concepts.md).


##Fornecendo Mídia sob Demanda com os Serviços de Mídia do Azure

O tópico a seguir descreve as etapas de fluxos de trabalho de serviços de mídia comuns de vídeo sob demanda. Os tópico fornecem links para outros tópicos que mostram como realizar essas etapas usando tecnologias com suporte pelos serviços de mídia.

[Fornecendo Mídia sob Demanda com os Serviços de Mídia do Azure](media-services-video-on-demand-workflow.md)

##Fornecendo mídia sob demanda com os Serviços de Mídia do Azure

O tópico a seguir descreve as etapas de fluxos de trabalho comuns à transmissão ao vivo do Media Services. Os tópico fornecem links para outros tópicos que mostram como realizar essas etapas usando tecnologias com suporte pelos serviços de mídia.

[Fornecendo mídia sob demanda com os Serviços de Mídia do Azure](media-services-live-streaming-workflow.md)

##Consumo de conteúdo

Os Serviços de Mídia do Azure fornecem as ferramentas necessárias para criar aplicativos de player do cliente sofisticados e dinâmicos para a maioria das plataformas, incluindo: dispositivos iOS, dispositivos Android, Windows, Windows Phone, Xbox e decodificadores de sinais. O tópico a seguir fornece links para SDKs e estruturas de Player que você pode usar para desenvolver seus próprios aplicativos de cliente que podem consumir mídia de streaming dos Serviços de Mídia.

[Desenvolvendo aplicativos de player de vídeo](media-services-develop-video-players.md)

##Padrões e práticas de orientação

[Padrões e práticas de orientação](https://wamsg.codeplex.com/) [Documentação online](https://msdn.microsoft.com/library/dn735912.aspx) [Livro eletrônico para download](https://www.microsoft.com/download/details.aspx?id=42629)

##Suporte

[Suporte do Azure](http://azure.microsoft.com/support/options/) fornece opções de suporte do Azure, incluindo serviços de mídia.

##Próximas etapas

[Fornecendo mídia sob demanda com os Serviços de Mídia do Azure](media-services-live-streaming-workflow.md)

[Desenvolvendo aplicativos de player de vídeo](media-services-develop-video-players.md)
 
[Desenvolvendo aplicativos de player de vídeo](media-services-develop-video-players.md)


<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
 

<!---HONumber=August15_HO6-->