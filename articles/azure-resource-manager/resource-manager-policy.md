---
title: "Política do Azure Resource Manager | Microsoft Docs"
description: "Descreve como usar a política do Gerenciador de Recursos do Azure para evitar violações em escopos diferentes como assinatura de recursos individuais ou grupos de recursos."
services: azure-resource-manager
documentationcenter: na
author: ravbhatnagar
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2016
ms.author: gauravbh;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: bdc759341e1f9707ddf688512249c3297d85c29b


---
# <a name="use-policy-to-manage-resources-and-control-access"></a>Usar a política para gerenciar recursos e controlar o acesso
O Gerenciador de Recursos do Azure agora permite que você controle o acesso por meio de políticas personalizadas. Com as políticas, você pode impedir que os usuários em sua organização violem convenções que são necessárias para gerenciar os recursos de sua organização. 

Crie definições de política que descrevem as ações ou os recursos que são especificados negados. Atribua essas definições de política no escopo desejado, como a assinatura, grupo de recursos ou um recurso individual. As políticas são herdadas por todos os recursos filho. Então, se uma política for aplicada a um grupo de recursos, ela será aplicável a todos os recursos desse grupo de recursos.

Neste artigo, explicamos a estrutura básica de linguagem de definição de política que você pode usar para criar políticas. Em seguida, descrevemos como você pode aplicar essas políticas em escopos diferentes.

## <a name="how-is-it-different-from-rbac"></a>Qual é a diferença dela em relação ao RBAC?
Há algumas diferenças importantes entre a política e o controle de acesso baseado em função, mas a primeira coisa que é preciso entender é que as políticas e o RBAC funcionam juntos. Para usar políticas, você deve estar autenticado pelo RBAC. Ao contrário do RBAC, a política é um sistema de permissão padrão e negação explícita. 

O RBAC concentra-se nas ações que um **usuário** pode realizar em escopos diferentes. Por exemplo, um usuário específico é adicionado à função de colaborador de um grupo de recursos no escopo do desejado, para que o usuário possa fazer alterações a esse grupo de recursos. 

A política concentra-se nas ações de **recurso** em vários escopos. Por exemplo, por meio de políticas, é possível controlar os tipos de recursos que podem ser provisionados ou restringir os locais em que os recursos podem ser provisionados.

## <a name="common-scenarios"></a>Cenários comuns
Um cenário comum é requerer marcas departamentais para fins de estorno. Uma organização talvez queira permitir operações somente quando o centro de custo apropriado estiver associado; se este não for o caso, a solicitação será negada. Esta política os ajuda a cobrar o centro de custo apropriado para as operações executadas.

Outro cenário comum é que a organização talvez queira controlar os locais onde os recursos são criados. Ou talvez queiram controlar o acesso aos recursos, permitindo que somente determinados tipos de recursos sejam provisionados.

Da mesma forma, uma organização pode controlar o catálogo de serviços ou aplicar as convenções de nomenclatura desejadas para os recursos.

Usando políticas, esses cenários podem ser facilmente obtidos.

