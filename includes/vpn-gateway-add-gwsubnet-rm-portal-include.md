---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a1fa4d58cefa82e70c036d697957254531042b9c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
1. No [portal](http://portal.azure.com), navegue até a rede virtual do Gerenciador de Recursos para o qual você deseja criar um gateway de rede virtual.
2. Na seção **Configurações** da página VNet, clique em **Sub-redes** para expandir a página Sub-redes.
3. Na página **Sub-redes**, clique em **+Sub-rede de gateway** para abrir a página **Adicionar sub-rede**.

  ![Adicionar a sub-rede de gateway](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "Adicionar a sub-rede de gateway")
4. O **Nome** da sua sub-rede será automaticamente preenchido com o valor 'GatewaySubnet'. Esse valor é necessário para que o Azure reconheça a sub-rede como a sub-rede de gateway. Ajuste os valores **Intervalo de endereços** com preenchimento automático para corresponder aos seus requisitos de configuração, em seguida, clique em **OK** na parte inferior da página para criar a sub-rede.

  ![Adicionar a sub-rede](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Adicionar sub-rede")