---
title: "Solução de problemas na conexão VPN Site a Site do Azure | Microsoft Docs"
description: "Saiba como solucionar problemas quando a conexão VPN Site a Site para repentinamente e não pode mais ser reconectada."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/21/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: a72a46e4c2c93fdb3ab1a26c8854adb8cdc488a5
ms.contentlocale: pt-br
ms.lasthandoff: 06/22/2017

---

# <a name="troubleshooting-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Solução de problemas: a conexão VPN Site a Site do Azure não pode se conectar e para de funcionar

Você configura a conexão VPN Site a Site entre a rede local e uma rede virtual do Microsoft Azure. A conexão VPN para de funcionar repentinamente e não pode ser reconectada. Este artigo fornece as etapas para a solução de problemas de modo a ajudá-lo a resolver esse problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

Para resolver o problema tente, primeiro, [redefinir o Gateway de rede virtual do Azure ](vpn-gateway-resetgw-classic.md) e redefinir o Túnel a partir do dispositivo VPN local. Se o problema persistir, siga essas etapas para identificar a causa do problema.

### <a name="prerequisite-step"></a>Etapa de pré-requisito

Verifique o tipo de gateway de rede virtualdo  Azure:

1. Vá para o [portal do Azure](https://portal.azure.com).
2. Verifique a página **Visão Geral** do gateway de rede virtual para os tipos de informações.
    
    ![Visão geral do gateway](media\vpn-gateway-troubleshoot-site-to-site-cannot-connect\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Etapa 1 Verifique se o dispositivo VPN local está validado

1. Verifique se você está usando um [dispositivo VPN validado e a versão do SO](vpn-gateway-about-vpn-devices.md#a-namedevicetableavalidated-vpn-devices-and-device-configuration-guides). Se o dispositivo não for um dispositivo VPN validado, talvez seja necessário contatar o fabricante do dispositivo para verificar se há algum problema de compatibilidade.
2. Certifique-se de que o dispositivo VPN esteja configurado corretamente. Para obter mais informações, consulte [Editar exemplos de configuração de dispositivo](/vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-keypsk"></a>Etapa 2 Verifique a PSK (Chave Compartilhada)

Compare a **Chave Compartilhada** do dispositivo VPN local e o VPN de rede virtual para certificar-se de que as chaves correspondem. 

Para verificar a PSK para a conexão VPN do Azure, utilize um dos seguintes métodos:

**Portal do Azure**

1. Vá para gateway de rede virtual> Conexão de site a site que você criou.
2. Na seção **Configurações**, clique em **Chave Compartilhada**.
    
    ![Chave compartilhada](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**PowerShell do Azure**

Para modo do Resource Manager

    Get-AzureRmVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

Para Clássico

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Etapa 3 Verifique os IPs Pares de VPN

-   A definição do IP no objeto do **Gateway de Rede Local** no Azure deve corresponder com o IP do dispositivo local.
-   A definição de IP do Gateway do Azure que está configurada no dispositivo local deve corresponder com o IP de Gateway do Azure.

### <a name="step-4-nsg-and-udr-on-gateway-subnet"></a>Etapa 4 NSG e UDR na Sub-Rede de Gateway

Verifique e remova o UDR (Rotas Definidas pelo Usuário) ou NSG (Grupos de Segurança de Rede) na Sub-Rede de Gateway e, em seguida, teste o resultado. Se o problema for resolvido, valide as configurações de NSG ou UDR aplicadas.

### <a name="step-5-check-on-premises-vpn-device-external-interface-address"></a>Etapa 5 Verifique o endereço de interface externa do dispositivo VPN local

- Se o endereço IP para a Internet do dispositivo VPN estiver incluído na definição de **Rede local** no Azure, você poderá experimentar desconexões esporádicas.
- A interface externa do dispositivo deve estar diretamente na Internet. Não deve haver nenhuma Conversão de Endereços de Rede ou firewall entre a Internet e o dispositivo.
-  Se você configurar o Clustering de Firewall para ter um IP virtual, será necessário interromper o cluster e expor o dispositivo VPN diretamente a uma interface pública com a qual o gateway pode se conectar.

### <a name="step-6-verify--subnets-match-exactly-azure-policy-based-gateways"></a>Etapa 6 Verifique se as sub-redes coincidem exatamente (gateways baseados em políticas do Azure)

-   Verifique se as sub-redes correspondem exatamente entre a Rede Virtual do Azure e as definições locais para a Rede Virtual do Azure.
-   Verifique se as sub-redes correspondem exatamente entre o **Gateway de Rede Local** e as definições para rede local.

### <a name="step-5-verify-azure-gateway-health-probe"></a>Etapa 5 Verifique a investigação de integridade do Gateway do Azure

1. Navegue até https://&lt;YourVirtualNetworkGatewayIP&gt;:8081/healthprobe
2. Clique no aviso do certificado.
3. Se você receber uma resposta, o gateway de rede virtual será considerado íntegro. Se você não receber uma resposta, o gateway talvez não esteja íntegro ou existe um NSG na sub-rede de gateway que está causando o problema. O texto a seguir é uma resposta de exemplo:

    &lt;?xml version="1.0"?>  <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string&gt;

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-pfs-feature-enabled"></a>Etapa 7 Verifique se o dispositivo VPN local possui o recurso PFS (Perfect Forward Secrecy) ativado

O recurso Perfect Forward Secrecy pode causar os problemas de desconexão. Se o dispositivo VPN estiver com o recurso Perfect Forward Secrecy habilitado, desabilite-o. Em seguida, atualize a política IPsec do gateway de rede virtual.

## <a name="next-steps"></a>Próximas etapas

-   [Configurar uma conexão Site a Site para uma rede virtual](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Configure a política IPsec/IKE para conexões VPN Site a Site](vpn-gateway-ipsecikepolicy-rm-powershell.md)

