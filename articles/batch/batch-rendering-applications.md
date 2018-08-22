---
title: Aplicativos de renderização de Lote
description: Aplicativos pré-instalados de renderização de Lote
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 28acd1b7275694d38a52f14d2b2c32b79cc8183e
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2018
ms.locfileid: "40036702"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Aplicativos pré-instalados de renderização de imagens da VM

É possível usar qualquer aplicativo de renderização com o Lote do Azure. No entanto, as imagens de VM do Azure Marketplace estão disponíveis com aplicativos pré-instalados comuns.

Onde aplicável, o licenciamento de pagamento por uso está disponível para os aplicativos pré-instalados de renderização. Quando um pool do Lote é criado, os aplicativos necessários podem ser especificados e o custo da VM e dos aplicativos será cobrado por minuto. Os preços dos aplicativos estão listados na [Página de preços do Lote do Azure](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Alguns aplicativos dão suporte apenas para Windows, mas a maioria tem suporte no Windows e Linux.

## <a name="applications-on-centos-7-rendering-nodes"></a>Aplicativos nos nós de renderização do CentOS 7

* E/S de Autodesk Maya 2017 Atualização 5 (recortar 201708032230)
* Autodesk Maya I/O 2018 Atualização 2 (recortar 201711281015)
* Autodesk Arnold for Maya 2017 (versão Arnold 5.0.1.1) MtoA 2.0.1.1 2017
* Autodesk Arnold for Maya 2018 (versão Arnold 5.0.1.4) MtoA-2.1.0.3-2018
* Chaos Group V-Ray for Maya 2017 (versão 3.60.04)
* Chaos Group V-Ray for Maya 2018 (versão 3.60.04)
* Blender (2.68)

## <a name="applications-on-windows-server-2016-rendering-nodes"></a>Aplicativos nos nós de renderização do Windows Server 2016

* E/S de Autodesk Maya 2017 Atualização 5 (versão 17.4.5459)
* Autodesk Maya I/O 2018 Atualização 3 (versão 18.3.0.7040)  
* Autodesk 3ds Max I/O 2019 Atualização 1 (versão 21.10.1314)
* E/S de Autodesk 3ds Max 2018 Atualização 4 (versão 20.4.0.4254)
* Autodesk Arnold for Maya 5.0.1.1 (versão Arnold 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold for Maya 5.0.1.1 (versão Arnold 5.0.1.4) MtoA-2.0.2.3-2018
* Autodesk Arnold for 3ds Max (Arnold versão 5.0.2.4)(versão 1.2.926)
* Chaos Group V-Ray for Maya 3.52.03 (versão 3.52.03)
* Chaos Group V-Ray for Maya 3.52.03 (versão 3.60.02)
* Blender (2.79)

## <a name="next-steps"></a>Próximas etapas

Para usar as imagens de VM de renderização, é necessário que sejam especificadas na configuração do pool quando um pool for criado, consulte [Recursos de pool do Lote para renderização](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).