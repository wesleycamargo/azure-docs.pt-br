---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b965fea3d4f166b1a801dda7cafd8e4190790c68
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45739497"
---
As opções de replicação para uma conta de armazenamento incluem:

* [LRS (armazenamento com redundância local)](../articles/storage/common/storage-redundancy-lrs.md): uma estratégia de replicação simples e de baixo custo. Os dados são replicados em uma unidade de escala de armazenamento única.
* [ZRS (armazenamento com redundância de zona)](../articles/storage/common/storage-redundancy-zrs.md): replicação para alta disponibilidade e durabilidade. Os dados são replicados de forma síncrona em todas as três zonas de disponibilidade. 
* [GRS (armazenamento com redundância geográfica)](../articles/storage/common/storage-redundancy-grs.md): replicação entre regiões para impedir a indisponibilidade de toda a região.
* [RA-GRS (armazenamento com redundância geográfica com acesso de leitura)](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage): replicação entre regiões com acesso de leitura à réplica.