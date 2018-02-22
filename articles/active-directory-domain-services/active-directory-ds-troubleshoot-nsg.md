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
ms.date: 02/12/2018
ms.author: ergreenl
ms.openlocfilehash: cc9a61314de7e10afe370c3b1307d03544a379d5
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Solucionar problemas de configuração de rede inválida para o domínio gerenciado
Este artigo ajuda você a solucionar problemas e resolver erros de configuração relacionados à rede que resultam na seguinte mensagem de alerta:

## <a name="alert-aadds104-network-error"></a>Alerta AADDS104: erro de rede
**Mensagem de alerta:** *A Microsoft não consegue alcançar os controladores de domínio para este domínio gerenciado. Isso pode ocorrer se um grupo de segurança de rede (NSG) configurado na sua rede virtual bloquear o acesso ao domínio gerenciado. Outro motivo possível é a existência de uma rota definida pelo usuário que bloqueia o tráfego de entrada da Internet.*

Configurações inválidas do NSG são a causa mais comum de erros de rede para o Azure AD Domain Services. O NSG (Grupo de Segurança de Rede) configurado para a sua rede virtual deve permitir o acesso ao [portas específicas](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services). Se essas portas são bloqueadas, a Microsoft não é capaz de monitorar ou atualizar o domínio gerenciado. Além disso, a sincronização entre o seu diretório do Azure AD e seu domínio gerenciado é afetada. Ao criar o NSG, mantenha essas portas abertas para evitar interrupção no serviço.


## <a name="sample-nsg"></a>Exemplo de NSG
A tabela a seguir ilustra um exemplo de NSG que manteria seu domínio gerenciado seguro ao mesmo tempo em que permitiria o monitoramento, gestão e atualização das informações pela Microsoft.

![Exemplo de NSG](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> O Azure AD Domain Services exige acesso irrestrito de saída da rede virtual. É recomendável não criar nenhuma regra de NSG adicional que restrinja o acesso de saída para a rede virtual.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Adicionar uma regra a um Grupo de Segurança de Rede usando o Portal do Azure
Se você não quiser usar o PowerShell, você pode adicionar manualmente regras únicas para os NSGs usando o portal do Azure. Conclua estas etapas para criar regras no seu Grupo de Segurança de Rede:

1. Navegue até a página [Grupos de segurança de rede](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) no portal do Azure
2. Na tabela, escolha o NSG associado à sub-rede na qual o domínio gerenciado está habilitado.
3. Em **Configurações**, no painel de navegação esquerdo, clique em **Regras de segurança de entrada** ou em **Regras de segurança de saída**.
4. Crie a regra clicando em **Adicionar** e preenchendo as informações. Clique em **OK**.
5. Verifique se a regra foi criada localizando-a na tabela de regras.


## <a name="create-an-nsg-for-azure-ad-domain-services-using-powershell"></a>Criar um NSG para o Azure AD Domain Services usando o PowerShell
Este NSG está configurado para permitir o tráfego de entrada para as portas necessárias pelo Azure AD Domain Services, negando qualquer outro acesso de entrada indesejado.

**Pré-requisito: Instalar e configurar o Azure PowerShell** Siga as instruções para [instalar o módulo do Azure PowerShell e conectar-se à sua assinatura do Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

>[!TIP]
> É recomendável usar a versão mais recente do módulo do Azure PowerShell. Se você já tiver uma versão mais antiga do módulo do Azure PowerShell instalado, atualize para a versão mais recente.
>

Use as etapas a seguir para criar um novo NSG usando o PowerShell. 
1. Acesse a sua assinatura do Azure.

  ```PowerShell
  # Log in to your Azure subscription.
  Login-AzureRmAccount
  ```

2. Crie um NSG com três regras. O script a seguir define três regras para o NSG que permitem o acesso às portas necessárias para executar o Azure Active Directory Domain Services. Em seguida, o script cria um novo NSG que contém essas regras. Use o mesmo formato para adicionar mais regras que permitem outros tráfegos de entrada, se requerido por cargas de trabalho implantadas na rede virtual.

  ```PowerShell
  # Allow inbound HTTPS traffic to enable synchronization to your managed domain.
  $SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD `
  -Description "Allow synchronization with Azure AD" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443

  # Allow management of your domain over port 5986 (PowerShell Remoting)
  $PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting `
  -Description "Allow management of domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, `
  13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, `
  52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, `
  52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 `
  -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986

  # Allow management of your domain over port 3389 (remote desktop).
  $RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD `
  -Description "Allow management of domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389

  # Create the NSG with the 3 rules above
  $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Location $Location `
  -Name "AAD-DomainServices-NSG" -SecurityRules $SyncRule,$PSRemotingRule,$RemoteDesktopRule
  ```

3. Por fim, associe o NSG à rede virtual e à sub-rede de sua escolha.

  ```PowerShell
  # Find vnet and subnet
  $Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
  $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

  # Set the nsg to the subnet and save the changes
  $Subnet.NetworkSecurityGroup = $Nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
  ```

## <a name="full-script-to-create-and-apply-an-nsg-for-azure-ad-domain-services"></a>Script completo para criar e aplicar um NSG ao Azure AD Domain Services
>[!TIP]
> É recomendável usar a versão mais recente do módulo do Azure PowerShell. Se você já tiver uma versão mais antiga do módulo do Azure PowerShell instalado, atualize para a versão mais recente.
>

```PowerShell
# Change the following values to match your deployment
$ResourceGroup = "ResourceGroupName"
$Location = "westus"
$VnetName = "exampleVnet"
$SubnetName = "exampleSubnet"

# Log in to your Azure subscription.
Login-AzureRmAccount

# Allow inbound HTTPS traffic to enable synchronization to your managed domain.
$SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD `
-Description "Allow synchronization with Azure AD" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

# Allow management of your domain over port 5986 (PowerShell Remoting)
$PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting `
-Description "Allow management of domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, `
13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, `
52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, `
52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 `
-SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 5986

# Allow management of your domain over port 3389 (remote desktop).
$RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD `
-Description "Allow management of domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

# Create the NSG with the 3 rules above
$Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Location $Location `
-Name "AAD-DomainServices-NSG" -SecurityRules $SyncRule,$PSRemotingRule,$RemoteDesktopRule

# Find vnet and subnet
$Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

# Set the nsg to the subnet and save the changes
$Subnet.NetworkSecurityGroup = $Nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
```

> [!NOTE]
> Esse NSG padrão não bloqueia o acesso à porta usada para o LDAP Seguro. Para bloquear o acesso LDAP Seguro através da Internet, veja [este artigo](active-directory-ds-troubleshoot-ldaps.md).
>

## <a name="need-help"></a>Precisa de ajuda?
Entre em contato com a equipe de produto do Azure Active Directory Domain Services para [compartilhar comentários ou obter suporte](active-directory-ds-contact-us.md).
