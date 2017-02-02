---
title: Instalar o MySQL em uma VM OpenSUSE | Microsoft Docs
description: "Saiba como instalar o MySQL em uma máquina virtual Linux OpenSUSE no Azure."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/19/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: ff9e23e9324cc47ae1a5d7cb52f13920000b8557


---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Instalar MySQL em uma máquina virtual com o OpenSUSE Linux no Azure
O [MySQL][MySQL] é um popular Banco de Dados SQL de software livre. Este tutorial mostra como criar uma máquina virtual com o OpenSUSE Linux e instalar o MySQL.

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.

<br>

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Criar uma máquina virtual que executa o OpenSUSE Linux
[!INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>Instalar e executar MySQL na máquina virtual
[!INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>Próximas etapas
Para obter detalhes sobre o MySQL, consulte a [Documentação do MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com




<!--HONumber=Dec16_HO1-->


