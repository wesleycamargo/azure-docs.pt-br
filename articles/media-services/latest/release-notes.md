---
title: Notas de versão dos Serviços de Mídia do Azure v3 | Microsoft Docs
description: Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece as atualizações mais recentes sobre os Serviços de Mídia do Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: fc6c5ba6cd97c261dd44eade33bf21e8d1b74bf0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
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

Problema conhecido:

Ao enviar um Trabalho com uma URL HTTPS (JobInputHttp) apontando para o conteúdo de origem, certifique-se de que o servidor HTTP dá suporte à solicitação “HEAD”. Caso contrário, o Trabalho será rejeitado.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Visão geral](media-services-overview.md)
