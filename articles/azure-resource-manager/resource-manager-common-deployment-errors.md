---
title: "Solução de erros comuns de implantação do Azure | Microsoft Docs"
description: Descreve como resolver erros comuns ao implantar recursos no Azure usando o Azure Resource Manager.
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "erro de implantação, implantação do azure, implante no azure"
ms.assetid: c002a9be-4de5-4963-bd14-b54aa3d8fa59
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/18/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 5aa0677e6028c58b7a639f0aee87b04e7bd233a0
ms.openlocfilehash: 2093c6220ea01a83b7e43b3084d13b719feca3ca


---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Solução de erros comuns de implantação do Azure com o Azure Resource Manager
Este tópico descreve como é possível resolver alguns erros de implantação comuns do Azure que você pode encontrar.

## <a name="two-types-of-errors"></a>Dois tipos de erros
Há dois tipos de erros que você pode receber:

* erros de validação
* erros de implantação

A imagem a seguir mostra o log de atividades de uma assinatura. Há três operações que ocorreram em duas implantações. Na primeira implantação, o modelo passou na validação, mas falhou ao criar os recursos (**Implantações de Gravação**). Na segunda implantação, o modelo falhou na validação e não prosseguiu com as **Implantações de Gravação**.

![mostrar código de erro](./media/resource-manager-common-deployment-errors/show-activity-log.png)

Os erros de validação surgem de cenários que podem ser determinados previamente como causadores de problema. Os erros de validação incluem erros de sintaxe em seu modelo ou tentar implantar recursos que excederiam suas cotas de assinatura. Os erros de implantação surgem de condições que ocorrem durante o processo de implantação. Por exemplo, um erro de implantação pode surgir de uma tentativa de acessar um recurso que está sendo implantado em paralelo.

Ambos os tipos de erro retornam um código de erro que você pode usar para solucionar os problemas de implantação. Os dois tipos de erro aparecem no [log de atividades](resource-group-audit.md). No entanto, os erros de validação não aparecem no seu histórico de implantação porque a implantação nunca foi iniciada.


## <a name="error-codes"></a>Códigos do Erro

Os seguintes códigos de erro estão descritos neste tópico:

