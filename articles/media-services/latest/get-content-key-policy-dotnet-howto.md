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
ms.date: 12/08/2018
ms.author: juliako
ms.openlocfilehash: 882f4650c0a3d558ee06c96658b779f9f0c76f76
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54322479"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Obter uma chave de assinatura da política existente

Um dos princípios de design de chave da API v3 é tornar a API mais segura. As APIs v3 não retornam segredos ou as credenciais em uma operação **Get** ou **List**. As chaves são sempre nulas, vazias ou corrigidas na resposta. Você precisa chamar um método de ação separada para obter as credenciais ou segredos. As ações separadas permitem que você defina permissões de segurança RBAC diferentes no caso de algumas APIs recuperar/exibir segredos enquanto outras APIs, não. Para saber mais sobre como gerenciar o acesso usando o RBAC, veja [Usar RBAC para gerenciar o acesso](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Os exemplos disso incluem 

* não retornando valores de ContentKey em Get de StreamingLocator, 
* não retornando as chaves de restrição no get da ContentKeyPolicy, 
* não retornando a parte da cadeia de caracteres de consulta da URL (para remover a assinatura) de URLs de entrada de HTTP de trabalhos.

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
