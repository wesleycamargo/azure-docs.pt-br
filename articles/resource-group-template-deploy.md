<properties
   pageTitle="Implantar recursos com o PowerShell e o modelo | Microsoft Azure"
   description="Use o Azure Resource Manager e o Azure PowerShell para implantar recursos no Azure. Os recursos são definidos em um modelo do Resource Manager."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# Implantar recursos com modelos do Resource Manager e o Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI do Azure](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)
- [.NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Nó](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)


Este tópico explica como usar o Azure PowerShell com modelos do Resource Manager para implantar seus recursos no Azure.

> [AZURE.TIP] Para obter ajuda com a depuração de erros durante a implantação, consulte:
>
> - [Exibir operações de implantação com o Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md) para saber sobre como obter informações que o ajudarão a solucionar o erro
> - [Solucionar erros comuns ao implantar recursos no Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md) para saber como resolver os erros comuns da implantação

Seu modelo pode ser um arquivo local ou um arquivo externo que está disponível por meio de um URI. Quando seu modelo reside em uma conta de armazenamento, você pode restringir o acesso a ele e fornecer um token de SAS (Assinatura de Acesso Compartilhado) durante a implantação.

## Etapas rápidas para implantação

Este artigo descreve todas as diferentes opções disponíveis para você durante a implantação. No entanto, normalmente você somente precisará de dois comandos simples. Para começar a implantação de forma rápida, use os seguintes comandos:

    New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
    New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

Para saber mais sobre opções de implantação que podem ser mais adequado para seu cenário, continue lendo este artigo.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## Implantação com o PowerShell

1. Faça logon na sua conta do Azure.

        Add-AzureRmAccount

     Um resumo da sua conta é retornado.

        Environment : AzureCloud
        Account     : someone@example.com
        ...

2. Se você tiver várias assinaturas, forneça a ID da assinatura que deseja usar para a implantação com o comando **Set-AzureRmContext**.

        Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

3. Normalmente, ao implantar um novo modelo, convém criar um novo grupo de recursos para conter os recursos. Se tiver um grupo de recursos existente no qual deseja implantar, você poderá ignorar esta etapa e usar esse grupo de recursos.

     Para criar um grupo de recursos, forneça um nome e localização para esse grupo.

        New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
     Um resumo do novo grupo de recursos é retornado.
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
             Actions  NotActions
             =======  ==========
             *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

4. Antes de executar a implantação, você pode validar as configurações da implantação. O cmdlet **Test-AzureRmResourceGroupDeployment** permite encontrar os problemas antes de criar os recursos reais. O exemplo a seguir mostra como validar uma implantação.

        Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

