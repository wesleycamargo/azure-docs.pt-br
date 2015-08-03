<properties
   pageTitle="Funções de modelo do Gerenciador de Recursos do Azure"
   description="Descreve as funções a serem usadas no modelo do Gerenciador de Recursos do Azure para recuperar valores, cadeias de caracteres de formato e recuperar informações de implantação."
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
   ms.date="07/15/2015"
   ms.author="tomfitz"/>

# Funções de modelo do Gerenciador de Recursos do Azure

Este tópico descreve todas as funções que você pode usar em um modelo do Gerenciador de Recursos do Azure.

## base64

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

## concat

**concat (arg1, arg2, arg3, ...)**

Combina vários valores de cadeia de caracteres e retorna o valor de cadeia de caracteres resultante. Essa função pode conter qualquer número de argumentos.

O exemplo a seguir mostra como combinar diversos valores para retornar um valor.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

## implantação

**deployment()**

Retorna informações sobre a operação de implantação atual.

As informações sobre a implantação são retornadas como um objeto com as seguintes propriedades:

    {
      "name": "",
      "properties": {
        "template": {},
        "parameters": {},
        "mode": "",
        "provisioningState": ""
      }
    }

O exemplo a seguir mostra como retornar informações sobre a implantação da seção de saídas.

    "outputs": {
      "exampleOutput": {
        "value": "[deployment()]",
        "type" : "object"
      }
    }

## listKeys

**listKeys (resourceName or resourceIdentifier, [apiVersion])**

Retorna as chaves para uma conta de armazenamento. A identificação de recurso pode ser especificada usando a [função resourceId](./#resourceid) ou usando o formato **providerNamespace/resourceType/resourceName**. Você pode usar a função para obter a primaryKey e secondaryKey.
  
| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| resourceName ou resourceIdentifier | Sim | Identificador exclusivo de uma conta de armazenamento.
| apiVersion | Sim | Versão de API do estado de tempo de execução do recurso.

O exemplo a seguir mostra como retornar as chaves de uma conta de armazenamento na seção de saídas.

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]", 
        "type" : "object" 
      } 
    } 

## padLeft

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


## parameters

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

## provider

**provider (providerNamespace, [resourceType])**

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

## reference

**reference (resourceName or resourceIdentifier, [apiVersion])**

Habilita uma expressão a derivar seu valor do estado de tempo de execução do outro recurso.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| resourceName ou resourceIdentifier | Sim | Nome ou identificador exclusivo de um recurso.
| apiVersion | Não | Versão de API do estado de tempo de execução do recurso. O parâmetro deve ser usado se o recurso não está provisionado no mesmo modelo.

A função **referência** deriva seu valor de um estado de tempo de execução e, portanto, não pode ser usada na seção de variáveis. Ela pode ser usada na seção de saídas de um modelo.

Usando a expressão de referência, você declara que um recurso depende de outro recurso se o recurso referenciado é provisionado no mesmo modelo.

    "outputs": {
      "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
      }
    }

## substitui

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

## resourceGroup

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

## resourceId

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
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
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


## assinatura

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

## toLower

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

## toUpper

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


## variáveis

**variables (NomedaVariável)**

Retorna o valor da variável. O nome do parâmetro especificado deve ser definido na seção variáveis do modelo.

| Parâmetro | Obrigatório | Descrição
| :--------------------------------: | :------: | :----------
| Nome de variável | Sim | O nome da variável a retornar.


## Próximas etapas
- [Criação de modelos do Gerenciador de Recursos do Azure](./resource-group-authoring-templates.md)
- [Operações avançadas de modelo](./resource-group-advanced-template.md)
- [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](azure-portal/resource-group-template-deploy.md)
- [Visão Geral do Gerenciador de Recursos do Azure](./resource-group-overview.md)

<!---HONumber=July15_HO4-->