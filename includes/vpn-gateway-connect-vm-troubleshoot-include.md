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
ms.openlocfilehash: a22cc1f1bffb0126c4fe23bd9f28b292bfdc48a8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
ms.locfileid: "30198036"
---
Se você estiver tendo problemas para se conectar a uma máquina virtual em sua conexão VPN, verifique o seguinte:

- Verifique se a conexão VPN é estabelecida.
- Verifique se você está se conectando ao endereço IP privado para a VM.
- Se você puder se conectar à VM usando o endereço IP privado, mas não o nome do computador, verifique se você configurou o DNS corretamente. Para obter mais informações sobre como funciona a resolução de nome para VMs, confira [Resolução de nomes para VMs](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Quando você se conectar via Ponto a Site, verifique os seguintes itens adicionais:

- Use 'ipconfig' para verificar o endereço IPv4 atribuído ao adaptador Ethernet no computador do qual está se conectando. Se o endereço IP está dentro do intervalo de endereços da VNet a que você está se conectando ou dentro do intervalo de endereços de seu VPNClientAddressPool, isso é chamado de espaço de endereço sobreposto. Quando o espaço de endereço se sobrepõe dessa forma, o tráfego de rede não alcança o Azure; ele permanece na rede local.
- Verifique se o pacote de configuração de cliente VPN foi gerado depois que os endereços IP do servidor DNS foram especificados para a rede virtual. Se você atualizou os endereços IP do servidor DNS, gere e instale um novo pacote de configuração de cliente VPN.

Para obter mais informações sobre como solucionar problemas de conexões RDP, confira [Solucionar problemas de conexões da Área de Trabalho Remota a uma VM](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).