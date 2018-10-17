---
title: Criar modelos do Azure Resource Manager vinculados | Microsoft Docs
description: Saiba como criar modelos do Azure Resource Manager vinculados para criar máquinas virtuais
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/07/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d25ca14b78465a6c4fec7e90bc20436e3ca553fc
ms.sourcegitcommit: 3150596c9d4a53d3650cc9254c107871ae0aab88
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47419620"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Tutorial: Criar modelos do Azure Resource Manager vinculados

Aprenda a criar modelos do Azure Resource Manager vinculados. Usando modelos vinculados, você pode fazer com que um modelo chame outro. Isso é ótimo para modularizar modelos. Neste tutorial, você usa o mesmo modelo usado no [Tutorial: criar várias instâncias de recursos usando modelos do Resource Manager](./resource-manager-tutorial-create-multiple-instances.md), que cria uma máquina virtual, uma rede virtual e outros recursos dependentes, incluindo uma conta de armazenamento. Você separa o recurso de conta de armazenamento para um modelo vinculado.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de início rápido
> * Criar o modelo vinculado
> * Carregar o modelo vinculado
> * Link para o modelo vinculado
> * Configurar dependência
> * Obter valores de modelos vinculados
> * Implantar o modelo

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* [Visual Studio Code](https://code.visualstudio.com/).
* Extensão das Ferramentas do Gerenciador de Recursos.  Confira [Instalar a extensão](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Conclua o [Tutorial: criar várias instâncias de recursos usando modelos do Resource Manager](./resource-manager-tutorial-create-multiple-instances.md).

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Modelos de Início Rápido do Azure é um repositório de modelos do Gerenciador de Recursos. Em vez de criar um modelo do zero, você pode encontrar um exemplo de modelo e personalizá-lo. O modelo usado neste tutorial é chamado [Implantar uma VM Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/). Esse é o mesmo modelo usado no [Tutorial: criar várias instâncias de recursos usando modelos do Resource Manager](./resource-manager-tutorial-create-multiple-instances.md). Salve duas cópias do mesmo modelo a ser usado como:

- **O modelo principal**: crie todos os recursos exceto a conta de armazenamento.
- **O modelo vinculado**: crie a conta de armazenamento.

1. No Visual Studio Code, escolha **Arquivo**>**Abrir Arquivo**.
2. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Escolha **Abrir** para abrir o arquivo.
4. Selecione **Arquivo**>**Salvar como** para salvar uma cópia do arquivo no computador local com o nome **azuredeploy.json**.
5. Selecione **Arquivo**>**Salvar como** para criar outra cópia do arquivo com o nome **linkedTemplate.json**.

## <a name="create-the-linked-template"></a>Criar o modelo vinculado

O modelo vinculado cria uma conta de armazenamento. O modelo vinculado é quase idêntico ao modelo autônomo que cria uma conta de armazenamento. Neste tutorial, o modelo vinculado precisa retornar um valor para o modelo principal. Esse valor é definido no elemento `outputs`.

1. Abra linkedTemplate.json no Visual Studio Code, se já não estiver aberto.
2. Faça as seguintes alterações:

    - Remova todos os recursos, exceto a conta de armazenamento. Você removerá, no total, quatro recursos.
    - Atualize o elemento **outputs** para que ele se pareça com:

        ```json
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
        ```
        **storageUri** é exigido pela definição de recurso de máquina virtual no modelo principal.  Retorne o valor para o modelo principal como um valor de saída.
    - Remova os parâmetros que nunca são usados. Esses parâmetros têm uma linha verde ondulada embaixo deles. Você só deverá ter um parâmetro chamado **location**.
    - Remova o elemento **variables**. Ele não é necessário neste tutorial.
    - Adicione um parâmetro chamado **storageAccountName**. O nome da conta de armazenamento é passado do modelo principal para o modelo vinculado como um parâmetro.

    Quando você terminar, o modelo deverá ser semelhante a:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountName":{
            "type": "string",
            "metadata": {
              "description": "Azure Storage account name."
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
              "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
          }
        ],
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
    }
    ```
3. Salve as alterações.

## <a name="upload-the-linked-template"></a>Carregar o modelo vinculado

Os modelos devem ficar acessíveis onde você executa a implantação. Esse local pode ser uma conta de armazenamento do Azure, o Github ou o Dropbox. Se seus modelos contêm informações confidenciais, proteja o acesso a eles. Neste tutorial, você deve usar o método de implantação do Cloud Shell usado no [Tutorial: criar várias instâncias de recursos usando modelos do Resource Manager](./resource-manager-tutorial-create-multiple-instances.md). O modelo principal (azuredeploy.json) é carregado no shell. O modelo vinculado (linkedTemplate.json) deve ser compartilhado em algum lugar.  Para reduzir as tarefas deste tutorial, o modelo vinculado definido na seção anterior foi carregado em [uma conta de armazenamento do Azure](https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json).

## <a name="call-the-linked-template"></a>Chamar o modelo vinculado

O modelo principal se chama azuredeploy.json.

1. Abra o azuredeploy.json no Visual Studio Code se já não estiver aberto.
2. Exclua a definição de recurso de conta de armazenamento do modelo.
3. Adicione o seguinte trecho de json ao local em que você tinha a definição da conta de armazenamento:

    ```json
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json"
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    Preste atenção a estes detalhes:

    - Um recurso `Microsoft.Resources/deployments` no modelo principal é usado para vincular a outro modelo.
    - O recurso `deployments` se chama `linkedTemplate`. Esse nome é usado para [configurar a dependência](#configure-dependency).  
    - Você só pode usar o modo de implantação [Incremental](./deployment-modes.md) quando chama modelos vinculados.
    - `templateLink/uri` contém o URI do modelo vinculado. O modelo vinculado foi carregado em uma conta de armazenamento compartilhado. Se você carregar o modelo de outro local na Internet, poderá atualizar o URI.
    - Use `parameters` para passar valores do modelo principal para o modelo vinculado.
4. Salve as alterações.

## <a name="configure-dependency"></a>Configurar dependência

Lembre-se, do [Tutorial: criar várias instâncias de recursos usando o modelo do Resource Manager](./resource-manager-tutorial-create-multiple-instances.md), de que o recurso de máquina virtual depende da conta de armazenamento:

![Diagrama de dependência dos modelos do Azure Resource Manager](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Como a conta de armazenamento agora está definida no modelo vinculado, você deve atualizar os dois elementos a seguir do recurso `Microsoft.Compute/virtualMachines`.

- Reconfigure o elemento `dependOn`. A definição da conta de armazenamento é movida para o modelo vinculado.
- Reconfigure o elemento `properties/diagnosticsProfile/bootDiagnostics/storageUri`. Em [Criar o modelo vinculado](#create-the-linked-template), você adicionou um valor de saída:

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```
    Esse valor é exigido pelo modelo principal.

1. Abra o azuredeploy.json no Visual Studio Code se já não estiver aberto.
2. Expanda a definição de recurso de máquina virtual e atualize **dependsOn** conforme mostrado na seguinte captura de tela:

    ![Os modelos vinculados do Azure Resource Manager configuram a dependência ](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)
    
    "linkedTemplate" é o nome do recurso de implantações.  
3. Atualize **properties/diagnosticsProfile/bootDiagnostics/storageUri/** conforme mostrado na captura de tela anterior.

Para saber mais, confira [Usar modelos vinculados e aninhados ao implantar recursos do Azure](./resource-group-linked-templates.md)

## <a name="deploy-the-template"></a>Implantar o modelo

Consulte a seção [Implantar o modelo](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) para obter o procedimento de implantação.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Escolha o nome do grupo de recursos.  Você deverá ver um total de seis recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você desenvolve e implanta modelos vinculados. Para saber como implantar recursos do Azure em várias regiões e como usar práticas de implantação seguras, consulte


> [!div class="nextstepaction"]
> [Usar o Gerenciador de Implantação do Azure](./deployment-manager-tutorial.md)

