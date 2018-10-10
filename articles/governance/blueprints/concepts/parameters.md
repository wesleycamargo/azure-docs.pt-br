---
title: Criando blueprints dinâmicos por meio de parâmetros no Azure Blueprints
description: Conheça parâmetros estáticos e dinâmicos e como sua utilização cria blueprints dinâmicos.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: e1a1d736eb9b22cd444a75b94d112abfe3fbe5af
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993571"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Criando blueprints dinâmicos por meio de parâmetros

Um blueprint totalmente definido com vários artefatos (como grupos de recursos, modelos do Resource Manager, políticas ou atribuições de função) oferece a criação rápida e o provisionamento consistente de objetos dentro do Azure. Para permitir o uso flexível desses contêineres e padrões de design reutilizáveis, o Azure Blueprints dá suporte a parâmetros. O parâmetro cria flexibilidade, tanto durante a definição quanto na atribuição, para alterar as propriedades nos artefatos implantados pelo blueprint.

Um exemplo simples é o artefato do grupo de recursos. Quando um grupo de recursos é criado, ele tem dois valores obrigatórios que devem ser fornecidos: nome e local. Ao adicionar um grupo de recursos ao seu blueprint, se os parâmetros não existissem, você definiria esse nome e local para todo uso do blueprint. Isso faria cada uso do blueprint criar artefatos no mesmo grupo de recursos. Embora não seja um problema com o grupo de recursos em si, os recursos dentro desse grupo de recursos se tornariam duplicados e causariam um conflito.

> [!NOTE]
> Não é um problema o fato de dois blueprints diferentes incluírem um grupo de recursos com o mesmo nome.
> Se um grupo de recursos incluído em um blueprint já existir, o blueprint continuará criando os artefatos relacionados nesse grupo de recursos. Isso poderia causar um conflito, pois dois recursos com o mesmo nome e tipo de recurso não podem existir dentro de uma assinatura.

É aí em que os parâmetros se encaixam. O valor dessas propriedades, no caso do grupo de recursos, o nome e a propriedade de local, o Blueprints permite que você não os defina durante a definição do blueprint, mas, defina seus valores durante a atribuição a uma assinatura. Isso torna possível reutilizar um blueprint que cria um grupo de recursos e outros recursos dentro de uma assinatura única sem que haja conflito.

## <a name="blueprint-parameters"></a>Parâmetros de blueprint

