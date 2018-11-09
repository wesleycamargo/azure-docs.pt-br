---
title: Importar arquivos BACPAC do SQL com modelos do Azure Resource Manager | Microsoft Docs
description: Saiba como usar a extensão de Banco de Dados SQL para importar arquivos BACPAC do SQL com modelos do Azure Resource Manager
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/29/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 875662ec3bf4582c58f98e8afe3753fc202ad0b6
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243496"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Tutorial: Importar arquivos BACPAC do SQL com modelos do Azure Resource Manager

Saiba como usar extensões do Banco de Dados SQL do Azure para importar um arquivo BACPAC. Neste tutorial, você criará um modelo para implantar um Azure SQL Server, um Banco de Dados SQL e um arquivo BACPAC. Para obter informações sobre como implantar extensões de máquina virtual do Azure usando modelos do Azure Resource Manager, confira [# Tutorial: Implantar extensões de máquina virtual com modelos do Azure Resource Manager](./resource-manager-tutorial-deploy-vm-extensions.md).

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um arquivo BACPAC
> * Abrir um modelo de Início Rápido
> * Editar o modelo
> * Implantar o modelo
> * Verificar a implantação

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* [Visual Studio Code](https://code.visualstudio.com/) com a extensão de Ferramentas do Resource Manager. Confira [Instalar a extensão](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Para aumentar a segurança, use uma senha gerada para a conta de administrador do SQL Server. Veja um exemplo para gerar uma senha:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    O Azure Key Vault é projetado para proteger chaves de criptografia e outros segredos. Para obter mais informações, confira [Tutorial: Integrar o Azure Key Vault na implantação de modelo do Gerenciador de Recursos](./resource-manager-tutorial-use-key-vault.md). Também recomendamos que você atualize sua senha a cada três meses.

## <a name="prepare-a-bacpac-file"></a>Preparar um arquivo BACPAC

Um arquivo BACPAC é compartilhado em uma [Conta do Armazenamento do Azure com acesso público](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac). Para criar seus próprio arquivo, confira [Exportar um Banco de Dados SQL do Azure para um arquivo BACPAC](../sql-database/sql-database-export.md). Se você optar por publicar o arquivo em seu próprio local, deverá atualizar o modelo posteriormente no tutorial.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Modelos de Início Rápido do Azure é um repositório de modelos do Gerenciador de Recursos. Em vez de criar um modelo do zero, você pode encontrar um exemplo de modelo e personalizá-lo. O modelo usado neste tutorial tem o nome de [Implantar um Azure SQL Server com Detecção de Ameaças](https://azure.microsoft.com/resources/templates/201-sql-threat-detection-server-policy-optional-db/).

1. No Visual Studio Code, escolha **Arquivo**>**Abrir Arquivo**.
2. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-sql-threat-detection-server-policy-optional-db/azuredeploy.json
    ```
3. Escolha **Abrir** para abrir o arquivo.

    Há três recursos definidos no modelo:

    * `Microsoft.Sql/servers`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
    * `Microsoft.SQL/servers/securityAlertPolicies`. Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/securityalertpolicies).
    * `Microsoft.SQL.servers/databases`.  Consulte a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
    É útil ter algumas noções básicas do modelo antes de personalizá-lo.
4. Selecione **Arquivo**>**Salvar como** para salvar uma cópia do arquivo no computador local com o nome **azuredeploy.json**.

## <a name="edit-the-template"></a>Editar o modelo

Você precisa adicionar outros dois recursos ao modelo.

* Para permitir que a extensão do banco de dados SQL importe arquivos BACPAC, você precisará permitir o acesso a serviços do Azure. Adicione o seguinte JSON à definição do SQL Server:

    ```json
    {
        "type": "firewallrules",
        "name": "AllowAllAzureIps",
        "location": "[parameters('location')]",
        "apiVersion": "2014-04-01",
        "dependsOn": [
            "[variables('databaseServerName')]"
        ],
        "properties": {
            "startIpAddress": "0.0.0.0",
            "endIpAddress": "0.0.0.0"
        }
    }
    ```

    O modelo ficará mais ou menos assim:

    ![O Azure Resource Manager implanta extensões de SQL BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

* Adicione um recurso de extensão do Banco de Dados SQL à definição de banco de dados com o seguinte JSON:

    ```json
    "resources": [
        {
            "name": "Import",
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/databases', variables('databaseServerName'), variables('databaseName'))]"
            ],
            "properties": {
                "storageKeyType": "SharedAccessKey",
                "storageKey": "?",
                "storageUri": "https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac",
                "administratorLogin": "[variables('databaseServerAdminLogin')]",
                "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
                "operationMode": "Import",
            }
        }
    ]
    ```

    O modelo ficará mais ou menos assim:

    ![O Azure Resource Manager implanta extensões de SQL BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

    Para entender a definição de recurso, confira a [referência de extensão de Banco de Dados SQL](https://docs.microsoft.com/en-us/azure/templates/microsoft.sql/servers/databases/extensions). Abaixo estão alguns elementos importantes:

    * **dependsOn**: o recurso de extensão deve ser criado depois que o banco de dados SQL foi criado.
    * **storageKeyType**: o tipo de chave de armazenamento a ser usado. O valor pode ser `StorageAccessKey` ou `SharedAccessKey`. Como o arquivo BACPAC fornecido é compartilhado em uma conta do Armazenamento do Azure com acesso público, 'SharedAccessKey' é usado aqui.
    * **storageKey**: a chave de armazenamento a ser usada. Se o tipo de chave de armazenamento é SharedAccessKey, ele deve ser precedido por um "?."
    * **storageUri**: o URI de armazenamento a ser usado. Se você optar por não usar o arquivo BACPAC fornecido, precisará atualizar os valores.
    * **administratorLoginPassword**: a senha do administrador do SQL. É recomendável usar uma senha gerada. Consulte [Pré-requisitos](#prerequisites).

## <a name="deploy-the-template"></a>Implantar o modelo

Consulte a seção [Implantar o modelo](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) para obter o procedimento de implantação. Use o seguinte script de implantação do PowerShell:

```azurepowershell
$deploymentName = Read-Host -Prompt "Enter the name for this deployment"
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmResourceGroupDeployment -Name $deploymentName `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile azuredeploy.json
```

É recomendável usar uma senha gerada. Consulte [Pré-requisitos](#prerequisites).

## <a name="verify-the-deployment"></a>Verificar a implantação

No portal, selecione o banco de dados SQL no grupo de recursos implantado recentemente. Selecione **Editor de consultas (versão prévia)** e insira as credenciais de administrador. Você deverá ver duas tabelas importadas para o banco de dados:

![O Azure Resource Manager implanta extensões de SQL BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Escolha o nome do grupo de recursos.  Você deverá ver um total de seis recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou um SQL Server e um Banco de Dados SQL e importou um arquivo BACPAC. Para saber como implantar recursos do Azure em várias regiões e como usar práticas de implantação seguras, consulte

> [!div class="nextstepaction"]
> [Usar o Gerenciador de Implantação do Azure](./resource-manager-tutorial-deploy-vm-extensions.md)
