---
title: Instalar o MySQL em uma VM OpenSUSE | Microsoft Docs
description: Saiba como instalar o MySQL em uma máquina virtual Linux OpenSUSE no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-service-management

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/19/2016
ms.author: cynthn

---
# Instalar MySQL em uma máquina virtual com o OpenSUSE Linux no Azure
O [MySQL][MySQL] é um popular banco de dados SQL de software livre. Este tutorial mostra como criar uma máquina virtual com o OpenSUSE Linux e instalar o MySQL.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

<br>

## Criar uma máquina virtual que executa o OpenSUSE Linux
[!INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## Instalar e executar MySQL na máquina virtual
[!INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## Próximas etapas
Para obter detalhes sobre o MySQL, consulte a [Documentação do MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com

<!---HONumber=AcomDC_0727_2016-->