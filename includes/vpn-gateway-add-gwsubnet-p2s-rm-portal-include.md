---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5642533fe1015e88c3b27e83139bfd26cb0b1a53
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53443968"
---
1. No [portal](http://portal.azure.com), navegue até a rede virtual do Gerenciador de Recursos para o qual você deseja criar um gateway de rede virtual.
2. Na seção **Configurações** da página VNet, clique em **Sub-redes** para expandir a página **Sub-redes**.
3. Na página **Sub-redes**, clique em **+Sub-rede de gateway** para abrir a página **Adicionar sub-rede**. 

   ![Adicionar a sub-rede de gateway](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "Adicionar a sub-rede de gateway")
4. O **Nome** da sua sub-rede será automaticamente preenchido com o valor 'GatewaySubnet'. Esse valor é necessário para que o Azure reconheça a sub-rede como a sub-rede de gateway. Ajuste os valores preenchidos automaticamente de **Intervalo de endereços** para corresponder aos seus requisitos de configuração. Não configure a tabela de rotas ou pontos de extremidade de serviço.

   ![Adicionar a sub-rede](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "Adicionar sub-rede")
5. Clique em **Okey** na parte inferior da página para criar a sub-rede.
