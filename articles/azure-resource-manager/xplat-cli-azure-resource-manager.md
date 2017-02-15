---
title: Gerenciar recursos com a CLI do Azure | Microsoft Docs
description: Use a Interface de linha de comando (CLI) do Azure para gerenciar recursos e grupos do Azure
editor: 
manager: timlt
documentationcenter: 
author: tfitzmac
services: azure-resource-manager
ms.assetid: bb0af466-4f65-4559-ac3a-43985fa096ff
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/22/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e4d94d3f9736378d93e93be6645ed04ade763ca3
ms.openlocfilehash: 39ccb6e9b810d1c02a96b25946893ddd3e316463


---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Use a CLI do Azure para gerenciar recursos e grupos de recursos do Azure
> [!div class="op_single_selector"]
> * [Portal](resource-group-portal.md) 
> * [CLI do Azure](xplat-cli-azure-resource-manager.md)
> * [PowerShell do Azure](powershell-azure-resource-manager.md)
> * [API REST](resource-manager-rest-api.md)
> 
> 

A Interface de linha de comando do Azure (CLI do Azure) é uma das várias ferramentas que você pode usar para implantar e gerenciar recursos com o Resource Manager. Este artigo apresenta as maneiras comuns de gerenciar os recursos e os grupos de recursos do Azure usando a CLI do Azure no modo Resource Manager. Para saber mais sobre como usar a CLI para implantar recursos, confira [Implantar recursos com modelos do Resource Manager e a CLI do Azure](resource-group-template-deploy-cli.md). Para saber mais sobre o os recursos e o Resource Manager do Azure, visite a [Visão Geral do Azure Resource Manager](resource-group-overview.md).

> [!NOTE]
> Para gerenciar os recursos do Azure com a CLI do Azure, você precisa [instalar a CLI](../xplat-cli-install.md) do Azure, e [fazer logon no Azure](../xplat-cli-connect.md) usando o comando `azure login`. Verifique se a CLI está no modo Resource Manager (execute `azure config mode arm`). Se você tiver feito isso, você está pronto para continuar.
> 
> 

## <a name="get-resource-groups-and-resources"></a>Obtenha os grupos de recursos e os recursos
### <a name="resource-groups"></a>Grupos de recursos
Para obter uma lista de todos os grupos de recursos em sua assinatura e suas localizações, execute este comando.

    azure group list


### <a name="resources"></a>Recursos
 Para listar todos os recursos em um grupo, como um de nome *testRG*, por exemplo, use o comando a seguir.

    azure resource list testRG

Para exibir um recurso individual dentro do grupo, como uma VM denominada *MyUbuntuVM*, use um comando semelhante ao seguinte.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

Observe o parâmetro **Microsoft.Compute/virtualMachines**. Esse parâmetro indica o tipo do recurso sobre o qual você está solicitando informações.

> [!NOTE]
> Ao usar os comandos **azure resource** que não sejam o comando **list**, você deve especificar a versão da API do recurso com o parâmetro **-o**. Se você não tiver certeza sobre a versão da API a ser usada, consulte o arquivo de modelo e localize o campo apiVersion do recurso. Para saber mais sobre as versões da API no Resource Manager, consulte [Provedores do Resource Manager, regiões, versões de API e esquemas](resource-manager-supported-services.md).
> 
> 

Ao exibir detalhes em um recurso, costuma ser útil usar o parâmetro `--json`. Esse parâmetro torna a saída mais legível já que alguns valores são estruturas aninhadas ou coleções. O exemplo a seguir demonstra o retorno dos resultados do comando **show** como um documento JSON.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json

> [!NOTE]
> Caso deseje, salve os dados JSON no arquivo usando o caractere &gt; para direcionar a saída para um arquivo. Por exemplo:
> 
> `azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`
> 
> 

### <a name="tags"></a>Marcas
[!INCLUDE [resource-manager-tag-resources-cli](../../includes/resource-manager-tag-resources-cli.md)]

## <a name="manage-resources"></a>Gerenciar recursos
Para adicionar um recurso como uma conta de armazenamento a um grupo de recursos, execute um comando semelhante a:

    azure resource create testRG MyStorageAccount "Microsoft.Storage/storageAccounts" "westus" -o "2015-06-15" -p "{\"accountType\": \"Standard_LRS\"}"

Além de especificar a versão da API do recurso com o parâmetro **-o**, use o parâmetro **-p** para passar uma cadeia de caracteres formatada em JSON com quaisquer propriedades necessárias ou adicionais.

Para excluir um recurso existente, como um recurso de máquina virtual, use um comando como o seguinte.

    azure resource delete testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

Para mover os recursos existentes para outro grupo de recursos ou assinatura, use o comando **azure resource move** . O exemplo a seguir mostra como mover um Cache Redis para um novo grupo de recursos. No parâmetro **-i** , forneça uma lista separada por vírgulas de ids do recurso a serem movidas.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"

