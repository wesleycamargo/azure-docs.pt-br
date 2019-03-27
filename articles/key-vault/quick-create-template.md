---
title: Início Rápido do Azure – criar um Azure Key Vault e um segredo usando o modelo do Azure Resource Manager | Microsoft Docs
description: Início Rápido que mostra como criar cofres de chaves do Azure, assim como adicionar segredos aos cofres, usando o modelo do Azure Resource Manager.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/04/2019
ms.author: jgao
ms.openlocfilehash: 4b774e5b0a5c64a9af9a5a54ba264c6226558a24
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880005"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando o modelo do Resource Manager

[O Azure Key Vault](./key-vault-overview.md) é um serviço de nuvem que fornece um armazenamento seguro de segredos, como chaves, senhas e certificados, entre outros. Este Início Rápido concentra-se no processo de implantação de um modelo do Resource Manager para criar um cofre de chaves e um segredo. Para obter mais informações sobre como desenvolver modelos do Resource Manager, confira [documentação do Resource Manager](/azure/azure-resource-manager/) e a [referência de modelo](/azure/templates/microsoft.keyvault/allversions).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* Sua ID de objeto de usuário do Azure AD é necessária para o modelo configurar permissões. O procedimento a seguir obtém a ID do objeto (GUID).

    1. Execute o comando do Azure PowerShell ou CLI do Azure a seguir selecionando **Experimentar** e colando o script no painel do shell. Para colar o script, clique com o botão direito do mouse no shell e, em seguida, selecione **Colar**. 

        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --upn-or-object-id $upn --query "objectId" 
        ```

        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        ```

    2. Anote a ID do objeto. Você precisará dela na próxima seção deste início rápido.

## <a name="create-a-vault-and-a-secret"></a>Criar um cofre e um segredo

O modelo usado neste início rápido é proveniente dos [modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-key-vault-create/). Mais exemplos de modelos do Azure Key Vault podem ser encontrados [aqui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

1. Selecione a imagem a seguir para entrar no Azure e abrir um modelo. O modelo cria um cofre de chaves e um segredo.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Selecione ou insira os seguintes valores.  

    ![Portal de implantação da integração do Key Vault do modelo do Resource Manager](./media/quick-create-template/create-key-vault-using-template-portal.png)

    A menos que seja especificado, use o valor padrão para criar o cofre de chaves e um segredo.

    * **Assinatura**: selecione uma assinatura do Azure.
    * **Grupo de recursos**: selecione **Criar novo**, insira um nome exclusivo para o grupo de recursos e, em seguida, clique em **OK**. 
    * **Local**: selecione um local.  Por exemplo, **Centro dos EUA**.
    * **Nome do cofre de chaves**: insira um nome exclusivo para o cofre de chaves.  
    * **Id do locatário**: a função de modelo recupera automaticamente sua ID de locatário.  Não altere o valor padrão
    * **ID de Usuário do AD**: insira sua ID de objeto de usuário do Azure AD que recuperou de [Pré-requisitos](#prerequisites).
    * **Nome do segredo**: insira um nome para o segredo armazenado no cofre de chaves.  Por exemplo, **adminpassword**
    * **Valor do segredo**: insira o valor do segredo.  Para armazenar uma senha, é recomendável usar a senha gerada que você criou em Pré-requisitos.
    * **Concordo com os termos e condições declarados acima**: selecione.
3. Selecione **Comprar**.

## <a name="validate-the-deployment"></a>Validar a implantação

Você pode usar o portal do Azure para verificar o cofre de chaves e o segredo ou usar o script da CLI do Azure ou Azure PowerShell a seguir para listar o segredo criado.

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName
```

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
```

## <a name="clean-up-resources"></a>Limpar recursos

Outros tutoriais e inícios rápidos do Key Vault complementam este início rápido. Se você planeja continuar a trabalhar com os tutoriais e inícios rápidos subsequentes, deixe esses recursos onde estão.
Quando não for mais necessário, exclua o grupo de recursos, que excluirá o Key Vault e os recursos relacionados. Para excluir o grupo de recursos usando a CLI do Azure ou o Azure PowerShell:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName 
```
```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName 
```

## <a name="next-steps"></a>Próximas etapas

* [Página inicial do Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Documentação do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
* [SDK do Azure para Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Referência de API REST do Azure](https://docs.microsoft.com/rest/api/keyvault/)