## <a name="policy-definition-structure"></a>Estrutura da definição de política
A definição de política é criada usando JSON. Consiste em uma ou mais condições/operadores lógicos que definem as ações e o efeito que informa o que acontece quando as condições são atendidas. O esquema é publicado em [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 

Basicamente, uma política contém os seguintes elementos:

**Condição/Operadores lógicos:** um conjunto de condições que podem ser manipuladas por meio de um conjunto de operadores lógicos.

**Efeito:** o que acontece quando a condição é atendida – negar ou auditar. Um efeito de auditar emitirá um log de aviso de serviço de evento. Por exemplo, um administrador poderá criar uma política que causa um evento de auditoria se alguém criar uma VM grande. O administrador pode examinar os logs mais tarde.

    {
      "if" : {
          <condition> | <logical operator>
      },
      "then" : {
          "effect" : "deny | audit | append"
      }
    }

## <a name="policy-evaluation"></a>Avaliação da política
As políticas são avaliadas quando os recursos são criados. Para a implantação de modelo, as políticas são avaliadas durante a criação de cada recurso no modelo. 

> [!NOTE]
> Atualmente, a política não avalia os tipos de recursos que não dão suporte a marcas, tipo e local, como o tipo de recurso Microsoft.Resources/deployments. Esse suporte será adicionado no futuro. Para evitar problemas de compatibilidade com versões anteriores, você deve especificar explicitamente o tipo ao criar políticas. Por exemplo, uma política de marcação que não especifica tipos é aplicada a todos os tipos. Nesse caso, uma implantação de modelo poderá falhar se houver um recurso aninhado que não dê suporte a marcas e o tipo de recurso de implantação tiver sido adicionado à avaliação da política. 
> 
> 

## <a name="logical-operators"></a>Operadores lógicos
Os operadores lógicos com suporte juntamente com a sintaxe são:

| Nome do operador | Sintaxe |
|:--- |:--- |
| não |"not" : {&lt;condição ou operador &gt;} |
| e |"allOf" : [ {&lt;condição ou operador &gt;},{&lt;condição ou operador &gt;}] |
| ou o |"anyOf" : [ {&lt;condição ou operador &gt;},{&lt;condição ou operador &gt;}] |

O Gerenciador de Recursos permite que você especifique uma lógica complexa em sua política por meio de operadores aninhados. Por exemplo, você pode recusar a criação de recursos em um local específico para um tipo de recurso especificado. Um exemplo de operadores aninhados neste tópico.

## <a name="conditions"></a>Condições
Uma condição avalia se um **campo** ou uma **fonte** atende a determinados critérios. Os nomes de condição e a sintaxe com suporte são:

| Nome da condição | Sintaxe |
|:--- |:--- |
| É igual a |"equals" : "&lt;valor&gt;" |
| Como |"like" : "&lt;valor&gt;" |
| Contém: |"contains" : "&lt;valor&gt;" |
| Nesse |"in" : [ "&lt;valor1&gt;","&lt;valor2&gt;" ] |
| ContainsKey |"containsKey" : "&lt;nomeDaChave&gt;" |
| Exists |"exists" : "&lt;bool&gt;" |

### <a name="fields"></a>Campos
As condições são formadas por meio do uso de campos e fontes. Um campo representa as propriedades na carga de solicitação de recurso que é usada para descrever o estado do recurso. Uma fonte representa as características da solicitação em si. 

Há suporte para os seguintes campos e fontes:

Campos: **nome**, **variante**, **tipo**, **localização**, **marcações**, **marcações.*** e **alias de propriedade**. 

### <a name="property-aliases"></a>Aliases de propriedade
O alias de propriedade é um nome que pode ser usado em uma definição de política para acessar as propriedades específicas do tipo de recurso, como SKUs e configurações. Ele funciona em todas as versões de API que contêm a propriedade. Aliases podem ser recuperados usando a API REST abaixo (o suporte ao PowerShell será adicionado no futuro):

    GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01

O exemplo a seguir mostra uma definição de um alias. Como é possível ver, um alias define caminhos em diferentes versões de API, mesmo quando há uma alteração de nome da propriedade. 

    "aliases": [
        {
          "name": "Microsoft.Storage/storageAccounts/sku.name",
          "paths": [
            {
              "path": "properties.accountType",
              "apiVersions": [
                "2015-06-15",
                "2015-05-01-preview"
              ]
            },
            {
              "path": "sku.name",
              "apiVersions": [
                "2016-01-01"
              ]
            }
          ]
        }
    ]

Atualmente, os aliases com suporte são:

| Nome do alias | Descrição |
| --- | --- |
| {resourceType}/sku.name |Os tipos de recurso com suporte são: Microsoft.Compute/virtualMachines,<br />Microsoft.Storage/storageAccounts,<br />Microsoft.Web/serverFarms,<br /> Microsoft.Scheduler/jobcollections,<br />Microsoft.DocumentDB/databaseAccounts,<br />Microsoft.Cache/Redis,<br />Microsoft.CDN/profiles |
| {resourceType}/sku.family |O tipo de recurso com suporte é Microsoft.Cache/Redis |
| {resourceType}/sku.capacity |O tipo de recurso com suporte é Microsoft.Cache/Redis |
| Microsoft.Compute/virtualMachines/imagePublisher | |
| Microsoft.Compute/virtualMachines/imageOffer | |
| Microsoft.Compute/virtualMachines/imageSku | |
| Microsoft.Compute/virtualMachines/imageVersion | |
| Microsoft.Cache/Redis/enableNonSslPort | |
| Microsoft.Cache/Redis/shardCount | |
| Microsoft.SQL/servers/version | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveId | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveName | |
| Microsoft.SQL/servers/databases/edition | |
| Microsoft.SQL/servers/databases/elasticPoolName | |
| Microsoft.SQL/servers/elasticPools/dtu | |
| Microsoft.SQL/servers/elasticPools/edition | |

Atualmente, a política só funciona em solicitações PUT. 

## <a name="effect"></a>Efeito
A política dá suporte a três tipos de efeito: **negar**, **auditar** e **acrescentar**. 

* Negar gera um evento no log de auditoria e causa uma falha da solicitação
* Auditar gera um evento no log de auditoria, mas não causa falha da solicitação
* Acrescentar adiciona o conjunto de campos definido à solicitação 

Para **acrescentar**, você precisa fornecer os detalhes abaixo:

    ....
    "effect": "append",
    "details": [
      {
        "field": "field name",
        "value": "value of the field"
      }
    ]

O valor pode ser uma cadeia de caracteres ou um objeto no formato JSON. 

## <a name="policy-definition-examples"></a>Exemplos de definições de política
Agora vamos ver como podemos definir a política para obter os cenários anteriores.

### <a name="chargeback-require-departmental-tags"></a>Estorno: exigir marcas departamentais
A política a seguir nega todas as solicitações que não têm uma marcação contendo a chave "costCenter".

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

A política a seguir acrescentará a marcação costCenter com um valor predefinido quando nenhuma marca estiver presente. 

    {
      "if": {
        "field": "tags",
        "exists": "false"
      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags",
            "value": {"costCenter":"myDepartment" }
          }
        ]
      }
    }

