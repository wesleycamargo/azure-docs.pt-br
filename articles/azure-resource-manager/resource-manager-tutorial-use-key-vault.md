---
title: Integrar o Azure Key Vault à implantação de modelo do Resource Manager | Microsoft Docs
description: Saiba como usar o Azure Key Vault para passar valores de parâmetro seguros durante a implantação de modelo do Resource Manager
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/25/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 979867e7630c21b0bd724967dbc79c5f8155ca5e
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237171"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Tutorial: Integrar o Azure Key Vault na implantação de modelo do Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Saiba como recuperar segredos do Azure Key Vault e passar os segredos como parâmetros durante a implantação do Resource Manager. O valor nunca é exposto porque você apenas fazer referência à sua ID de cofre de chaves. Para obter mais informações, veja [Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](./resource-manager-keyvault-parameter.md)

No tutorial [Definir ordem de implantação dos recursos](./resource-manager-tutorial-create-templates-with-dependent-resources.md), você criará uma máquina virtual, uma rede virtual e alguns outros recursos dependentes. Neste tutorial, você deve personalizar o modelo para recuperar a senha de administrador da máquina virtual de um cofre de chaves.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um cofre de chaves
> * Abrir um modelo de Início Rápido
> * Edite o arquivo de parâmetros
> * Implantar o modelo
> * Validar a implantação
> * Limpar recursos

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* [Visual Studio Code](https://code.visualstudio.com/) com a [extensão de Ferramentas do Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Para aumentar a segurança, use uma senha gerada para a conta de administrador da máquina virtual. Veja um exemplo para gerar uma senha:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Verifique se a senha gerada atende aos requisitos de senha de máquina virtual. Cada serviço do Azure tem requisitos de senha específicos. Para os requisitos de senha da VM, veja [Quais são os requisitos de senha ao criar uma VM?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Preparar um cofre de chaves

Nesta seção, você pode usar um modelo do Resource Manager para criar um cofre de chaves e um segredo. Esse modelo executa estas ações:

* Crie um cofre de chaves com a propriedade `enabledForTemplateDeployment` habilitada. Essa propriedade deve ser true antes que o processo de implantação de modelo possa acessar os segredos definidos neste cofre de chaves.
* Adicionar um segredo ao cofre de chaves.  O segredo armazena a senha de administrador da máquina virtual.

Se você (como o usuário a implantar o modelo de máquina virtual) não for o proprietário ou o colaborador do cofre de chaves, o proprietário ou o colaborador do cofre de chaves deverá conceder o acesso à permissão Microsoft.KeyVault/vaults/deploy/action para o cofre de chaves. Para obter mais informações, veja [Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](./resource-manager-keyvault-parameter.md)

Sua ID de objeto de usuário do Azure AD é necessária para o modelo configurar permissões. O procedimento a seguir obtém a ID do objeto (GUID).

1. Execute o seguinte comando do Azure PowerShell ou da CLI do Azure.  

    ```azurecli-interactive
    echo "Enter your email address that is associated with your Azure subscription):" &&
    read upn &&
    az ad user show --upn-or-object-id $upn --query "objectId" &&
    ```
    ```azurepowershell-interactive
    $upn = Read-Host -Prompt "Input your user principal name (email address) used to sign in to Azure"
    (Get-AzADUser -UserPrincipalName $upn).Id
    ```
2. Anote a ID do objeto. Você precisará dela posteriormente neste tutorial.

Para criar um cofre de chaves:

1. Selecione a imagem a seguir para entrar no Azure e abrir um modelo. O modelo cria um cofre de chaves e um segredo.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Farmtutorials.blob.core.windows.net%2Fcreatekeyvault%2FCreateKeyVault.json"><img src="./media/resource-manager-tutorial-use-key-vault/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Selecione ou insira os seguintes valores.  Não selecione **Compra** depois de inserir os valores.

    ![Portal de implantação da integração do Key Vault do modelo do Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-key-vault-portal.png)

    * **Assinatura**: selecione uma assinatura do Azure.
    * **Grupo de recursos**: atribua um nome exclusivo. Anote esse nome, você usará o mesmo grupo de recursos para implantar a máquina virtual na próxima sessão. Colocar o cofre de chaves e a máquina virtual no mesmo grupo de recursos torna mais fácil limpar o recurso no final do tutorial.
    * **Local**: selecione um local.  O local padrão é **EUA Central**.
    * **Nome do Key Vault**: atribua um nome exclusivo. 
    * **Id do locatário**: a função de modelo recupera automaticamente sua ID de locatário.  Não altere o valor padrão
    * **ID de Usuário do AD**: insira sua ID de objeto de usuário do Azure AD que você recuperou do último procedimento.
    * **Nome do Segredo**: O nome padrão é **vmAdminPassword**. Se você alterar o nome do segredo aqui, precisará atualizar o nome do segredo ao implantar a máquina virtual.
    * **Valor do Segredo**: Insira seu segredo.  O segredo é a senha usada para entrar na máquina virtual. É recomendável usar a senha gerada que você criou no último procedimento.
    * **Concordo com os termos e condições declarados acima**: selecione.
3. Selecione **Editar parâmetros** na parte superior para dar uma olhada do modelo.
4. Navegue até a linha 28 do arquivo JSON do modelo. Esta é a definição do recurso de cofre de chaves.
5. Navegue até a linha 35:

    ```json
    "enabledForTemplateDeployment": true,
    ```
    `enabledForTemplateDeployment` é uma propriedade de Key Vault. Essa propriedade deve ser verdadeira antes que você possa recuperar os segredos do cofre de chaves durante a implantação.
6. Navegue até a linha 89. Essa é a definição de segredo do Key Vault.
7. Selecione **Descartar** na parte inferior da página. Você não fez nenhuma alteração.
8. Verifique se você forneceu todos os valores conforme mostrado na captura de tela anterior e, em seguida, clique em **Comprar** na parte inferior da página.
9. Selecione o ícone de sino (notificação) na parte superior da página para abrir o painel **Notificações**. Aguarde até o recurso ser implantado com êxito.
10. Selecione **Ir para o grupo de recursos** no painel **Notificação**. 
11. Selecione o nome do cofre de chaves para abri-lo.
12. Selecione **Segredos** no painel esquerdo. **vmAdminPassword** deverá estar listado lá.
13. Selecione **Políticas de acesso** no painel esquerdo. Seu nome (Active Directory) deverá estar listado, caso contrário, você não terá permissão para acessar o Key Vault.
14. Selecione **Clique para exibir as políticas de acesso avançado**. Observe que **Habilitar acesso ao Azure Resource Manager para implantação de modelo** está selecionado. Essa configuração é outra condição para fazer a integração do Key Vault funcionar.

    ![Políticas de acesso integração do Key Vault do modelo do Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-key-vault-access-policies.png)
15. Selecione **Propriedades** no painel esquerdo.
16. Faça uma cópia da **ID do Recurso**. Quando você implantar a máquina virtual, precisará dessa ID.  O formato da ID do Recurso é:

    ```json
    /subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
    ```

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Modelos de Início Rápido do Azure é um repositório de modelos do Gerenciador de Recursos. Em vez de criar um modelo do zero, você pode encontrar um exemplo de modelo e personalizá-lo. O modelo usado neste tutorial é chamado [Implantar uma VM Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. No Visual Studio Code, escolha **Arquivo**>**Abrir Arquivo**.
2. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Escolha **Abrir** para abrir o arquivo. Esse é o mesmo cenário usado no [Tutorial: Criar modelos do Azure Resource Manager com recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
4. Há cinco recursos definidos pelo modelo:

    * `Microsoft.Storage/storageAccounts`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
    * `Microsoft.Network/publicIPAddresses`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
    * `Microsoft.Network/virtualNetworks`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
    * `Microsoft.Network/networkInterfaces`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
    * `Microsoft.Compute/virtualMachines`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    É útil ter algumas noções básicas do modelo antes de personalizá-lo.
5. Selecione **Arquivo**>**Salvar como** para salvar uma cópia do arquivo no computador local com o nome **azuredeploy.json**.
6. Repita as etapas de 1 a 4 para abrir a URL a seguir e, em seguida, salve o arquivo como **azuredeploy.parameters.json**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Edite o arquivo de parâmetros

Você não precisa fazer nenhuma alteração ao arquivo de modelo.

1. Abra **azuredeploy.parameters.json** no Visual Studio Code se já não estiver aberto.
2. Atualize o parâmetro **adminPassword** para:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    Substitua **id** pela ID do recurso do seu cofre de chaves criado no último procedimento.  

    ![integrar o cofre de chaves e o arquivo de parâmetros de implantação de máquina virtual do modelo do Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Atribua valores a:

    * **adminUsername**: nome da conta de administrador da máquina virtual.
    * **dnsLabelPrefix**: dê um nome a dnsLabelPrefix.
4. Salve as alterações.

## <a name="deploy-the-template"></a>Implantar o modelo

Siga as instruções em [Implantar o modelo](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) para implantar o modelo. Você precisa fazer upload de **azuredeploy. JSON** e de **azuredeploy.parameters.json** para o Cloud Shell e, em seguida, usar o seguinte script do PowerShell para implantar o modelo:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

Quando você implantar o modelo, use o mesmo grupo de recursos que o cofre de chaves. Torna mais fácil quando você limpa os recursos. Você só precisa excluir um grupo de recursos, em vez de dois.

## <a name="valid-the-deployment"></a>Validar a implantação

Depois de ter implantado com êxito a máquina virtual, teste o logon usando a senha armazenada no cofre de chaves.

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Selecione **Grupos de Recursos**/**YourResourceGroupName>**/**simpleWinVM**
3. Selecione **Conectar** da parte superior.
4. Selecione **Baixar arquivo RDP** e, em seguida, siga as instruções para entrar na máquina virtual usando a senha armazenada no cofre de chaves.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Escolha o nome do grupo de recursos.  Você deverá ver um total de seis recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você recuperou um segredo do Azure Key Vault e usou o segredo em sua implantação de modelo.  Para saber como criar modelos vinculados, veja:

> [!div class="nextstepaction"]
> [Criar modelos vinculados](./resource-manager-tutorial-create-linked-templates.md)
