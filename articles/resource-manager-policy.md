<properties
	pageTitle="Política do Gerenciador de Recursos do Azure | Microsoft Azure"
	description="Descreve como usar a política do Gerenciador de Recursos do Azure para evitar violações em escopos diferentes como assinatura de recursos individuais ou grupos de recursos."
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="10/06/2015"
	ms.author="gauravbh;tomfitz"/>

# Usar a política para gerenciar recursos e controlar o acesso

O Gerenciador de Recursos do Azure agora permite que você controle o acesso por meio de políticas personalizadas. Uma política representa uma ou mais violações que podem ser evitadas no escopo desejado. Um escopo nesse caso pode ser uma assinatura, grupo de recursos ou um recurso individual.

Política é um sistema de ativação padrão. Uma política é definida por meio de uma definição de política e aplicada por meio de uma atribuição de política. as atribuições de política permite controlar o escopo da qual uma política pode ser aplicada.

Neste artigo, explicaremos a estrutura básica de linguagem de definição de política que você pode usar para criar políticas. Em seguida, descreveremos como você pode aplicar essas políticas em escopos diferentes e finalmente, mostraremos alguns exemplos sobre como você pode obter isso por meio da REST API. O suporte do PowerShell também será adicionado em breve.

## Cenários comuns

Um cenário comum é requerer marcas departamentais para fins de estorno. Uma organização talvez queira permitir operações somente quando o centro de custo apropriado for associado. Caso contrário, a solicitação será negada. Isso os ajudaria a cobrar o centro de custo apropriado para as operações executadas.

Outro cenário comum é que a organização talvez queira controlar os locais onde os recursos são criados. Ou talvez queiram controlar o acesso aos recursos, permitindo que somente determinados tipos de recursos sejam provisionados.

Da mesma forma, uma organização pode controlar o catálogo de serviços ou aplicar as convenções de nomenclatura desejadas para os recursos.

Usando políticas, esses cenários podem ser facilmente obtidos, conforme descrito abaixo.

## Estrutura da definição de política

A definição de política é criada usando JSON. Consiste em uma ou mais condições/operadores lógicos que definem as ações e o efeito que informa o que acontece quando as condições são atendidas.

Basicamente, uma política contém o seguinte:

**Condição/operadores lógicos/:** contém um conjunto de condições que podem ser manipuladas por meio de um conjunto de operadores lógicos.

**Efeito:** descreve qual será o efeito quando a condição for satisfeita – negar ou auditar. Um efeito de auditoria emitirá um log de aviso de serviço de evento. Por exemplo, um administrador pode criar uma política que ocasiona uma auditoria se alguém criar uma VM grande e examinar os logs mais tarde.

    {
      "if" : {
        <condition> | <logical operator>
      },
      "then" : {
        "effect" : "deny | audit"
      }
    }


## Operadores lógicos

Os operadores lógicos compatíveis junto com a sintaxe estão listados abaixo:

| Nome do operador | Sintaxe |
| :------------- | :------------- |
| Not | "not" : {&lt;condition&gt;} |
| e | "allOf" : [ {&lt;condition1&gt;},{&lt;condition2&gt;}] |
| Ou | "anyOf" : [ {&lt;condition1&gt;},{&lt;condition2&gt;}] |


## Condições

As condições compatíveis, juntamente com a sintaxe estão listadas abaixo:

| Nome da condição | Sintaxe |
| :------------- | :------------- |
| É igual a | "equals" : "&lt;value&gt;" |
| Como | "like" : "&lt;value&gt;" |
| Contém: | "contains" : "&lt;value&gt;"|
| No | "in" : [ "&lt;value1&gt;","&lt;value2&gt;" ]|
| ContainsKey | containsKey" : "&lt;keyName&gt;" |


## Campos e fontes

As condições são formadas por meio de campos e fontes. Há suporte para os seguintes campos e fontes:

Campos: **nome**, **forma**, **tipo**, **local**, **tags**, **tags.***.

Fontes: **ação**

## Exemplos de definições de política

Agora veremos como definir a política para obter os cenários listados acima.

### Estorno: Requerem marcas departamentais

A política a seguir nega todas as solicitações que não têm uma marca que contenha a chave "costCenter".

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


### Conformidade geográfica: Garantir locais de recursos

O exemplo abaixo mostra uma política que negará todas as solicitações em que o local não seja Norte da Europa ou Europa Ocidental.

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

### Curadoria de serviço: Selecione o catálogo de serviços

