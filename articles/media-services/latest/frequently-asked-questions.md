---
title: Perguntas frequentes sobre os Serviços de Mídia do Azure v3 | Microsoft Docs
description: Este artigo fornece respostas às perguntas frequentes sobre os Serviços de Mídia do Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/05/2019
ms.author: juliako
ms.openlocfilehash: be4c08bc31c8811655230ab89b48271f4c2b3164
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756573"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Perguntas frequentes sobre os serviços de mídia do Azure v3

Este artigo fornece respostas às perguntas frequentes sobre os Serviços de Mídia do Azure (AMS) v3.

## <a name="v3-apis"></a>APIs v3

### <a name="how-do-i-configure-media-reserved-units"></a>Como fazer para configurar Unidades Reservadas para Mídia?

Para os trabalhos de análise de áudio e análise de vídeo acionados pelo Serviços de Mídia do Microsoft Azure v3 ou pelo Video Indexer, é altamente recomendável provisionar sua conta com 10 MRUs do S3. Se você precisar de mais de 10 MRUs do S3, abra um ticket de suporte usando o [Portal do Microsoft Azure](https://portal.azure.com/).

Para obter detalhes, confira [Dimensionar o processamento de mídia com a CLI](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Qual é o método recomendado para processar vídeos?

É recomendável que você envie Trabalhos usando uma URL HTTP(s) que aponte para o vídeo. Para obter mais informações, confira [Ingestão de HTTP(s)](job-input-from-http-how-to.md). Você não precisa criar um Ativo com o vídeo de entrada para que ele possa ser processado.

### <a name="how-does-pagination-work"></a>Como a paginação funciona?

Os Serviços de Mídia dão suporte para $top para recursos que dão suporte para OData, mas o valor passado para $top deve ser menor que 1000 (por exemplo, o tamanho da página para paginação).

Isso permite que você obtenha uma pequena amostra de itens usando $top (por exemplo, os 100 itens mais recentes) ou paginar todos os itens usando paginação. 

Os Serviços de Mídia não dão suporte para paginação pelos dados com um tamanho de página especificado pelo usuário.

Para obter mais informações, confira [Filtragem, classificação, paginação](entities-overview.md).

### <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>Como recuperar uma entidade nos Serviços de Mídia v3?

A v3 é baseada em uma superfície de API unificada que expõe a funcionalidade de operações e gerenciamento compilada no **Azure Resource Manager**. De acordo com o **Azure Resource Manager**, os nomes dos recursos são sempre exclusivos. Desse modo, é possível usar qualquer cadeia de caracteres de identificador exclusivo (por exemplo, GUIDs) para o recurso.

## <a name="live-streaming"></a>Transmissão ao vivo 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Como inserir quebras/vídeos e imagens fixas durante a transmissão ao vivo?

A codificação ativa dos Serviços de Mídia v3 ainda não dá suporte à inserção de vídeo ou de imagens fixas durante a transmissão ao vivo. 

Você pode usar um [codificador ativo local](recommended-on-premises-live-encoders.md) para alternar para o vídeo de origem. Muitos aplicativos fornecem a capacidade de alternar fontes, incluindo o Telestream Wirecast, o Switcher Studio (no iOS), o OBS Studio (aplicativo gratuito) e muito mais.

## <a name="media-services-v2-vs-v3"></a>Serviços de Mídia v2 comparado com v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Eu posso usar o portal do Azure para gerenciar recursos da v3?

Ainda não. Você pode usar um dos SDKs com suporte. Consulte os tutoriais e exemplos neste conjunto de documentos.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Existe um conceito de AssetFile no v3?

Os AssetFiles foram removidos da API do AMS para separar os Serviços de Mídia das dependências do SDK do Armazenamento. Agora o Armazenamento, não os Serviços de Mídia, mantém as informações que pertencem ao Armazenamento. 

Para obter mais informações, confira [Migrar para os Serviços de Mídia v3](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Onde está a criptografia de armazenamento do lado do cliente?

Agora é recomendável usar a criptografia de armazenamento do lado do servidor (que está ativada por padrão). Para obter mais informações, consulte [Criptografia de serviço do Armazenamento do Azure para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Visão geral dos Serviços de Mídia v3](media-services-overview.md)
