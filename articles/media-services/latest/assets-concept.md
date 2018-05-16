---
title: Ativos nos Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo fornece uma explicação sobre a natureza dos ativos e como eles são usados pelos Serviços de Mídia do Azure.
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
ms.openlocfilehash: 928d88d51503350abe7df847ce58ff066b987c8e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="assets"></a>Ativos

Um **Ativo** contém arquivos digitais (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda oculta) e os metadados sobre esses arquivos. Depois que os arquivos digitais são carregados em um ativo, eles podem ser usados nos fluxos de trabalho de codificação e streaming dos serviços de mídia.

Um ativo é mapeado para um contêiner de blob na [conta de Armazenamento do Azure](storage-account-concept.md) e os arquivos no ativo são armazenados como blobs de blocos nesse contêiner. Você pode interagir com os arquivos de Ativo nos contêineres usando os clientes do SDK de Armazenamento.

Os Serviços de Mídia do Azure oferecem suporte a camadas de Blob quando a conta usa Armazenamento de uso geral v2 (GPv2). Com o GPv2, você pode mover arquivos para o armazenamento esporádico. Armazenamento esporádico é adequado para o arquivamento de arquivos de mezanino quando eles não são mais necessários (por exemplo, após a codificação.)

Nos Serviços de Mídia v3, a entrada do trabalho pode ser criada de ativos ou de URLs HTTP(s). Para criar um ativo que possa ser usado como entrada para o trabalho, consulte [Criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md).

Além disso, leia sobre [contas de armazenamento nos Serviços de Mídia](storage-account-concept.md) e [transformações e trabalhos](transform-concept.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
