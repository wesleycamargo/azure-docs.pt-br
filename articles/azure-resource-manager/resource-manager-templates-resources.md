---
title: Estrutura e sintaxe do modelo do Azure Resource Manager | Microsoft Docs
description: Descreve a estrutura e as propriedades dos modelos do Azure Resource Manager usando a sintaxe JSON declarativa.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2017
ms.author: tomfitz
ms.openlocfilehash: 89e4b52e7d306bd495c426bcf775f59d0f30eb55
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="resources-section-of-azure-resource-manager-templates"></a>Seção de recursos dos modelos do Azure Resource Manager

Na seção de recursos, você define os recursos que são implantados ou atualizados. Essa seção pode ficar mais complicada, porque você precisa entender os tipos que está implantando para fornecer os valores corretos.

## <a name="available-properties"></a>Propriedades disponíveis

Você define recursos com a seguinte estrutura:

```json
"resources": [
  {
      "condition": "<boolean-value-whether-to-deploy>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": "<number-of-iterations>",
          "mode": "<serial-or-parallel>",
          "batchSize": "<number-to-deploy-serially>"
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nome do elemento | Obrigatório | DESCRIÇÃO |
|:--- |:--- |:--- |
| condition | Não  | Valor booliano que indica se o recurso está implantado. |
| apiVersion |sim |Versão da API REST a ser usada para criar o recurso. |
| Tipo |sim |Tipo do recurso. Esse valor é uma combinação do namespace do provedor de recursos e do tipo de recurso (como **Microsoft.Storage/storageAccounts**). |
| Nome |sim |Nome do recurso. O nome deve seguir as restrições de componente URI definidas em RFC3986. Além disso, os serviços do Azure que expõem o nome do recurso a terceiros validam o nome para garantir que ele não é uma tentativa de falsificar outra identidade. |
| location |Varia |Locais geográficos com suporte do recurso fornecido. Você pode selecionar qualquer uma das localizações disponíveis, mas geralmente faz sentido escolher um que esteja perto de seus usuários. Normalmente, também faz sentido colocar recursos que interagem entre si na mesma região. A maioria dos tipos de recursos exige um local, mas alguns deles (como uma atribuição de função) não. |
| marcas |Não  |Marcas que são associadas ao recurso. Aplique marcas para organizar recursos logicamente em toda a sua assinatura. |
| comentários |Não  |Suas anotações para documentar os recursos em seu modelo |
| cópia |Não  |Se mais de uma instância for necessária, o número de recursos a serem criados. O modo padrão é paralelo. Especifica o modo serial, quando você não deseja que sejam todos ou os recursos para implantação ao mesmo tempo. Para obter mais informações, consulte [Criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Não  |Recursos que devem ser implantados antes deste recurso. O Gerenciador de Recursos avalia as dependências entre os recursos e os implanta na ordem correta. Quando os recursos não dependem uns dos outros, são implantados paralelamente. O valor pode ser uma lista separada por vírgulas de nomes de recursos ou identificadores exclusivos de recursos. Somente lista recursos que são implantados neste modelo. Recursos que não são definidos neste modelo já devem existir. Evite adicionar dependências desnecessárias, pois elas podem reduzir sua implantação e criar dependências circulares. Para obter orientação sobre como configurar as dependências, confira [Definir as dependências nos modelos do Azure Resource Manager](resource-group-define-dependencies.md). |
| propriedades |Não  |Definições de configuração específicas do recurso. Os valores para as propriedades são iguais aos valores que você fornece no corpo da solicitação para a operação da API REST (método PUT) para criar o recurso. Você também pode especificar uma matriz de cópia para criar várias instâncias de uma propriedade. |
| recursos |Não  |Recursos filho que dependem do recurso que está sendo definido. Forneça apenas os tipos de recurso permitidos pelo esquema do recurso pai. O tipo totalmente qualificado do recurso filho inclui o tipo de recurso pai, como **Microsoft.Web/sites/extensions**. A dependência do recurso pai não é implícita. Você deve definir explicitamente essa dependência. |

## <a name="resource-specific-values"></a>Valores específicos do recurso

A **apiVersion**, **tipo**  e **propriedades** são diferentes para cada tipo de recurso. Para determinar valores para essas propriedades, consulte [referência de modelo](/azure/templates/).

## <a name="resource-names"></a>Nomes de recurso
Normalmente, você trabalha com três tipos de nome de recurso no Resource Manager:

* Os nomes de recurso devem ser exclusivos.
* Nomes de recursos que não precisam ser exclusivos, mas você opta por fornecer um nome que pode ajudá-lo a identificar o recurso.
* Nomes de recursos que podem ser genéricos.

### <a name="unique-resource-names"></a>Nomes de recurso exclusivos
Você deve fornecer um nome de recurso exclusivo para qualquer tipo de recurso que tenha um ponto de extremidade de acesso de dados. Entre os tipos comuns de recurso que exigem um nome exclusivo estão:

* Armazenamento do Azure<sup>1</sup> 
* Recurso de Aplicativos Web do Serviço de Aplicativo do Azure
* SQL Server
* Cofre da Chave do Azure
* Cache Redis do Azure
* Lote do Azure
* Gerenciador de Tráfego do Azure
* Azure Search
* Azure HDInsight

<sup>1</sup> Os nomes de conta de armazenamento devem estar em letras minúsculas, ter até 24 caracteres e não incluir hifens.

Ao configurar o nome, é possível criar manualmente um nome exclusivo ou usar a função [uniqueString()](resource-group-template-functions-string.md#uniquestring) para gerar um nome. Também convém adicionar um prefixo ou sufixo ao resultado **uniqueString**. Modificar o nome exclusivo pode ajudar você a identificar mais facilmente o tipo de recurso pelo nome. Por exemplo, você pode gerar um nome exclusivo para uma conta de armazenamento usando a seguinte variável:

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Nomes de recurso para identificação
Alguns tipos de recurso que talvez você queira nomear não precisam que seus nomes sejam exclusivos. Para esses tipos de recurso, você pode fornecer um nome que identifique o contexto do recurso e o tipo de recurso.

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

### <a name="generic-resource-names"></a>Nomes de recurso genéricos
Para os tipos de recurso que você mais acessa por meio de um recurso diferente, é possível usar um nome genérico que seja embutido em código no modelo. Por exemplo, você pode definir um nome genérico padrão para regras de firewall em um servidor SQL:

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="location"></a>Local padrão
Ao implantar um modelo, é necessário fornecer um local para cada recurso. Tipos de recursos diferentes têm suporte em locais diferentes. Para consultar uma lista de locais disponíveis para sua assinatura para um tipo de recurso específico, use o Microsoft Azure PowerShell ou a CLI do Azure. 

O exemplo a seguir usa o PowerShell para obter os locais para o tipo de recurso `Microsoft.Web\sites`:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

O exemplo a seguir usa a CLI do Azure 2.0 para obter os locais para o tipo de recurso `Microsoft.Web\sites`:

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

Após determinar os locais com suporte para seus recursos, defina esse local no modelo. A maneira mais fácil de definir esse valor é criar um grupo de recursos em um local que dê suporte aos tipos de recursos e defina cada local como `[resourceGroup().location]`. É possível reimplantar o modelo para grupos de recursos em diferentes locais e não alterar quaisquer valores no modelo ou nos parâmetros. 

O exemplo a seguir mostra uma conta de armazenamento implantada no mesmo local que o grupo de recursos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

Se for necessário codificar o local em seu modelo, informe o nome de uma das regiões com suporte. O exemplo a seguir mostra uma conta de armazenamento que sempre é implantada para o Centro-Norte dos EUA:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="tags"></a>Marcas
[!INCLUDE [resource-manager-tag-introduction](../../includes/resource-manager-tag-introduction.md)]

### <a name="add-tags-to-your-template"></a>Adicionar marcas ao seu modelo

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="child-resources"></a>Recursos filho

Em alguns tipos de recursos, também é possível definir uma matriz de recursos filho. Os recursos filho são recursos que somente existem no contexto de outro recurso. Por exemplo, um banco de dados SQL não pode existir sem um servidor SQL para que o banco de dados seja filho do servidor. É possível definir o banco de dados dentro da definição para o servidor.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

Quando aninhado, o tipo é definido como `databases`, mas o tipo de recurso completo é `Microsoft.Sql/servers/databases`. O `Microsoft.Sql/servers/` não é fornecido porque ele é presumido do tipo de recurso pai. O nome do recurso filho é definido como `exampledatabase` , mas o nome completo inclui o nome do pai. O `exampleserver` não é fornecido porque ele é presumido do recurso pai.

O formato do tipo do recurso filho é: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

O formato do nome do recurso filho é: `{parent-resource-name}/{child-resource-name}`

Porém, não é necessário definir o banco de dados dentro do servidor. Você pode definir o recurso filho no nível superior. Você pode usar essa abordagem se o recurso pai não estiver implantado no mesmo modelo ou se quiser usar o `copy` para criar vários recursos filho. Com essa abordagem, forneça o tipo de recurso completo e inclua o nome do recurso pai no nome do recurso filho.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

Ao construir uma referência totalmente qualificada para um recurso, a ordem para combinar os segmentos do tipo e nome não é simplesmente uma concatenação dos dois.  Em vez disso, após o namespace, use uma sequência de pares *tipo/nome* do menos específico para o mais específico:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Por exemplo: 

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`está correto, `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` não está correto

