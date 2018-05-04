---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: cab04a7eafbc21e0d26cd5a287f3dbee8d3d22b7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
## <a name="scenario"></a>Cenário
Este documento orientará durante uma implantação que usa várias NICs em máquinas virtuais em um cenário específico. Nesse cenário, você tem uma carga de trabalho de IaaS em duas camadas hospedadas no Azure. Cada camada é implantada na sua própria sub-rede em uma rede virtual (VNet). A camada de front-end é composta de vários servidores da Web, agrupados em um balanceador de carga definida para alta disponibilidade. A camada de back-end é composta de vários servidores de banco de dados. Esses servidores de banco de dados serão implantados com duas NICs cada, uma para acesso de banco de dados, outra para gerenciamento. O cenário também inclui grupos de segurança de rede (NSGs) para controlar o que o tráfego seja permitido para cada sub-rede e NIC na implantação. A figura a seguir mostra a arquitetura básica desse cenário:

![Cenário de MultiNIC](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