5. Para implantar recursos em seu grupo de recursos, execute o comando **New-AzureRmResourceGroupDeployment** e forneça os parâmetros necessários. Os parâmetros incluem um nome para sua implantação, o nome do seu grupo de recursos, o caminho ou a URL para o modelo criado e qualquer outro parâmetro necessário para seu cenário. Caso o parâmetro **Mode** não esteja especificado, o valor padrão **Incremental** será usado. Para executar uma implantação completa, defina **Mode** como **Complete**. Tenha cuidado ao usar o modo completo, pois você pode excluir acidentalmente recursos que não estão em seu modelo.

     Para implantar um modelo local, use o parâmetro **TemplateFile**:

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

     Para implantar um modelo externo, use o parâmetro **TemplateUri**:

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate>
   
     Você tem as seguintes opções para fornecer valores de parâmetro:
   
     1. Use parâmetros embutidos.

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"

     2. Use um objeto de parâmetro.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters

     3. Use um arquivo de parâmetro local. Para obter informações sobre o arquivo de modelo, consulte [Arquivo de parâmetro](#parameter-file).

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

     4. Use um arquivo de parâmetro externo. Para obter informações sobre o arquivo de modelo, consulte [Arquivo de parâmetro](#parameter-file).

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri <LinkToParameterFile>

        Quando você usa um arquivo de parâmetro externo, não é possível passar outros valores embutidos ou de um arquivo local. Para saber mais, confira [Precedência de parâmetro](#parameter-precendence).

     Após os recursos terem sido implantados, você verá um resumo da implantação.

        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleResourceGroup
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2015 7:00:27 PM
        Mode              : Incremental
        ...

     Se o modelo incluir um parâmetro com o mesmo nome que um dos parâmetros no comando do PowerShell para implantação do modelo, você receberá uma solicitação para fornecer um valor para esse parâmetro com o sufixo **FromTemplate**. Por exemplo, um parâmetro chamado **ResourceGroupName** em seu modelo entra em conflito com o parâmetro **ResourceGroupName** no cmdlet [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx). Você recebe uma solicitação para fornecer um valor para **ResourceGroupNameFromTemplate**. Em geral, você deve evitar essa confusão não dando aos parâmetros o mesmo nome dos parâmetros usados para operações de implantação.

6. Se você quiser registrar informações adicionais sobre a implantação que podem ajudar a solucionar quaisquer erros de implantação, use o parâmetro **DeploymentDebugLogLevel**. Você pode especificar que o conteúdo da solicitação, o conteúdo da resposta ou ambos sejam registrados com a operação de implantação.

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
        
     Para obter mais informações sobre como usar este conteúdo de depuração para solucionar os problemas da implantação, consulte [Solucionando problemas de implantações do grupo de recursos com o Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md).

## Implantar o modelo do armazenamento com um token SAS

Você pode adicionar seus modelos a uma conta de armazenamento e vinculá-los durante a implantação com um token SAS.

> [AZURE.IMPORTANT] Seguindo as etapas abaixo, o blob que contém o modelo fica acessível somente para o proprietário da conta. No entanto, quando você cria um token SAS para o blob, o blob fica acessível para qualquer pessoa com o URI. Se outro usuário interceptar o URI, esse usuário será capaz de acessar o modelo. Usar um token SAS é uma boa maneira de limitar o acesso aos seus modelos, mas você não deve incluir dados confidenciais como senhas diretamente no modelo.

### Adicionar modelo privado à conta de armazenamento

As etapas a seguir configuram uma conta de armazenamento para os modelos:

1. Crie um grupos de recursos.

        New-AzureRmResourceGroup -Name ManageGroup -Location "West US"

2. Criar uma conta de armazenamento. O nome da conta de armazenamento deve ser exclusivo no Azure, então forneça seu próprio nome para a conta.

        New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"

3. Definir a conta de armazenamento atual.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

4. Criar um contêiner. A permissão é definida como **Desativada**, o que significa que o contêiner está acessível apenas para o proprietário.

        New-AzureStorageContainer -Name templates -Permission Off
        
5. Adicionar seu modelo ao contêiner.

        Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
        
### Forneça um token SAS durante a implantação

Para implantar um modelo privado em uma conta de armazenamento, recupere um token SAS e inclua-o no URI para o modelo.

1. Se você tiver alterado a conta de armazenamento atual, defina a conta de armazenamento atual como aquela que contém os modelos.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

2. Crie um token SAS com permissões de leitura e uma hora de vencimento para limitar o acesso. Recupere o URI completo do modelo, incluindo o token SAS.

        $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

3. Implante o modelo fornecendo o URI que inclui o token SAS.

        New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri

Para ver um exemplo de como usar um token SAS com modelos vinculados, consulte [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## Precedência de parâmetro

Você pode usar parâmetros embutidos e um arquivo de parâmetro local na mesma operação de implantação. Por exemplo, você pode especificar alguns valores no arquivo de parâmetro local e adicionar outros valores embutidos durante a implantação. Se você fornecer valores para um parâmetro no arquivo de parâmetros local e embutido, o valor embutido terá precedência.

No entanto, você não pode usar parâmetros embutidos com um arquivo de parâmetro externo. Quando você especificar um arquivo de parâmetro no parâmetro **TemplateParameterUri**, todos os parâmetros embutidos serão ignorados. Você deve fornecer todos os valores de parâmetro no arquivo externo. Se o seu modelo incluir um valor confidencial que não podem ser incluído no arquivo de parâmetros, adicione esse valor em um cofre de chaves e faça referência ao cofre de chaves em seu arquivo de parâmetro externo, ou forneça dinamicamente todos os valores de parâmetro embutidos.

Para ver os detalhes sobre como usar uma referência do KeyVault para transmitir valores seguros, consulte [Transmitir valores seguros durante a implantação](resource-manager-keyvault-parameter.md).

## Próximas etapas
- Para obter um exemplo de como implantar recursos por meio da biblioteca de cliente do .NET, veja [Implantar recursos usando bibliotecas do .NET e um modelo](virtual-machines/virtual-machines-windows-csharp-template.md).
- Para definir os parâmetros no modelo, consulte [Criando modelos](resource-group-authoring-templates.md#parameters).
- Para obter orientação sobre como implantar a solução em ambientes diferentes, confira [Ambientes de desenvolvimento e de teste no Microsoft Azure](solution-dev-test-environments.md).

<!---HONumber=AcomDC_0817_2016-->