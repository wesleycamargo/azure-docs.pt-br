---
title: Visão geral dos discos gerenciados do Armazenamento em Disco do Azure para VMs do Linux | Microsoft Docs
description: Visão geral dos discos gerenciados do Azure, que lida com as contas de armazenamento ao usar VMs do Linux
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/11/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d8394121019338b53c87f7229d0ccf52fbdf8f21
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327020"
---
# <a name="introduction-to-azure-managed-disks"></a>Introdução aos discos gerenciados do Azure

Um disco gerenciado do Azure é um disco rígido virtual (VHD). Você pode pensar nisso como um disco físico em um servidor local, mas virtualizado. Os discos gerenciados do Azure são armazenados como blobs de página, que são um objeto de armazenamento de E/S aleatório no Azure. Chamamos um disco gerenciado de 'gerenciado' porque é uma abstração sobre blobs de páginas, contêineres de blob e contas de armazenamento do Azure. Com discos gerenciados, tudo o que você precisa fazer é provisionar o disco, e o Azure cuida do restante.

Quando você seleciona o uso de discos gerenciados do Azure com suas cargas de trabalho, o Azure cria e gerencia o disco para você. Os tipos disponíveis de discos são discos ultra (versão prévia), unidades de estado sólido (SSD) premium, SSD padrão e unidades de disco rígido padrão (HDD). Para saber mais sobre cada tipo de disco individual, confira [Selecionar um tipo de disco para VMs IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Selecionar um tipo de disco para VMs de IaaS](disks-types.md)
