---
title: Implantar recursos com a CLI do Azure e o modelo | Microsoft Docs
description: "Use o Azure Resource Manager e a CLI do Azure para implantar recursos no Azure. Os recursos são definidos em um modelo do Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e4851e872349fa6483e1f1a340d0968e845a3518
ms.openlocfilehash: ccbb918a3377094395a27a9b7a63f213c5085027


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Implantar recursos com modelos do Resource Manager e a CLI do Azure
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [CLI do Azure](resource-group-template-deploy-cli.md)
> * [Portal](resource-group-template-deploy-portal.md)
> * [API REST](resource-group-template-deploy-rest.md)
> 
> 

Este tópico explica como usar a CLI do Azure com modelos do Resource Manager para implantar seus recursos no Azure.  Seu modelo pode ser um arquivo local ou um arquivo externo que está disponível por meio de um URI. Quando seu modelo reside em uma conta de armazenamento, você pode restringir o acesso a ele e fornecer um token de SAS (Assinatura de Acesso Compartilhado) durante a implantação.

> [!TIP]
> Para obter ajuda com a depuração de erros durante a implantação, consulte:
> 
> * [Exibir operações de implantação](resource-manager-deployment-operations.md) para saber como obter informações que ajudarão a solucionar o erro
> * [Solucionar erros comuns ao implantar recursos no Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md) para saber como resolver os erros comuns da implantação
> 
> 

## <a name="quick-steps-to-deployment"></a>Etapas rápidas para implantação
Para começar a implantação de forma rápida, use os seguintes comandos:

```
azure group create -n examplegroup -l "West US"
azure group deployment create -f "c:\MyTemplates\example.json" -e "c:\MyTemplates\example.params.json" -g examplegroup -n exampledeployment
```

Esses comandos criam um grupo de recursos e implantam um modelo para esse grupo de recursos. O arquivo de modelo e o arquivo de parâmetro são os ambos arquivos locais. Se isso funcionar para você, você terá tudo que você precisa para implantar recursos. No entanto, há mais opções disponíveis para especificar os recursos a implantar. O restante deste artigo descreve todas as opções disponíveis para você durante a implantação. 

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy"></a>Implantar
Se você não utilizou anteriormente a CLI do Azures com o Gerenciamento de Recursos, consulte [Usando a CLI do Azure para Mac, Linux e Windows e Windows com o Gerenciamento de Recursos do Azure](xplat-cli-azure-resource-manager.md).

1. Faça logon na sua conta do Azure. Depois de fornecer suas credenciais, o comando retornará o resultado do seu logon.
   
   ```
   azure login
   ```
2. Se você tiver várias assinaturas, forneça a ID da assinatura que deseja usar para implantação.
   
   ```
   azure account set <YourSubscriptionNameOrId>
   ```
4. Ao implantar um modelo, você deverá especificar um grupo de recursos que conterá os recursos implantados. Se tiver um grupo de recursos existente no qual deseja implantar, você poderá ignorar esta etapa e usar esse grupo de recursos. 
   
     Para criar um grupo de recursos, forneça um nome e localização para esse grupo. Você fornece um local para o grupo de recursos pois o grupo de recursos armazena metadados sobre os recursos. Por motivos de conformidade, talvez você queira especificar onde os metadados são armazenados. Em geral, é recomendável que você especifique um local em que a maioria de seus recursos residirá. Usar o mesmo local pode simplificar seu modelo.
   
   ```
   azure group create -n ExampleResourceGroup -l "West US"
   ```

     Um resumo do novo grupo de recursos é retornado.
   
5. Valide sua implantação antes de executá-la usando o comando **azure group template validate** . Ao testar a implantação, forneça parâmetros exatamente como faria ao executar a implantação (mostrado na próxima etapa).
   
   ```
   azure group template validate -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup
   ```