A política a seguir acrescentará a marcação costCenter com um valor predefinido quando a marcação costCenter não estiver presente, mas outras marcas sim. 

    {
      "if": {
        "allOf": [
          {
            "field": "tags",
            "exists": "true"
          },
          {
            "field": "tags.costCenter",
            "exists": "false"
          }
        ]

      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags.costCenter",
            "value": "myDepartment"
          }
        ]
      }
    }


### <a name="geo-compliance-ensure-resource-locations"></a>Conformidade geográfica: garantir locais de recursos
O exemplo a seguir mostra uma política que nega as solicitações em que o local não é Europa Setentrional nem Europa Ocidental.

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="service-curation-select-the-service-catalog"></a>Curadoria de serviço: selecionar o catálogo de serviços
O exemplo a seguir mostra uma política que permite ações somente nos serviços de tipo Microsoft.Resources/\*, Microsoft.Compute/\*, Microsoft.Storage/\* e Microsoft.Network/\*. Qualquer outra coisa é negada.

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "field" : "type",
              "like" : "Microsoft.Resources/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Compute/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Storage/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="use-approved-skus"></a>Usar SKUs aprovados
O exemplo a seguir mostra o uso de alias de propriedade para restringir SKUs. No exemplo, apenas Standard_LRS e Standard_GRS estão aprovados para contas de armazenamento.

    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "allof": [
                {
                  "field": "Microsoft.Storage/storageAccounts/sku.name",
                  "in": ["Standard_LRS", "Standard_GRS"]
                }
              ]
            }
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }


### <a name="naming-convention"></a>Convenção de nomenclatura
O exemplo a seguir mostra o uso de curingas que é compatível com a condição "como". A condição declara que, se o nome corresponder ao padrão mencionado (namePrefix\*nameSuffix), a solicitação será negada.

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="tag-requirement-just-for-storage-resources"></a>Requisito de marca apenas para recursos de armazenamento
O exemplo a seguir mostra como aninhar operadores lógicos para exigir uma marcação de aplicativo somente para recursos de armazenamento.

    {
        "if": {
            "allOf": [
              {
                "not": {
                  "field": "tags",
                  "containsKey": "application"
                }
              },
              {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
              }
            ]
        },
        "then": {
            "effect": "audit"
        }
    }

## <a name="create-and-assign-a-policy"></a>Criar e atribuir uma política
Aplicar uma política requer a criação de uma definição de política e, em seguida, sua aplicação um escopo. 

### <a name="rest-api"></a>API REST
Você pode criar uma política com a [API REST para Definições de Política](https://docs.microsoft.com/rest/api/resources/policydefinitions). A API REST permite que você crie e exclua as definições de políticas e obtenha informações sobre as definições existentes.

Para criar uma política, execute:

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

Para a api-version, use *2016-04-01*. Inclua um corpo de solicitação semelhante ao exemplo a seguir:

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "name":"testdefinition"
    }

