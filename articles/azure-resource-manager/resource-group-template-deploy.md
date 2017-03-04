---
title: Implantar recursos com o PowerShell e o modelo | Microsoft Docs
description: "Use o Azure Resource Manager e o Azure PowerShell para implantar recursos no Azure. Os recursos são definidos em um modelo do Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 5b8b293b5b37365088a3df55581be7b7bf76691c
ms.openlocfilehash: 7bc3421e00215ca4629ea11811c98e581377b24a
ms.lasthandoff: 01/18/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Implantar recursos com modelos do Resource Manager e o Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [CLI do Azure](resource-group-template-deploy-cli.md)
> * [Portal](resource-group-template-deploy-portal.md)
> * [API REST](resource-group-template-deploy-rest.md)
> 
> 

Este tópico explica como usar o Azure PowerShell com modelos do Resource Manager para implantar seus recursos no Azure. Seu modelo pode ser um arquivo local ou um arquivo externo que está disponível por meio de um URI. Quando seu modelo reside em uma conta de armazenamento, você pode restringir o acesso a ele e fornecer um token de SAS (Assinatura de Acesso Compartilhado) durante a implantação.

> [!TIP]
> Para obter ajuda com a depuração de erros durante a implantação, consulte:
> 
> * [Exibir operações de implantação](resource-manager-deployment-operations.md) para saber como obter informações que ajudarão a solucionar o erro
> * [Solucionar erros comuns ao implantar recursos no Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md) para saber como resolver os erros comuns da implantação
> 
> 

## <a name="quick-steps-to-deployment"></a>Etapas rápidas para implantação
Para começar a implantação de forma rápida, use os seguintes comandos:

```powershell
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\example.json -TemplateParameterFile c:\MyTemplates\example.params.json
```

Esses comandos criam um grupo de recursos e implantam um modelo para esse grupo de recursos. O arquivo de modelo e o arquivo de parâmetro são os ambos arquivos locais. Se isso funcionar para você, você terá tudo que você precisa para implantar recursos. No entanto, há mais opções disponíveis para especificar os recursos a implantar. O restante deste artigo descreve todas as opções disponíveis para você durante a implantação. 

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy"></a>Implantar
1. Faça logon na sua conta do Azure.

   ```powershell
   Login-AzureRmAccount
   ```

    Um resumo da sua conta é retornado.
    
   ```powershell
   Environment           : AzureCloud
   Account               : someone@example.com
   TenantId              : {guid}
   SubscriptionId        : {guid}
   SubscriptionName      : Example Subscription
   CurrentStorageAccount :
   ```

2. Se você tiver várias assinaturas, forneça a ID da assinatura que deseja usar para a implantação com o comando **Set-AzureRmContext**. 
   
   ```powershell
   Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
   ```
3. Ao implantar um modelo, você deverá especificar um grupo de recursos que conterá os recursos implantados. Se tiver um grupo de recursos existente no qual deseja implantar, você poderá ignorar esta etapa e usar esse grupo de recursos. 
   
     Para criar um grupo de recursos, forneça um nome e localização para esse grupo. Você fornece um local para o grupo de recursos pois o grupo de recursos armazena metadados sobre os recursos. Por motivos de conformidade, talvez você queira especificar onde os metadados são armazenados. Em geral, é recomendável que você especifique um local em que a maioria de seus recursos residirá. Usar o mesmo local pode simplificar seu modelo.
   
   ```powershell
   New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   ```
   
     Um resumo do novo grupo de recursos é retornado.
4. Antes de executar a implantação, você pode validar as configurações da implantação. O cmdlet **Test-AzureRmResourceGroupDeployment** permite encontrar os problemas antes de criar os recursos reais. O exemplo a seguir mostra como validar uma implantação.
   
   ```powershell
   Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>
   ```
