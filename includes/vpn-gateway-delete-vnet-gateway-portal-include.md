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
ms.openlocfilehash: 5b2aa7fedbc203c50796a0e0c8d9cdb3895ae6c1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Etapa 1 – Navegar para o gateway de rede virtual

1. No [portal do Azure](https://portal.azure.com), navegue até **Todos os recursos**. 
2. Para abrir a página do gateway de rede virtual, navegue até o gateway de rede virtual que você deseja excluir e clique nele.

### <a name="step-2-delete-connections"></a>Etapa 2: Excluir conexões

1. Na páina do gateway de rede virtual, clique em **Conexões** para exibir todas as conexões com o gateway.
2. Clique em **'...'** na linha do nome da conexão e selecione **Excluir** na lista suspensa.
3. Clique em **Sim** para confirmar que você deseja excluir a conexão. Se tiver várias conexões, exclua cada uma delas.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Etapa 3: Excluir o gateway de rede virtual

Tenha em mente que se você tiver uma configuração de P2S nessa VNet além da configuração de S2S, a exclusão do gateway de rede virtual desconectará automaticamente todos os clientes de P2S sem aviso.

1. Na página do gateway de rede virtual, clique em **Visão geral**.
2. Na página **Visão geral**, clique em **Excluir** para excluir o gateway.
