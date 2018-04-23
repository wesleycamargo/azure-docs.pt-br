---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 10f723d5298e745520c4db41b994bd2b97aaa365
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
Embora o uso de rotas do sistema facilite o tráfego automaticamente para a sua implantação, há casos em que você deseja controlar o roteamento de pacotes por meio de um dispositivo virtual. Você pode fazer isso criando rotas definidas pelo usuário que especificam o próximo salto para os pacotes que fluem para uma sub-rede específica indo, então, para o dispositivo virtual e habilitar o encaminhamento de IP para a VM em execução como o dispositivo virtual.

Alguns dos casos nos quais os dispositivos virtuais podem ser usados incluem:

* Monitorando o tráfego com um sistema de detecção de intrusões (IDS)
* Controlando o tráfego com um firewall

Para saber mais sobre UDR, visite [Rotas definidas pelo usuário e encaminhamento de IP](../articles/virtual-network/virtual-networks-udr-overview.md).