5. Para implantar recursos em seu grupo de recursos, execute o comando **New-AzureRmResourceGroupDeployment** e forneça os parâmetros necessários. Os parâmetros incluem um nome para sua implantação, o nome do seu grupo de recursos, o caminho ou a URL para o modelo e qualquer outro parâmetro necessário para seu cenário. Caso o parâmetro **Mode** não esteja especificado, o valor padrão de **Incremental** será usado. Para executar uma implantação completa, defina **Mode** como **Complete**. Tenha cuidado ao usar o modo completo, pois você pode excluir acidentalmente recursos que não estão em seu modelo.
   
     Para implantar um modelo local, use o parâmetro **TemplateFile**:
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\example.json
   ```
   
     Para implantar um modelo externo, use o parâmetro **TemplateUri**:
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.json
   ```
   
     Os dois exemplos anteriores não incluem valores de parâmetro. Conheça as opções para passar valores de parâmetro na seção [Parâmetros](#parameters). Por enquanto, você precisará fornecer valores de parâmetro com a seguinte sintaxe:

   ```powershell  
   cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
   Supply values for the following parameters:
   (Type !? for Help.)
   firstParameter: <type here>
   ```
     
     Após os recursos terem sido implantados, você verá um resumo da implantação. O resumo inclui um **ProvisioningState** que indica se a implantação foi bem-sucedida.

   ```powershell   
   DeploymentName    : ExampleDeployment
   ResourceGroupName : ExampleResourceGroup
   ProvisioningState : Succeeded
   Timestamp         : 4/14/2015 7:00:27 PM
   Mode              : Incremental
   ```
      
6. Se você quiser registrar informações adicionais sobre a implantação que podem ajudar a solucionar quaisquer erros de implantação, use o parâmetro **DeploymentDebugLogLevel**. Você pode especificar que o conteúdo da solicitação, o conteúdo da resposta ou ambos sejam registrados com a operação de implantação.
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
   ```
   
     Para obter mais informações sobre como usar esse conteúdo de depuração para solucionar problemas de implantação, confira [View deployment operations](resource-manager-deployment-operations.md) (Exibir operações de implantação).

## <a name="deploy-private-template-with-sas-token"></a>Implantar o modelo particular com o token SAS
Você pode adicionar seus modelos a uma conta de armazenamento e vinculá-los durante a implantação com um token SAS.

> [!IMPORTANT]
> Seguindo as etapas abaixo, o blob que contém o modelo fica acessível somente para o proprietário da conta. No entanto, quando você cria um token SAS para o blob, o blob fica acessível para qualquer pessoa com o URI. Se outro usuário interceptar o URI, esse usuário será capaz de acessar o modelo. Usar um token SAS é uma boa maneira de limitar o acesso aos seus modelos, mas você não deve incluir dados confidenciais como senhas diretamente no modelo.
> 
> 

### <a name="add-private-template-to-storage-account"></a>Adicionar modelo privado à conta de armazenamento
As etapas a seguir configuram uma conta de armazenamento para os modelos:

1. Crie um grupos de recursos.
   
   ```powershell
   New-AzureRmResourceGroup -Name ManageGroup -Location "West US"
   ```
2. Criar uma conta de armazenamento. O nome da conta de armazenamento deve ser exclusivo no Azure, então forneça seu próprio nome para a conta.
   
   ```powershell
   New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"
   ```
3. Definir a conta de armazenamento atual.
   
   ```powershell
   Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
   ```
4. Criar um contêiner. A permissão é definida como **Desativada** , o que significa que o contêiner está acessível apenas para o proprietário.
   
   ```powershell
   New-AzureStorageContainer -Name templates -Permission Off
   ```
5. Adicionar seu modelo ao contêiner.
   
   ```powershell
   Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
   ```

### <a name="provide-sas-token-during-deployment"></a>Forneça um token SAS durante a implantação
Para implantar um modelo privado em uma conta de armazenamento, recupere um token SAS e inclua-o no URI para o modelo.

1. Se você tiver alterado a conta de armazenamento atual, defina a conta de armazenamento atual como aquela que contém os modelos.
   
   ```powershell
   Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
   ```
2. Crie um token SAS com permissões de leitura e uma hora de vencimento para limitar o acesso. Recupere o URI completo do modelo, incluindo o token SAS.
   
   ```powershell
   $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri
   ```
3. Implante o modelo fornecendo o URI que inclui o token SAS.
   
   ```powershell
   New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri
   ```

Para ver um exemplo de como usar um token SAS com modelos vinculados, consulte [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).

## <a name="parameters"></a>parâmetros

Você tem as seguintes opções para fornecer valores de parâmetro: 
   
- Parâmetros embutidos. Incluir nomes de parâmetro individuais no cmdlet (por exemplo, **-myParameterName**.)

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"
   ```
- Objeto de parâmetro. Inclua o parâmetro **-TemplateParameterObject**.

   ```powershell   
   $parameters = @{"<ParameterName>"="<Parameter Value>"}
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters
   ```
- Arquivo de parâmetro local. Inclua o parâmetro **-TemplateParameterFile**.

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile c:\MyTemplates\example.params.json
   ```
- Arquivo de parâmetro externo. Inclua o parâmetro **-TemplateParameterUri**.

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.params.json
   ```
      
[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)] 

Se o modelo incluir um parâmetro com o mesmo nome que um dos parâmetros no comando do PowerShell, você receberá uma solicitação para fornecer um valor para esse parâmetro. O Azure PowerShell apresenta o parâmetro do modelo com o sufixo **FromTemplate**. Por exemplo, um parâmetro chamado **ResourceGroupName** em seu modelo entra em conflito com o parâmetro **ResourceGroupName** no cmdlet [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroupdeployment). Você recebe uma solicitação para fornecer um valor para **ResourceGroupNameFromTemplate**. Em geral, você deve evitar essa confusão não dando aos parâmetros o mesmo nome dos parâmetros usados para operações de implantação.

## <a name="parameter-precedence"></a>Precedência de parâmetro

Você pode usar parâmetros embutidos e um arquivo de parâmetro local na mesma operação de implantação. Por exemplo, você pode especificar alguns valores no arquivo de parâmetro local e adicionar outros valores embutidos durante a implantação. Se você fornecer valores para um parâmetro no arquivo de parâmetros local e embutido, o valor embutido terá precedência.

No entanto, quando você usa um arquivo de parâmetro externo, não é possível passar outros valores embutidos ou de um arquivo local. Quando você especificar um arquivo de parâmetro no parâmetro **TemplateParameterUri**, todos os parâmetros embutidos serão ignorados. Forneça todos os valores de parâmetro no arquivo externo. Se o seu modelo incluir um valor confidencial que não podem ser incluído no arquivo de parâmetros, adicione esse valor em um cofre de chaves ou forneça dinamicamente todos os valores de parâmetro embutidos.

## <a name="next-steps"></a>Próximas etapas
* Para obter um exemplo de como implantar recursos por meio da biblioteca de cliente do .NET, veja [Implantar recursos usando bibliotecas do .NET e um modelo](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para definir os parâmetros no modelo, consulte [Criando modelos](resource-group-authoring-templates.md#parameters).
* Para obter orientação sobre como implantar a solução em ambientes diferentes, confira [Ambientes de desenvolvimento e de teste no Microsoft Azure](solution-dev-test-environments.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).
* Para uma série de quatro partes sobre como automatizar a implantação, consulte [automatizar implantações de aplicativo para Azure Virtual Machines](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Esta série aborda a arquitetura do aplicativo, acesso e segurança, disponibilidade e dimensionamento e implantação de aplicativos.