## <a name="control-access-to-resources"></a>Controlar acesso a recursos
Você pode usar a CLI do Azure para criar e gerenciar políticas para controlar o acesso aos recursos do Azure. Para obter informações sobre definições de política e atribuição de políticas para recursos, confira [Usar a política para gerenciar recursos e controlar o acesso](resource-manager-policy.md).

Por exemplo, defina a seguinte política para negar todas as solicitações onde o local não é Oeste dos EUA ou Centro-Norte dos EUA e salvar para o arquivo de definição de política policy.json:

    {
    "if" : {
        "not" : {
        "field" : "location",
        "in" : ["westus" ,  "northcentralus"]
        }
    },
    "then" : {
        "effect" : "deny"
    }
    }

Em seguida, execute o comando **policy definition create**:

    azure policy definition create MyPolicy -p c:\temp\policy.json

Esse comando exibe uma saída semelhante à seguinte.

    + Creating policy definition MyPolicy data:    PolicyName:             MyPolicy data:    PolicyDefinitionId:     /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/MyPolicy

    data:    PolicyType:             Custom data:    DisplayName:            undefined data:    Description:            undefined data:    PolicyRule:             field=location, in=[westus, northcentralus], effect=deny

 Para atribuir uma política no escopo desejado, use a **PolicyDefinitionId** retornada pelo comando anterior. No exemplo a seguir, este escopo é a assinatura, mas você pode definir o escopo para recursos individuais ou grupos de recursos:

    azure policy assignment create MyPolicyAssignment -p /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/MyPolicy -s /subscriptions/########-####-####-####-############/

Você pode obter, alterar ou remover definições de política usando os comandos **policy definition show**, **policy definition set**, e **policy definition delete**.

De modo semelhante você pode obter, alterar ou remover atribuições de política usando os comandos **policy assignment show**, **policy assignment set** e **policy assignment delete**.

## <a name="export-a-resource-group-as-a-template"></a>Exportar um grupo de recursos como um modelo
Para um grupo de recursos existente, você pode exibir o modelo Resource Manager do grupo de recursos. A exportação do modelo oferece dois benefícios:

1. Você pode automatizar com facilidade as implantações futuras da solução, pois toda a infraestrutura está definida no modelo.
2. Você pode familiarizar-se com a sintaxe do modelo analisando o JSON que representa sua solução.

Usando a CLI do Azure, você pode exportar um modelo que representa o estado atual do seu grupo de recursos ou baixar o modelo que foi usado para uma determinada implantação.

* **Exportar o modelo para um grupo de recursos** - Isso será útil quando você tiver feito alterações em um grupo de recursos e precisar recuperar a representação JSON de seu estado atual. No entanto, o modelo gerado contém apenas uma quantidade mínima de parâmetros e nenhuma variável. A maioria dos valores no modelo é embutida em código. Antes de implantar o modelo gerado, convém converter mais valores em parâmetros, para que você possa personalizar a implantação para ambientes diferentes.
  
    Para exportar o modelo de um grupo de recursos para um diretório local, execute o comando `azure group export` como mostrado no exemplo a seguir. (Substitua seu ambiente do sistema operacional por um diretório local apropriado).
  
        azure group export testRG ~/azure/templates/
* **Baixar o modelo de uma determinada implantação** – Isso será útil quando você precisar exibir o modelo real que foi usado para implantar os recursos. O modelo inclui todos os parâmetros e variáveis definidos para a implantação original. No entanto, se alguém em sua organização tiver feito alterações ao grupo de recursos que estejam fora da definição no modelo, esse modelo não representa o estado atual do grupo de recursos.
  
    Para baixar o modelo usado de uma implantação específica para um diretório local, execute o comando `azure group deployment template download`. Por exemplo:
  
        azure group deployment template download TestRG testRGDeploy ~/azure/templates/downloads/

> [!NOTE]
> A exportação do modelo está na visualização e nem todos os tipos de recursos oferecem suporte atualmente para a exportação de um modelo. Ao tentar exportar um modelo, talvez você veja um erro afirmando que alguns recursos não foram exportados. Se for necessário, defina manualmente esses recursos em seu modelo depois de baixá-lo.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* Para obter detalhes de operações de implantação e solucionar problemas de erros de implantação com a CLI do Azure, confira [Exibir operações de implantação com a CLI do Azure](resource-manager-troubleshoot-deployments-cli.md).
* Se você quiser usar a CLI para configurar um aplicativo ou script para acessar recursos, confira [Usar a CLI do Azure para criar uma entidade de serviço a fim de acessar recursos](resource-group-authenticate-service-principal-cli.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).




<!--HONumber=Dec16_HO2-->


