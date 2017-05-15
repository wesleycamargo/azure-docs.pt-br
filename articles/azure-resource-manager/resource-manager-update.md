---
title: "Modelos do Azure Resource Manager – atualizar um recurso | Microsoft Docs"
description: Descreve como estender a funcionalidade dos modelos do Azure Resource Manager para atualizar um recurso
services: azure-resource-manager, guidance
documentationcenter: na
author: petertay
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 05/02/2017
ms.author: mspnp
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 7807b3f6135ede80cd7e7e6486364482cde72e29
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---updating-a-resource"></a>Padrões para estender a funcionalidade dos modelos do Azure Resource Manager – atualizando um recurso

Existem alguns cenários nos quais você precisa atualizar um recurso durante a implantação. Você encontra esse cenário quando não é possível especificar todas as propriedades de um recurso até que outros recursos dependentes sejam criados. Por exemplo, se você criar um pool de back-end para um balanceador de carga, poderá atualizar as NICs (adaptadores de rede) nas VMs (máquinas virtuais) para incluí-las no pool de back-end. O Resource Manager dá suporte à atualização de recursos durante a implantação, mas é necessário projetar o modelo corretamente para evitar erros e garantir que a implantação é tratada como uma atualização.

## <a name="understand-the-pattern"></a>Entender o padrão

Primeiro, é necessário referenciar o recurso uma vez no modelo para criá-lo, mas, em seguida, você deve referenciar o recurso com o mesmo nome para atualizá-lo mais tarde. No entanto, se dois recursos tiverem o mesmo nome em um modelo, o Resource Manager gerará uma exceção. Para evitar esse erro, especifique o recurso atualizado em um segundo modelo que está vinculado ou incluído como um submodelo usando o tipo de recurso `Microsoft.Resources/deployments`.

Em segundo lugar, no segundo modelo, você deve especificar o nome da propriedade existente a ser alterado ou um novo nome de uma propriedade a ser adicionado. Em seguida, você também deve especificar as propriedades originais e seus valores originais. Se você não fornecer as propriedades e os valores originais, o Resource Manager considerará que você deseja criar um novo recurso e excluirá o recurso original. Ele substituirá o recurso original por um novo recurso que inclui apenas as novas propriedades especificadas.

Por fim, é necessário tornar o recurso dependente de todos os recursos relacionados que você deseja implantar. Essa dependência garante que os recursos são criados na ordem correta. A ordem é:

1. Recurso criado
2. Recursos dependentes criados
3. Recursos (da etapa 1) atualizados com valores dos recursos dependentes (etapa 2)

## <a name="example-template"></a>Modelo de exemplo

O modelo de exemplo a seguir demonstra esse padrão. Ela implanta uma VNet (rede virtual) chamada `firstVNet` que tem uma sub-rede chamada `firstSubnet`. Em seguida, ele implanta uma NIC (adaptador de rede virtual) chamada `nic1` e o associa à sub-rede. Depois, um recurso de implantação chamado `updateVNet` inclui um modelo aninhado que referencia o nome do recurso `firstVNet`. 