Por meio da API REST, os parâmetros podem ser criados no próprio blueprint, além de cada um dos artefatos com suporte. Quando um parâmetro é criado no blueprint, ele pode ser usado pelos artefatos nesse blueprint. Um exemplo pode ser o prefixo para nomenclatura do grupo de recursos. O artefato pode usar o parâmetro de blueprint para criar um parâmetro "principalmente dinâmico", uma vez que o parâmetro ainda poderia ser definido durante a atribuição, mas terá uma consistência que pode atender às regras de nomenclatura da organização. Para obter as etapas, confira [definindo parâmetros estáticos – parâmetro do nível de blueprint](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Usando parâmetros secureString e secureObject

Embora um _artefato_ do modelo do Resource Manager seja compatível com parâmetros dos tipos **secureString** e **secureObject**, o Azure Blueprints requer que cada um esteja conectado a um Azure Key Vault.
Isso impede a prática não segura de armazenar segredos juntamente com o Blueprint e incentiva o emprego de padrões seguros. O Azure Blueprints facilita isso detectando a inclusão do parâmetro seguro em um _artefato_ do modelo do Resource Manager e solicitando, durante a atribuição do Blueprint, a inserção das seguintes propriedades do Key Vault por parâmetro seguro detectado:

- ID do recurso do Key Vault
- Nome do segredo do Key Vault
- Versão do segredo do Key Vault

O Key Vault referenciado deve existir na mesma assinatura à qual o Blueprint está sendo atribuído e também deve ter **Habilitar o acesso ao Azure Resource Manager para implantação de modelo** configurado na página **Políticas de acesso** do Key Vault. Para obter instruções sobre como habilitar esse recurso, confira [Key Vault – Habilitar implantação de modelo](../../../managed-applications/key-vault-access.md#enable-template-deployment).
Para obter mais informações sobre o Azure Key Vault, confira [Visão geral do Key Vault](../../../key-vault/key-vault-overview.md).

## <a name="parameter-types"></a>Tipos de parâmetro

### <a name="static-parameters"></a>Parâmetros estáticos

Um valor de parâmetro definido na definição de um blueprint é denominado **parâmetro estático**. Isso ocorre porque cada uso do blueprint implantará o artefato usando esse valor estático. No exemplo do grupo de recursos, embora isso não faça sentido para o nome do grupo de recursos, poderia fazer para o local. Em seguida, todas as atribuições do blueprint criariam o grupo de recursos, o que quer que seja chamado durante a atribuição, no mesmo local. Isso permite que você seja seletivo no que define como obrigatório versus o que pode ser alterado durante a atribuição.

#### <a name="setting-static-parameters-in-the-portal"></a>Parâmetros de configuração estáticos no portal

1. Inicie o serviço Azure Blueprints no portal do Azure clicando no **todos os serviços** e procurando e selecionando **Política** no painel esquerdo. Sobre a **política** página, clique em **plantas**.

1. Selecione **Definições do Blueprint** na página à esquerda.

1. Clique em um blueprint existente e, em seguida, em **Editar Blueprint** OU clique em **+ Criar Blueprint** e preencha as informações na guia **Básico**.

1. Clique em **Próximo: artefatos** OU clique na guia **Artefatos**.

1. Os artefatos adicionados ao blueprint que têm opções de parâmetro exibem **X de Y parâmetros populados** na coluna **Parâmetros**. Clique na linha do artefato para editar os seus parâmetros.

   ![Parâmetros de blueprint](../media/parameters/parameter-column.png)

1. A página **Editar artefato** exibe opções de valor apropriadas para o artefato clicado. Cada parâmetro no artefato tem um título, uma caixa de valor e uma caixa de seleção. Defina a caixa como desmarcada para torná-la um **parâmetro estático**. No exemplo abaixo, apenas _Local_ é um **parâmetro estático**, porque está desmarcado e _Nome do grupo de recursos_ está marcado.

   ![Parâmetros estáticos do Blueprint](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>Definindo parâmetros estáticos com base na API REST

Em cada URI da API REST, há variáveis usadas que precisam ser substituídas com seus próprios valores:

- `{YourMG}`: substitua pelo nome do seu grupo de gerenciamento
- `{subscriptionId}`: substitua por sua ID da assinatura

##### <a name="blueprint-level-parameter"></a>Parâmetro de nível do Blueprint

Ao criar um blueprint por meio da API REST, é possível criar [parâmetros de blueprint](#blueprint-parameters). Para fazer isso, use o formato do URI da API REST e de corpo a seguir:

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2017-11-11-preview
  ```

- Corpo da solicitação

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

Após a criação de um parâmetro de nível de blueprint, ela pode ser usado em artefatos adicionados a esse blueprint.
O exemplo de API REST a seguir cria um artefato de atribuição de função no blueprint e usa o parâmetro de nível de blueprint.

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2017-11-11-preview
  ```

- Corpo da solicitação

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

Neste exemplo, a propriedade **principalIds** fez uso do parâmetro de nível de blueprint **owners** fornecendo um valor de `[parameters('owners')]`. A definição de um parâmetro em um artefato que usa um parâmetro de nível de blueprint ainda é um exemplo de um **parâmetro estático**, pois não pode ser definida durante a atribuição do blueprint e será esse valor em cada atribuição.

##### <a name="artifact-level-parameter"></a>Parâmetro de nível do artefato

A criação de **parâmetros estáticos** em um artefato é semelhante, mas usa um valor direto em vez de usar a função `parameters()`. O exemplo a seguir cria dois parâmetros estáticos, **tagName** e **tagValue**. O valor em cada um é fornecido diretamente e não usa uma chamada de função.

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2017-11-11-preview
  ```

- Corpo da solicitação

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>Parâmetros dinâmicos

O oposto de um parâmetro estático é um **parâmetro dinâmico**. Esse é um parâmetro que não é definido no blueprint, mas que é definido durante cada atribuição dele. No exemplo do grupo de recursos, isso faz sentido para o nome do grupo de recursos, fornecendo um nome diferente para toda atribuição do blueprint.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Definindo parâmetros dinâmicos no portal

1. Inicie o serviço Azure Blueprints no portal do Azure clicando no **todos os serviços** e procurando e selecionando **Política** no painel esquerdo. Sobre a **política** página, clique em **plantas**.

1. Selecione **Definições do Blueprint** na página à esquerda.

1. Clique com o botão direito do mouse no blueprint que você deseja atribuir e selecione **Atribuir Blueprint** OU clique no blueprint que você deseja atribuir e, em seguida, no botão **Atribuir Blueprint**.

1. Na página **Atribuir blueprint**, localize a seção **Parâmetros do artefato**. Cada artefato com pelo menos um **parâmetro dinâmico** exibe o artefato e as opções de configuração. Forneça os valores necessários aos parâmetros antes de atribuir o blueprint. No exemplo abaixo, _Nome_ é um **parâmetro dinâmico** que deve ser definido para concluir a atribuição do blueprint.

   ![Parâmetro dinâmico do blueprint](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Definindo parâmetros dinâmicos com base na API REST

A definição de **parâmetros dinâmicos** durante a atribuição é feita fornecendo o valor desejado diretamente. Em vez de usar uma função, como `parameters()`, o valor fornecido é uma cadeia de caracteres apropriada. Os artefatos para um grupo de recursos são definidos com um "nome de modelo" e as propriedades **nome** e **local**. Todos os outros parâmetro para cada artefato incluído são definidos em **parâmetros** com um par de chaves **\<nome\>** e **valor**. Se o blueprint estiver configurado para um parâmetro dinâmico não fornecido durante a atribuição, ela falhará.

- URI da API REST

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2017-11-11-preview
  ```

- Corpo da solicitação

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>Próximas etapas

- Aprenda sobre o [ciclo de vida do blueprint](lifecycle.md)
- Aprenda a personalizar a [ordem de sequenciamento de blueprint](sequencing-order.md)
- Saiba como fazer uso do [bloqueio de recurso de blueprint](resource-locking.md)
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md)
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md)