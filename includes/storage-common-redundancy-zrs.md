---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4a43966180850645584043690b1be9d6ae232f6e
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027440"
---
O armazenamento com redundância de zona (ZRS) replica seus dados de forma síncrona em três clusters de armazenamento em uma única região. Cada cluster de armazenamento é fisicamente separado dos outros e reside em sua própria zona de disponibilidade (AZ). Cada zona de disponibilidade e o cluster ZRS dentro dele, é autônomo, com recursos de rede e utilitários separados.

Armazenar dados em uma conta do ZRS assegura que você é capaz de acessar e gerenciar seus dados no caso de uma região ficar indisponível. O ZRS oferece excelente desempenho e baixa latência. O ZRS oferece as mesmas [metas de escalabilidade](../articles/storage/common/storage-scalability-targets.md) como [armazenamento localmente redundante (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Considere o ZRS para cenários que exigem consistência forte, forte durabilidade e alta disponibilidade, mesmo se uma interrupção ou desastre natural renderiza um zonal data center não estiver disponível. O ZRS oferece durabilidade para objetos de armazenamento de, pelo menos, 99,9999999999% (doze noves) em um ano específico.

Para obter informações sobre zonas de disponibilidade, consulte [visão geral de Zonas de Disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).