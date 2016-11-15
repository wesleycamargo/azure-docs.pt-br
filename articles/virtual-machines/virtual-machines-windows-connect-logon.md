---
title: Conectar uma VM do Windows Server | Microsoft Docs
description: "Saiba como se conectar e fazer logon em uma VM do Windows usando o portal do Azure e o modelo de implantação do Gerenciador de Recursos."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: db417fb72442ea8a5cd4ef882eb657b08bebaa0a


---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows
Você usará o botão **Conectar** no portal do Azure para iniciar uma sessão da Área de Trabalho Remota (RDP). Primeiro, conecte-se à máquina virtual, em seguida, faça logon.

## <a name="connect-to-the-virtual-machine"></a>Conectar-se à máquina virtual
1. Se ainda não tiver feito isso, entre no [portal do Azure](https://portal.azure.com/).
2. No menu Hub, clique em **Máquinas Virtuais**.
3. Selecione a máquina virtual na lista.
4. Na folha da máquina virtual, clique em **Conectar**.
   
    ![Captura de tela do portal do Azure mostrando como conectar sua VM.](./media/virtual-machines-windows-connect-logon/connect.png)
   
   > [!TIP]
   > Se o botão **Conectar** no portal estiver esmaecido e você não estiver conectado ao Azure por meio de uma [ExpressRoute](../expressroute/expressroute-introduction.md) ou de uma conexão [VPN Site a Site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), será necessário criar e atribuir à VM um endereço IP público antes de usar o RDP. Leia mais sobre [endereços IP públicos no Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="log-on-to-the-virtual-machine"></a>Faça logon na máquina virtual
[!INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Próximas etapas
Se você tiver problemas ao tentar se conectar, consulte [Solucionar Problemas de conexões da Área de Trabalho Remota](virtual-machines-windows-troubleshoot-rdp-connection.md). Este artigo orienta você no diagnóstico e na solução de problemas comuns.




<!--HONumber=Nov16_HO2-->


