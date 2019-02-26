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
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: c5bf56482534a55d24d8ca043e36c39cec99b1f0
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267522"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Tutorial: Criar modelos do Azure Resource Manager com recursos dependentes

Saiba como criar um modelo do Azure Resource Manager para implantar vários recursos.  Depois de criar o modelo, você pode implantá-lo usando o Cloud Shell no portal do Azure.

Neste tutorial, você criará uma conta de armazenamento, uma máquina virtual, uma rede virtual e alguns outros recursos dependentes. Alguns recursos não podem ser implantados até que outro recurso exista. Por exemplo, não é possível criar a máquina virtual enquanto a conta de armazenamento e o adaptador de rede não existirem. Defina essa relação marcando um recurso como dependente dos outros. O Gerenciador de Recursos avalia as dependências entre os recursos e os implanta na ordem de dependência. Quando os recursos não dependem uns dos outros, o Gerenciador de Recursos os implanta paralelamente. Para obter mais informações, consulte [Definir a ordem de implantação dos recursos em modelos do Azure Resource Manager](./resource-group-define-dependencies.md).

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início Rápido
> * Explorar o modelo
> * Implantar o modelo

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* [Visual Studio Code](https://code.visualstudio.com/) com a extensão de Ferramentas do Resource Manager.  Confira [Instalar a extensão](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Para aumentar a segurança, use uma senha gerada para a conta de administrador da máquina virtual. Veja um exemplo para gerar uma senha:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    O Azure Key Vault é projetado para proteger chaves de criptografia e outros segredos. Para obter mais informações, confira [Tutorial: Integrar o Azure Key Vault na implantação de Modelo do Resource Manager](./resource-manager-tutorial-use-key-vault.md). Também recomendamos que você atualize sua senha a cada três meses.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Modelos de Início Rápido do Azure é um repositório de modelos do Gerenciador de Recursos. Em vez de criar um modelo do zero, você pode encontrar um exemplo de modelo e personalizá-lo. O modelo usado neste tutorial é chamado [Implantar uma VM Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. No Visual Studio Code, escolha **Arquivo**>**Abrir Arquivo**.
2. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Escolha **Abrir** para abrir o arquivo.
4. Selecione **Arquivo**>**Salvar como** para salvar uma cópia do arquivo no computador local com o nome **azuredeploy.json**.

## <a name="explore-the-template"></a>Explorar o modelo

Ao explorar o modelo nesta seção, tente responder a essas perguntas:

* Quantos recursos do Azure estão definidos nesse modelo?
* Um dos recursos é uma conta de armazenamento do Azure.  A definição se parece com a usada no último tutorial?
* Você consegue encontrar as referências de modelo para os recursos definidos neste modelo?
* Você consegue encontrar as dependências dos recursos?

1. No Visual Studio Code, recolha os elementos até ver apenas os elementos de primeiro nível e os elementos de segundo nível dentro dos **recursos**:

    ![Modelos do Azure Resource Manager no Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Há cinco recursos definidos pelo modelo:

    * `Microsoft.Storage/storageAccounts`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
    * `Microsoft.Network/publicIPAddresses`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
    * `Microsoft.Network/virtualNetworks`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
    * `Microsoft.Network/networkInterfaces`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
    * `Microsoft.Compute/virtualMachines`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    É útil ter algumas noções básicas do modelo antes de personalizá-lo.

2. Expanda o primeiro recurso. Trata-se de uma conta de armazenamento. Compare a definição de recurso com a [referência de modelo](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).

    ![Definição da conta de armazenamento do Azure Resource Manager no Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Expanda o segundo recurso. O tipo de recurso é `Microsoft.Network/publicIPAddresses`. Compare a definição de recurso com a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).

    ![Definição do endereço IP do Azure Resource Manager no Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. Expanda o quarto recurso. O tipo de recurso é `Microsoft.Network/networkInterfaces`:  

    ![DependsOn dos modelos do Azure Resource Manager no Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    O elemento dependsOn permite definir um recurso como dependente de um ou mais recursos. O recurso depende de dois outros recursos:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

5. Expanda o quinto recurso. Esse recurso é uma máquina virtual. Depende de dois outros recursos:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

O diagrama a seguir ilustra os recursos e as informações de dependência para o modelo:

![Diagrama dos modelos do Azure Resource Manager no Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Ao especificar as dependências, o Gerenciador de Recursos implanta a solução eficientemente. Ele implanta a conta de armazenamento, o endereço IP público e a rede virtual em paralelo porque eles não têm dependências. Depois que o endereço IP público e a rede virtual são implantados, o adaptador de rede é criado. Quando todos os outros recursos são implantados, o Gerenciador de Recursos implanta a máquina virtual.

## <a name="deploy-the-template"></a>Implantar o modelo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Há muitos métodos para implantar modelos.  Neste tutorial, você usa o Cloud Shell no portal do Azure.

1. Entrar no [Cloud Shell](https://shell.azure.com). 
2. Selecione **PowerShell** no canto superior esquerdo do Cloud Shell e, em seguida, selecione **Confirmar**.  Use o PowerShell neste tutorial.
3. Selecione **Carregar arquivo** no Cloud Shell:

    ![Cloud Shell no portal do Azure carregar arquivo](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. Escolha o modelo que você salvou anteriormente no tutorial. O nome padrão é **azuredeploy.json**.  Se você tiver um arquivo com o mesmo nome de arquivo, o arquivo antigo será substituído sem nenhuma notificação.
5. No Cloud Shell, execute o comando a seguir para verificar se o arquivo foi carregado com êxito. 

    ```bash
    ls
    ```

    ![Cloud Shell no portal do Azure listar arquivo](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    O nome de arquivo mostrado na captura de tela é azuredeploy.json.

6. No Cloud Shell, execute o seguinte comando para verificar o conteúdo do arquivo JSON:

    ```bash
    cat azuredeploy.json
    ```
7. No Cloud Shell, execute os seguintes comandos do PowerShell. Para aumentar a segurança, use uma senha gerada para a conta de administrador da máquina virtual. Consulte [Pré-requisitos](#prerequisites).

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile azuredeploy.json
    ```

    > [!NOTE]
    > Há um problema de E/S de arquivo com o uso do Azure PowerShell no Cloud Shell.  A mensagem de erro é *Não é possível recuperar os parâmetros dinâmicos para o cmdlet. Não é possível encontrar o caminho 'Azure:/azuredeploy.json' porque ele não existe.*  Uma solução alternativa temporária é não incluir a opção **-TemplateFile** no comando `New-AzResourceGroupDeploy`. O comando solicitará que você insira o nome do arquivo.

8. Execute o seguinte comando do PowerShell para listar a máquina virtual criada recentemente:

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    Get-AzVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    O nome da máquina virtual é codificado como **SimpleWinVM** dentro do modelo.

9. RDP para a máquina virtual para verificar se a máquina virtual foi criada com êxito.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Escolha o nome do grupo de recursos.  Você deverá ver um total de seis recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você desenvolveu e implantou um modelo para criar uma máquina virtual, uma rede virtual e os recursos dependentes. Para saber como implantar recursos do Azure com base em condições, veja:

> [!div class="nextstepaction"]
> [Condições de uso](./resource-manager-tutorial-use-conditions.md)