Examine as propriedades `addressSpace` e `subnets` desse recurso. Observe que o valor `addressSpace` está definido com o mesmo valor da propriedade do objeto de implantação de recurso `firstVNet`. Na matriz `subnets`, o valor de `firstSubnet` é definido da mesma forma. Como todas as propriedades `firstVNet` originais foram especificadas, o Resource Manager atualiza o recurso no Azure. Nesse caso, a atualização é a adição de uma nova sub-rede chamada `secondSubnet`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
      {
      "apiVersion": "2016-03-30",
      "name": "firstVNet",
      "location":"[resourceGroup().location]",
      "type": "Microsoft.Network/virtualNetworks",
      "properties": {
          "addressSpace":{"addressPrefixes": [
              "10.0.0.0/22"
          ]},
          "subnets":[              
              {
                  "name":"firstSubnet",
                  "properties":{
                    "addressPrefix":"10.0.0.0/24"
                  }
              }
            ]
      }
    },
    {
        "apiVersion": "2015-06-15",
        "type":"Microsoft.Network/networkInterfaces",
        "name":"nic1",
        "location":"[resourceGroup().location]",
        "dependsOn": [
            "firstVNet"
        ],
        "properties": {
            "ipConfigurations":[
                {
                    "name":"ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod":"Dynamic",
                        "subnet": {
                            "id": "[concat(resourceId('Microsoft.Network/virtualNetworks','firstVNet'),'/subnets/firstSubnet')]"
                        }
                    }
                }
            ]
        }
    },
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "updateVNet",
      "dependsOn": [
          "nic1"
      ],
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
              {
                  "apiVersion": "2016-03-30",
                  "name": "firstVNet",
                  "location":"[resourceGroup().location]",
                  "type": "Microsoft.Network/virtualNetworks",
                  "properties": {
                      "addressSpace": "[reference('firstVNet').addressSpace]",
                      "subnets":[
                          {
                              "name":"[reference('firstVNet').subnets[0].name]",
                              "properties":{
                                  "addressPrefix":"[reference('firstVNet').subnets[0].properties.addressPrefix]"
                                  }
                          },
                          {
                              "name":"secondSubnet",
                              "properties":{
                                  "addressPrefix":"10.0.1.0/24"
                                  }
                          }
                     ]
                  }
              }
          ],
          "outputs": {}
          }
        }
    }
  ],
  "outputs": {}
}
```

## <a name="try-the-template"></a>Experimentar o modelo

Para experimentar esse modelo, siga estas etapas:

1.    Visite o portal do Azure, selecione o ícone "+" e pesquise pelo tipo de recurso "implantação de modelo". Quando você o achar nos resultados da pesquisa, selecione-o.
2.    Quando você chegar à página “Implantação de modelo”, selecione o botão **Criar**, que abre a folha “Implantação personalizada”.
3.    Selecione o ícone **Editar**.
4.    Exclua o modelo vazio.
5.    Copie e cole o modelo de exemplo anterior no painel à direita.
6.    Selecione o botão **Salvar**.
7.    Você retorna ao painel “Implantação personalizada”, mas desta vez há algumas caixas suspensas. Selecione sua assinatura, crie um novo grupo de recursos ou use o existente e selecione um local. Examine os termos e condições e, em seguida, selecione o botão **Eu concordo**.
8.    Selecione o botão **Comprar**.

Depois que a implantação for concluída, abra o grupo de recursos que você especificou no portal. Você verá uma VNet chamada `firstVNet` e uma NIC chamada `nic1`. Clique em `firstVNet` e, depois, em `subnets`. Você verá a `firstSubnet` que foi originalmente criada e a `secondSubnet` que foi adicionada ao recurso `updateVNet`. 

![Sub-rede original e sub-rede atualizada](./media/resource-manager-update/firstVNet-subnets.png)

Depois, retorne ao grupo de recursos e clique em `nic1`. Em seguida, clique em `IP configurations`. Na seção `IP configurations`, a `Subnet` é definida como `firstSubnet (10.0.0.0/24)`. 

![definições de configuração de IP da NIC1](./media/resource-manager-update/nic1-ipconfigurations.png)

A `firstVNet` original foi atualizada em vez de recriada. Se `firstVNet` tivesse sido recriada, a `nic1` não seria associada a `firstVNet`.

## <a name="next-steps"></a>Próximas etapas

Você pode usar esse padrão em seus modelos para reespecificar as propriedades originais do recurso que você deseja atualizar. Especifique o recurso de atualização em um modelo vinculado ou aninhado usando o tipo de recurso `Microsoft.Resources/deployments`.

* Para obter uma introdução à função `reference()`, consulte [Funções de modelo do Azure Resource Manager](resource-group-template-functions.md).
* Esse padrão também é implementado no [projeto de blocos de construção do modelo](https://github.com/mspnp/template-building-blocks) e nas [arquiteturas de referência do Azure](/azure/architecture/reference-architectures/).
