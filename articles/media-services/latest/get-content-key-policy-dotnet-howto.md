---
title: Obter uma chave de assinatura da política existente usando o SDK dos Serviços de Mídia v3 .NET – Azure | Microsoft Docs
description: Este tópico mostra como obter uma chave de assinatura da política existente usando o SDK do .NET do Serviços de Mídia v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 04/09/2019
ms.author: juliako
ms.openlocfilehash: 49cc2b8c151053377f8f1da0792f10a06695b332
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471164"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Obter uma chave de assinatura da política existente

Um dos princípios de design de chave da API v3 é tornar a API mais segura. APIs v3 não retornar segredos ou as credenciais na **Obtenha** ou **lista** operações. As chaves são sempre nulas, vazias ou corrigidas na resposta. O usuário precisa chamar um método de ação separada para obter as credenciais ou segredos. O **leitor** função não é possível chamar as operações para que ele não é possível chamar operações como Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Ter ações separadas permite que você defina permissões mais granulares de segurança RBAC em uma função personalizada se desejado.

Para obter mais informações, consulte [contas RBAC e os serviços de mídia](rbac-overview.md)

O exemplo neste artigo mostra como usar o .NET para obter uma chave de assinatura da política existente. 
 
## <a name="download"></a>Baixar 

Clone um repositório do GitHub que contém o exemplo do .NET Core completo em seu computador usando o comando a seguir:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
O ContentKeyPolicy com exemplo de segredos está localizado na pasta [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM).

## <a name="get-contentkeypolicy-with-secrets"></a>Obter ContentKeyPolicy com segredos 

Para obter a chave, use **GetPolicyPropertiesWithSecretsAsync**, conforme mostrado no exemplo a seguir.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Próximas etapas

[Criação de um sistema de proteção de conteúdo de DRM múltiplo com controle de acesso](design-multi-drm-system-with-access-control.md) 
