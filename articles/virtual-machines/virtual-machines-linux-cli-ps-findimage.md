---
title: Selecionar imagens da VM do Linux com a CLI do Azure | Microsoft Docs
description: Saiba como determinar o editor, a oferta e SKU para imagens ao criar uma máquina virtual Linux com o modelo de implantação do Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: squillace
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill

---
# Selecionar imagens da VM Linux com a CLI do Azure
Este tópico descreve como localizar editores, ofertas, SKUs e versões de cada local no qual você pode implantar. Para dar um exemplo, algumas das imagens mais usadas da VM Linux são:

**Tabela de imagens do Linux usadas frequentemente**

| PublisherName | Oferta | Sku |
|:--- |:--- |:--- |:--- |
| RedHat |RHEL |7,2 |
| credativ |Debian |8 |
| SUSE |openSUSE |13\.2 |
| SUSE |SLES |12-SP1 |
| OpenLogic |CentOS |7\.1 |
| Canonical |UbuntuServer |14\.04.4-LTS |
| CoreOS |CoreOS |Estável |

[!INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]

<!---HONumber=AcomDC_0824_2016-->