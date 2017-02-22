---
title: "Redefinir um gateway de VPN do Azure para restabelecer túneis IPsec | Microsoft Docs"
description: "Este artigo orienta você pela redefinição de seu Gateway de VPN do Azure para reestabelecer os túneis IPsec. O artigo se aplica a gateways de VPN tanto nos modelos de implantação clássicos quanto nos modelos de implantação do Resource Manager."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 79d77cb8-d175-4273-93ac-712d7d45b1fe
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 1c93a8900ea5fae8abe0d2d47f632a067736ac56
ms.openlocfilehash: 7aef9360ab341dd7d4932a6e9c2d8ed1d7bf1163


---
# <a name="reset-a-vpn-gateway"></a>Redefinir um Gateway de VPN

Redefinir o gateway de VPN do Azure é útil se você perde a conectividade VPN entre locais em um ou mais túneis de VPN site a site. Nessa situação, os dispositivos VPN locais estão funcionando corretamente, mas não são capazes de estabelecer túneis IPsec com os gateways de VPN do Azure. Este artigo o orienta sobre como redefinir o Gateway de VPN do Azure. 

Cada gateway de VPN do Azure é um gateway de rede virtual composto por duas instâncias de VM em execução em uma configuração de modo de espera-ativo. Quando você redefine o gateway, ele reinicializa o gateway e aplica novamente as configurações entre instalações a ele. O gateway mantém o endereço IP público que já tem. Isso significa que você não precisa atualizar a configuração do roteador VPN com um novo endereço IP público do gateway de VPN do Azure.  

Quando o comando for emitido, a instância ativa atual do gateway de VPN do Azure será reinicializada imediatamente. Haverá um breve intervalo durante o failover da instância ativa (que está sendo reinicializada) à instância em espera. O intervalo deve ser menor que um minuto.

Se a conexão não é restaurada após a primeira reinicialização, execute o mesmo comando novamente para reiniciar a segunda instância VM (o novo gateway ativo). Se as duas reinicializações são solicitadas uma após a outra, haverá um período um pouco mais longo durante o qual as duas instâncias da VM (ativas e em espera) estão sendo reinicializadas. Isso causará um intervalo maior na conectividade VPN, exigindo até 2 a 4 minutos para que as VMs concluam as reinicializações.

Após duas reinicializações, se você ainda estiver tendo problemas de conectividade entre instalações, abra uma solicitação de suporte no portal do Azure.

## <a name="before-you-begin"></a>Antes de começar
Antes de redefinir o gateway, verifique os principais itens listados abaixo para cada túnel VPN Site a Site (S2S) de IPsec. Qualquer incompatibilidade nos itens resultará na desconexão de túneis VPN S2S. Verificar e corrigir as configurações para seus gateways de VPN do Azure e locais faz com que você evite reinicializações desnecessárias e interrupções para as outras conexões de trabalho nos gateways.

Verifique os itens a seguir antes de redefinir seu gateway:

* Os VIPs (endereços IP da Internet) para o gateway de VPN do Azure e o gateway de VPN local estão configurados corretamente em ambas a políticas de VPN do Azure e de VPN local.
* A chave pré-compartilhada deve ser a mesma em ambos o gateway de VPN do Azure e o gateway de VPN local.
* Se você aplicar a configuração de IPsec/IKE específica, como criptografia, algoritmos de hash e PFS (Perfect Forward Secrecy), verifique se que o gateway de VPN do Azure e o gateway de VPN local têm as mesmas configurações.

## <a name="reset-a-vpn-gateway-using-the-azure-portal"></a>Redefinir um Gateway de VPN usando o Portal do Azure

Você pode redefinir um gateway de VPN do Resource Manager usando o Portal do Azure. Se você quiser redefinir um gateway clássico, veja as etapas [PowerShell](#resetclassic).

### <a name="resource-manager-deployment-model"></a>Modelo de implantação do Gerenciador de Recursos

1. Abra o Portal do Azure e navegue até o gateway de rede virtual do Resource Manager que você deseja redefinir.
2. Na folha do gateway de rede virtual, clique em "Redefinir".

    ![Folha Redefinir Gateway de VPN](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)

3. Na folha Redefinir, clique na ![Folha Redefinir Gateway de VPN](./media/vpn-gateway-howto-reset-gateway/reset-button.png) .


## <a name="reset-a-vpn-gateway-using-powershell"></a>Redefinir um Gateway de VPN usando o PowerShell

### <a name="resource-manager-deployment-model"></a>Modelo de implantação do Gerenciador de Recursos

Será necessário baixar a versão mais recente dos cmdlets do PowerShell. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para obter mais informações. O cmdlet do PowerShell Resource Manager para redefinir o gateway é `Reset-AzureRmVirtualNetworkGateway`. O exemplo a seguir redefine o gateway de VPN do Azure, "VNet1GW", no grupo de recursos "TestRG1".

    $gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
    Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw

### <a name="a-nameresetclassicaclassic-deployment-model"></a><a name="resetclassic"></a> Modelo de implantação clássico

Será necessário baixar a versão mais recente dos cmdlets do PowerShell. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para obter mais informações. O cmdlet do PowerShell para redefinir o gateway de VPN do Azure é `Reset-AzureVNetGateway`. O exemplo a seguir redefine o gateway de VPN do Azure para a rede virtual chamada "ContosoVNet".

    Reset-AzureVNetGateway –VnetName “ContosoVNet” 

Resultado:

    Error          :
    HttpStatusCode : OK
    Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
    Status         : Successful
    RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
    StatusCode     : OK





<!--HONumber=Feb17_HO2-->


