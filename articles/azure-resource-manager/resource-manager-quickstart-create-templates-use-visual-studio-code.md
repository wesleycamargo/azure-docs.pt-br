---
title: Usar o Visual Studio Code para criar modelos do Azure Resource Manager | Microsoft Docs
description: Use a extensão de ferramentas do Visual Studio Code e do Azure Resource Manager para trabalhar em modelos do Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 08/24/2018
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 540aabc9164e43776d2166926430f4512dd23f49
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43106042"
---
# <a name="quickstart-create-azure-resource-manager-templates-by-using-visual-studio-code"></a>Início Rápido: criar modelos do Azure Resource Manager usando o Visual Studio Code

Aprenda a criar modelos do Azure Resource Manager usando o Visual Studio Code e a extensão das Ferramentas do Azure Resource Manager. Você pode criar modelos do Gerenciador de Recursos no Visual Studio Code sem a extensão, mas a extensão fornece opções de preenchimento automático que simplificam o desenvolvimento do modelo. Para entender os conceitos associados à implantação e ao gerenciamento de soluções do Azure, consulte [Visão geral do Azure Resource Manager](resource-group-overview.md).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

- [Visual Studio Code](https://code.visualstudio.com/).
- Extensão das Ferramentas do Gerenciador de Recursos. Para instalar, use estas etapas:

    1. Abra o Visual Studio Code.
    2. Pressione **CTRL+SHIFT+X** para abrir o painel Extensões
    3. Procure **Ferramentas do Azure Resource Manager** e escolha **Instalar**.
    4. Para concluir a instalação da extensão, escolha **Recarregar**.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Em vez de criar um modelo do zero, você pode abrir um modelo de [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/). Modelos de Início Rápido do Azure é um repositório de modelos do Gerenciador de Recursos.

O modelo usado neste início rápido é chamado [Criar uma conta de armazenamento padrão](https://azure.microsoft.com/resources/templates/101-storage-account-create/). O modelo define um recurso da conta de Armazenamento do Azure.

1. No Visual Studio Code, escolha **Arquivo**>**Abrir Arquivo**.
2. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Escolha **Abrir** para abrir o arquivo.
4. Escolha **Arquivo**>**Salvar como** para salvar o arquivo como **azuredeploy.json** em seu computador local.

## <a name="edit-the-template"></a>Editar o modelo

Para saber como editar um modelo usando o Visual Studio Code, adicione mais um elemento à seção de saídas.

1. No Visual Studio Code, adicione mais uma saída ao modelo exportado:

    ```json
    "storageUri": {
      "type": "string",
      "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
    }
    ```

    Quando terminar, a seção de saídas terá essa aparência:

    ```json
    "outputs": {
      "storageAccountName": {
        "type": "string",
        "value": "[variables('storageAccountName')]"
      },
      "storageUri": {
        "type": "string",
        "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

    Se você tiver copiado e colado o código dentro do Visual Studio Code, tente digitar novamente o elemento **value** para experimentar a funcionalidade do intellisense da extensão das Ferramentas do Resource Manager.

    ![Intellisense do Visual Studio Code do modelo do Gerenciador de Recursos](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. Escolha **Arquivo**>**Salvar** para salvar o arquivo.

## <a name="deploy-the-template"></a>Implantar o modelo

Há muitos métodos para implantar modelos.  Neste início rápido, use o Azure Cloud Shell no portal do Azure. O Cloud Shell oferece suporte à CLI do Azure e ao Azure PowerShell. 

1. Entre no [Portal do Azure](https://portal.azure.com)
2. Escolha **Cloud Shell** no canto superior direito, conforme mostrado na imagem a seguir:

    ![Cloud Shell no portal do Azure](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell.png)

    O Cloud Shell é aberto na parte inferior da tela.

3. No canto superior esquerdo do Cloud Shell, é exibido **PowerShell** ou **Bash**. Para usar a CLI, você precisa abrir uma sessão do Bash. Para executar o PowerShell, você precisará abrir uma sessão do PowerShell. Para alternar, selecione a seta para baixo e depois selecione o interpretador. A imagem a seguir mostra como alternar do PowerShell ao Bash.

    ![CLI do Cloud Shell no portal do Azure](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)

    Ao alternar, é necessário reiniciar o shell.
4. Escolha **Carregar/fazer o download dos arquivos** e, em seguida, escolha **Carregar**.

    ![Cloud Shell no portal do Azure carregar arquivo](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)

    É preciso carregar o arquivo de modelo antes de implantá-lo a partir do shell.
5. Escolha o arquivo que você salvou anteriormente no início rápido. O nome padrão é **azuredeploy.json**.
6. No Cloud Shell, execute o comando **ls** para verificar se o arquivo foi carregado com êxito. Você também pode usar o comando **cat** para verificar o conteúdo do modelo. A imagem a seguir mostra como executar o comando a partir do Bash.  Você usa os mesmos comandos em uma sessão do PowerShell.

    ![Cloud Shell no portal do Azure listar arquivo](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)
7. No Cloud Shell, execute os seguintes comandos. Selecione a guia para mostrar o código do PowerShell ou o código da CLI.

    # <a name="clitabcli"></a>[CLI](#tab/CLI)
    ```cli
    az group create --name <ResourceGroupName> --location <AzureLocation>

    az group deployment create --name <DeploymentName> --resource-group <ResourceGroupName> --template-file <TemplateFileName>
    ```
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ```powershell
    New-AzureRmResourceGroup -Name <ResourceGroupName> -Location <AzureLocation>

    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroupName> -TemplateFile <TemplateFileName>
    ```
    
    ---

    A captura de tela a seguir mostra uma implantação da CLI de amostra:

    ![Cloud Shell no portal do Azure implantar modelo](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)

    Na captura de tela, esses valores são usados:

    - **&lt;ResourceGroupName>**: myresourcegroup0709. Há dois aspectos do parâmetro.  Certifique-se de usar o mesmo valor.
    - **&lt;AzureLocation>**: eastus2
    - **&lt;DeployName>**: mydeployment0709
    - **&lt;TemplateFile>**: azuredeploy.json

    Na saída da captura de tela, o nome da conta de armazenamento é *3tqebj3slyfyestandardsa*. 

7. Execute o seguinte comando da CLI ou do PowerShell para listar as contas de armazenamento criadas recentemente:

    # <a name="clitabcli"></a>[CLI](#tab/CLI)
    ```cli
    az storage account show --resource-group <ResourceGroupName> --name <StorageAccountName>
    ```
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ```powershell
    Get-AzureRmStorageAccount -ResourceGroupName <ResourceGroupName> -Name <StorageAccountName>
    ```
    
    ---

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Escolha o nome do grupo de recursos.  Você deverá ver um total de seis recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

O foco principal deste tutorial é usar o Visual Studio Code para editar um modelo existente a partir de modelos de Início Rápido do Azure. Você também aprendeu como implantar o modelo usando a CLI ou o PowerShell a partir do Azure Cloud Shell. Os modelos do Início Rápido do Azure podem não oferecer tudo o que você precisa. O próximo tutorial mostra como localizar as informações de referência de modelo para que você possa criar uma conta de armazenamento criptografada do Azure.

> [!div class="nextstepaction"]
> [Criar uma conta de armazenamento criptografada](./resource-manager-tutorial-create-encrypted-storage-accounts.md)
