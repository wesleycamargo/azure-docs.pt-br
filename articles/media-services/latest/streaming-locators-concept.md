---
title: Localizadores de Transmissão nos Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo fornece uma explicação sobre o que são os Localizadores de Transmissão e como eles são usados pelos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: be66dcf8115258b6f593ec913e75785a3f8dbe1f
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55743473"
---
# <a name="streaming-locators"></a>Localizadores de Streaming

Para fazer vídeos na saída do ativo disponível para clientes de reprodução, você precisa criar um [Localizador de Streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) e, em seguida, criar URLs de streaming. Para um exemplo de .NET, veja [Obter um Localizador de Streaming](stream-files-tutorial-with-api.md#get-a-streaming-locator).

O processo de criação de um **Localizador de Streaming** é chamado de publicação. Por padrão, o **Localizador de Streaming** é válido imediatamente após você fazer as chamadas à API e dura até ser excluído, a menos que você configure os horários de início e término opcionais. 

Ao criar um **Localizador de Streaming**, você precisa especificar o nome do [Ativo](https://docs.microsoft.com/rest/api/media/assets) e o nome da [Política Streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies). Você pode usar uma das Políticas de Streaming predefinidas ou criar uma política personalizada. As políticas predefinidas disponíveis no momento são: 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' e 'Predefined_MultiDrmStreaming'. Ao usar uma Política de Streaming personalizada, será necessário estruturar um conjunto limitado dessas políticas para sua conta de Serviço de Mídia e reutilizá-las em seus Localizadores de Streaming, sempre que as mesmas opções e protocolos forem necessários. 

Se você quiser especificar opções de criptografia em seu fluxo, crie a [Política de Chave de Conteúdo](https://docs.microsoft.com/rest/api/media/contentkeypolicies) que configura como a chave de conteúdo é entregue aos clientes finais por meio do componente Entrega de chave de Serviços de Mídia. Associe o Localizador de Streaming com a **Política de Chave de Conteúdo** e a chave de conteúdo. Você pode permitir que os Serviços de Mídia gerem automaticamente a chave. O exemplo de .NET a seguir mostra como configurar a criptografia AES com uma restrição de token em Serviços de Mídia v3: [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted). **Políticas de Chave de Conteúdo** serão atualizáveis, você pode desejar atualizar a política se precisar fazer uma rotação de chaves. Pode levar até 15 minutos para que os caches de entrega de Chaves atualizem e selecionem a política atualizada. É recomendável não criar uma nova Política de Chave de Conteúdo para cada localizador de Streaming. Você deve tentar reutilizar as políticas existentes sempre que as mesmas opções forem necessárias.

> [!IMPORTANT]
> * As propriedades de **Localizadores de Streaming** que são do tipo Datetime estão sempre no formato UTC.
> * Você deve criar um conjunto limitado de políticas para sua conta de serviço de mídia e reutilizá-los para os Localizadores de Streaming sempre que as mesmas opções forem necessárias. 

## <a name="filtering-ordering-paging"></a>Filtragem, classificação, paginação

Veja [Filtragem, classificação, paginação de entidades dos Serviços de Mídia](entities-overview.md).

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Carregar, codificar e transmitir vídeos usando .NET](stream-files-tutorial-with-api.md)
* [Use criptografia dinâmica DRM e serviço de entrega de licenças](protect-with-drm.md)
