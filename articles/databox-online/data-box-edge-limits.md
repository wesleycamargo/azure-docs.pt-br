---
title: Limites do Azure Data Box Edge | Microsoft Docs
description: Descreve os limites do sistema e tamanhos recomendados para o Microsoft Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/05/2019
ms.author: alkohli
ms.openlocfilehash: 30e0c37d3d0c03e77b6dab9c06c0a50bff27e8bc
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966311"
---
# <a name="azure-data-box-edge-limits-preview"></a>Limites do Azure Data Box Edge (Versão Prévia)

Considere esses limites ao implantar e operar sua solução de Microsoft Azure Data Box Edge.

> [!IMPORTANT]
> O Data Box Edge está em versão prévia. Examine os [termos de uso para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de implantar essa solução.


## <a name="data-box-edge-service-limits"></a>Limites de serviço do Data Box Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-edge-device-limits"></a>Limites de dispositivo do Data Box Edge

A tabela a seguir descreve esses limites para o dispositivo do Data Box Edge.

| DESCRIÇÃO | Valor |
|---|---|
|Não. de arquivos por dispositivo |100 milhões |
| Não. de compartilhamentos por dispositivo |24 |
| Não. de compartilhamentos por contêiner |1 |
|Tamanho máximo do arquivo gravado em um compartilhamento| 5 TB |

## <a name="azure-storage-limits"></a>Limites de armazenamento do Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Limitações de upload de dados

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limites de tamanho da conta de armazenamento e tamanho do objeto do Azure

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Limites de tamanho do objeto do Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
