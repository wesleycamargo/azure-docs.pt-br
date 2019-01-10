---
ms.assetid: ''
title: Configurar redes virtuais e firewalls do Azure Key Vault - Azure Key Vault
description: Instruções passo a passo para configurar redes virtuais e firewalls do Key Vault
services: key-vault
author: amitbapat
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 01/02/2019
ms.author: ambapat
ms.openlocfilehash: 09a19b92a496650f94be208d4f463f1fb3fa4256
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2019
ms.locfileid: "54001848"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Configurar redes virtuais e firewalls do Azure Key Vault

Este guia fornece instruções passo a passo para configurar redes virtuais e firewalls do Azure Key Vault para restringir acesso ao cofre de chaves. Os [pontos de extremidade de serviço da rede virtual para Key Vault](key-vault-overview-vnet-service-endpoints.md) permitem restringir o acesso a uma rede virtual especificada e um conjunto de intervalos de endereços IPv4 (protocolo de internet versão 4).

> [!IMPORTANT]
> Depois que as regras de firewall estiverem ativas, os usuários podem realizar apenas operações de [plano de dados](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) do Key Vault quando as solicitações deles originarem-se de redes virtuais ou intervalos de endereços IPv4 permitidos. Isso também aplica-se ao acessar o Key Vault a partir do portal do Azure. Embora os usuários possam navegar em um cofre de chaves a partir do portal do Azure, eles talvez não consigam listar chaves, segredos ou certificados se o computador cliente deles não estiver na lista permitida. Isso também afeta o Seletor do Cofre de Chaves de outros serviços do Azure. Os usuários poderão ver a lista de cofres de chaves, mas não listar chaves, se as regras de firewall impedirem o computador cliente.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

Segue como configurar redes virtuais e firewalls do Key Vault usando o portal do Azure:

1. Navegue até o cofre de chaves que você quer proteger.
2. Selecione **Firewalls e redes virtuais**.
3. Em **Permitir acesso de**, clique em **Redes selecionadas**.
4. Para adicionar redes virtuais existentes a firewalls e regras de rede virtual, selecione **+ Adicionar redes virtuais existentes**.
5. Na nova folha que é aberta, selecione a assinatura, as redes virtuais e as sub-redes que você quer permitir acesso a esse cofre de chaves. Se as redes virtuais e sub-redes que você selecionar não tiverem pontos de extremidade de serviço habilitados, confirme que você quer habilitar pontos de extremidade de serviço e, em seguida, selecione **Habilitar**. Pode demorar até 15 minutos para entrar em vigor.
6. Em **Redes IP**, adicione intervalos de endereços IPv4, digitando intervalos de endereços IPv4 na [notação CIDR (Roteamento Entre Domínios Sem Classe)](https://tools.ietf.org/html/rfc4632) ou endereços IP individuais.
7. Clique em **Salvar**.

Também é possível adicionar novas redes virtuais e sub-redes e, em seguida, habilitar pontos de extremidade de serviço para as redes virtuais e sub-redes recém-criadas, selecionando **+ Adicionar nova rede virtual**. Em seguida, siga os prompts.

## <a name="use-the-azure-cli-20"></a>Usar a CLI 2.0 do Azure

Segue como configurar redes virtuais e firewalls do Key Vault usando a CLI 2.0 do Azure:

1. [Instale a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e [conecte](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Liste as regras de rede virtual disponíveis. Se você ainda não definiu regras para esse cofre de chaves, a lista estará vazia.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Habilite um ponto de extremidade de serviço para Key Vault em uma rede virtual e sub-rede existentes.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Adicionar uma regra de rede para uma rede virtual e sub-rede.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Adicione um intervalo de endereços IP que permite tráfego.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Se o cofre de chaves precisar ser acessível por qualquer serviço confiável, defina `bypass` para `AzureServices`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Ative as regras de rede definindo a ação padrão como `Deny`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Usar PowerShell do Azure

Segue como configurar redes virtuais e firewalls do Key Vault usando o PowerShell:

1. Instale o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) mais recente e [conecte](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Liste as regras de rede virtual disponíveis. Se você ainda não definiu regras para esse cofre de chaves, a lista estará vazia.
   ```PowerShell
   (Get-AzureRmKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Habilitar ponto de extremidade de serviço para Key Vault em uma rede virtual e sub-rede existentes.
   ```PowerShell
   Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzureRmVirtualNetwork
   ```

4. Adicionar uma regra de rede para uma rede virtual e sub-rede.
   ```PowerShell
   $subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Adicione um intervalo de endereços IP que permite tráfego.
   ```PowerShell
   Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Se o cofre de chaves precisar ser acessível por qualquer serviço confiável, defina `bypass` para `AzureServices`.
   ```PowerShell
   Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Ative as regras de rede definindo a ação padrão como `Deny`.
   ```PowerShell
   Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Referências

* Comandos da CLI 2.0 do Azure - [az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Cmdlets do Azure PowerShell: [Get-AzureRmKeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurermkeyvault), [Add-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureRmKeyVaultNetworkRule), [Remove-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureRmKeyVaultNetworkRule), [Update-AzureRmKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureRmKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Próximas etapas

* [Pontos de extremidade de serviço de rede virtual para Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Proteger seu cofre de chaves](key-vault-secure-your-key-vault.md)