O exemplo abaixo mostra o uso do código-fonte. Ele mostra que ações somente nos serviços do tipo Microsoft.Resources/*, Microsoft.Compute/*, Microsoft.Storage/*, Microsoft.Network/* são permitidas. Todo o resto será negado.

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "source" : "action",
              "like" : "Microsoft.Resources/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Compute/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Storage/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### Convenção de nomenclatura

O exemplo abaixo mostra o uso de curingas que é compatível com a condição "como". A condição declara que se o nome corresponder ao padrão mencionado (namePrefix * nameSuffix), a solicitação é negada.

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

## Atribuição de política

As políticas podem ser aplicadas em escopos diferentes, como assinatura, grupos de recursos e recursos individuais. As políticas são herdadas por todos os recursos filho. Então, se uma política for aplicada a um grupo de recursos, será aplicável a todos os recursos desse grupo de recursos.

## Criando uma política

Esta seção fornece detalhes sobre como uma política pode ser criada usando a API REST.

### Crie definição de política com a API REST

Você pode criar uma política com a [API REST para Definições de Política](https://msdn.microsoft.com/library/azure/mt588471.aspx). A API REST permite que você crie e exclua as definições de políticas e obtenha informações sobre as definições existentes.

Para criar uma nova política, execute:

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

Com um corpo de solicitação semelhante ao seguinte:

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
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
      "type":"Microsoft.Authorization/policyDefinitions",
      "name":"testdefinition"
    }


Definição de política pode ser definida como um dos exemplos mostrados acima. Para a versão de api, use a *2015-10-01-preview*. Para obter mais detalhes e exemplos, consulte a [API REST para Definições de Política](https://msdn.microsoft.com/library/azure/mt588471.aspx).

### Criar definição de política usando o PowerShell

Você pode criar uma nova definição de política usando o cmdlet New-AzureRmPolicyDefinition, conforme mostrado abaixo. Os exemplos a seguir criam uma política para permitir recursos somente na Europa Setentrional e Ocidental.

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation onlyin certain regions" -Policy '{	"if" : {
    	    			    "not" : {
    	      			    	"field" : "location",
    	      			    		"in" : ["northeurope" , "westeurope"]
    	    			    	}
    	    		          },
    	      		    		"then" : {
    	    			    		"effect" : "deny"
    	      			    		}
    	    		    	}'    		

A saída da execução é armazenada no objeto $policy, visto que ele pode ser usado durante a atribuição da política. Para o parâmetro de política, o caminho para um arquivo .json que contém a política também pode ser fornecido em vez de especificar a política incorporada, conforme mostrado abaixo.

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain 	regions" -Policy "path-to-policy-json-on-disk"


## Aplicando uma política

### Atribuição de política com a API REST

Você pode aplicar a definição de política no escopo desejado por meio da [API REST para atribuições de política](https://msdn.microsoft.com/library/azure/mt588466.aspx). A API REST permite que você crie e exclua as atribuições de políticas e obtenha informações sobre as atribuições existentes.

Para criar uma nova atribuição de política, execute:

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

A {Atribuição da política} é o nome da atribuição da política. Para a versão de api, use a *2015-10-01-preview*.

Com um corpo de solicitação semelhante ao seguinte:

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyAssignments/VMPolicyAssignment",
      "type":"Microsoft.Authorization/policyAssignments",
      "name":"VMPolicyAssignment"
    }

Para obter mais detalhes e exemplos, veja a [API REST para Atribuições de Política](https://msdn.microsoft.com/library/azure/mt588466.aspx).

### Atribuição de política usando o PowerShell

Você pode aplicar a política criada acima por meio do PowerShell para o escopo desejado usando o cmdlet New-AzureRmPolicyAssignment, conforme mostrado abaixo:

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>
        
Aqui, $policy é o objeto de política que foi retornado como resultado da execução do cmdlet New-AzureRmPolicyDefinition, conforme mostrado acima. O escopo aqui é o nome do grupo de recursos que você especificar.

Se você desejar remover a atribuição de política acima, você pode fazê-lo da seguinte forma:

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Você pode obter, alterar ou remover as definições de políticas por meio dos cmdlets Get-AzureRmPolicyDefinition, Set-AzureRmPolicyDefinition e Remove-AzureRmPolicyDefinitionn respectivamente.

Da mesma forma, você pode obter, alterar ou remover as atribuições da política por meio dos cmdlets Get-AzureRmPolicyAssignment, Set-AzureRmPolicyAssignment e Remove-AzureRmPolicyAssignment respectivamente.

<!---HONumber=Oct15_HO3-->