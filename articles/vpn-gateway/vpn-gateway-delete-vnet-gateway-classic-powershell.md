---
title: "Excluir um gateway de rede virtual: PowerShell: clássico do Azure | Microsoft Docs"
description: "Exclua um gateway de rede virtual usando o PowerShell no modelo de implantação clássico."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b5d90aa60de4a24b6d76414204ceae24670e48a3
ms.lasthandoff: 03/21/2017


---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Excluir um gateway de rede virtual usando o PowerShell (clássico)
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Clássico - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Você pode excluir um gateway de VPN no modelo de implantação clássico usando o PowerShell. Depois que o gateway de rede virtual tiver sido excluído, modifique o arquivo de configuração de rede para remover elementos que você não está mais usando.

##<a name="part-1-connect-to-azure"></a>Parte 1. Conecte-se ao Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Instale os cmdlets mais recentes do PowerShell.

Baixe e instale a versão mais recente dos cmdlets do PowerShell do SM (Gerenciamento de Serviços) do Azure. Para saber mais, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs):

### <a name="2-connect-to-your-azure-account"></a>2. Conectar-se à sua conta do Azure. 

Abra o console do PowerShell com direitos elevados e conecte-se à sua conta. Use o exemplo a seguir para ajudar a se conectar:

    Login-AzureRmAccount

Verificar as assinaturas da conta.

    Get-AzureRmSubscription

Se você tiver mais de uma assinatura, selecione a assinatura que deseja usar.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

Use o cmdlet a seguir para adicionar sua assinatura do Azure ao PowerShell para o modelo de implantação clássico.

    Add-AzureAccount

## <a name="part-2-export-and-view-the-network-configuration-file"></a>Parte 2. Exportar e exibir o arquivo de configuração de rede

Crie um diretório no seu computador e exporte o arquivo de configuração de rede para o diretório. Use esse arquivo para exibir as informações da configuração atual e também para modificar a configuração de rede.

Neste exemplo, o arquivo de configuração de rede é exportado para C:\AzureNet.

     Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml

Abra o arquivo com um editor de texto e exiba o nome da rede virtual clássica. Quando você cria uma rede virtual no portal do Azure, o nome completo que o Azure usa não fica visível no portal do Azure. Por exemplo, uma rede virtual que pareça se chamar 'ClassicVNet1' no portal do Azure pode ter um nome muito mais longo no arquivo de configuração de rede. O nome pode ser algo parecido com ‘Grupo ClassicRG1 ClassicVNet1’. Os nomes de rede virtual são listados como ‘VirtualNetworkSite name =’.<br>Use os nomes no arquivo de configuração de rede ao executar os cmdlets do PowerShell.

## <a name="part-3-delete-the-virtual-network-gateway"></a>Parte 3. Excluir o gateway de rede virtual

Quando você exclui um gateway de rede virtual, todas as conexões com a rede virtual por meio do gateway são desconectadas. Se você tiver clientes P2S conectados à rede virtual, eles serão desconectados sem aviso.

Este exemplo remove o gateway de rede virtual. Quando você executar este exemplo, use o nome completo da rede virtual do arquivo de configuração de rede.

    Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"

Se for bem-sucedido, o retorno mostrará:

    Status : Successful

## <a name="part-4-modify-the-network-configuration-file"></a>Parte 4. Modificar o arquivo de configuração de rede

Quando você exclui um gateway de rede virtual, o cmdlet não modifica o arquivo de configuração de rede. Você precisa modificar o arquivo para remover os elementos que não estão mais sendo usados. As seções a seguir ajudarão você a modificar o arquivo de configuração de rede que baixou.

###<a name="local-network-site-references"></a>Referências do site de rede local

Para remover as informações de referência do site, faça alterações de configuração em ConnectionsToLocalNetwork/LocalNetworkSiteRef. A remoção de uma referência de site local dispara o Azure para excluir um túnel. Dependendo da configuração que você criou, é possível que não haja uma LocalNetworkSiteRef listada.

    <Gateway>
       <ConnectionsToLocalNetwork>
         <LocalNetworkSiteRef name="D1BFC9CB_Site2">
           <Connection type="IPsec" />
         </LocalNetworkSiteRef>
       </ConnectionsToLocalNetwork>
    </Gateway>

Exemplo:

    <Gateway>
       <ConnectionsToLocalNetwork>
       </ConnectionsToLocalNetwork>
     </Gateway>

###<a name="local-network-sites"></a>Sites de rede locais

Remova os sites locais que não estiver mais usando. Dependendo da configuração que você criou, é possível que não haja um Site de Rede Local listado.

    <LocalNetworkSites>
      <LocalNetworkSite name="Site1">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
      </LocalNetworkSite>
      <LocalNetworkSite name="Site3">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

Neste exemplo, removemos apenas Site3.

    <LocalNetworkSites>
        <LocalNetworkSite name="Site1">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="client-addresspool"></a>AddressPool do cliente

Caso tenha uma conexão P2S com a rede virtual, você terá um VPNClientAddressPool. Remova os pools de endereços de cliente que correspondem ao gateway de rede virtual que você excluiu.

    <Gateway>
       <VPNClientAddressPool>
         <AddressPrefix>10.1.0.0/24</AddressPrefix>
       </VPNClientAddressPool>
       <ConnectionsToLocalNetwork />
    </Gateway>

Exemplo:

     <Gateway>
       <ConnectionsToLocalNetwork />
     </Gateway>

### <a name="gatewaysubnet"></a>GatewaySubnet

Exclua GatewaySubnet que corresponde à rede virtual.

    <Subnets>
       <Subnet name="FrontEnd">
         <AddressPrefix>10.11.0.0/24</AddressPrefix>
       </Subnet>
       <Subnet name="GatewaySubnet">
         <AddressPrefix>10.11.1.0/29</AddressPrefix>
       </Subnet>
     </Subnets>

Exemplo:

    <Subnets>
       <Subnet name="FrontEnd">
         <AddressPrefix>10.11.0.0/24</AddressPrefix>
       </Subnet>
     </Subnets>

## <a name="part-5-upload-the-network-configuration-file"></a>Parte 5. Carregar o arquivo de configuração de rede

Salve suas alterações e faça upload do arquivo de configuração de rede no Azure. Altere o caminho do arquivo conforme for necessário para seu ambiente.

     Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml

Se for bem-sucedido, o retorno mostrará algo semelhante a este exemplo:

     OperationDescription        OperationId                      OperationStatus                                                
     --------------------        -----------                      ---------------                                                
     Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded
