---
title: "Capacidade de suporte de adição de VMs do Azure a um conjunto de disponibilidade existente | Microsoft Docs"
description: "Capacidade de suporte de adição de VMs do Azure a um conjunto de disponibilidade existente."
services: virtual-machines-linux
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 7/15/2017
ms.author: delhan
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 786c5fa4a72abac4dfcbce2c0b987ff622e96ed3
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Capacidade de suporte de adição de VMs do Azure a um conjunto de disponibilidade existente

Ocasionalmente, você poderá encontrar limitações ao adicionar novas VMs (máquinas virtuais) a um conjunto de disponibilidade existente. O gráfico a seguir fornece detalhes sobre quais séries de VM podem ser combinadas no mesmo conjunto de disponibilidade.

Esta é a matriz de capacidade de suporte para combinação de diferentes tipos de VMs:

Série e conjunto de disponibilidade|Segunda VM|O |Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Primeira VM|||||||
|O ||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Todas as outras séries não podem estar no mesmo conjunto de disponibilidade porque exigem um hardware específico.

O tamanho de VM A8/A9 não pode ser combinado devido ao requisito na rede dedicada de back-end RDMA.

