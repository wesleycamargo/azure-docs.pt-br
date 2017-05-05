---
title: Loop sequencial em modelos do Azure | Microsoft Docs
description: "Descreve como estender a funcionalidade dos modelos do Azure Resource Manager para implementar loop sequencial ao implantar várias instâncias de um tipo de recurso."
services: guidance, azure-resource-manager
documentationcenter: na
author: petertaylor9999
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 04/18/2017
ms.author: petertay
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 44d59488600e05d6ae80a169b5d682e8fa94ccc5
ms.lasthandoff: 04/18/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---sequential-looping"></a>Padrões para estender a funcionalidade dos modelos do Azure Resource Manager – Loop sequencial

Modelos do Azure Resource Manager dão suporte à implantação de um grupo de recursos semelhantes por meio de um loop de cópia. Um loop de cópia em um objeto de recurso pode ser usado para iterar uma matriz de cadeias de caracteres, usados para gerar nomes exclusivos para os recursos. Um loop de cópia no objeto de recurso também pode ser usado para iterar em uma matriz de variáveis que descrevem o recurso.

Esses padrões todos funcionam bem, mas isso só ocorre quando não há dependências entre os membros do grupo. Durante um loop de iteração, o Resource Manager tenta implantar recursos em paralelo. Se um primeiro recurso depender de um segundo, a implantação poderá falhar se o Resource Manager implantar o segundo recurso antes do primeiro.

O verdadeiro problema é que o Resource Manager não dá suporte a `dependsOn` atualmente dentro de um loop de iteração. No entanto, essa funcionalidade pode ser implementada usando a funcionalidade existente do Resource Manager e alguns recursos criativos de nomenclatura. 

## <a name="sequential-looping-pattern"></a>Padrão de loop sequencial

O padrão é o seguinte: um primeiro recurso é nomeado usando uma concatenação de um prefixo de nome e `0`, ou qualquer que seja o primeiro índice do loop. Um segundo recurso inclui um loop de cópia e no loop de cópia o próximo nome de recurso é uma concatenação do prefixo de nome e o resultado da função `copyIndex(1)`, que adiciona 1 ao `copyIndex()` atual. O segundo recurso também inclui um elemento `dependsOn` que faz referência a concatenação de prefixo de nome e o resultado da função `copyIndex()`. Essa abordagem cria uma relação `dependsOn` do próximo recurso com o recurso anterior. O Resource Manager espera até que o recurso anterior seja implantado para implantar o próximo recurso.

O modelo a seguir demonstra esse padrão. O tipo de recurso de Microsoft.Resources/deployments é um modelo aninhado que realmente não implanta nada.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberToDeploy": {
      "type": "int",
      "minValue": 2,
      "defaultValue": 5
    }
  },
  "variables": {
    "count": "[sub(parameters('numberToDeploy'), 1)]"
  },
  "resources": [
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "loop-0",
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
            "collection": {
              "type": "string",
              "value": "loop-0 "
            }
          }
        }
      }
    },
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('loop-', copyIndex(1))]",
      "dependsOn": [
        "[concat('loop-', copyIndex())]"
      ],
      "copy": {
        "name": "iterator",
        "count": "[variables('count')]"
      },
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
            "collection": {
              "type": "string",
              "value": "[concat(reference(concat('loop-',copyIndex())).outputs.collection.value,'loop-',copyIndex(1), ' ')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[reference(concat('loop-',variables('count'))).outputs.collection.value]"
    }
  }
}

```
Neste modelo, o primeiro objeto de recurso é denominado `loop-0`. Em seguida, no segundo objeto do recurso, o próximo nome de recurso é uma concatenação da palavra `loop-` e o resultado da função `copyIndex(1)`: `loop-1`. O elemento `dependsOn` faz referência ao recurso anterior porque é uma concatenação da palavra `loop-` e o resultado da função `copyIndex()`: `loop-0`. O padrão no segundo objeto de recurso se repete até que `count` seja alcançado, terminando com um recurso denominado `loop-4` que `dependsOn` `loop-3`. Observe que `count` é uma variável que subtrai `1` do parâmetro `numberToDeploy` para manter a contagem baseada em zero correta.

## <a name="try-the-template"></a>Experimentar o modelo

Para experimentar esse modelo, siga estas etapas:

1.    Visite o portal do Azure, selecione o ícone "+" e pesquise pelo tipo de recurso "implantação de modelo". Quando você o achar nos resultados da pesquisa, selecione-o.
2.    Na página de "implantação de modelo", selecione o botão "criar". Vá até a folha "implantação personalizada" e veja que o modelo não tem recursos.
3.    Selecione o ícone "editar".
4.    Exclua o modelo vazio no painel à direita.
5.    Copie e cole o modelo de exemplo anterior no painel à direita.
6.    Selecione o botão "salvar".
7.    Você retorna ao painel "implantação personalizada", mas desta vez há algumas caixas suspensas. Selecione sua assinatura, crie um novo grupo de recursos ou use o existente e selecione um local. Reveja os termos e as condições e clique no botão "Eu concordo".
8.    Selecione o botão "comprar".

Verifique se os recursos estão sendo implantados sequencialmente, selecione "grupos de recursos" e selecione o grupo de recursos selecionado anteriormente. Selecione o botão "implantações" na folha do grupo de recursos e consulte os recursos implantados em ordem sequencial com carimbo de data/hora correspondente.

![Implantação do modelo de loop sequencial no Azure Resource Manager](./media/resource-manager-sequential-loop/deployments.png)

## <a name="next-steps"></a>Próximas etapas

Use esse padrão em seus modelos, adicionando os recursos ao modelo aninhado. Você pode criá-los diretamente no elemento de modelo do recurso `Microsoft.Resources/deployments` ou vinculá-los usando o elemento `templateLink`. O tipo de recurso no exemplo é um modelo aninhado, mas qualquer tipo de recurso pode ser implantado. A única exceção é que os recursos filho não podem ser referenciados por meio de um loop de iteração.

* Para obter uma introdução sobre como criar várias instâncias de um recurso, consulte [Implantar várias instâncias de recursos em modelos do Azure Resource Manager](resource-group-create-multiple.md).
* Esse padrão também é implementado no [projeto de blocos de construção do modelo](https://github.com/mspnp/template-building-blocks) e nas [arquiteturas de referência do Azure](/azure/architecture/reference-architectures/).