* [AccountNameInvalid](#accountnameinvalid)
* [Falha na autorização](#authorization-failed)
* [BadRequest](#badrequest)
* [DeploymentFailed](#deploymentfailed)
* [InvalidContentLink](#invalidcontentlink)
* [InvalidTemplate](#invalidtemplate)
* [MissingSubscriptionRegistration](#noregisteredproviderfound)
* [NotFound](#notfound)
* [NoRegisteredProviderFound](#noregisteredproviderfound)
* [OperationNotAllowed](#quotaexceeded)
* [ParentResourceNotFound](#parentresourcenotfound)
* [QuotaExceeded](#quotaexceeded)
* [RequestDisallowedByPolicy](#requestdisallowedbypolicy)
* [ResourceNotFound](#notfound)
* [SkuNotAvailable](#skunotavailable)
* [StorageAccountAlreadyExists](#storagenamenotunique)
* [StorageAccountAlreadyTaken](#storagenamenotunique)

### <a name="deploymentfailed"></a>DeploymentFailed

Esse código de erro indica um erro de implantação geral, mas não é o código de erro que você precisa para começar a solução de problemas. O código de erro que o ajuda a resolver o problema de verdade fica um nível abaixo desse erro. Por exemplo, a imagem a seguir mostra o código de erro **RequestDisallowedByPolicy** que está por baixo do erro de implantação.

![mostrar código de erro](./media/resource-manager-common-deployment-errors/error-code.png)

### <a name="skunotavailable"></a>SkuNotAvailable

Ao implantar um recurso (normalmente uma máquina virtual), você pode receber o seguinte código de erro e a mensagem de erro:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

Você recebe esse erro quando o recurso SKU selecionado (como o tamanho da VM) não está disponível para o local escolhido. Para resolver esse problema, você precisa determinar quais SKUs estão disponíveis em uma região. Você pode usar o portal ou uma operação REST para localizar SKUs disponíveis.

- Para usar o [portal](https://portal.azure.com), faça logon no portal e adicionar um recurso por meio da interface. Ao definir os valores, verá os SKUs disponíveis para esse recurso. Não é necessário concluir a implantação.

    ![SKUs disponíveis](./media/resource-manager-common-deployment-errors/view-sku.png)

- Para usar a API REST para máquinas virtuais, envie a solicitação a seguir:

  ```HTTP 
  GET
  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/skus?api-version=2016-03-30
  ```

  Ele retorna SKUs e regiões disponíveis no seguinte formato:

  ```json
  {
    "value": [
      {
        "resourceType": "virtualMachines",
        "name": "Standard_A0",
        "tier": "Standard",
        "size": "A0",
        "locations": [
          "eastus"
        ],
        "restrictions": []
      },
      {
        "resourceType": "virtualMachines",
        "name": "Standard_A1",
        "tier": "Standard",
        "size": "A1",
        "locations": [
          "eastus"
        ],
        "restrictions": []
      },
      ...
    ]
  }    
  ```

Se não for possível encontrar um SKU adequado nessa região, ou em uma região alternativa que atenda às suas necessidades de negócios, entre em contato com o [Suporte do Azure](https://portal.azure.com/#create/Microsoft.Support).

### <a name="invalidtemplate"></a>InvalidTemplate
Esse erro pode resultar de vários tipos diferentes de erros.

- Erro de sintaxe

   Se você receber uma mensagem de erro que indica a validação do modelo falhou, você poderá ter um problema de sintaxe em seu modelo.

  ```
  Code=InvalidTemplate
  Message=Deployment template validation failed
  ```

   Esse erro é fácil de cometer porque as expressões do modelo podem ser complexas. Por exemplo, a atribuição de nome a seguir para uma conta de armazenamento contém um par de colchetes, três funções, três pares de parênteses, um par de aspas simples e uma propriedade:

  ```json
  "name": "[concat('storage', uniqueString(resourceGroup().id))]",
  ```

   Se você não fornecer a sintaxe correspondente, o modelo produzirá um valor diferente do que era sua intenção.

   Quando você receber esse tipo de erro, examine cuidadosamente a sintaxe da expressão. Considere usar um editor JSON como o [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) ou o [Visual Studio Code](resource-manager-vs-code.md) que pode avisá-lo sobre os erros de sintaxe.

- Comprimentos de segmento incorretos

   Outro erro de modelo inválido ocorre quando o nome do recurso não está no formato correto.

  ```
  Code=InvalidTemplate
  Message=Deployment template validation failed: 'The template resource {resource-name}'
  for type {resource-type} has incorrect segment lengths.
  ```

   Um recurso no nível-raiz deve ter um segmento a menos no nome que no tipo de recurso. Cada segmento é diferenciado por uma barra. No exemplo a seguir, o tipo tem dois segmentos e o nome tem um segmento, portanto, é um **nome válido**.

  ```json
  {
    "type": "Microsoft.Web/serverfarms",
    "name": "myHostingPlanName",
    ...
  }
  ```

   Mas o exemplo a seguir não é **um nome válido** porque ele tem o mesmo número de segmentos do tipo.

  ```json
  {
    "type": "Microsoft.Web/serverfarms",
    "name": "appPlan/myHostingPlanName",
    ...
  }
  ```

   Para os recursos-filho, o tipo e o nome têm o mesmo número de segmentos. Esse número de segmentos faz sentido, porque o nome completo e o tipo para o filho incluem o nome do pai e o tipo. Portanto, o nome completo ainda tem um segmento a menos que o tipo completo.

  ```json
  "resources": [
      {
          "type": "Microsoft.KeyVault/vaults",
          "name": "contosokeyvault",
          ...
          "resources": [
              {
                  "type": "secrets",
                  "name": "appPassword",
                  ...
              }
          ]
      }
  ]
  ```

   Ter os segmentos corretos pode ser difícil com os tipos de Resource Manager que são aplicados aos provedores de recursos. Por exemplo, aplicar um bloqueio de recurso a um site da Web requer um tipo com quatro segmentos. Portanto, o nome tem três segmentos:

  ```json
  {
      "type": "Microsoft.Web/sites/providers/locks",
      "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
      ...
  }
  ```

- O índice de cópia não é esperado

   Você encontrou esse erro **InvalidTemplate** quando aplicou o elemento **copy** em uma parte do modelo que não dá suporte a esse elemento. Só é possível aplicar o elemento copy em um tipo de recurso. Não é possível aplicar a cópia em uma propriedade dentro de um tipo de recurso. Por exemplo, você aplica a cópia em uma máquina virtual, mas não pode aplicá-la nos discos do SO para uma máquina virtual. Em alguns casos, você pode converter um recurso-filho em um recurso-pai para criar um loop de cópia. Para obter mais informações sobre como usar a cópia, consulte [Criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md).

- O parâmetro não é válido

   Se o modelo especificar os valores permitidos para um parâmetro e você fornecer um valor que não é um deles, você receberá uma mensagem semelhante ao erro a seguir:

  ```
  Code=InvalidTemplate;
  Message=Deployment template validation failed: 'The provided value {parameter value}
  for the template parameter {parameter name} is not valid. The parameter value is not
  part of the allowed values
  ``` 

   Verifique uma segunda vez os valores permitidos no modelo e forneça um durante a implantação.

- Dependência circular detectada

   Você recebe esse erro quando recursos dependem entre si de uma maneira que impede que a implantação seja iniciado. Uma combinação de interdependências faz com que dois ou mais recursos aguardar para outros recursos que também estão aguardando. Por exemplo, recurso1 depende resource3 resource2 depende recurso1 e resource3 depende resource2. Geralmente você pode resolver esse problema removendo dependências desnecessárias. Para obter sugestões sobre como solucionar erros de dependência, veja [Verificar a sequência de implantação](#check-deployment-sequence).

<a id="notfound" />
### <a name="notfound-and-resourcenotfound"></a>NotFound e ResourceNotFound
Quando o modelo inclui o nome de um recurso que não pode ser resolvido, você recebe um erro semelhante a:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Caso você esteja tentando implantar o recurso ausente no modelo, verifique se você precisa adicionar uma dependência. O Gerenciador de Recursos otimiza a implantação criando recursos em paralelo, quando possível. Se um recurso precisar ser implantado após outro recurso, você precisará usar o elemento **dependsOn** em seu modelo para criar uma dependência no outro recurso. Por exemplo, ao implantar um aplicativo Web, o plano do Serviço de Aplicativo deve existir. Se você não tiver especificado que o aplicativo Web depende do plano do Serviço de Aplicativo, o Gerenciador de Recursos criará os dois recursos ao mesmo tempo. Você recebe um erro informando que o recurso do plano do Serviço de Aplicativo não pode ser encontrado porque ele ainda não existe ao tentar definir uma propriedade no aplicativo Web. Você evita esse erro configurando a dependência no aplicativo Web.

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Para obter sugestões sobre como solucionar erros de dependência, veja [Verificar a sequência de implantação](#check-deployment-sequence).

Você também vê esse erro quando o recurso existe em um grupo de recursos diferente daquele que está sendo implantado. Nesse caso, use a [função resourceId](resource-group-template-functions.md#resourceid) para obter o nome totalmente qualificado do recurso.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

Se tentar usar as funções [reference](resource-group-template-functions.md#reference) ou [listKeys](resource-group-template-functions.md#listkeys) com um recurso que não pode ser resolvido, você receberá o seguinte erro:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

Procure por uma expressão que inclui a função **reference**. Verifique uma segunda vez se os valores de parâmetro estão corretos.

### <a name="parentresourcenotfound"></a>ParentResourceNotFound

Quando um recurso é pai de outro recurso, o recurso pai deve existir antes da criação do recurso filho. Se ele ainda não existir, você receberá o seguinte erro:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

O nome do recurso filho inclui o nome do pai. Por exemplo, um Banco de Dados SQL pode ser definido como:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Mas, se você não especificar uma dependência no recurso pai, o recurso filho poderá obter implantado antes do pai. Para resolver esse erro, inclua uma dependência.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

<a id="storagenamenotunique" />
### <a name="storageaccountalreadyexists-and-storageaccountalreadytaken"></a>StorageAccountAlreadyExists e StorageAccountAlreadyTaken
Para contas de armazenamento, você deve fornecer um nome para o recurso que é exclusivo no Azure. Se você não fornecer um nome exclusivo, receberá um erro como:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Você pode criar um nome exclusivo concatenando a convenção de nomenclatura com o resultado da função [uniqueString](resource-group-template-functions.md#uniquestring) .

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Se você implantar uma conta de armazenamento com o mesmo nome que uma conta de armazenamento existente na sua assinatura, mas fornecer um local diferente, você receberá um erro indicando que a conta de armazenamento já existe em um local diferente. Exclua a conta de armazenamento existente ou forneça o mesmo local da conta de armazenamento existente.

### <a name="accountnameinvalid"></a>AccountNameInvalid
Você verá o erro **AccountNameInvalid** ao tentar conceder um nome que inclui caracteres proibidos a uma conta de armazenamento. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres, usar números e apenas letras minúsculas. O [uniqueString](resource-group-template-functions.md#uniquestring) função retorna 13 caracteres. Se você concatenar um prefixo para o **uniqueString** resultar, forneça um prefixo de 11 caracteres ou menos.

### <a name="badrequest"></a>BadRequest

Você pode receber um status BadRequest ao fornecer um valor inválido para uma propriedade. Por exemplo, se você fornecer um valor incorreto de SKU para uma conta de armazenamento, a implantação falhará. Para determinar os valores válidos para a propriedade, veja o [API REST](/rest/api) para o tipo de recurso que você está implantando.

<a id="noregisteredproviderfound" />
### <a name="noregisteredproviderfound-and-missingsubscriptionregistration"></a>NoRegisteredProviderFound e MissingSubscriptionRegistration
Ao implantar recursos, você pode receber o seguinte código de erro e a mensagem:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Ou você poderá receber uma mensagem semelhante que indica:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

Você recebe esses erros por um dos três motivos:

1. O provedor de recursos não foi registrado para sua assinatura
2. Versão da API não suportada para o tipo de recurso
3. Local não suportado para o tipo de recurso

A mensagem de erro deve fornecer sugestões para os locais com suporte e as versões da API. Você pode alterar o modelo para um dos valores sugeridos. A maioria dos provedores são registrados automaticamente pelo portal do Azure ou pela interface de linha de comando que você está usando, mas nem todos. Se você não usou um provedor de recursos específico antes, precisará registrá-lo. Você pode descobrir mais sobre provedores de recursos por meio do PowerShell ou CLI do Azure.

**Portal**

É possível ver o status de registro e registrar um namespace do provedor de recursos por meio do portal.

1. Em sua assinatura, selecione **Provedores de recursos**.

   ![selecionar provedores de recursos](./media/resource-manager-common-deployment-errors/select-resource-provider.png)

2. Examine a lista de provedores de recursos e, se necessário, selecione o link **Registrar** para registrar o provedor de recursos do tipo que você está tentando implantar.

   ![listar provedores de recursos](./media/resource-manager-common-deployment-errors/list-resource-providers.png)

**PowerShell**

Para ver o status do registro, use **Get-AzureRmResourceProvider**.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

Para registrar um provedor, use **Register-AzureRmResourceProvider** e forneça o nome do provedor de recursos que você deseja registrar.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

Para obter os locais com suporte para um determinado tipo de recurso, use:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Para obter as versões da API com suporte para um determinado tipo de recurso, use:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

**CLI do Azure**

Para ver se o provedor está registrado, use o comando `azure provider list` .

```azurecli
azure provider list
```

Para registrar um provedor de recursos, use o comando `azure provider register` e especifique o *namespace* para registrar.

```azurecli
azure provider register Microsoft.Cdn
```

Para ver os locais com suporte e as versões da API para um provedor de recursos, use:

```azurecli
azure provider show -n Microsoft.Compute --json > compute.json
```

<a id="quotaexceeded" />
### <a name="quotaexceeded-and-operationnotallowed"></a>QuotaExceeded e OperationNotAllowed
Você pode ter problemas quando a implantação ultrapassar uma cota, que pode ser por grupo de recursos, assinaturas, contas e outros escopos. Por exemplo, sua assinatura pode estar configurada para limitar o número de núcleos de uma região. Se tentar implantar uma máquina virtual com mais núcleos do que o valor permitido, você receberá um erro informando que a cota foi excedida.
Para obter informações completas sobre cotas, consulte [Limites, cotas e restrições de serviço e assinatura do Azure](../azure-subscription-service-limits.md).

Para examinar as cotas de núcleos da assinatura, você deve usar o comando `azure vm list-usage` na CLI do Azure. O exemplo a seguir ilustra que a cota de núcleo de uma conta de avaliação gratuita é 4:

```azurecli
azure vm list-usage
```

Que retorna:

```azurecli
info:    Executing command vm list-usage
Location: westus
data:    Name   Unit   CurrentValue  Limit
data:    -----  -----  ------------  -----
data:    Cores  Count  0             4
info:    vm list-usage command OK
```

Se implantar um modelo que cria mais de quatro núcleos na região Oeste dos EUA, você receberá um erro de implantação que se parece com:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Ou no PowerShell, você pode usar o cmdlet **Get-AzureRmVMUsage** .

```powershell
Get-AzureRmVMUsage
```

Que retorna:

```powershell
...
CurrentValue : 0
Limit        : 4
Name         : {
                 "value": "cores",
                 "localizedValue": "Total Regional Cores"
               }
Unit         : null
...
```

Nesses casos, você deve ir para o portal e abrir um problema de suporte para aumentar a cota para a região na qual você deseja implantar.

> [!NOTE]
> Lembre-se de que, para grupos de recursos, a cota é para cada região individual, não para a assinatura inteira. Se você precisar implantar 30 núcleos no Oeste dos EUA, será necessário pedir 30 núcleos do Gerenciador de Recursos no Oeste dos EUA. Se precisar implantar 30 núcleos em qualquer uma das regiões às quais tenha acesso, você deverá solicitar 30 núcleos do Resource Manager em todas as regiões.
>
>

### <a name="invalidcontentlink"></a>InvalidContentLink
Quando você recebe a mensagem de erro:

```
Code=InvalidContentLink
Message=Unable to download deployment content from ...
```

Provavelmente você tentou vincular a um modelo aninhado que não está disponível. Verifique uma segunda vez o URI que você forneceu para o modelo aninhado. Caso o modelo exista em uma conta de armazenamento, verifique se o URI está acessível. Pode ser necessário passar um token SAS. Para saber mais, confira [Usando modelos vinculados com o Gerenciador de Recursos do Azure](resource-group-linked-templates.md).

### <a name="requestdisallowedbypolicy"></a>RequestDisallowedByPolicy
Você recebe esse erro quando sua assinatura inclui uma política de recursos que impede uma ação que está tentando executar durante a implantação. Na mensagem de erro, procure o identificador da política.

```
Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'
```

No **PowerShell**, forneça o identificador de política como o parâmetro **Id** para recuperar detalhes sobre a política que bloqueou a sua implantação.

```powershell
(Get-AzureRmPolicyAssignment -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

Na **CLI do Azure**, forneça o nome da definição da política:

```azurecli
azure policy definition show regionPolicyDefinition --json
```

Para obter mais informações sobre as políticas, consulte [Usar a política para gerenciar os recursos e controlar o acesso](resource-manager-policy.md).

### <a name="authorization-failed"></a>Falha na autorização
Você pode receber um erro durante a implantação porque a conta ou a entidade de serviço que está tentando implantar os recursos de serviço não tem acesso para executar essas ações. O Azure Active Directory permite que você ou seu administrador controlem quais identidades podem acessar os recursos com um alto grau de precisão. Por exemplo, se sua conta estiver atribuída à função Leitor, você não poderá criar recursos. Nesse caso, você vê uma mensagem de erro indicando que houve falha na autorização.

Para obter mais informações sobre o controle de acesso baseado em funções, consulte [Controle de Acesso Baseado em Funções do Azure](../active-directory/role-based-access-control-configure.md).

## <a name="troubleshooting-tricks-and-tips"></a>Dicas e truques para solução de problemas

### <a name="enable-debug-logging"></a>Habilitar o log de depuração
Você pode descobrir informações valiosas sobre o processamento da sua implantação registrando a solicitação, a resposta ou ambas em log.

- PowerShell

   No PowerShell, defina o parâmetro **DeploymentDebugLogLevel** como All, ResponseContent ou RequestContent.

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   Examine o conteúdo da solicitação com o seguinte cmdlet:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   Ou o conteúdo da resposta com:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   Essas informações podem ajudá-lo a determinar se um valor no modelo está sendo definido incorretamente.

- CLI do Azure

   Na CLI do Azure, defina o parâmetro **--debug-setting** como All, ResponseContent ou RequestContent.

  ```azurecli
  azure group deployment create --debug-setting All -f c:\Azure\Templates\storage.json -g examplegroup -n ExampleDeployment
  ```

   Examine o conteúdo de solicitação e resposta registrado com o seguinte comando:

  ```azurecli
  azure group deployment operation list --resource-group examplegroup --name ExampleDeployment --json
  ```

   Essas informações podem ajudá-lo a determinar se um valor no modelo está sendo definido incorretamente.

- Modelo aninhado

   Para registrar informações de depuração de um modelo aninhado, use o elemento **debugSetting**.

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```


### <a name="create-a-troubleshooting-template"></a>Criar um modelo de solução de problemas
Em alguns casos, a maneira mais fácil de solucionar problemas do seu modelo é testar partes dele. Você pode criar um modelo simplificado que permite a você se concentrar na parte que acredita estar causando o erro. Por exemplo, suponha que você esteja recebendo um erro ao fazer referência a um recurso. Em vez de lidar com um modelo inteiro, crie um modelo que retorne a parte que pode estar causando o problema. Ele pode ajudá-lo a determinar se você está sendo aprovado nos parâmetros certos, usando funções de modelo corretamente e obtendo os recursos desejados.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

Ou, vamos supor que você esteja enfrentando erros de implantação, os quais você acredita terem relação com as dependências definidas incorretamente. Teste seu modelo dividindo-o em modelos simplificados. Primeiro, crie um modelo que implanta um único recurso (como um SQL Server). Quando você tiver certeza de que esse recurso foi definido corretamente, adicione um recurso depende dele (como um Banco de Dados SQL). Quando esses dois recursos estiverem definidos corretamente, adicione outros recursos dependentes (como políticas de auditoria). Entre cada implantação de teste, exclua o grupo de recursos para garantir o teste adequado das dependências. 

### <a name="check-deployment-sequence"></a>Verificar a sequência de implantação

Muitos erros de implantação ocorrem quando os recursos são implantados em uma sequência inesperada. Esses erros surgem quando as dependências não são definidas corretamente. Quando você não tiver uma dependência necessária, um recurso tenta usar um valor para outro recurso, mas o outro ainda não existir. Você obterá um erro informando que um recurso não foi encontrado. Você pode encontrar esse tipo de erro intermitente porque o tempo de implantação para cada recurso pode variar. Por exemplo, sua primeira tentativa de implantar seus recursos é bem-sucedida pois aleatoriamente conclui um recurso necessário no tempo. No entanto, a segunda tentativa falha porque o recurso necessário não foi concluída no tempo. 

Mas, para evitar definindo dependências que não são necessários. Quando você tiver dependências desnecessárias, você prolongar a duração da implantação, impedindo que os recursos que não são dependentes entre si seja implantado em paralelo. Além disso, você pode criar dependências circulares que bloqueiam a implantação. O [referência](resource-group-template-functions.md#reference) função cria uma dependência implícita do recurso que você especificar como um parâmetro na função, se esse recurso for implantado no mesmo modelo. Portanto, você pode ter dependências mais que as dependências especificada no **dependsOn** propriedade. O [resourceId](resource-group-template-functions.md#resourceid) função não cria uma dependência implícita ou validar que o recurso existe.

Quando você encontrar problemas de dependência, você precisa obter informações sobre a ordem de implantação de recursos. Para exibir a ordem das operações de implantação:

1. Selecione o histórico de implantação do grupo de recursos.

   ![selecionar o histórico de implantação](./media/resource-manager-common-deployment-errors/select-deployment.png)

2. Selecione uma implantação no histórico e selecione **Eventos**.

   ![selecionar os eventos de implantação](./media/resource-manager-common-deployment-errors/select-deployment-events.png)

3. Examine a sequência de eventos de cada recurso. Preste atenção ao status de cada operação. Por exemplo, a imagem a seguir mostra três contas de armazenamento implantadas em paralelo. Observe que as três contas de armazenamento são iniciadas ao mesmo tempo.

   ![implantação paralela](./media/resource-manager-common-deployment-errors/deployment-events-parallel.png)

   A imagem a seguir mostra três contas de armazenamento que não são implantadas em paralelo. A segunda conta de armazenamento depende da primeira conta de armazenamento e a terceira conta de armazenamento depende da segunda conta de armazenamento. Portanto, a primeira conta de armazenamento é iniciada, aceita e concluída antes que a próxima seja iniciada.

   ![implantação sequencial](./media/resource-manager-common-deployment-errors/deployment-events-sequence.png)

Cenários do mundo real podem ser consideravelmente mais complicados, mas você pode usar a mesma técnica para descobrir quando a implantação é iniciada e concluída para cada recurso. Examine os eventos de implantação para ver se a sequência é diferente do esperado. Nesse caso, reavalie as dependências desse recurso.

O Resource Manager identifica dependências circulares durante a validação do modelo. Ele retorna uma mensagem de erro afirmando especificamente uma dependência circular existe. Para resolver uma dependência circular:

1. No modelo, localize o recurso identificado na dependência circular. 
2. Para esse recurso, examine o **dependsOn** propriedade e quaisquer usos da **referência** função para ver quais recursos ele depende. 
3. Examine os recursos para ver quais recursos eles dependem. Siga as dependências até você perceber um recurso depende do recurso original.
5. Para os recursos envolvidos na dependência circular, examine cuidadosamente todas as funções de **dependsOn** propriedade para identificar quaisquer dependências que não são necessários. Remova essas dependências. Se você não tiver certeza de que uma dependência é necessária, tente removê-lo. 
6. Reimplante o modelo.

Removendo valores do **dependsOn** propriedade pode causar erros quando você implanta o modelo. Se você encontrar um erro, adicione a dependência de volta para o modelo. 

Se essa abordagem não resolver a dependência circular, considere mover parte de sua lógica de implantação para recursos filho (como extensões ou definições de configuração). Configure os recursos filho para implantar após os recursos envolvidos na dependência circular. Por exemplo, suponha que você estiver implantando duas máquinas virtuais, mas você deve definir propriedades em cada um deles que se referem a outro. Você pode implantá-los na seguinte ordem:

1. vm1
2. vm2
3. Extensão na vm1 depende vm1 e vm2. A extensão define valores na vm1 que ele obtém da vm2.
4. Extensão da vm2 depende vm1 e vm2. A extensão define valores de vm2 obtido do vm1.

A mesma abordagem funciona para aplicativos de serviço de aplicativo. Considere a mudança de valores de configuração em um recurso filho de recurso de aplicativo. Você pode implantar dois aplicativos web na seguinte ordem:

1. webapp1
2. webapp2
3. a configuração para webapp1 depende de webapp1 e webapp2. Ele contém configurações do aplicativo com os valores do webapp2.
4. a configuração para webapp2 depende de webapp1 e webapp2. Ele contém configurações do aplicativo com os valores do webapp1.

## <a name="troubleshooting-other-services"></a>Solucionando problemas de outros serviços
Se os códigos de erro de implantação anteriores não ajudaram a solucionar o problema, procure diretrizes de solução de problemas mais detalhadas para cada serviço do Azure.

A tabela a seguir lista os tópicos de solução de problemas para máquinas virtuais.

| Erro | Artigos |
| --- | --- |
| Erros de extensão de script personalizado |[Falhas de extensão da VM do Windows](../virtual-machines/virtual-machines-windows-extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />ou o<br />[Falhas de extensão da VM do Linux](../virtual-machines/virtual-machines-linux-extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Erros de provisionamento de imagem do SO |[Novos erros da VM do Windows](../virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />ou o<br />[Novos erros da VM do Linux](../virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Falhas na alocação |[Falhas de alocação da VM do Windows](../virtual-machines/virtual-machines-windows-allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />ou o<br />[Falhas de alocação da VM do Linux](../virtual-machines/virtual-machines-linux-allocation-failure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Erros do Secure Shell (SSH) ao tentar conectar |[Conexões do Secure Shell com a VM do Linux](../virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Erros ao conectar o aplicativo em execução na VM |[Aplicativo em execução na VM do Windows](../virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />ou o<br />[Aplicativo em execução na VM do Linux](../virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Erros de conexão da Área de Trabalho Remota |[Conexões da Área de Trabalho Remota com a VM do Windows](../virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Erros de conexão resolvidos por meio da reimplantação |[Reimplantar Máquina Virtual em um novo nó do Azure](../virtual-machines/virtual-machines-windows-redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Erros do serviço de nuvem |[Problemas de implantação do serviço de nuvem](../cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

A tabela a seguir lista os tópicos de solução de problemas para outros serviços do Azure. Ela se concentra nos problemas relacionados à implantação ou à configuração dos recursos. Se você precisar de ajuda para solucionar os problemas de execução com um recurso, confira a documentação desse serviço do Azure.

| O Barramento de | Artigo |
| --- | --- |
| Automação |[Dicas de solução de problemas para erros comuns na Automação do Azure](../automation/automation-troubleshooting-automation-errors.md) |
| Azure Stack |[Solução de problemas do Microsoft Azure Stack](../azure-stack/azure-stack-troubleshooting.md) |
| Data Factory |[Solucionar problemas da Data Factory](../data-factory/data-factory-troubleshoot.md) |
| Service Fabric |[Solucionar problemas comuns quando você implanta serviços no Azure Service Fabric](../service-fabric/service-fabric-diagnostics-troubleshoot-common-scenarios.md) |
| Recuperação de Site |[Monitorar e solucionar problemas de proteção para máquinas virtuais e sites físicos](../site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| Armazenamento |[Monitoramento, diagnóstico e solução de problemas de Armazenamento do Microsoft Azure](../storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple |[Solucionar problemas de implantação do dispositivo StorSimple](../storsimple/storsimple-troubleshoot-deployment.md) |
| Banco de Dados SQL |[Solucionar problemas de conexão no Banco de Dados SQL do Azure](../sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| SQL Data Warehouse |[Solução de problemas do Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## <a name="next-steps"></a>Próximas etapas
* Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](resource-group-audit.md).
* Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](resource-manager-deployment-operations.md).



<!--HONumber=Jan17_HO3-->


