<properties
   pageTitle="Expressões do modelo do Gerenciador de Recursos | Microsoft Azure"
   description="Descreve as funções a serem usadas no modelo do Gerenciador de Recursos do Azure para recuperar valores, trabalhar com cadeias de caracteres e numéricos e recuperar informações de implantação."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/23/2015"
   ms.author="tomfitz"/>

# Expressões de modelo do Gerenciador de Recursos do Azure

Este tópico descreve todas as expressões que você pode usar em um modelo do Gerenciador de Recursos do Azure.

As expressões do modelo e seus parâmetros não diferenciam maiúsculas de minúsculas. Por exemplo, o Gerenciador de Recursos resolve **variables('var1')** e **VARIABLES('VAR1')** da mesma forma. Quando avaliada, a menos que a expressão modifique nitidamente as maiúsculas e minúsculas (como toUpper ou toLower), a expressão preservará a capitalização. Determinados tipos de recursos podem ter requisitos de maiúsculas e minúsculas independentemente de como as expressões são avaliadas.

## Expressões numéricas

O Gerenciador de Recursos fornece as seguintes expressões para trabalhar com números inteiros:

- [adicionar](#add)
- [copyIndex](#copyindex)
- [div](#div)
- [int](#int)
- [length](#length)
- [mod](#mod)
- [mul](#mul)
- [sub](#sub)


<a id="add" />
### adicionar

**add(operand1, operand2)**

Retorna a soma dos dois inteiros fornecidos.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| operand1 | Sim | Primeiro operando a usar.
| operand2 | Sim | Segundo operando a usar.


<a id="copyindex" />
### copyIndex

**copyIndex(offset)**

Retorna o índice atual de um loop de iteração.

Esta expressão é sempre usada com um objeto **copy**. Para obter exemplos de como usar **copyIndex**, confira [Criar várias instâncias de recursos no Gerenciador de Recursos do Azure](resource-group-create-multiple.md).


<a id="div" />
### div

**div(operand1, operand2)**

Retorna a divisão de inteiros dos dois inteiros fornecidos.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| operand1 | Sim | Número que está sendo dividido.
| operand2 | Sim | Número que é usado para dividir, deve ser diferente de 0.


<a id="int" />
### int

**int(valueToConvert)**

Converte o valor especificado em Integer.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| valueToConvert | Sim | O valor a ser convertido em Integer. O tipo de valor pode ser apenas String ou Integer.

O exemplo a seguir converte o valor do parâmetro fornecido pelo usuário em Integer.

    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    }


<a id="length" />
### length

**comprimento (matriz ou cadeia de caracteres)**

Retorna o número de elementos em uma matriz ou o número de caracteres em uma cadeia de caracteres. Essa função pode ser usada com uma matriz para especificar o número de iterações durante a criação de recursos. No exemplo a seguir, o parâmetro **siteNames** faz referência a uma matriz de nomes a serem usados durante a criação de sites da web.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

Para saber mais sobre como usar essa função com uma matriz, confira [Criar várias instâncias de recursos no Gerenciador de Recursos do Azure](resource-group-create-multiple.md).

Ou você pode usar com uma cadeia de caracteres:

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "nameLength": "[length(parameters('appName'))]"
    }


<a id="mod" />
### mod

**mod(operand1, operand2)**

Retorna o restante da divisão de inteiros usando os dois inteiros fornecidos.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| operand1 | Sim | Número que está sendo dividido.
| operand2 | Sim | Número que é usado para dividir, deve ser diferente de 0.



<a id="mul" />
### mul

**mul(operand1, operand2)**

Retorna a multiplicação de dois inteiros fornecidos.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| operand1 | Sim | Primeiro operando a usar.
| operand2 | Sim | Segundo operando a usar.


<a id="sub" />
### sub

**sub(operand1, operand2)**

Retorna a subtração dos dois inteiros fornecidos.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| operand1 | Sim | Número que deve ser subtraído.
| operand2 | Sim | Número a ser subtraído.


## Expressões de cadeia de caracteres

O Gerenciador de Recursos fornece as seguintes expressões para trabalhar com cadeias de caracteres:

- [base64](#base64)
- [concat](#concat)
- [padLeft](#padleft)
- [substitui](#replace)
- [split](#split)
- [string](#string)
- [substring](#substring)
- [toLower](#tolower)
- [toUpper](#toupper)
- [cortar](#trim)
- [uniqueString](#uniquestring)
- [uri](#uri)

Para obter o número de caracteres em uma cadeia de caracteres ou em uma matriz, consulte [comprimento](#length).

<a id="base64" />
### base64

**base64 (inputString)**

Retorna a representação base64 da cadeia de caracteres de entrada.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| inputString | Sim | O valor de cadeia de caracteres a retornar como uma representação base64.

O exemplo a seguir mostra como usar a função base64.

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

<a id="concat" />
### concat

**concat (arg1, arg2, arg3, ...)**

Combina vários valores de cadeia de caracteres e retorna o valor de cadeia de caracteres resultante. Essa função pode conter qualquer número de argumentos.

O exemplo a seguir mostra como combinar diversos valores para retornar um valor.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

<a id="padleft" />
### padLeft

**padLeft(stringToPad, totalLength, paddingCharacter)**

Retorna uma cadeia de caracteres alinhada à direita adicionando caracteres à esquerda até alcançar o comprimento total especificado.
  
| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| stringToPad | Sim | A cadeia de caracteres para alinhar à direita.
| totalLength | Sim | O número total de caracteres na cadeia de caracteres retornada.
| paddingCharacter | Sim | O caractere a ser usado para o preenchimento à esquerda até que o tamanho total seja atingido.

O exemplo a seguir mostra como preencher o valor do parâmetro fornecido pelo usuário adicionando o caractere zero até que a cadeia de caracteres atinja 10 caracteres. Se o valor do parâmetro original for maior que 10 caracteres, nenhum caractere será adicionado.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }

<a id="replace" />
### substitui

**replace(originalString, oldCharacter, newCharacter)**

Retorna uma nova cadeia de caracteres com todas as instâncias de um caractere na cadeia de caracteres especificada substituída por outro caractere.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| originalString | Sim | A cadeia de caracteres que terá todas as instâncias de um caractere substituído por outro caractere.
| oldCharacter | Sim | O caractere a ser removido da cadeia de caracteres original.
| newCharacter | Sim | O caractere a ser adicionado no lugar do caractere removido.

O exemplo a seguir mostra como remover todos os traços da cadeia de caracteres fornecida pelo usuário.

    "parameters": {
        "identifier": { "type": "string" }
    },
    "variables": { 
        "newidentifier": "[replace(parameters('identifier'),'-','')]"
    }

<a id="split" />
### split

**split(inputString, delimiter)** **split(inputString, [delimiters])**

Retorna uma matriz de cadeias de caracteres que contém as subcadeias de caracteres da cadeia de caracteres de entrada que são delimitadas por delimitadores enviados.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| inputString | Sim | A cadeia de caracteres a ser dividida.
| delimiter | Sim | O delimitador a ser usado, pode ser uma única cadeia de caracteres ou uma matriz de cadeias de caracteres.

O exemplo a seguir divide a cadeia de caracteres de entrada com uma vírgula.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "stringPieces": "[split(parameters('inputString'), ',')]"
    }

<a id="string" />
### cadeia de caracteres

**string(valueToConvert)**

Converte o valor especificado em String.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| valueToConvert | Sim | O valor a ser convertido em String. O tipo de valor pode ser apenas Boolean, Integer ou String.

O exemplo a seguir converte o valor do parâmetro fornecido pelo usuário em String.

    "parameters": {
        "appId": { "type": "int" }
    },
    "variables": { 
        "stringValue": "[string(parameters('appId'))]"
    }

<a id="substring" />
### substring

**substring(stringToParse, startIndex, length)**

Retorna uma subcadeia de caraceteres que começa na posição do caractere especificado e contém o número especificado de caracteres.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| stringToParse | Sim | A cadeia original da qual a subcadeia de caracteres é extraída.
| startIndex | Não | A posição inicial do caractere baseada em zero para a subcadeia de caracteres.
| length | Não | O número de caracteres para a subcadeia de caracteres.

O exemplo a seguir extrai os três primeiros caracteres de um parâmetro.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "prefix": "[substring(parameters('inputString'), 0, 3)]"
    }

<a id="tolower" />
### toLower

**toLower(stringToChange)**

Converte a cadeia de caracteres especificada em letras minúsculas.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| stringToChange | Sim | A cadeia de caracteres a ser convertida em letras minúsculas.

O exemplo a seguir converte o valor do parâmetro fornecido pelo usuário em letras minúsculas.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "lowerCaseAppName": "[toLower(parameters('appName'))]"
    }

<a id="toupper" />
### toUpper

**toUpper(stringToChange)**

Converte a cadeia de caracteres especificada em maiúsculas.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| stringToChange | Sim | A cadeia de caracteres a ser convertida em letras maiúsculas.

O exemplo a seguir converte o valor do parâmetro fornecido pelo usuário em letras maiúsculas.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "upperCaseAppName": "[toUpper(parameters('appName'))]"
    }

<a id="trim" />
### cortar

**cortar (stringToTrim)**

Remove todos os caracteres de espaço em branco à esquerda e à direita da cadeia de caracteres especificada.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| stringToTrim | Sim | Cadeia de caracteres para cortar.

O exemplo a seguir remove os caracteres de espaço em branco do valor de parâmetro fornecido pelo usuário.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "trimAppName": "[trim(parameters('appName'))]"
    }

<a id="uniquestring" />
### uniqueString

**uniqueString (stringForCreatingUniqueString, ...)**

Executa um hash de 64 bits das cadeias de caracteres fornecidas para criar uma cadeia de caracteres exclusiva. Essa função é útil quando você precisa criar um nome exclusivo para um recurso. Você fornece valores de parâmetros que representam o nível de exclusividade para o resultado. Você pode especificar se o nome é exclusivo para sua assinatura, grupo de recursos ou implantação.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| stringForCreatingUniqueString | Sim | A cadeia de caracteres de base usada na função de hash para criar uma cadeia de caracteres exclusiva.
| parâmetros extras conforme necessário | Não | Você pode adicionar quantas cadeias de caracteres forem necessárias para criar o valor que especifica o nível de exclusividade.

O valor retornado não é uma cadeia de caracteres totalmente aleatória, mas na verdade, o resultado de uma função de hash. O valor retornado tem 13 caracteres. Não é garantido que ele seja globalmente exclusivo. Você talvez queira combinar o valor com um prefixo de sua convenção de nomenclatura para criar um nome mais amigável.

Os exemplos a seguir mostram como usar uniqueString para criar um valor exclusivo para níveis diferentes mais usados.

Exclusivo com base na assinatura

    "[uniqueString(subscription().subscriptionId)]"

Exclusivo com base no grupo de recursos

    "[uniqueString(resourceGroup().id)]"

Exclusivo com base na implantação de um grupo de recursos

    "[uniqueString(resourceGroup().id, deployment().name)]"
    
O exemplo a seguir mostra como criar um nome exclusivo para uma conta de armazenamento com base em seu grupo de recursos.

    "resources": [{ 
        "name": "[concat('ContosoStorage', uniqueString(resourceGroup().id))]", 
        "type": "Microsoft.Storage/storageAccounts", 
        ...

<a id="uri" />
### uri

**URI (baseUri, relativeUri)**

Cria um URI absoluto, combinando o baseUri e a cadeia de caracteres relativeUri.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| baseUri | Sim | Cadeia de caracteres do URI de base.
| relativeUri | Sim | Cadeia de caracteres de uri relativo para adicionar a cadeia de caracteres do uri de base.

O valor para o parâmetro **baseUri** pode incluir um arquivo específico, mas apenas o caminho base é usado ao construir a URI. Por exemplo, transmitir ****http://contoso.com/resources/azuredeploy.json** como parâmetro baseUri resultará em uma URI base ****http://contoso.com/resources/**.

O exemplo a seguir mostra como criar um link para um modelo aninhado com base no valor do modelo pai.

    "templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"



## Expressões de valor de implantação

O Gerenciador de Recursos fornece as seguintes expressões para obter valores de seções do modelo e valores relacionados à implantação:

- [implantação](#deployment)
- [parâmetros](#parameters)
- [variáveis](#variables)

Para obter valores de recursos, grupos de recursos ou assinaturas, consulte [Expressões de recurso](#resource-expressions).

<a id="deployment" />
### implantação

**deployment()**

Retorna informações sobre a operação de implantação atual.

Essa expressão retorna o objeto que é transmitido durante a implantação. As propriedades no objeto retornado vão variar dependendo se o objeto de implantação for transmitido como um link ou como um objeto na linha. Quando o objeto de implantação é passado na linha, como ao usar o parâmetro **- TemplateFile** no Azure PowerShell para apontar para um arquivo local, o objeto retornado fica no seguinte formato:

    {
        "name": "",
        "properties": {
            "template": {
                "$schema": "",
                "contentVersion": "",
                "resources": [
                ],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

Quando o objeto é transmitido como um link, como ao usar o parâmetro **- TemplateUri** para apontar para um objeto remoto, o objeto é retornado no seguinte formato.

    {
        "name": "",
        "properties": {
            "templateLink": {
                "uri": "",
                "contentVersion": ""
            },
            "mode": "",
            "provisioningState": ""
        }
    }

O exemplo a seguir mostra como usar a implantação() para um modelo aninhado com base no URI do modelo pai.

    "variables": {  
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
    }  


<a id="parameters" />
### parameters

**parâmetros (parameterName)**

Retorna um valor de parâmetro. O nome do parâmetro especificado deve ser definido na seção de parâmetros do modelo.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| parameterName | Sim | O nome do parâmetro a retornar.

O exemplo a seguir mostra um uso simplificado da função parâmetros.

    "parameters": { 
      "siteName": {
          "type": "string"
      }
    },
    "resources": [
       {
          "apiVersion": "2014-06-01",
          "name": "[parameters('siteName')]",
          "type": "Microsoft.Web/Sites",
          ...
       }
    ]

<a id="variables" />
### variáveis

**variables (NomedaVariável)**

Retorna o valor da variável. O nome do parâmetro especificado deve ser definido na seção variáveis do modelo.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| Nome de variável | Sim | O nome da variável a retornar.



## Expressões de recurso

O Gerenciador de Recursos fornece as seguintes expressões para obtenção de valores de recurso:

- [listkeys](#listkeys)
- [providers](#providers)
- [reference](#reference)
- [resourceGroup](#resourcegroup)
- [resourceId](#resourceid)
- [assinatura](#subscription)

Para obter valores de parâmetros, variáveis ou a implantação atual, confira [Expressões de valor de implantação](#deployment-value-expressions).

<a id="listkeys" />
### listKeys

**listKeys (resourceName ou resourceIdentifier, apiVersion)**

Retorna as chaves para uma conta de armazenamento. A identificação de recurso pode ser especificada usando a [função resourceId](./#resourceid) ou usando o formato **providerNamespace/resourceType/resourceName**. Você pode usar a função para obter a primaryKey e secondaryKey.
  
| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| resourceName ou resourceIdentifier | Sim | Identificador exclusivo de uma conta de armazenamento.
| apiVersion | Sim | Versão de API do estado de tempo de execução do recurso.

O exemplo a seguir mostra como retornar as chaves de uma conta de armazenamento na seção de saídas.

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2015-05-01-preview')]", 
        "type" : "object" 
      } 
    } 

<a id="providers" />
### providers

**providers (providerNamespace, [resourceType])**

Retorna informações sobre um provedor de recursos e seus tipos de recursos com suporte. Se nenhum tipo for fornecido, todos os tipos com suport são retornados.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| providerNamespace | Sim | Namespace do provedor
| resourceType | Não | O tipo de recurso no namespace especificado.

Cada tipo com suporte é retornado no seguinte formato:

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

O exemplo a seguir mostra como usar a função provider:

    "outputs": {
	    "exampleOutput": {
		    "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
		    "type" : "object"
	    }
    }

<a id="reference" />
### reference

**reference (resourceName or resourceIdentifier, [apiVersion])**

Habilita uma expressão a derivar seu valor do estado de tempo de execução do outro recurso.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| resourceName ou resourceIdentifier | Sim | Nome ou identificador exclusivo de um recurso.
| apiVersion | Não | Versão da API do recurso especificado. Você deverá incluir esse parâmetro quando o recurso não estiver provisionado no mesmo modelo.

A função **referência** deriva seu valor de um estado de tempo de execução e, portanto, não pode ser usada na seção de variáveis. Ela pode ser usada na seção de saídas de um modelo.

Usando a expressão de referência, você declara implicitamente que um recurso depende de outro recurso se o recurso referenciado é provisionado no mesmo modelo. Não é preciso usar a propriedade **dependsOn** também. A expressão não é avaliada até que o recurso referenciado conclua a implantação.

O exemplo a seguir faz referência a uma conta de armazenamento implantada no mesmo modelo.

    "outputs": {
		"NewStorage": {
			"value": "[reference(parameters('storageAccountName'))]",
			"type" : "object"
		}
	}

O exemplo a seguir faz referência a uma conta de armazenamento que não foi implantada no modelo, mas existe dentro do mesmo grupo de recursos que os recursos que estão sendo implantados.

    "outputs": {
		"ExistingStorage": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15')]",
			"type" : "object"
		}
	}

Você pode recuperar um valor específico do objeto retornado, como o URI do ponto de extremidade do blob, conforme mostrado abaixo.

    "outputs": {
		"BlobUri": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

Se agora você quiser especificar diretamente a versão da API no modelo, use a expressão **providers** e recupere um dos valores, como a versão mais recente mostrada abaixo.

    "outputs": {
		"BlobUri": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).primaryEndpoints.blob]",
			"type" : "string"
		}
	}

O exemplo a seguir faz referência a uma conta de armazenamento em um grupo de recursos diferente.

    "outputs": {
		"BlobUri": {
			"value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

<a id="resourcegroup" />
### resourceGroup

**resourceGroup()**

Retorna um objeto estruturado que representa o grupo de recursos atual. O objeto estará no seguinte formato:

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
    }

O exemplo a seguir usa o local do grupo de recursos para atribuir o local de um site Web.

    "resources": [
       {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          ...
       }
    ]

<a id="resourceid" />
### resourceId

**resourceId ([resourceGroupName], resourceType, resourceName1, [resourceName2]...)**

Retorna o identificador exclusivo de um recurso. Você pode usar essa função quando o nome do recurso é ambíguo ou não provisionado no mesmo modelo. O identificador é retornado no seguinte formato:

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}
      
| Parâmetro | Obrigatório | Descrição
| :---------------: | :------: | :----------
| resourceGroupName | Não | Grupo de recursos opcional. O valor padrão é o grupo de recursos atual. Especifique esse valor quando você recuperar um recurso em outro grupo de recursos.
| resourceType | Sim | Tipo de recurso, incluindo o namespace do provedor de recursos.
| resourceName1 | Sim | Nome do recurso.
| resourceName2 | Não | Próximo segmento de nome do recurso se o recurso está aninhado.

O exemplo a seguir mostra como recuperar as IDs de recurso para um site Web e um banco de dados. O site Web existe em um grupo de recursos denominado **myWebsitesGroup** e o banco de dados existe no grupo de recursos para este modelo.

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'),parameters('databaseName'))]
    
Frequentemente, você precisa usar essa função ao usar uma conta de armazenamento ou rede virtual em um grupo de recursos alternativo. A conta de armazenamento ou a rede virtual pode ser usada em vários grupos de recursos; portanto, você não deve excluí-los ao excluir um único grupo de recursos. O exemplo a seguir mostra como um recurso de um grupo de recursos externo pode ser facilmente usado:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]
    }

<a id="subscription" />
### assinatura

**subscription()**

Retorna detalhes sobre a assinatura no formato a seguir.

    {
        "id": "/subscriptions/#####"
        "subscriptionId": "#####"
    }

O exemplo a seguir mostra a função de assinatura chamada na seção de saídas.

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 


## Próximas etapas
- Para obter uma descrição das seções de um modelo do Gerenciador de Recursos do Azure, confira a seção [Criação de modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md)
- Para mesclar diversos modelos, confira a seção [Como usar modelos vinculados com o Gerenciador de Recursos do Azure](resource-group-linked-templates.md)
- Para iterar um número de vezes especificado ao criar um tipo de recurso, consulte [Criar várias instâncias de recursos no Gerenciador de Recursos do Azure](resource-group-create-multiple.md).
- Para ver como implantar o modelo que você criou, consulte [Implantar um aplicativo com o Modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md)

<!---HONumber=AcomDC_1223_2015-->