## <a name="recommendations"></a>Recomendações
As seguintes informações podem ser úteis quando você trabalha com recursos:

* Para ajudar outros colaboradores a entender a finalidade do recurso, especifique **comentários** para cada recurso no modelo:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Se você usar um *ponto de extremidade público* em seu modelo (como um ponto de extremidade público de Armazenamento de Blobs do Azure), o namespace *não deverá ser embutido em código*. Use a função **reference** para recuperar dinamicamente o namespace. Você pode usar essa abordagem para implantar o modelo em ambientes de namespace públicos diferentes, sem alterar manualmente o ponto de extremidade no modelo. Defina a versão da API para a mesma versão que você está usando para a conta de armazenamento em seu modelo:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Se a conta de armazenamento for implantada no mesmo modelo que você estiver criando, não será preciso especificar o namespace do provedor quando referenciar o recurso. O exemplo a seguir mostra a sintaxe simplificada:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Se você tiver outros valores no seu modelo que sejam configurados para usar um namespace público, altere esses valores para refletir a mesma função **reference**. Por exemplo, a propriedade **storageUri** do perfil de diagnóstico da máquina virtual:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Você também pode fazer referência a uma conta de armazenamento existente que esteja em um grupo de recursos diferente:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Atribua endereços IP públicos a uma máquina virtual somente quando um aplicativo exigir. Para se conectar a uma VM (máquina virtual) para depuração, ou para fins administrativos ou de gerenciamento, use regras NAT de entrada, um gateway de rede virtual ou um jumpbox.
   
     Para saber mais sobre como se conectar às máquinas virtuais, confira:
   
   * [Executar VMs para uma arquitetura de N camadas no Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Configurar acesso WinRM para VMs no Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Permitir acesso externo à sua VM usando o portal do Azure](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Permitir acesso externo à sua VM usando o PowerShell](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Permitir acesso externo à sua VM Linux usando a CLI do Azure](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)
