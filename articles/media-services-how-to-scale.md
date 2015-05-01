<properties 
	pageTitle="Como dimensionar um Serviço de Mídia" 
	description="Saiba como dimensionar os Serviços de Mídia especificando o número de Unidades Reservadas para Streaming por Demanda e Unidades Reservadas para Codificação com as quais você deseja provisionar sua conta." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="juliako"/>


# Como dimensionar um Serviço de Mídia  

## Visão geral

Você pode dimensionar os **Serviços de Mídia** especificando o número de **Unidades Reservadas para Streaming** e **Unidades Reservadas para Codificação** com as quais você deseja provisionar sua conta. 

Você também pode dimensionar sua conta dos Serviços de Mídia adicionando contas de armazenamento a ela. Cada conta de armazenamento é limitada a 500 TB. Para expandir o armazenamento além das limitações padrão, você pode optar por anexar diversas contas de armazenamento a uma única conta de serviços de mídia.

Este tópico fornece links para tópicos relevantes.

## <a id="streaming_endpoins"></a>Unidades Reservadas para Streaming

Para obter mais informações, consulte [Dimensionando as unidades de streaming](media-services-manage-origins.md#scale_streaming_endpoints).

## <a id="encoding_reserved_units"></a>Codificando Unidades Reservadas

Para obter informações sobre o dimensionamento de unidades de codificação, consulte os tópicos do **Portal** e do **.NET** a seguir.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../includes/media-services-selector-scale-encoding-units.md)]

Observe que as unidades reservadas são as mesmas para tarefas de Codificação e Indexação.

## <a id="storage"></a>Armazenamento de Dimensionamento

Para obter mais informações, consulte [Gerenciando Ativos de Serviços de Mídia entre Diversas Contas de Armazenamento](https://msdn.microsoft.com/library/azure/dn271889.aspx) e [Trabalhando com o Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn767951.aspx).





<!--HONumber=52-->