---
title: Implantar extensões de máquina virtual do Azure com modelos do Azure Resource Manager | Microsoft Docs
description: Aprenda a implantar extensões de máquina virtual do Azure com modelos do Azure Resource Manager
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7e0b3fff0ed60d5eb77194e7f9081d35f2e38571
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869630"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>Tutorial: Implantar extensões de máquina virtual do Azure com modelos do Azure Resource Manager

Saiba como usar [extensões de máquina virtual do Azure](../virtual-machines/extensions/features-windows.md) para executar tarefas de configuração e automação pós-implantação em VMs do Azure. Muitas extensões de VM diferentes estão disponíveis para uso com as VMs do Azure. Neste tutorial, você implantará uma extensão de Script Personalizado de um modelo do Resource Manager para executar um script do PowerShell em uma VM Windows.  O script instala o servidor Web na VM.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um script do PowerShell
> * Abrir um modelo de Início Rápido
> * Editar o modelo
> * Implantar o modelo
> * Verificar a implantação

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* [Visual Studio Code](https://code.visualstudio.com/) com a extensão de Ferramentas do Resource Manager.  Confira [Instalar a extensão](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Para aumentar a segurança, use uma senha gerada para a conta de administrador da máquina virtual. Veja um exemplo para gerar uma senha:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    O Azure Key Vault é projetado para proteger chaves de criptografia e outros segredos. Para obter mais informações, confira [Tutorial: Integrar o Azure Key Vault na implantação de modelo do Gerenciador de Recursos](./resource-manager-tutorial-use-key-vault.md). Também recomendamos que você atualize sua senha a cada três meses.

## <a name="prepare-a-powershell-script"></a>Preparar um script do PowerShell

Um script do PowerShell com o seguinte conteúdo é compartilhado de uma [conta do Armazenamento do Azure com acesso público](https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Se você optar por publicar o arquivo em seu próprio local, deverá atualizar o elemento [fileUri] no modelo posteriormente no tutorial.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Modelos de Início Rápido do Azure é um repositório de modelos do Gerenciador de Recursos. Em vez de criar um modelo do zero, você pode encontrar um exemplo de modelo e personalizá-lo. O modelo usado neste tutorial é chamado [Implantar uma VM Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. No Visual Studio Code, escolha **Arquivo**>**Abrir Arquivo**.
2. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Escolha **Abrir** para abrir o arquivo.
4. Há cinco recursos definidos pelo modelo:

    * `Microsoft.Storage/storageAccounts`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
    * `Microsoft.Network/publicIPAddresses`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
    * `Microsoft.Network/virtualNetworks`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
    * `Microsoft.Network/networkInterfaces`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
    * `Microsoft.Compute/virtualMachines`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    É útil ter algumas noções básicas do modelo antes de personalizá-lo.
5. Selecione **Arquivo**>**Salvar como** para salvar uma cópia do arquivo no computador local com o nome **azuredeploy.json**.

## <a name="edit-the-template"></a>Editar o modelo

Adicione um recurso de extensão de máquina virtual ao modelo existente com o seguinte conteúdo:

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "autoUpgradeMinorVersion":true,
        "settings": {
            "fileUris": [
                "https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1"
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
        }
    }
}
```

Confira a [referência de extensão](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions) se você precisar de mais informações sobre essa definição de recurso. Abaixo estão alguns elementos importantes:

* **nome**: como o recurso de extensão é um recurso filho do objeto de máquina virtual, o nome deve ter o prefixo do nome da máquina virtual. Confira [Recursos filho](./resource-manager-templates-resources.md#child-resources).
* **dependsOn**: o recurso de extensão deve ser criado depois que a máquina virtual foi criada.
* **fileUris**: esses são os locais onde os arquivos de script ficam armazenados. Se você optar por não usar o fornecido, precisará atualizar os valores.
* **commandToExecute**: esse é o comando para invocar o script.  

## <a name="deploy-the-template"></a>Implantar o modelo

Consulte a seção [Implantar o modelo](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) para obter o procedimento de implantação. É recomendável usar uma senha gerada para a conta de administrador da máquina virtual. Consulte [Pré-requisitos](#prerequisites).

## <a name="verify-the-deployment"></a>Verificar a implantação

No portal, selecione a VM e, na visão geral da VM, use **Clique para copiar** à direita do endereço IP para copiá-lo e colá-lo em uma guia do navegador. A página de boas-vinda do IIS padrão será aberta e deverá ter esta aparência:

![O Azure Resource Manager implanta o servidor Web IIS do script do cliente de extensões de VM](./media/resource-manager-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Escolha o nome do grupo de recursos.  Você deverá ver um total de seis recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou uma máquina virtual e uma extensão da máquina virtual. A extensão instalou o servidor Web do IIS na máquina virtual. Para saber como usar a extensão do Banco de Dados SQL para importar um arquivo BACPAC, confira:

> [!div class="nextstepaction"]
> [](./resource-manager-tutorial-deploy-vm-extensions.md)
