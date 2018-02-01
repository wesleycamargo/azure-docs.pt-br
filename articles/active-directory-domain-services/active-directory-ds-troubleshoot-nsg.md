---
title: "Azure Active Directory Domain Services: Resolução de problemas de configuração do Grupo de Segurança de Rede | Microsoft Docs"
description: "Resolução de problemas de configuração do NSG para o Azure AD Domain Services"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: ergreenl
ms.openlocfilehash: 447f9119ea379e278be77d8699c7dcb751ea3085
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-network-security-group-configuration"></a>Azure Active Directory Domain Services: Resolução de problemas de configuração do Grupo de Segurança de Rede



## <a name="aadds104-network-error"></a>AADDS104: Erro de rede

**Mensagem de alerta:** *A Microsoft não consegue alcançar os controladores de domínio para este domínio gerenciado. Isso pode ocorrer se um grupo de segurança de rede (NSG) configurado na sua rede virtual bloquear o acesso ao domínio gerenciado. Outro motivo possível é a existência de uma rota definida pelo usuário que bloqueia o tráfego de entrada da internet.*

A causa mais comum de erros de rede para o Azure Active Directory Domain Services pode ser atribuída a configurações incorretas do NSG. Para garantir que a Microsoft possa fornecer serviços e manter o seu domínio gerenciado, você deve usar um grupo de segurança de rede (NSG) para permitir o acesso a [portas específicas](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services) dentro da sua sub-rede. Se essas portas estiverem bloqueadas, a Microsoft não pode acessar os recursos necessários, o que pode ser prejudicial para seu serviço. Ao criar o NSG, mantenha essas portas abertas para garantir que não haja nenhuma interrupção no serviço.

## <a name="sample-nsg"></a>Exemplo de NSG
A tabela a seguir ilustra um exemplo de NSG que manteria seu domínio gerenciado seguro ao mesmo tempo em que permitiria o monitoramento, gestão e atualização das informações pela Microsoft.

![Exemplo de NSG](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> O Azure AD Domain Services exige acesso irrestrito de saída. É recomendável que você não crie nenhuma regra de saída adicional para seus NSGs.

## <a name="adding-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Adicionando uma regra a um grupo de segurança de rede usando o portal do Azure
Se você não quiser usar o PowerShell, você pode adicionar manualmente regras únicas para os NSGs usando o portal do Azure. Siga estas etapas para criar regras no seu grupo de segurança de rede.

1. Navegue até a página [Grupos de segurança de rede](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) no portal do Azure
2. Escolha o NSG associado ao seu domínio a partir da tabela.
3. Em configurações no painel de navegação esquerdo, clique em **regras de segurança de entrada** ou **regras de segurança de saída**.
4. Crie a regra clicando em **Adicionar** e preenchendo as informações. Clique em **OK**.
5. Verifique se a regra foi criada localizando-a na tabela de regras.


## <a name="create-a-default-nsg-using-powershell"></a>Criar um NSG padrão usando o PowerShell

Os passos anteriores são as etapas que você pode usar para criar um novo NSG usando o PowerShell que mantém todas as portas necessárias para executar o Azure Active Directory Domain Services aberto ao negar qualquer outro acesso indesejado.


Essa resolução requer a instalação e execução do [Powershell do Microsoft Azure AD](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?toc=%2Fazure%2Factive-directory-domain-services%2Ftoc.json&view=azureadps-2.0).

1. Conecte-se ao seu diretório do Microsoft Azure AD.

  ```PowerShell
  # Connect to your Azure AD directory.
  Connect-AzureAD
  ```
2. Acesse a sua assinatura do Azure.

  ```PowerShell
  # Log in to your Azure subscription.
  Login-AzureRmAccount
  ```

3. Crie um NSG com três regras. O script a seguir define três regras para o NSG que permitem o acesso às portas necessárias para executar o Azure Active Directory Domain Services. Em seguida, o script cria um novo NSG que contém essas regras. Você pode adicionar mais regras conforme necessário usando o mesmo formato.

  ```PowerShell
  # Create the rules needed
  $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443
  $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389
  $rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986
  # Create the NSG with the 3 rules above
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
  -Name "AADDS-NSG" -SecurityRules $rule1,$rule2,$rule3
  ```

4. Por fim, o script associa o NSG à rede virtual e a sub-rede de sua escolha.

  ```PowerShell
  # Find vnet and subnet
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
  # Set the nsg to the subnet and save the changes
  $subnet.NetworkSecurityGroup = $nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

### <a name="full-script"></a>Script completo

```PowerShell
#Change the following values to match your deployment
$resourceGroup = "ResourceGroupName"
$vnetName = "exampleVnet"
$subnetName = "exampleSubnet"

$serviceIPs = "52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161"

# Create the rules needed
$rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

$rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

$rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389


# Connect to your Azure AD directory.
Connect-AzureAD

# Log in to your Azure subscription.
Login-AzureRmAccount

# Create the NSG with the 3 rules above
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
-Name "NSG-Default" -SecurityRules $rule1,$rule2,$rule3

# Find vnet and subnet
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName

# Set the nsg to the subnet and save the changes
$subnet.NetworkSecurityGroup = $nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

> [!NOTE]
>Esse NSG padrão não bloqueia o acesso à porta usada para o LDAP Seguro. Para saber como criar uma regra para essa porta, visite [este artigo](active-directory-ds-troubleshoot-ldaps.md).
>

## <a name="contact-us"></a>Fale conosco
Entre em contato com a equipe de produto do Azure Active Directory Domain Services para [compartilhar comentários ou obter suporte](active-directory-ds-contact-us.md).
