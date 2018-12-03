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
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0bcff6e0cec234f17b0aaab9828602eb4a194d85
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334203"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Tutorial: Integrar o Azure Key Vault na implantação de modelo do Resource Manager

Saiba como recuperar valores do segredo do Azure Key Vault e passar os valores secretos como parâmetros durante a implantação do Resource Manager. O valor nunca é exposto porque você apenas fazer referência à sua ID do Key Vault. Para obter mais informações, veja [Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](./resource-manager-keyvault-parameter.md)

No tutorial [Definir ordem de implantação dos recursos](./resource-manager-tutorial-create-templates-with-dependent-resources.md), você criará uma máquina virtual, uma rede virtual e alguns outros recursos dependentes. Neste tutorial, você deve personalizar o modelo para recuperar a senha de administrador da máquina virtual do Azure Key Vault.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Preparar o Key Vault
> * Abrir um modelo de Início Rápido
> * Edite o arquivo de parâmetros
> * Implantar o modelo
> * Validar a implantação
> * Limpar recursos

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* [Visual Studio Code](https://code.visualstudio.com/) com a [extensão de Ferramentas do Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Para aumentar a segurança, use uma senha gerada para a conta de administrador da máquina virtual. Veja um exemplo para gerar uma senha:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    O Azure Key Vault é projetado para proteger chaves de criptografia e outros segredos. Para obter mais informações, confira [Tutorial: Integrar o Azure Key Vault na implantação de modelo do Gerenciador de Recursos](./resource-manager-tutorial-use-key-vault.md). Também recomendamos que você atualize sua senha a cada três meses.

## <a name="prepare-the-key-vault"></a>Preparar o Key Vault

Nesta seção, você pode usar um modelo do Resource Manager para criar um Key Vault e um segredo. Esse modelo executa estas ações:

* Crie um cofre de chaves com a propriedade `enabledForTemplateDeployment`. Essa propriedade deve ser true antes que o processo de implantação de modelo possa acessar os segredos definidos neste Key Vault.
* Adicionar um segredo ao Key Vault.  O segredo armazena a senha de administrador da máquina virtual.

Se você (como o usuário a implantar o modelo de máquina virtual) não for o proprietário ou o colaborador do Key Vault, o proprietário ou o colaborador do Key Vault deverá conceder o acesso à permissão Microsoft.KeyVault/vaults/deploy/action para o Key Vault. Para obter mais informações, veja [Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](./resource-manager-keyvault-parameter.md)

Sua ID de objeto de usuário do Azure AD é necessária para o modelo configurar permissões. O procedimento a seguir obtém a ID do objeto (GUID) e também gera a senha de administrador. Para impedir ataque de spray de senha, é recomendável usar as senhas geradas.

1. Execute o seguinte comando do Azure PowerShell ou da CLI do Azure.  

    ```azurecli-interactive
    echo "Enter your email address that is associated with your Azure subscription):" &&
    read upn &&
    az ad user show --upn-or-object-id $upn --query "objectId" &&
    openssl rand -base64 32
    ```
    ```azurepowershell-interactive
    $upn = Read-Host -Prompt "Input your user principal name (email address) used to sign in to Azure"
    (Get-AzureADUser -ObjectId $upn).ObjectId
    openssl rand -base64 32
    ```
2. Anote a ID de objeto e a senha gerada. Você precisará dessa informação mais tarde.
3. Verifique se a senha gerada atende aos requisitos de senha de máquina virtual. Cada serviço do Azure tem requisitos de senha específicos. Para os requisitos de senha da VM, veja [Quais são os requisitos de senha ao criar uma VM?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

Para criar um Key Vault:

1. Selecione a imagem a seguir para entrar no Azure e abrir um modelo. O modelo cria um Key Vault e um segredo do Key Vault.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Farmtutorials.blob.core.windows.net%2Fcreatekeyvault%2FCreateKeyVault.json"><img src="./media/resource-manager-tutorial-use-key-vault/deploy-to-azure.png" /></a>

2. Selecione ou insira os seguintes valores.  Não selecione **Compra** depois de inserir os valores.

    ![Portal de implantação da integração do Key Vault do modelo do Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-key-vault-portal.png)

    * **Assinatura**: selecione uma assinatura do Azure.
    * **Grupo de recursos**: atribua um nome exclusivo. Anote esse nome, você usará o mesmo grupo de recursos para implantar a máquina virtual na próxima sessão. Colocar o Key Vault e a máquina virtual no mesmo grupo de recursos torna mais fácil limpar o recurso no final do tutorial.
    * **Local**: selecione um local.  O local padrão é **EUA Central**.
    * **Nome do Key Vault**: atribua um nome exclusivo. 
    * **Id do locatário**: a função de modelo recupera automaticamente sua ID de locatário.  Não altere o valor padrão
    * **ID de Usuário do AD**: insira sua ID de objeto de usuário do Azure AD que você recuperou do último procedimento.
    * **Nome do Segredo**: o nome padrão é **vmAdminPassword**. Se você alterar o nome do segredo aqui, precisará atualizar o nome do segredo ao implantar a máquina virtual.
    * **Valor do Segredo**: insira seu segredo.  O segredo é a senha usada para entrar na máquina virtual. É recomendável usar a senha gerada que você criou no último procedimento.
    * **Concordo com os termos e condições declarados acima**: selecione.
3. Selecione **Editar parâmetros** na parte superior para dar uma olhada do modelo.
4. Navegue até a linha 28 do arquivo JSON do modelo. Essa é a definição de recurso Key Vault.
5. Navegue até a linha 35:

    ```json
    "enabledForTemplateDeployment": true,
    ```
    `enabledForTemplateDeployment` é uma propriedade de Key Vault. Essa propriedade deve ser verdadeira antes que você possa recuperar os segredos do Key Vault durante a implantação.
6. Navegue até a linha 89. Essa é a definição de segredo do Key Vault.
7. Selecione **Descartar** na parte inferior da página. Você não fez nenhuma alteração.
8. Verifique se você forneceu todos os valores conforme mostrado na captura de tela anterior e, em seguida, clique em **Comprar** na parte inferior da página.
9. Selecione o ícone de sino (notificação) na parte superior da página para abrir o painel **Notificações**. Aguarde até o recurso ser implantado com êxito.
10. Selecione **Ir para o grupo de recursos** no painel **Notificação**. 
11. Selecione o nome do Key Vault para abri-lo.
12. Selecione **Políticas de acesso** no painel esquerdo. Seu nome (Active Directory) deverá estar listado, caso contrário, você não terá permissão para acessar o Key Vault.
13. Selecione **Clique para exibir as políticas de acesso avançado**. Observe que **Habilitar acesso ao Azure Resource Manager para implantação de modelo** está selecionado. Essa é outra condição para fazer a integração do Key Vault funcionar.

    ![Políticas de acesso integração do Key Vault do modelo do Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-key-vault-access-policies.png)
14. Selecione **Propriedades** no painel esquerdo.
15. Faça uma cópia da **ID do Recurso**. Quando você implantar a máquina virtual, precisará dessa ID.  O formato da ID do Recurso é:

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
    Substitua **id** pela ID do recurso do seu Key Vault criado no último procedimento.  

    ![integrar o cofre de chaves e o arquivo de parâmetros de implantação de máquina virtual do modelo do Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Atribua valores a:

    * **adminUsername**: nome da conta de administrador da máquina virtual.
    * **dnsLabelPrefix**: dê um nome a dnsLabelPrefix.
4. Salve as alterações.

## <a name="deploy-the-template"></a>Implantar o modelo

Siga as instruções em [Implantar o modelo](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) para implantar o modelo. Você precisa fazer upload de **azuredeploy. JSON** e de **azuredeploy.parameters.json** para o Cloud Shell e, em seguida, usar o seguinte script do PowerShell para implantar o modelo:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name of the Key Vault"
$deploymentName = Read-Host -Prompt "Enter the name for this deployment"
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $resourceGroupName `
    -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
```

Quando você implantar o modelo, use o mesmo grupo de recursos que o Key Vault. Torna mais fácil quando você limpa os recursos. Você só precisa excluir um grupo de recursos, em vez de dois.

## <a name="valid-the-deployment"></a>Validar a implantação

Depois de ter implantado com êxito a máquina virtual, teste o logon usando a senha armazenada no Key Vault.

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Selecione **Grupos de Recursos**/**YourResourceGroupName>**/**simpleWinVM**
3. Selecione **Conectar** da parte superior.
4. Selecione **Baixar arquivo RDP** e, em seguida, siga as instruções para entrar na máquina virtual usando a senha armazenada no Key Vault.

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