Você pode aplicar a definição de política no escopo desejado por meio da [API REST para atribuições de política](https://docs.microsoft.com/rest/api/resources/policyassignments). A API REST permite que você crie e exclua as atribuições de políticas e obtenha informações sobre as atribuições existentes.

Para criar uma atribuição de política, execute:

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

A {Atribuição da política} é o nome da atribuição da política. Para a api-version, use *2016-04-01*. 

Com um corpo de solicitação semelhante ao exemplo a seguir:

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "name":"VMPolicyAssignment"
    }

### <a name="powershell"></a>PowerShell
Você pode criar uma definição de política usando o cmdlet New-AzureRmPolicyDefinition. O exemplo a seguir cria uma política para permitir recursos somente na Europa Setentrional e na Ocidental.

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{    
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'            

A saída da execução é armazenada no objeto $policy e pode ser usada posteriormente durante a atribuição da política. Para o parâmetro de política, o caminho para um arquivo .json que contém a política também pode ser fornecido em vez de especificar a política incorporada.

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain     regions" -Policy "path-to-policy-json-on-disk"

Você pode aplicar a política para o escopo desejado usando o cmdlet New-AzureRmPolicyAssignment:

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Aqui, $policy é o objeto de política que foi retornado como resultado da execução do cmdlet New-AzureRmPolicyDefinition. O escopo aqui é o nome do grupo de recursos que você especificar.

Para remover uma atribuição de política, use:

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Você pode obter, alterar ou remover as definições de políticas por meio dos cmdlets Get-AzureRmPolicyDefinition, Set-AzureRmPolicyDefinition e Remove-AzureRmPolicyDefinition respectivamente.

Da mesma forma, você pode obter, alterar ou remover as atribuições da política por meio dos cmdlets Get-AzureRmPolicyAssignment, Set-AzureRmPolicyAssignment e Remove-AzureRmPolicyAssignment respectivamente.

### <a name="azure-cli"></a>CLI do Azure
Você pode criar uma definição de política usando a CLI do Azure com o comando de definição de política. O exemplo a seguir cria uma política para permitir recursos somente na Europa Setentrional e na Ocidental.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{    
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'    


É possível especificar o caminho para um arquivo .json contendo a política em vez de especificar a política embutida.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"

Você pode aplicar a política para o escopo desejado usando o comando de atribuição de política:

    azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/<policy-name> --scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

O escopo aqui é o nome do grupo de recursos que você especificar. Se o valor do parâmetro policy-definition-id for desconhecido, será possível obtê-lo por meio da CLI do Azure. 

    azure policy definition show <policy-name>

Para remover uma atribuição de política, use:

    azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Você pode obter, alterar ou remover as definições de política por meio dos comandos show, set e delete da definição de política, respectivamente.

De modo semelhante, é possível obter, alterar ou remover atribuições de política por meio dos comandos show e delete da atribuição de política, respectivamente.

## <a name="policy-audit-events"></a>Eventos de auditoria de política
Depois de aplicar a política, você começará a ver eventos relacionados à política. Você pode acessar o portal, usar o PowerShell ou a CLI do Azure para obter esses dados. 

### <a name="powershell"></a>PowerShell
Para exibir todos os eventos relacionados ao efeito de recusa, você pode usar o comando do PowerShell a seguir:

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/deny/action"} 

Para exibir todos os eventos relacionados ao efeito de auditoria, você pode usar o comando a seguir:

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/audit/action"} 

### <a name="azure-cli"></a>CLI do Azure
Para exibir todos os eventos de um grupo de recursos que se relacionam ao efeito de recusa, você pode usar o comando de CLI a seguir:

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/deny/action\")"

Para exibir todos os eventos relacionados ao efeito de auditoria, você pode usar o comando de CLI a seguir:

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/audit/action\")"

## <a name="view-a-policy"></a>Exibir uma política
Use o PowerShell, CLI do Azure ou API REST para exibir uma política. Talvez seja necessário exibir uma política depois de uma implantação falhar e você desejará ver a regra que negou a implantação. A mensagem de erro inclui uma ID para a definição de política.

### <a name="powershell"></a>PowerShell
Para obter uma política, use o seguinte cmdlet:

    (Get-AzureRmPolicyAssignment -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}").Properties.policyRule | ConvertTo-Json

Ele retorna o JSON para a definição de política.

### <a name="azure-cli"></a>CLI do Azure
Para obter uma política, use o seguinte comando:

    azure policy definition show {definition-name} --json

### <a name="rest-api"></a>API REST
Para obter uma política, use a operação [Obter definição de política](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get).

## <a name="next-steps"></a>Próximas etapas
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).




<!--HONumber=Nov16_HO3-->


