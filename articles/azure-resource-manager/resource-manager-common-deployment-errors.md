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
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 54db95fd50e5d61d54d0c03a8589cfe8292c012c
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Solução de erros comuns de implantação do Azure com o Azure Resource Manager
Este tópico descreve como é possível resolver alguns erros de implantação comuns do Azure que você pode encontrar.

Os seguintes códigos de erro estão descritos neste tópico:

* [AccountNameInvalid](#accountnameinvalid)
* [Falha na autorização](#authorization-failed)
* [BadRequest](#badrequest)
* [DeploymentFailed](#deploymentfailed)
* [DisallowedOperation](#disallowedoperation)
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

## <a name="deploymentfailed"></a>DeploymentFailed

Esse código de erro indica um erro de implantação geral, mas não é o código de erro que você precisa para começar a solução de problemas. O código de erro que o ajuda a resolver o problema de verdade fica um nível abaixo desse erro. Por exemplo, a imagem a seguir mostra o código de erro **RequestDisallowedByPolicy** que está por baixo do erro de implantação.

![mostrar código de erro](./media/resource-manager-common-deployment-errors/error-code.png)

## <a name="skunotavailable"></a>SkuNotAvailable

Ao implantar um recurso (normalmente uma máquina virtual), você pode receber o seguinte código de erro e a mensagem de erro:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

Você recebe esse erro quando o recurso SKU selecionado (como o tamanho da VM) não está disponível para o local escolhido. Para resolver esse problema, você precisa determinar quais SKUs estão disponíveis em uma região. Você pode usar o PowerShell, o portal ou uma operação REST para localizar SKUs disponíveis.

- Para o PowerShell, use [Get-AzureRmComputeResourceSku](/powershell/module/azurerm.compute/get-azurermcomputeresourcesku) e filtre por localização. Você deve ter a versão mais recente do PowerShell para esse comando.

  ```powershell
  Get-AzureRmComputeResourceSku | where {$_.Locations.Contains("southcentralus")}
  ```

  Os resultados incluem uma lista de SKUs para o local e as restrições desse SKU.

  ```powershell
  ResourceType                Name      Locations Restriction                      Capability Value
  ------------                ----      --------- -----------                      ---------- -----
  availabilitySets         Classic southcentralus             MaximumPlatformFaultDomainCount     3
  availabilitySets         Aligned southcentralus             MaximumPlatformFaultDomainCount     3
  virtualMachines      Standard_A0 southcentralus
  virtualMachines      Standard_A1 southcentralus
  virtualMachines      Standard_A2 southcentralus
  ```

- Para a CLI do Azure, use o comando `az vm list-skus`. Você pode usar o `grep` ou um utilitário semelhante para filtrar a saída.

  ```
  az vm list-skus --output table
  ResourceType      Locations           Name                    Capabilities                       Tier      Size           Restrictions
  ----------------  ------------------  ----------------------  ---------------------------------  --------  -------------  ---------------------------
  availabilitySets  eastus              Classic                 MaximumPlatformFaultDomainCount=3
  avilabilitySets  eastus              Aligned                 MaximumPlatformFaultDomainCount=3
  availabilitySets  eastus2             Classic                 MaximumPlatformFaultDomainCount=3
  availabilitySets  eastus2             Aligned                 MaximumPlatformFaultDomainCount=3
  availabilitySets  westus              Classic                 MaximumPlatformFaultDomainCount=3
  availabilitySets  westus              Aligned                 MaximumPlatformFaultDomainCount=3
  availabilitySets  centralus           Classic                 MaximumPlatformFaultDomainCount=3
  availabilitySets  centralus           Aligned                 MaximumPlatformFaultDomainCount=3
  ```

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

Caso você não consiga encontrar um SKU adequado na região ou em uma região alternativa que atende às suas necessidades de negócios, envie uma [solicitação de SKU](https://aka.ms/skurestriction) para o Suporte do Azure.

## <a name="disallowedoperation"></a>DisallowedOperation

```
Code: DisallowedOperation
Message: The current subscription type is not permitted to perform operations on any provider 
namespace. Please use a different subscription.
```

Se você receber esse erro, você estará usando uma assinatura que não tem permissão para acessar nenhum serviço do Azure com a exceção do Azure Active Directory. Você pode ter esse tipo de assinatura quando você precisa acessar o portal clássico, mas não tem permissão para implantar recursos. Para resolver esse problema, você deve usar uma assinatura que tenha permissão para implantar recursos.  

Para exibir suas assinaturas disponíveis com o PowerShell, use:

```powershell
Get-AzureRmSubscription
```

E para definir a assinatura atual, use:

```powershell
Set-AzureRmContext -SubscriptionName {subscription-name}
```

Para exibir suas assinaturas disponíveis com a CLI do Azure 2.0, use:

```azurecli
az account list
```

E para definir a assinatura atual, use:

```azurecli
az account set --subscription {subscription-name}
```

## <a name="invalidtemplate"></a>InvalidTemplate
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

   Você recebe esse erro quando recursos dependem entre si de uma maneira que impede que a implantação seja iniciado. Uma combinação de interdependências faz com que dois ou mais recursos aguardar para outros recursos que também estão aguardando. Por exemplo, recurso1 depende resource3 resource2 depende recurso1 e resource3 depende resource2. Geralmente você pode resolver esse problema removendo dependências desnecessárias. 

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

Você também vê esse erro quando o recurso existe em um grupo de recursos diferente daquele que está sendo implantado. Nesse caso, use a [função resourceId](resource-group-template-functions-resource.md#resourceid) para obter o nome totalmente qualificado do recurso.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

Se tentar usar as funções [reference](resource-group-template-functions-resource.md#reference) ou [listKeys](resource-group-template-functions-resource.md#listkeys) com um recurso que não pode ser resolvido, você receberá o seguinte erro:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

Procure por uma expressão que inclui a função **reference**. Verifique uma segunda vez se os valores de parâmetro estão corretos.

## <a name="parentresourcenotfound"></a>ParentResourceNotFound

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

## <a name="storageaccountalreadyexists-and-storageaccountalreadytaken"></a>StorageAccountAlreadyExists e StorageAccountAlreadyTaken
Para contas de armazenamento, você deve fornecer um nome para o recurso que é exclusivo no Azure. Se você não fornecer um nome exclusivo, receberá um erro como:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Você pode criar um nome exclusivo concatenando a convenção de nomenclatura com o resultado da função [uniqueString](resource-group-template-functions-string.md#uniquestring) .

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Se você implantar uma conta de armazenamento com o mesmo nome que uma conta de armazenamento existente na sua assinatura, mas fornecer um local diferente, você receberá um erro indicando que a conta de armazenamento já existe em um local diferente. Exclua a conta de armazenamento existente ou forneça o mesmo local da conta de armazenamento existente.

## <a name="accountnameinvalid"></a>AccountNameInvalid
Você verá o erro **AccountNameInvalid** ao tentar conceder um nome que inclui caracteres proibidos a uma conta de armazenamento. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres, usar números e apenas letras minúsculas. O [uniqueString](resource-group-template-functions-string.md#uniquestring) função retorna 13 caracteres. Se você concatenar um prefixo para o **uniqueString** resultar, forneça um prefixo de 11 caracteres ou menos.

## <a name="badrequest"></a>BadRequest

Você pode receber um status BadRequest ao fornecer um valor inválido para uma propriedade. Por exemplo, se você fornecer um valor incorreto de SKU para uma conta de armazenamento, a implantação falhará. Para determinar os valores válidos para a propriedade, veja o [API REST](/rest/api) para o tipo de recurso que você está implantando.

<a id="noregisteredproviderfound" />

## <a name="noregisteredproviderfound-and-missingsubscriptionregistration"></a>NoRegisteredProviderFound e MissingSubscriptionRegistration
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
az provider list
```

Para registrar um provedor de recursos, use o comando `azure provider register` e especifique o *namespace* para registrar.

```azurecli
az provider register --namespace Microsoft.Cdn
```

Para ver os locais com suporte e as versões da API para um tipo de recurso, use:

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

<a id="quotaexceeded" />

## <a name="quotaexceeded-and-operationnotallowed"></a>QuotaExceeded e OperationNotAllowed
Você pode ter problemas quando a implantação ultrapassar uma cota, que pode ser por grupo de recursos, assinaturas, contas e outros escopos. Por exemplo, sua assinatura pode estar configurada para limitar o número de núcleos de uma região. Se tentar implantar uma máquina virtual com mais núcleos do que o valor permitido, você receberá um erro informando que a cota foi excedida.
Para obter informações completas sobre cotas, consulte [Limites, cotas e restrições de serviço e assinatura do Azure](../azure-subscription-service-limits.md).

Para examinar as cotas de núcleos da assinatura, você deve usar o comando `azure vm list-usage` na CLI do Azure. O exemplo a seguir ilustra que a cota de núcleo de uma conta de avaliação gratuita é 4:

```azurecli
az vm list-usage --location "South Central US"
```

Que retorna:

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
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

## <a name="invalidcontentlink"></a>InvalidContentLink
Quando você recebe a mensagem de erro:

```
Code=InvalidContentLink
Message=Unable to download deployment content from ...
```

Provavelmente você tentou vincular a um modelo aninhado que não está disponível. Verifique uma segunda vez o URI que você forneceu para o modelo aninhado. Caso o modelo exista em uma conta de armazenamento, verifique se o URI está acessível. Pode ser necessário passar um token SAS. Para saber mais, confira [Usando modelos vinculados com o Gerenciador de Recursos do Azure](resource-group-linked-templates.md).

## <a name="requestdisallowedbypolicy"></a>RequestDisallowedByPolicy
Você recebe esse erro quando sua assinatura inclui uma política de recursos que impede uma ação que está tentando executar durante a implantação. Na mensagem de erro, procure o identificador da política.

```
Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'
```

No **PowerShell**, forneça o identificador de política como o parâmetro **Id** para recuperar detalhes sobre a política que bloqueou a sua implantação.

```powershell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

Na **CLI do Azure**, forneça o nome da definição da política:

```azurecli
az policy definition show --name regionPolicyAssignment
```

Para obter mais informações, consulte os seguintes artigos:

- [Erro RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
- [Usar a Política para gerenciar recursos e controlar o acesso](resource-manager-policy.md).

## <a name="authorization-failed"></a>Falha na autorização
Você pode receber um erro durante a implantação porque a conta ou a entidade de serviço que está tentando implantar os recursos de serviço não tem acesso para executar essas ações. O Azure Active Directory permite que você ou seu administrador controlem quais identidades podem acessar os recursos com um alto grau de precisão. Por exemplo, se sua conta estiver atribuída à função Leitor, você não poderá criar recursos. Nesse caso, você vê uma mensagem de erro indicando que houve falha na autorização.

Para obter mais informações sobre o controle de acesso baseado em funções, consulte [Controle de Acesso Baseado em Funções do Azure](../active-directory/role-based-access-control-configure.md).


## <a name="next-steps"></a>Próximas etapas
* Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](resource-group-audit.md).
* Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](resource-manager-deployment-operations.md).

