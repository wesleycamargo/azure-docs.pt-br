---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0dcb591db5f487a103feccf92ffa577a1cbf8b23
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
1. Navegue até a página do seu gateway de rede virtual e a abra. Há várias maneiras de navegar. Você pode navegar para o gateway “VNet1GW” acessando **TestVNet1 -> Visão geral -> Dispositivos conectados -> VNet1GW**.
2. Na página do VNet1GW, clique em **Conexões**. Na parte superior da página Conexões, clique em **+ Adicionar** para abrir a página **Adicionar conexão**.

  ![Criar uma conexão site a site](./media/vpn-gateway-add-site-to-site-connection-portal-include/configure-site-to-site-connection.png)
3. Na página **Adicionar conexão**, configure os valores da sua conexão.

  - **Nome:** nomeie sua conexão.
  - **Tipo de conexão:** selecione **Site a site (IPSec)**.
  - **Gateway de rede virtual:** o valor é fixo porque você está se conectando por esse gateway.
  - **Gateway de rede local:** clique em **Escolher um gateway de rede local** e selecione o gateway de rede local que você deseja usar.
  - **Chave Compartilhada:** o valor aqui deve corresponder ao valor usado para seu dispositivo VPN local. O exemplo usa “abc123”, mas você pode (e deve) usar algo mais complexo. O importante é que o valor especificado aqui deve ser o mesmo valor especificado ao configurar seu dispositivo VPN.
  - Os valores restantes para **Assinatura**, **Grupo de Recursos** e **Local** são corrigidos.

4. Clique em **OK** para criar sua conexão. Você verá *Criar Conexão* piscar na tela.
5. Você pode exibir a conexão na página **Conexões** do seu gateway de rede virtual. O Status será alterado de *Desconhecido* para *Conectando* e então para *Êxito*.