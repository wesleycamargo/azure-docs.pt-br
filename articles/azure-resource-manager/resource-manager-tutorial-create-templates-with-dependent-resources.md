---
title: Criar modelos do Azure Resource Manager com recursos dependentes | Microsoft Docs
description: Saiba como criar um modelo do Azure Resource Manager com vários recursos e como implantá-lo usando o portal do Azure
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/09/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 50f1c81f08787181de2fe3a9f6fb97a96a2bd882
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114305"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Tutorial: criar modelos do Azure Resource Manager com recursos dependentes

Saiba como criar um modelo do Azure Resource Manager para implantar vários recursos.  Depois de criar o modelo, você pode implantá-lo usando o Cloud Shell no portal do Azure.

Neste tutorial, você criará uma conta de armazenamento, uma máquina virtual, uma rede virtual e alguns outros recursos dependentes. Alguns recursos não podem ser implantados até que outro recurso exista. Por exemplo, não é possível criar a máquina virtual enquanto a conta de armazenamento e o adaptador de rede não existirem. Defina essa relação marcando um recurso como dependente dos outros. O Gerenciador de Recursos avalia as dependências entre os recursos e os implanta na ordem de dependência. Quando os recursos não dependem uns dos outros, o Gerenciador de Recursos os implanta paralelamente. Para obter mais informações, consulte [Definir a ordem de implantação dos recursos em modelos do Azure Resource Manager](./resource-group-define-dependencies.md).

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Preparar o Key Vault
> * Abrir um modelo de início rápido
> * Explorar o modelo
> * Edite o arquivo de parâmetros
> * Implantar o modelo

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* [Visual Studio Code](https://code.visualstudio.com/) com a extensão de Ferramentas do Resource Manager.  Confira [Instalar a extensão](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="prepare-key-vault"></a>Preparar o Key Vault

Para evitar ataques de pulverização de senha, é recomendável usar uma senha gerada automaticamente para a conta de administrador da máquina virtual e usar o Key Vault para armazenar a senha. O procedimento a seguir cria um Key Vault e um segredo para armazenar a senha. Ele também configura as permissões necessárias para a implantação de modelo acessar o segredo armazenado no Key Vault. As políticas de acesso adicionais são necessárias se o Key Vault estiver em uma assinatura diferente do Azure. Para obter detalhes, confira [Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](./resource-manager-keyvault-parameter.md).

1. Entrar no [Azure Cloud Shell](https://shell.azure.com).
2. Alterne para seu ambiente favorito, **PowerShell** ou **Bash** no canto superior esquerdo.
3. Execute o seguinte comando do Azure PowerShell ou da CLI do Azure.  

    ```azurecli-interactive
    keyVaultName='<your-unique-vault-name>'
    resourceGroupName='<your-resource-group-name>'
    location='Central US'
    userPrincipalName='<your-email-address-associated-with-your-subscription>'
    
    # Create a resource group
    az group create --name $resourceGroupName --location $location
    
    # Create a Key Vault
    keyVault=$(az keyvault create \
      --name $keyVaultName \
      --resource-group $resourceGroupName \
      --location $location \
      --enabled-for-template-deployment true)
    keyVaultId=$(echo $keyVault | jq -r '.id')
    az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

    # Create a secret
    password=$(openssl rand -base64 32)
    az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: $keyVaultId."
    ```

    ```azurepowershell-interactive
    $keyVaultName = "<your-unique-vault-name>"
    $resourceGroupName="<your-resource-group-name>"
    $location='Central US'
    $userPrincipalName="<your-email-address-associated-with-your-subscription>"
    
    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
        
    # Create a Key Vault
    $keyVault = New-AzureRmKeyVault `
      -VaultName $keyVaultName `
      -resourceGroupName $resourceGroupName `
      -Location $location `
      -EnabledForTemplateDeployment
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list
      
    # Create a secret
    $password = openssl rand -base64 32
    
    $secretValue = ConvertTo-SecureString $password -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretValue
    
    # Print the useful property values
    echo "You need the following values for the virtual machine deployment:"
    echo "Resource group name is: $resourceGroupName."
    echo "The admin password is: $password."
    echo "The Key Vault resource ID is: " $keyVault.ResourceID
    ```
4. Anote os valores de saída. Você precisará deles mais tarde no tutorial

> [!NOTE]
> Cada serviço do Azure tem requisitos de senha específicos. Por exemplo, os requisitos da máquina virtual do Azure podem ser encontrados em Quais são os requisitos de senha ao criar uma VM?

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Modelos de Início Rápido do Azure é um repositório de modelos do Gerenciador de Recursos. Em vez de criar um modelo do zero, você pode encontrar um exemplo de modelo e personalizá-lo. O modelo usado neste tutorial é chamado [Implantar uma VM Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. No Visual Studio Code, escolha **Arquivo**>**Abrir Arquivo**.
2. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Escolha **Abrir** para abrir o arquivo.
4. Selecione **Arquivo**>**Salvar como** para salvar uma cópia do arquivo no computador local com o nome **azuredeploy.json**.
5. Repita as etapas de 1 a 4 para abrir **https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json** a seguir e, em seguida, salve o arquivo como **azuredeploy.parameters.json**.

## <a name="explore-the-template"></a>Explorar o modelo

Ao explorar o modelo nesta seção, tente responder a essas perguntas:

- Quantos recursos do Azure estão definidos nesse modelo?
- Um dos recursos é uma conta de armazenamento do Azure.  A definição se parece com a usada no último tutorial?
- Você consegue encontrar as referências de modelo para os recursos definidos neste modelo?
- Você consegue encontrar as dependências dos recursos?

1. No Visual Studio Code, recolha os elementos até ver apenas os elementos de primeiro nível e os elementos de segundo nível dentro dos **recursos**:

    ![Modelos do Azure Resource Manager no Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Há cinco recursos definidos pelo modelo.
2. Expanda o primeiro recurso. Trata-se de uma conta de armazenamento. A definição deverá ser idêntica à usada no início do último tutorial.

    ![Definição da conta de armazenamento do Azure Resource Manager no Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Expanda o segundo recurso. O tipo de recurso é **Microsoft.Network/publicIPAddresses**. Para encontrar a referência de modelo, navegue até a [referência de modelo](https://docs.microsoft.com/azure/templates/), insira o **endereço ip público** ou os **endereços ip públicos** no campo **Filtrar por título**. Compare a definição de recurso com a referência de modelo.

    ![Definição do endereço IP do Azure Resource Manager no Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. Repita a última etapa para encontrar as referências de modelo dos recursos definidos neste modelo.  Compare as definições de recurso com as referências.
5. Expanda o quarto recurso:

    ![DependsOn dos modelos do Azure Resource Manager no Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    O elemento dependsOn permite definir um recurso como dependente de um ou mais recursos. Neste exemplo, o recurso é um adaptador de rede.  Depende de dois outros recursos:

    * publicIPAddress
    * virtualNetwork

6. Expanda o quinto recurso. Esse recurso é uma máquina virtual. Depende de dois outros recursos:

    * storageAccount
    * networkInterface

O diagrama a seguir ilustra os recursos e as informações de dependência para o modelo:

![Diagrama dos modelos do Azure Resource Manager no Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Ao especificar as dependências, o Gerenciador de Recursos implanta a solução eficientemente. Ele implanta a conta de armazenamento, o endereço IP público e a rede virtual em paralelo porque eles não têm dependências. Depois que o endereço IP público e a rede virtual são implantados, o adaptador de rede é criado. Quando todos os outros recursos são implantados, o Gerenciador de Recursos implanta a máquina virtual.

## <a name="edit-the-parameters-file"></a>Edite o arquivo de parâmetros

Você não precisa fazer nenhuma alteração ao arquivo de modelo. Mas você precisa modificar o arquivo de parâmetros para recuperar a senha do administrador do Key Vault.

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
    Substitua **id** pela ID do recurso do seu Key Vault criado no último procedimento. Ela é uma das saídas. 

    ![integrar o cofre de chaves e o arquivo de parâmetros de implantação de máquina virtual do modelo do Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Atribua valores a:

    - **adminUsername**: nome da conta de administrador da máquina virtual.
    - **dnsLabelPrefix**: dê um nome a dnsLablePrefix.
4. Salve as alterações.

## <a name="deploy-the-template"></a>Implantar o modelo

Há muitos métodos para implantar modelos.  Neste tutorial, você usa o Cloud Shell no portal do Azure.

1. Entrar no [Cloud Shell](https://shell.azure.com). Você também pode entrar no [portal do Azure](https://portal.azure.com) e selecionar **Cloud Shell** no canto superior direito, conforme mostrado na imagem a seguir:

    ![Cloud Shell no portal do Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
2. Selecione **PowerShell** no canto superior esquerdo do Cloud Shell e, em seguida, selecione **Confirmar**.  Use o PowerShell neste tutorial.
3. Selecione **Carregar arquivo** no Cloud Shell:

    ![Cloud Shell no portal do Azure carregar arquivo](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. Selecione os arquivos que você salvou anteriormente no tutorial. O nome padrão é **azuredeploy.json** e **azuredeploy.paraemters.json**.  Se você tiver arquivos com os mesmos nomes de arquivo, o arquivo antigo será substituído sem nenhuma notificação.
5. No Cloud Shell, execute o comando a seguir para verificar se o arquivo foi carregado com êxito. 

    ```bash
    ls
    ```

    ![Cloud Shell no portal do Azure listar arquivo](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    O nome de arquivo mostrado na captura de tela é azuredeploy.json.

6. No Cloud Shell, execute o seguinte comando para verificar o conteúdo do arquivo JSON:

    ```bash
    cat azuredeploy.json
    cat azuredeploy.parameters.json
    ```
7. No Cloud Shell, execute os seguintes comandos do PowerShell. O script de exemplo usa o mesmo grupo de recursos criado para o Key Vault. Usar o mesmo grupo de recursos torna mais fácil limpar os recursos.

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $deploymentName = "<Enter a deployment name>"

    New-AzureRmResourceGroupDeployment -Name $deploymentName `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json `
        -TemplateparameterFile azuredeploy.parameters.json
    ```
8. Execute o seguinte comando do PowerShell para listar a máquina virtual criada recentemente:

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    O nome da máquina virtual é codificado como **SimpleWinVM** dentro do modelo.

9. Entre na máquina virtual para testar as credenciais do administrador. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Escolha o nome do grupo de recursos.  Você deverá ver um total de seis recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você desenvolve e implanta um modelo para criar uma máquina virtual, uma rede virtual e os recursos dependentes. Para saber como implantar recursos do Azure com base em condições, veja:


> [!div class="nextstepaction"]
> [Condições de uso](./resource-manager-tutorial-use-conditions.md)

