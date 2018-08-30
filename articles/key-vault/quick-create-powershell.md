---
title: Início Rápido do Azure – Definir e recuperar um segredo do Key Vault usando o PowerShell | Microsoft Docs
description: ''
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 1126f665-2e6c-4cca-897e-7d61842e8334
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/28/2018
ms.author: barclayn
ms.openlocfilehash: 50448691fb136278cf7fdf3687ffb3b13fbb54ca
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122256"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando o PowerShell

O Azure Key Vault é um serviço de nuvem que funciona como um repositório de segredos seguro. Você pode armazenar chaves, senhas, certificados e outros segredos com segurança. Para saber mais sobre o Key Vault, reveja a [Visão geral](key-vault-overview.md). Neste início rápido, você usará o PowerShell para criar um cofre de chaves. Em seguida, você armazenará o segredo no cofre recém-criado.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.1.1 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure.

```azurepowershell-interactive
Login-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Agora, você criará um Key Vault. Ao realizar esta etapa, você precisará de algumas informações:

Embora usemos "Contoso KeyVault2" como o nome do nosso Key Vault em todo este início rápido, use um nome exclusivo.

- **Nome do cofre** Contoso-Vault2.
- **Nome do grupo de recursos** ContosoResourceGroup.
- **Local:** Leste dos EUA.

```azurepowershell-interactive
New-AzureRmKeyVault -VaultName 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

A saída desse cmdlet mostra as propriedades do cofre de chaves criado recentemente. Observe as duas propriedades listadas abaixo:

* **Nome do Cofre**: neste exemplo, é **Contoso-Vault2**. Você usará esse nome para outros cmdlets do Cofre da Chave.
* **URI do cofre**: neste exemplo, é https://contosokeyvault.vault.azure.net/. Aplicativos que usam seu cofre via API REST devem usar esse URI.

Após a criação do cofre, sua conta do Azure é a única conta que pode fazer algo nesse novo cofre.

![Saída após a conclusão do comando de criação do Key Vault](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Adicionando um segredo ao Key Vault

Para adicionar um segredo ao cofre, basta executar algumas etapas. Nesse caso, adicione uma senha que pode ser usada por um aplicativo. A senha é chamada **ExamplePassword** e armazena o valor de '''**Pa$ $w0rd**''' nele.

Primeiro, converta o valor de Pa$$w0rd em uma cadeia de caracteres segura digitando:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force
```

Em seguida, digite os comandos do PowerShell abaixo para criar um segredo no Key Vault chamado **ExamplePassword** com o valor **Pa$ $w0rd**:

```azurepowershell-interactive
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'ExamplePassword' -SecretValue $secretvalue
```

Para exibir o valor contido no segredo como texto sem formatação:

```azurepowershell-interactive
(Get-AzureKeyVaultSecret -vaultName "Contosokeyvault" -name "ExamplePassword").SecretValueText
```

Agora, você criou um Key Vault, armazenou um segredo e o recuperou.

## <a name="clean-up-resources"></a>Limpar recursos

 Outros guias de início rápido e tutoriais da coleção aproveitam esse guia de início rápido. Se você planeja continuar a trabalhar com outros tutoriais e inícios rápidos, deixe esses recursos onde estão.

Quando não for mais necessário, use o comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos, o Key Vault e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Key Vault e armazenou uma chave de software nele. Para saber mais sobre o Key Vault e como você pode usá-lo com seus aplicativos, prossiga para o tutorial de aplicativos Web que trabalham com o Key Vault.

> [!div class="nextstepaction"]
> Para saber como ler um segredo do Key Vault com um aplicativo Web usando identidades de serviço gerenciada, continue com o tutorial a seguir, [Configurar um aplicativo Web do Azure para ler um segredo do Key Vault](quick-create-net.md).
