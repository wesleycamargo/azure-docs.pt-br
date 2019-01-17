---
title: Limites de recursos no Azure NetApp Files | Microsoft Docs
description: Descreve os limites de recursos do Azure NetApp Files, incluindo limites para pools de capacidade, volumes e a sub-rede delegada.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/03/2019
ms.author: b-juche
ms.openlocfilehash: f34afb1df2ae38353f29a80bfb6798c16856dbeb
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54056210"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limites de recursos no Azure NetApp Files
O reconhecimento dos limites de recursos no Azure NetApp Files ajuda você a gerenciar seus volumes.

## <a name="capacity_pools"></a>Pools de capacidade

- O tamanho mínimo de um pool de capacidade único é 4 TiB e o tamanho máximo é 500 TiB. 
- Cada pool de capacidade pode pertencer a apenas uma conta do NetApp. No entanto, é possível ter vários pools de capacidade dentro de uma conta do NetApp.  

## <a name="volumes"></a>Volumes

- O tamanho mínimo de um volume único é 100 GiB e o tamanho máximo é 92 TiB.
- É possível ter, no máximo, 100 volumes por assinatura do Azure por região.  

## <a name="delegated_subnet"></a>Sub-rede delegada 

Em cada Vnet (Rede virtual) do Azure, apenas uma sub-rede pode ser delegada para o Azure NetApp Files.

## <a name="next-steps"></a>Próximas etapas

[Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
