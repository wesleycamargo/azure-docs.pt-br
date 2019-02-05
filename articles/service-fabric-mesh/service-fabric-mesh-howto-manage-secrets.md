---
title: Gerenciar segredos do aplicativo da Malha do Azure Service Fabric | Microsoft Docs
description: Gerencie os Segredos de aplicativos para que você possa criar e implantar um aplicativo de Malha do Service Fabric com segurança.
services: service-fabric-mesh
keywords: segredos
author: aljo-microsoft
ms.author: aljo
ms.date: 11/28/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: chackdan
ms.openlocfilehash: 06d8519836129a557ec69d59d15eb12129e8099b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236744"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>Gerenciar segredos do aplicativo de Malha do Azure Service Fabric
A Malha do Service Fabric dá suporte aos Segredos como recursos do Azure. Um segredo da Malha do Service Fabric pode ser qualquer informação de texto confidencial como cadeias de conexão de armazenamento, senhas ou outros valores que devem ser armazenados e transmitidos de forma segura. Este artigo mostra como usar o Serviço de Repositório Seguro do Service Fabric para implantar e manter Segredos.

Um Segredo de aplicativo de Malha consiste em:
* Um recurso **Segredos**, que é um contêiner que armazena os segredos de texto. Os segredos contidos no recurso **Segredos** são armazenados e transmitidos de forma segura.
* Um ou mais recursos **Segredos/Valores** que são armazenados no contêiner do recurso **Segredos**. Cada recurso **Segredos/valores** é diferenciado por um número de versão. Não é possível modificar a versão de um recurso **Segredos/valores**, anexe apenas uma nova versão.

O gerenciamento dos Segredos é composto pelas seguintes etapas:
1. Declare um recurso **Segredos** de Malha em um arquivo YAML ou JSON do Azure Resource Model que usa as opções inlinedValue kind e SecretsStoreRef contentType.
2. Declare recursos **Segredos/Valores** de Malha em um arquivo YAML ou JSON do Azure Resource Model que será armazenado no recurso **Secrets** (da etapa 1).
3. Modificar o aplicativo de Malha para fazer referência a valores de segredos de Malha.
4. Implemente ou atualize o aplicativo de Malha para consumir valores de segredos.
5. Use os comandos da CLI do Azure "az" no gerenciamento do ciclo de vida do Serviço de Repositório Seguro.

## <a name="declare-a-mesh-secrets-resource"></a>Declarar um recurso de Segredos de Malha
Um recurso Segredos de Malha que é declarado em um arquivo YAML ou JSON do Azure Resource Model que usa as definições inlinedValue kind e SecretsStoreRef contentType. O recurso de Segredos de Malha dá suporte aos segredos originados do Serviço de Repositório Seguro. 
>
Veja a seguir um exemplo de como declarar os recursos de Segredos de Malha em um arquivo JSON:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "eastus",
      "metadata": {
        "description": "Location of the resources."
      }
    },
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MySecret.txt",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "My Mesh Application Secret",
        "contentType": "SecretsStoreRef",
        "value": "mysecret",
      }
    },
  ]
}
```
Veja a seguir um exemplo de como declarar os recursos de Segredos de Malha em um arquivo YAML:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="declare-mesh-secretsvalues-resources"></a>Declarar recursos Segredos/valores de Malha
Os recursos Segredos/Valores de Malha dependem dos recursos Segredos de Malha definidos na etapa anterior.

Quanto à relação entre os campos "valor:" e "nome:" da seção "recursos": a segunda parte da cadeia de caracteres de "nome:", delimitada por dois pontos, é o número da versão usada no segredo e o nome antes dos dois pontos precisa corresponder ao valor do segredo de Malha do qual possui uma dependência. Por exemplo, para o elemento ```name: mysecret:1.0```, o número da versão é 1.0 e o nome ```mysecret``` deve corresponder ao ```"value": "mysecret"``` definido anteriormente.

>
Veja a seguir um exemplo de como declarar os recursos de Segredos/Valores de Malha em um arquivo JSON:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "eastus",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "my-secret-value-v1": {
      "type": "string",
      "metadata": {
        "description": "My Mesh Application Secret Value."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MySecret.txt",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]",
      "properties": {
        "kind": "inlinedValue",
        "description": "My Mesh Application Secret",
        "contentType": "SecretsStoreRef",
        "value": "mysecret",
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "mysecret:1.0",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        'Microsoft.ServiceFabricMesh/secrets/MySecret.txt'
      ],
      "properties": {
        "value": "[parameters('my-secret-value-v1)]"
      }
    }
  ]
}
```
Veja a seguir um exemplo de como declarar os recursos de Segredos/Valores de Malha em um arquivo YAML:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          Secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
            - name: mysecret:1.0
            description: My Mesh Application Secret Value
            secret_type: value
            content_type: text/plain
            value: "P@ssw0rd#1234"
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>Modificar o aplicativo de Malha para fazer referência a valores de Segredo de Malha
Os aplicativos de Malha do Service Fabric precisam estar cientes das duas cadeias de caracteres a seguir para consumir os valores do Segredo do Serviço de Repositório Seguro:
1. Microsoft.ServiceFabricMesh/Secrets.name contém o nome do arquivo e conterá o valor de Segredos em texto sem formatação.
2. A variável de ambiente do Windows ou Linux "Fabric_SettingPath" contém o caminho do diretório para o qual os arquivos contendo os valores dos Segredos do Serviço de Repositório Seguro estarão acessíveis. Ou seja, "C:\Settings" para aplicativos de Malha hospedados no Windows e "/var/settings" para aplicativos de Malha hospedados no Linux, respectivamente.

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>Implantar ou usar uma atualização sem interrupção para o aplicativo de Malha para consumir valores de Segredos
A criação de Segredos e/ou Segredos/Valores com versão é limitada a implantações declaradas do modelo de recursos. A única maneira de criar esses recursos é passando um arquivo JSON ou YAML do modelo de recursos usando o comando **implantação do az mesh** da seguinte forma:

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>Usar os comandos da CLI do Azure no gerenciamento do ciclo de vida do Serviço de Repositório Seguro

### <a name="create-a-new-secrets-resource"></a>Criar um novo recurso Segredos
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
Passe **template-file** ou **template-uri** (mas não ambos).

Por exemplo: 
- criar implantação do az mesh --c:\MyMeshTemplates\SecretTemplate1.txt
- criar implantação do az mesh -- https://www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>Mostrar um segredo
Retorna a descrição do segredo (mas não o valor).
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>Excluir um segredo

- Um segredo não pode ser excluído enquanto ele estiver sendo referenciado por um aplicativo de Malha.
- Excluir um recurso Segredos excluirá todas as versões de Segredos/recursos.
```azurecli-interactive
az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="list-secrets-in-subscription"></a>Listar segredos na assinatura
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>Listar segredos no grupo de recursos
```azurecli-interactive
az mesh secret list -g <myResourceGroup>
```
### <a name="list-all-versions-of-a-secret"></a>Listar todas as versões de um segredo
```azurecli-interactive
az mesh secretvalue list --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="show-secret-version-value"></a>Mostrar o valor da versão do segredo
```azurecli-interactive
az mesh secretvalue show --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

### <a name="delete-secret-version-value"></a>Excluir o valor da versão do segredo
```azurecli-interactive
az mesh secretvalue delete --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

## <a name="next-steps"></a>Próximas etapas 
Para saber mais sobre Malha do Service Fabric, leia a visão geral:
- [Visão geral da Malha do Service Fabric](service-fabric-mesh-overview.md)
