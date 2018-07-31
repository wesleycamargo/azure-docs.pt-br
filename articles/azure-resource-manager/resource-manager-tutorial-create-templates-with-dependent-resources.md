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
ms.date: 07/20/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: bd559cb9f0140706a4b9735c642367e03616a14d
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188158"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Tutorial: Criar modelos do Azure Resource Manager com recursos dependentes

Saiba como criar um modelo do Azure Resource Manager para implantar vários recursos.  Depois de criar o modelo, você pode implantá-lo usando o Cloud Shell no portal do Azure.

Alguns recursos não podem ser implantados até que outro recurso exista. Por exemplo, não é possível criar a máquina virtual enquanto a conta de armazenamento e o adaptador de rede não existirem. Defina essa relação marcando um recurso como dependente dos outros. O Gerenciador de Recursos avalia as dependências entre os recursos e os implanta na ordem de dependência. Quando os recursos não dependem uns dos outros, o Gerenciador de Recursos os implanta paralelamente. Para obter mais informações, consulte [Definir a ordem de implantação dos recursos em modelos do Azure Resource Manager](./resource-group-define-dependencies.md).

> [!div class="checklist"]
> * Abrir um modelo de início rápido
> * Explorar o modelo
> * Implantar o modelo
> * Limpar recursos

As instruções neste tutorial criam uma máquina virtual, uma rede virtual e outros recursos dependentes. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* [Visual Studio Code](https://code.visualstudio.com/).
* Extensão das Ferramentas do Gerenciador de Recursos.  Confira [Instalar a extensão](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

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

1. No Visual Studio Code, recolha os elementos até ver apenas os elementos de primeiro nível e os elementos de segundo nível dentro dos **recursos**:

    ![Modelos do Azure Resource Manager no Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Há cinco recursos definidos pelo modelo.
2. Expanda o quarto elemento:

    ![DependsOn dos modelos do Azure Resource Manager no Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    O elemento dependsOn permite definir um recurso como dependente de um ou mais recursos. Neste exemplo, o recurso é um adaptador de rede.  Depende de dois outros recursos:

    * publicIPAddress
    * virtualNetwork

3. Expanda o quinto elemento. Esse recurso é uma máquina virtual. Depende de dois outros recursos:

    * storageAccount
    * networkInterface

O diagrama a seguir ilustra os recursos e as informações de dependência para o modelo:

![Diagrama dos modelos do Azure Resource Manager no Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Ao especificar as dependências, o Gerenciador de Recursos implanta a solução eficientemente. Ele implanta a conta de armazenamento, o endereço IP público e a rede virtual em paralelo porque eles não têm dependências. Depois que o endereço IP público e a rede virtual são implantados, o adaptador de rede é criado. Quando todos os outros recursos são implantados, o Gerenciador de Recursos implanta a máquina virtual.

## <a name="deploy-the-template"></a>Implantar o modelo

Há muitos métodos para implantar modelos.  Neste tutorial, você usa o Cloud Shell no portal do Azure.

1. Entre no [Portal do Azure](https://portal.azure.com)
2. Escolha **Cloud Shell** no canto superior direito, conforme mostrado na imagem a seguir:

    ![Cloud Shell no portal do Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
3. Selecione **PowerShell** no canto superior esquerdo do Cloud Shell.  Use o PowerShell neste tutorial.
4. Selecione **Reiniciar**
5. Selecione **Carregar arquivo** no Cloud Shell:

    ![Cloud Shell no portal do Azure carregar arquivo](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
6. Escolha o arquivo que você salvou anteriormente no tutorial. O nome padrão é **azuredeploy.json**.  Se você tiver um arquivo com o mesmo nome de arquivo, o arquivo antigo será substituído sem nenhuma notificação.
7. No Cloud Shell, execute o comando a seguir para verificar se o arquivo foi carregado com êxito. 

    ```shell
    ls
    ```

    ![Cloud Shell no portal do Azure listar arquivo](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    O nome de arquivo mostrado na captura de tela é azuredeploy.json.

8. No Cloud Shell, execute o seguinte comando para verificar o conteúdo do arquivo JSON:

    ```shell
    cat azuredeploy.json
    ```
9. No Cloud Shell, execute os seguintes comandos do PowerShell:

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $location = "<Enter the Azure location>"
    $vmAdmin = "<Enter the admin username>"
    $vmPassword = "<Enter the password>"
    $dnsLabelPrefix = "<Enter the prefix>"

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    $vmPW = ConvertTo-SecureString -String $vmPassword -AsPlainText -Force
    New-AzureRmResourceGroupDeployment -Name mydeployment0710 -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json -adminUsername $vmAdmin -adminPassword $vmPW `
        -dnsLabelPrefix $dnsLabelPrefix
    ```
    Veja uma captura de tela de uma implantação de exemplo:

    ![Cloud Shell no portal do Azure implantar modelo](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-deploy-template.png)

    Na captura de tela, esses valores são usados:

    * **$resourceGroupName**: myresourcegroup0710. 
    * **$Location**: eastus2
    * **&lt;DeployName>**: mydeployment0710
    * **&lt;TemplateFile>**: azuredeploy.json
    * **Parâmetros de modelo**:

        * **adminUsername**: JohnDole
        * **adminPassword**: Pass@word123
        * **dnsLabelPrefix**: myvm0710

10. Execute o seguinte comando do PowerShell para listar a máquina virtual criada recentemente:

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName <ResourceGroupName>
    ```

    O nome da máquina virtual é codificado como **SimpleWinVM** dentro do modelo.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Escolha o nome do grupo de recursos.  Você deverá ver um total de seis recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você desenvolve e implanta um modelo para criar uma máquina virtual, uma rede virtual e os recursos dependentes. Para saber mais sobre modelos, confira [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](./resource-group-authoring-templates.md).