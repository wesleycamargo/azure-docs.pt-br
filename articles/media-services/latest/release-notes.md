---
title: Notas de versão dos Serviços de Mídia do Azure v3 | Microsoft Docs
description: Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece as atualizações mais recentes sobre os Serviços de Mídia do Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/25/2018
ms.author: juliako
ms.openlocfilehash: ed2550c1df4645933fb968c54ee536995c810136
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219308"
---
# <a name="azure-media-services-v3-preview-release-notes"></a>Notas de versão dos Serviços de Mídia do Azure v3 (versão prévia) 

Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

* As versões mais recentes
* Problemas conhecidos
* Correções de bug
* Funcionalidades preteridas
* Planos de alterações

## <a name="may-07-2018"></a>07 de maio de 2018

### <a name="net-sdk"></a>SDK .NET

Os recursos a seguir estão presentes no SDK .NET:

1. **Transformações** e **Trabalhos** para codificar ou analisar o conteúdo de mídia. Para obter exemplos, consulte [Transmitir arquivos por stream](stream-files-tutorial-with-api.md) e [Analisar](analyze-videos-tutorial-with-api.md).
2. **StreamingLocators** para publicar e transmitir conteúdo aos dispositivos do usuário final
3. **StreamingPolicies** e **ContentKeyPolicies** para configurar a distribuição de chaves e a proteção de conteúdo (DRM) durante a entrega de conteúdo.
4. **LiveEvents** e **LiveOutputs** para configurar a ingestão e arquivamento de conteúdo de transmissão ao vivo.
5. **Ativos** para armazenar e publicar o conteúdo de mídia no Armazenamento do Azure. 
6. **StreamingEndpoints** para configurar e dimensionar o empacotamento dinâmico, criptografia e streaming de conteúdo de mídia ao vivo e sob demanda.

### <a name="known-issues"></a>Problemas conhecidos

* Ao enviar um trabalho, você pode especificar para ingerir o vídeo de origem usando URLs HTTPS, URLs SAS ou caminhos para arquivos localizados no Armazenamento de Blobs do Azure. Atualmente, o AMS v3 não oferece suporte à codificação de transferência em partes sobre URLs HTTPS.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Visão geral](media-services-overview.md)
