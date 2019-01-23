---
title: Contas de armazenamento nos Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo descreve como os Serviços de Mídia usam contas de armazenamento.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/15/2019
ms.author: juliako
ms.openlocfilehash: f5fc1385bff92851db81cd4ed397d66cb8a832f2
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54350924"
---
# <a name="storage-accounts"></a>Contas de armazenamento

Ao criar uma conta dos Serviços de Mídia, você precisa fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está conectada à sua conta de Serviços de Mídia. A conta de Serviços de Mídia e a conta de armazenamento associada a ela devem ser parte do mesmo datacenter e do mesmo grupo de recursos.

Você deve ter uma conta de armazenamento **Primária**, e pode ter quantas contas de armazenamento **Secundárias** você quiser associadas à sua conta dos Serviços de Mídia. Os Serviços de Mídia oferecem suporte a contas **v2 para fins gerais** (GPv2) ou contas **v1 para fins gerais** (GPv1). 

>[!NOTE]
> As contas somente blob não são permitidas como **Primárias**. 

É recomendável que você use GPv2, para que você possa aproveitar a vantagem de escolher entre camadas de armazenamento frequente e esporádico. Para saber mais sobre as contas de armazenamento, consulte [Visão geral da conta de Armazenamento do Microsoft Azure](../../storage/common/storage-account-overview.md). 

## <a name="assets-in-a-storage-account"></a>Ativos em uma conta de armazenamento

Nos Serviços de Mídia v3, as APIs de Armazenamento são usadas para carregar arquivos. Para saber como usar APIs de Armazenamento com os Serviços de Mídia para carregar os arquivos de entrada, confira [Criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md). 

> [!Note]
> Você não deve tentar alterar o conteúdo de contêineres de blob que foram gerados pelo SDK dos Serviços de Mídia sem o uso de APIs de Serviços de Mídia.

## <a name="next-steps"></a>Próximas etapas

Para saber como anexar uma conta de armazenamento para sua conta de Serviços de Mídia, consulte [Criar uma conta](create-account-cli-quickstart.md).
