---
ms.assetid: ''
title: Configurar redes virtuais e firewalls do Azure Key Vault
description: Instruções passo a passo para configurar redes virtuais e firewalls do Key Vault
services: key-vault
author: amitbapat
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/31/2018
ms.author: ambapat
ms.openlocfilehash: c58fc56742c0a11771bdd84e4195e6f476622a3b
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43346760"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Configurar redes virtuais e firewalls do Azure Key Vault

Este guia descreve instruções passo a passo para configurar redes virtuais e firewalls do Key Vault para restringir acesso ao cofre de chaves. Os [Pontos de Extremidade de Serviço de Rede Virtual para Key Vault](key-vault-overview-vnet-service-endpoints.md) permitem restringir o acesso ao cofre de chaves à Rede Virtual especificada e/ou a um conjunto de intervalos de endereços IPv4 (Protocolo IP versão 4).

> [!IMPORTANT]
> Depois que as regras de rede virtual e firewall estiverem ativas, todas as operações do [plano de dados](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) do Key Vault SOMENTE poderão ser executadas quando as solicitações do chamador originarem-se de redes virtuais ou intervalos de endereços IPV4 permitidos. Isso também aplica-se ao acesso ao cofre de chaves do portal do Azure. Embora um usuário possa navegar em um cofre de chaves do portal do Azure, talvez ele não consiga listar chaves/segredos/certificados, se o computador cliente não estiver na lista permitida. Isso também afeta o 'Seletor do Cofre de Chaves' de outros serviços do Azure. Os usuários poderão ver a lista de cofres de chaves, mas não listar chaves, se as regras de firewall impedirem o computador cliente.

## <a name="azure-portal"></a>Portal do Azure

1. Navegue até o cofre de chaves que você quer proteger.
2. Clique em **Firewalls e redes virtuais**.
3. Clique em **Redes selecionadas** em **Permitir acesso de**.
4. Para adicionar redes virtuais existentes a firewalls e regras de rede virtual, clique em **+ Adicionar redes virtuais existentes**.
5. Na nova folha que aparece, selecione a assinatura, as redes virtuais e as sub-redes que você quer permitir acesso a esse cofre de chaves. Se as redes virtuais e as sub-redes selecionadas não tiverem pontos de extremidade de serviço habilitados, será exibida uma mensagem informando "As seguintes redes não têm pontos de extremidade de serviço habilitados...". Clique em **Habilitar**, após confirmar que você quer habilitar pontos de extremidade de serviço para as redes virtuais e sub-redes listadas. Pode demorar até 15 minutos para entrar em vigor.
6. Também é possível adicionar novas redes virtuais e sub-redes e, em seguida, habilitar pontos de extremidade de serviço para as redes virtuais e sub-redes criadas recentemente, clicando em **+ Adicionar nova rede virtual** e seguindo as solicitações.
7. Em **Redes IP**, é possível adicionar intervalos de endereços IPv4, digitando intervalos de endereços IPv4 na notação [CIDR (Roteamento Entre Domínios Sem Classe)](https://tools.ietf.org/html/rfc4632) ou endereços IP individuais.
8. Clique em **Salvar**.

## <a name="azure-cli-20"></a>CLI do Azure 2.0

1. [Instale a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) e [faça logon](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Liste as regras de rede virtual disponíveis, se você não tiver definido nenhuma regra para esse cofre de chaves, a lista estará vazia.
```azurecli
az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
```

3. Habilitar Ponto de Extremidade de Serviço para Key Vault em uma Rede Virtual e sub-rede existentes
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
```

4. Adicionar uma regra de rede para uma rede virtual e sub-rede
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
```

5. Adicionar intervalo de endereço IP para permitir tráfego de
```azurecli
az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
```

6. Se o cofre de chaves precisar ser acessado por qualquer serviço confiável, defina 'ignorar' para AzureServices
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
```

7. Agora, a etapa final e importante, ative as regras de rede, definindo a ação padrão como 'Negar'
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
```

## <a name="azure-powershell"></a>Azure PowerShell

1. Instale o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) mais recente e [faça logon](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Liste as regras de rede virtual disponíveis, se você não tiver definido nenhuma regra para esse cofre de chaves, a lista estará vazia.
```PowerShell
(Get-AzureRmKeyVault -VaultName "mykeyvault").NetworkAcls
```

3. Habilitar Ponto de Extremidade de Serviço para Key Vault em uma Rede Virtual e sub-rede existentes
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzureRmVirtualNetwork
```

4. Adicionar uma regra de rede para uma rede virtual e sub-rede
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
```

5. Adicionar intervalo de endereço IP para permitir tráfego de
```PowerShell
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
```

6. Se o cofre de chaves precisar ser acessado por qualquer serviço confiável, defina 'bypass' para AzureServices
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
```

7. Agora, a etapa final e importante, ative as regras de rede, definindo a ação padrão como 'Negar'
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
```

## <a name="references"></a>Referências

* Comandos da CLI 2.0 do Azure - [az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Cmdlets do Azure PowerShell - [Get-AzureRmKeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurermkeyvault), [Add-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureRmKeyVaultNetworkRule), [Remove-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureRmKeyVaultNetworkRule), [Update-AzureRmKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureRmKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Próximas etapas

* [Pontos de Extremidade de Serviço de Rede Virtual para Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Proteger seu cofre de chaves](key-vault-secure-your-key-vault.md)