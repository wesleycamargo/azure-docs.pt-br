---
title: Recursos de modelo do Azure Resource Manager | Microsoft Docs
description: Descreve a seção de recursos dos modelos do Azure Resource Manager usando a sintaxe JSON declarativa.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2019
ms.author: tomfitz
ms.openlocfilehash: 01aacf8815ce4150eb1c243d4337f52c4e0b03e9
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55697020"
---
# <a name="resources-section-of-azure-resource-manager-templates"></a>Seção de recursos dos modelos do Azure Resource Manager

Na seção de recursos, você define os recursos que são implantados ou atualizados. Essa seção pode ficar mais complicada, porque você precisa entender os tipos que está implantando para fornecer os valores corretos.

## <a name="available-properties"></a>Propriedades disponíveis

Você define recursos com a seguinte estrutura:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nome do elemento | Obrigatório | DESCRIÇÃO |
|:--- |:--- |:--- |
| condition | Não  | Valor booliano que indica se o recurso será provisionado durante esta implantação. Quando for `true`, o recurso será criado durante a implantação. Quando `false`, o recurso será ignorado para essa implantação. |
| apiVersion |Sim |Versão da API REST a ser usada para criar o recurso. |
| Tipo |Sim |Tipo do recurso. Esse valor é uma combinação do namespace do provedor de recursos e do tipo de recurso (como **Microsoft.Storage/storageAccounts**). |
| Nome |Sim |Nome do recurso. O nome deve seguir as restrições de componente URI definidas em RFC3986. Além disso, os serviços do Azure que expõem o nome do recurso a terceiros validam o nome para garantir que ele não é uma tentativa de falsificar outra identidade. |
| location |Varia |Locais geográficos com suporte do recurso fornecido. Você pode selecionar qualquer uma das localizações disponíveis, mas geralmente faz sentido escolher um que esteja perto de seus usuários. Normalmente, também faz sentido colocar recursos que interagem entre si na mesma região. A maioria dos tipos de recurso exige um local, ao contrário de alguns deles (como uma atribuição de função). |
| marcas |Não  |Marcas que são associadas ao recurso. Aplique marcas para organizar recursos logicamente em toda a sua assinatura. |
| comentários |Não  |Suas anotações para documentar os recursos no modelo. Para obter mais informações, confira [Comentários em modelos](resource-group-authoring-templates.md#comments). |
| cópia |Não  |Se mais de uma instância for necessária, o número de recursos a serem criados. O modo padrão é paralelo. Especifica o modo serial quando você não deseja que todos os recursos sejam implantados ao mesmo tempo. Para obter mais informações, consulte [Criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Não  |Recursos que devem ser implantados antes deste recurso. O Gerenciador de Recursos avalia as dependências entre os recursos e os implanta na ordem correta. Quando os recursos não dependem uns dos outros, eles são implantados em paralelo. O valor pode ser uma lista separada por vírgulas de nomes de recursos ou identificadores exclusivos de recursos. Somente lista recursos que são implantados neste modelo. Os recursos que não são definidos neste modelo já devem existir. Evite adicionar dependências desnecessárias, pois elas podem reduzir sua implantação e criar dependências circulares. Para obter orientação sobre como configurar as dependências, confira [Definir as dependências nos modelos do Azure Resource Manager](resource-group-define-dependencies.md). |
| propriedades |Não  |Definições de configuração específicas do recurso. Os valores para as propriedades são iguais aos valores que você fornece no corpo da solicitação para a operação da API REST (método PUT) para criar o recurso. Especifique também uma matriz de cópia para criar várias instâncias de uma propriedade. |
| sku | Não  | Alguns recursos permitem que os valores definam a SKU para implantar. Por exemplo, você pode especificar o tipo de redundância para uma conta de armazenamento. |
| kind | Não  | Alguns recursos permitem que um valor defina o tipo de recurso que você implantar. Por exemplo, você pode especificar o tipo de Cosmos DB para criar. |
| plan | Não  | Alguns recursos permitem que um valor defina o plano para implantar. Por exemplo, você pode especificar a imagem do marketplace para uma máquina virtual. | 
| recursos |Não  |Recursos filho que dependem do recurso que está sendo definido. Forneça apenas os tipos de recurso permitidos pelo esquema do recurso pai. O tipo totalmente qualificado do recurso filho inclui o tipo de recurso pai, como **Microsoft.Web/sites/extensions**. A dependência do recurso pai não é implícita. Você deve definir explicitamente essa dependência. |

## <a name="condition"></a>Condição

Durante a implantação, quando você precisar decidir se deseja ou não criar um recurso, use o elemento `condition`. O valor desse elemento é resolvido como verdadeiro ou falso. Quando o valor for true, o recurso será criado. Quando o valor for false, o recurso não será criado. O valor só pode ser aplicado para o recurso inteiro.

Normalmente, você usa esse valor quando deseja criar um novo recurso ou usar um existente. Por exemplo, para especificar se uma nova conta de armazenamento é implantada ou uma conta de armazenamento existente é usada, use:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Para obter um modelo de exemplo completo que usa o elemento `condition`, confira [VM com uma Rede Virtual nova ou existente, um Armazenamento e um IP público](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="resource-specific-values"></a>Valores específicos do recurso

A **apiVersion**, **tipo**  e **elementos de propriedades** são diferentes para cada tipo de recurso. Os elementos de **sku**, **tipo**, e **plano** estão disponíveis para alguns tipos de recursos, mas não todos. Para determinar valores para essas propriedades, consulte [referência de modelo](/azure/templates/).

## <a name="resource-names"></a>Nomes de recurso

Normalmente, você trabalha com três tipos de nome de recurso no Resource Manager:

* Os nomes de recurso devem ser exclusivos.
* Nomes de recursos que não precisam ser exclusivos, mas você opta por fornecer um nome que pode ajudá-lo a identificar o recurso.
* Nomes de recursos que podem ser genéricos.

### <a name="unique-resource-names"></a>Nomes de recurso exclusivos

Forneça um nome de recurso exclusivo para qualquer tipo de recurso que tenha um ponto de extremidade de acesso a dados. Entre os tipos comuns de recurso que exigem um nome exclusivo estão:

* Armazenamento do Azure<sup>1</sup> 
* Recurso de Aplicativos Web do Serviço de Aplicativo do Azure
* SQL Server
* Cofre da Chave do Azure
* Cache Redis do Azure
* Lote do Azure
* Gerenciador de Tráfego do Azure
* Azure Search
* Azure HDInsight

<sup>1</sup> Os nomes de conta de armazenamento devem estar em letras minúsculas, ter até 24 caracteres e não incluir hifens.

Ao configurar o nome, é possível criar manualmente um nome exclusivo ou usar a função [uniqueString()](resource-group-template-functions-string.md#uniquestring) para gerar um nome. Também convém adicionar um prefixo ou sufixo ao resultado **uniqueString**. Modificar o nome exclusivo pode ajudar você a identificar mais facilmente o tipo de recurso pelo nome. Por exemplo, você pode gerar um nome exclusivo para uma conta de armazenamento usando a seguinte variável:

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Nomes de recurso para identificação
Alguns tipos de recurso que talvez você queira nomear não precisam que seus nomes sejam exclusivos. Para esses tipos de recurso, você pode fornecer um nome que identifique o contexto do recurso e o tipo de recurso.

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

### <a name="generic-resource-names"></a>Nomes de recurso genéricos
Para os tipos de recurso que você mais acessa por meio de um recurso diferente, é possível usar um nome genérico que seja embutido em código no modelo. Por exemplo, você pode definir um nome genérico padrão para regras de firewall em um servidor SQL:

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="location"></a>Local padrão
Ao implantar um modelo, é necessário fornecer um local para cada recurso. Tipos de recursos diferentes têm suporte em locais diferentes. Para obter os locais com suporte para um tipo de recurso, consulte [provedores de recursos e tipos do Azure](resource-manager-supported-services.md).

Use um parâmetro para especificar o local de recursos e defina o valor padrão para `resourceGroup().location`.

O exemplo a seguir mostra uma conta de armazenamento implantada a um local especificado como um parâmetro:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
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
  "variables": {
    "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

Se for necessário codificar o local em seu modelo, informe o nome de uma das regiões com suporte. O exemplo a seguir mostra uma conta de armazenamento que sempre é implantada para o Centro-Norte dos EUA:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="tags"></a>Marcas
[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

### <a name="add-tags-to-your-template"></a>Adicionar marcas ao seu modelo

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="child-resources"></a>Recursos filho

Em alguns tipos de recursos, também é possível definir uma matriz de recursos filho. Os recursos filho são recursos que somente existem no contexto de outro recurso. Por exemplo, um banco de dados SQL não pode existir sem um servidor SQL para que o banco de dados seja filho do servidor. É possível definir o banco de dados dentro da definição para o servidor.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

Quando aninhado, o tipo é definido como `databases`, mas o tipo de recurso completo é `Microsoft.Sql/servers/databases`. `Microsoft.Sql/servers/` não é fornecido porque ele é presumido do tipo de recurso pai. O nome do recurso filho é definido como `exampledatabase` , mas o nome completo inclui o nome do pai. `exampleserver` não é fornecido porque ele é presumido do recurso pai.

O formato do tipo do recurso filho é: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

O formato do nome do recurso filho é: `{parent-resource-name}/{child-resource-name}`

Porém, não é necessário definir o banco de dados dentro do servidor. Você pode definir o recurso filho no nível superior. Você pode usar essa abordagem se o recurso pai não estiver implantado no mesmo modelo ou se desejar usar `copy` para criar mais de um recurso filho. Com essa abordagem, forneça o tipo de recurso completo e inclua o nome do recurso pai no nome do recurso filho.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

Ao construir uma referência totalmente qualificada a um recurso, a ordem para combinação dos segmentos do tipo e do nome não é simplesmente uma concatenação dos dois. Em vez disso, após o namespace, use uma sequência de pares *tipo/nome* do menos específico para o mais específico:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Por exemplo: 

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`está correto, `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` não está correto



## <a name="next-steps"></a>Próximas etapas
* Para exibir modelos completos para muitos tipos diferentes de soluções, consulte os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
* Para obter detalhes sobre as funções que podem ser usadas em um modelo, consulte [Funções do Modelo do Azure Resource Manager](resource-group-template-functions.md).
* Para ver recomendações sobre como criar modelos, confira [Práticas recomendadas para modelos do Azure Resource Manager](template-best-practices.md).
* Talvez seja necessário usar recursos que existam em um grupo de recursos diferente. Esse cenário é comum ao trabalhar com contas de armazenamento ou redes virtuais compartilhadas com vários grupos de recursos. Para obter mais informações, consulte a [função resourceId](resource-group-template-functions-resource.md#resourceid).
* Para obter informações sobre restrições de nome de recurso, confira [Recursos recomendados de convenções de nomenclatura do Azure](../guidance/guidance-naming-conventions.md).