---
title: Limites de recursos no Azure NetApp Files | Microsoft Docs
description: Descreve os limites de recursos do Azure NetApp Files, incluindo limites para contas do NetApp, pools de capacidade, volumes, instantâneos e a sub-rede delegada.
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
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: 897ca26bcbb05287d33a4fb8e731ca959e39e271
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60452629"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limites de recursos no Azure NetApp Files

O reconhecimento dos limites de recursos no Azure NetApp Files ajuda você a gerenciar seus volumes.

- Cada assinatura do Azure pode ter no máximo 10 contas do NetApp.
- Cada conta do NetApp pode ter no máximo 25 pools de capacidade.
- Cada pool de capacidade pode pertencer a apenas uma conta do NetApp.  
- O tamanho mínimo de um pool de capacidade único é 4 TiB e o tamanho máximo é 500 TiB. 
- Cada pool de capacidade pode ter no máximo 500 volumes.
- O tamanho mínimo de um volume único é 100 GiB e o tamanho máximo é 92 TiB.
- Cada volume pode ter no máximo 255 instantâneos.
- Cada Rede Virtual do Azure (Vnet) pode ter apenas uma sub-rede delegada ao Azure NetApp Files.

**Próximas etapas**

[Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
