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
ms.topic: article
ms.date: 7/15/2017
ms.author: delhan
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: f07108f5d7a792dcc9aae8a4fe889878db168bc6
ms.contentlocale: pt-br
ms.lasthandoff: 07/21/2017

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
