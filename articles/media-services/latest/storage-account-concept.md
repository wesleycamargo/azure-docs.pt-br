---
title: Carregamento e armazenamento na nuvem com Serviços de Mídia do Microsoft Azure | Microsoft Docs
description: Este artigo aborda conceitos de carregamento e armazenamento na nuvem.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/21/2019
ms.author: juliako
ms.openlocfilehash: cda029dd11e8cb4cb07e9fce7eef95d6d4d78d7e
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960201"
---
# <a name="cloud-upload-and-storage"></a>Upload e armazenamento na nuvem

Para iniciar o gerenciamento, a criptografia, a codificação, a análise e a transmissão de conteúdo de mídia no Azure, você precisa criar uma conta nos Serviços de Mídia. Ao criar uma conta dos Serviços de Mídia, você precisa fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está conectada à sua conta de Serviços de Mídia. 

A conta dos Serviços de Mídia e todas as contas de armazenamento associadas precisam estar na mesma assinatura do Azure. Recomenda-se usar as contas de armazenamento no mesmo local da conta de Serviços de Mídia para evitar os custos adicionais da saída de dados e latência.

Você deve ter uma conta de armazenamento **Primária**, e pode ter quantas contas de armazenamento **Secundárias** você quiser associadas à sua conta dos Serviços de Mídia. Os Serviços de Mídia oferecem suporte a contas **v2 para fins gerais** (GPv2) ou contas **v1 para fins gerais** (GPv1). 

>[!NOTE]
> As contas somente blob não são permitidas como **Primárias**. 

É recomendável que você use GPv2, para que você possa aproveitar a vantagem de escolher entre camadas de armazenamento frequente e esporádico. Para saber mais sobre as contas de armazenamento, consulte [Visão geral da conta de Armazenamento do Microsoft Azure](../../storage/common/storage-account-overview.md). 

Há diferentes SKUs que você pode escolher para sua conta de armazenamento. Para obter mais informações, confira [Contas de armazenamento](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Caso deseje fazer experimentos com contas de armazenamento, use `--sku Standard_LRS`. No entanto, ao escolher um SKU para produção, você deverá considerar `--sku Standard_RAGRS`, que fornece replicação geográfica para a continuidade dos negócios. 

## <a name="assets-in-a-storage-account"></a>Ativos em uma conta de armazenamento

Em serviços de mídia v3, as APIs de armazenamento são usadas para carregar arquivos em ativos. Para obter mais informações, consulte [conceito de ativos](assets-concept.md).

> [!Note]
> Você não deve tentar alterar o conteúdo de contêineres de blob que foram gerados pelo SDK dos Serviços de Mídia sem o uso de APIs de Serviços de Mídia.
 
## <a name="next-steps"></a>Próximas etapas

Para saber como anexar uma conta de armazenamento para sua conta de Serviços de Mídia, consulte [Criar uma conta](create-account-cli-quickstart.md).
