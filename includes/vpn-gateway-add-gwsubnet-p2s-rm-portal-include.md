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
ms.openlocfilehash: bc1e2803a420b95e1abec0886733c5e42a8cfdb4
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50026581"
---
1. No [portal](http://portal.azure.com), navegue até a rede virtual do Gerenciador de Recursos para o qual você deseja criar um gateway de rede virtual.
2. Na seção **Configurações** da página VNet, clique em **Sub-redes** para expandir a página **Sub-redes**.
3. Na página **Sub-redes**, clique em **+Sub-rede de gateway** para abrir a página **Adicionar sub-rede**. 

  ![Adicionar a sub-rede de gateway](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "Adicionar a sub-rede de gateway")
4. O **Nome** da sua sub-rede será automaticamente preenchido com o valor 'GatewaySubnet'. Esse valor é necessário para que o Azure reconheça a sub-rede como a sub-rede de gateway. Ajuste os valores preenchidos automaticamente de **Intervalo de endereços** para corresponder aos seus requisitos de configuração. Não configure a tabela de rotas ou pontos de extremidade de serviço.

  ![Adicionar a sub-rede](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "Adicionar sub-rede")
5.  Clique em **Okey** na parte inferior da página para criar a sub-rede.