6. Para implantar recursos em seu grupo de recursos, execute o comando de **azure group deployment create** e forneça os parâmetros necessários. Os parâmetros incluem um nome para sua implantação, o nome do seu grupo de recursos, o caminho ou a URL para o modelo e qualquer outro parâmetro necessário para seu cenário. Se o parâmetro **mode** não estiver especificado, o valor padrão de **Incremental** será usado. Para executar uma implantação completa, defina **mode** como **Complete**. Tenha cuidado ao usar o modo completo, pois você pode excluir acidentalmente recursos que não estão em seu modelo.
   
     Para implantar um modelo local, use o parâmetro **template-file**:
   
   ```
   azure group deployment create --resource-group examplegroup --template-file "c:\MyTemplates\example.json"
   ```
   
     Para implantar um modelo externo, use o parâmetro **template-uri**:
   
   ```
   azure group deployment create --resource-group examplegroup --template-uri "https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.json"
   ```
   
     Os dois exemplos anteriores não incluem valores de parâmetro. Conheça as opções para passar valores de parâmetro na seção [Parâmetros](#parameters). Por enquanto, você precisará fornecer valores de parâmetro com a seguinte sintaxe:

   ```
   info:    Executing command group deployment create
   info:    Supply values for the following parameters
   firstParameters:  <type here>
   ```

      Após os recursos terem sido implantados, você verá um resumo da implantação. O resumo inclui um **ProvisioningState** que indica se a implantação foi bem-sucedida.

   ```
   + Initializing template configurations and parameters
   + Creating a deployment
   info:    Created template deployment "example"
   + Waiting for deployment to complete
   +
   +
   data:    DeploymentName     : example
   data:    ResourceGroupName  : examplegroup
   data:    ProvisioningState  : Succeeded
   ```
7. Se você quiser registrar informações adicionais sobre a implantação que podem ajudar a solucionar quaisquer erros de implantação, use o parâmetro **debug-setting** . Você pode especificar que o conteúdo da solicitação, o conteúdo da resposta ou ambos sejam registrados com a operação de implantação.
   
   ```
   azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g examplegroup -n exampleDeployment
   ```

## <a name="deploy-template-from-storage-with-sas-token"></a>Implantar o modelo do armazenamento com um token SAS
Você pode adicionar seus modelos a uma conta de armazenamento e vinculá-los durante a implantação com um token SAS.

> [!IMPORTANT]
> Seguindo as etapas abaixo, o blob que contém o modelo fica acessível somente para o proprietário da conta. No entanto, quando você cria um token SAS para o blob, o blob fica acessível para qualquer pessoa com o URI. Se outro usuário interceptar o URI, esse usuário será capaz de acessar o modelo. Usar um token SAS é uma boa maneira de limitar o acesso aos seus modelos, mas você não deve incluir dados confidenciais como senhas diretamente no modelo.
> 
> 

### <a name="add-private-template-to-storage-account"></a>Adicionar modelo privado à conta de armazenamento
As etapas a seguir configuram uma conta de armazenamento para os modelos:

1. Crie um grupos de recursos.
   
   ```
   azure group create -n "ManageGroup" -l "westus"
   ```
2. Criar uma conta de armazenamento. O nome da conta de armazenamento deve ser exclusivo no Azure, então forneça seu próprio nome para a conta.
   
   ```
   azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates
   ```
3. Definir variáveis para a conta de armazenamento e a chave.
   
   ```
   export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
   export AZURE_STORAGE_ACCESS_KEY={storage_account_key}
   ```
4. Criar um contêiner. A permissão é definida como **Desativada** , o que significa que o contêiner está acessível apenas para o proprietário.
   
   ```
   azure storage container create --container templates -p Off 
   ```
5. Adicionar seu modelo ao contêiner.
   
   ```
   azure storage blob upload --container templates -f c:\MyTemplates\azuredeploy.json
   ```

### <a name="provide-sas-token-during-deployment"></a>Forneça um token SAS durante a implantação
Para implantar um modelo privado em uma conta de armazenamento, recupere um token SAS e inclua-o no URI para o modelo.

1. Crie um token SAS com permissões de leitura e uma hora de vencimento para limitar o acesso. Defina a hora de vencimento de forma a permitir que haja tempo suficiente para concluir a implantação. Recupere o URI completo do modelo, incluindo o token SAS.
   
   ```
   expiretime=$(date -I'minutes' --date "+30 minutes")
   fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")
   ```
2. Implante o modelo fornecendo o URI que inclui o token SAS.
   
   ```
   azure group deployment create --template-uri $fullurl -g ExampleResourceGroup
   ```

Para ver um exemplo de como usar um token SAS com modelos vinculados, consulte [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).

## <a name="parameters"></a>parâmetros

Você tem as seguintes opções para fornecer valores de parâmetro: 
   
- Use parâmetros embutidos. Cada parâmetro está no formato: `"ParameterName": { "value": "ParameterValue" }`. O exemplo a seguir mostra os parâmetros com caracteres de escape.

   ```   
   azure group deployment create -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment
   ```
- Use um arquivo de parâmetro.

  ```    
  azure group deployment create -f "c:\MyTemplates\example.json" -e "c:\MyTemplates\example.params.json" -g ExampleResourceGroup -n ExampleDeployment
  ```
      

[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Próximas etapas
* Para obter um exemplo de como implantar recursos por meio da biblioteca de cliente do .NET, veja [Implantar recursos usando bibliotecas do .NET e um modelo](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para definir os parâmetros no modelo, consulte [Criando modelos](resource-group-authoring-templates.md#parameters).
* Para obter orientação sobre como implantar a solução em ambientes diferentes, confira [Ambientes de desenvolvimento e de teste no Microsoft Azure](solution-dev-test-environments.md).
* Para ver os detalhes sobre como usar uma referência do KeyVault para transmitir valores seguros, consulte [Transmitir valores seguros durante a implantação](resource-manager-keyvault-parameter.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).
* Para uma série de quatro partes sobre como automatizar a implantação, consulte [automatizar implantações de aplicativo para Azure Virtual Machines](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Esta série aborda a arquitetura do aplicativo, acesso e segurança, disponibilidade e dimensionamento e implantação de aplicativos.




<!--HONumber=Jan17_HO2-->