* A propriedade **domainNameLabel** para endereços IP públicos deve ser exclusiva. O valor de **domainNameLabel** deve ter entre 3 e 63 caracteres e seguir as regras especificadas por essa expressão regular: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Como a função **uniqueString** gera uma cadeia de caracteres com tamanho de 13 caracteres, o parâmetro **dnsPrefixString** é limitado a 50 caracteres:

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Quando você adiciona uma senha a uma extensão de script personalizada, use a propriedade **commandToExecute** na propriedade **protectedSettings**:
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Para garantir que os segredos sejam criptografados quando passados como parâmetros para VMs e extensões, use a propriedade **protectedSettings** das extensões relevantes.
   > 
   > 


## <a name="next-steps"></a>Próximas etapas
* Para exibir modelos completos para muitos tipos diferentes de soluções, consulte os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
* Para obter detalhes sobre as funções que podem ser usadas em um modelo, consulte [Funções do Modelo do Azure Resource Manager](resource-group-template-functions.md).
* Para combinar vários modelos durante a implantação, consulte [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).
* Talvez seja necessário usar recursos que existam em um grupo de recursos diferente. Essa situação é comum ao trabalhar com contas de armazenamento ou redes virtuais compartilhadas com vários grupos de recursos. Para obter mais informações, consulte a [função resourceId](resource-group-template-functions-resource.md#resourceid).
* Para obter informações sobre restrições de nome de recurso, confira [Recursos recomendados de convenções de nomenclatura do Azure](../guidance/guidance-naming-conventions.md).