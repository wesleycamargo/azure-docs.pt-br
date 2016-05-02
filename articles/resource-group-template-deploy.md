<properties
   pageTitle="Implantar recursos com o modelo do Gerenciador de Recursos | Microsoft Azure"
   services="azure-resource-manager"
   description="Use o Gerenciador de Recursos do Azure para implantar recursos no Azure. Um modelo é um arquivo JSON e pode ser usado do Portal, PowerShell, a Interface de linha de comando do Azure para Mac, Linux e Windows ou REST."
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
   ms.date="04/11/2016"
   ms.author="tomfitz"/>

# Implantar recursos com modelos do Azure Resource Manager

Este tópico explica como usar modelos do Azure Resource Manager para implantar seus Recursos no Azure. Ele mostra como implantar seus Recursos usando o Azure PowerShell, a CLI do Azure, a API REST ou o portal do Azure.

Ao implantar uma definição de aplicativo com um modelo, você pode fornecer valores de parâmetro para personalizar como os recursos são criados. Você especifica valores para esses parâmetros embutidos ou em um arquivo de parâmetros.

## Implantações incrementais e completas

Por padrão, o Gerenciador de Recursos trata as implantações como atualizações incrementais para o grupo de recursos. Com a implantação incremental, o Gerenciador de Recursos:

- **deixa inalterados** os recursos existentes no grupo de recursos, mas que não foram especificados no modelo
- **adiciona** os recursos especificados no modelo, mas que não existem no grupo de recursos 
- **não reprovisiona** os recursos existentes no grupo de recursos na mesma condição definida no modelo

Com a implantação completa, o Gerenciador de Recursos:

- **exclui** os recursos existentes no grupo de recursos, mas que não foram especificados no modelo
- **adiciona** os recursos especificados no modelo, mas que não existem no grupo de recursos 
- **não reprovisiona** os recursos existentes no grupo de recursos na mesma condição definida no modelo
 
Especifique o tipo de implantação por meio da propriedade **Mode**, conforme mostrado nos exemplos abaixo.

## Implantação com o PowerShell

1. Faça logon em sua conta do Azure. Depois de fornecer suas credenciais, o comando retornará informações sobre sua conta.

        Add-AzureRmAccount

     Um resumo da sua conta é retornado.

        Environment : AzureCloud
        Account    : someone@example.com
        ...


2. Se você tiver várias assinaturas, forneça a ID da assinatura que deseja usar para implantação com o comando **Set-AzureRmContext**.

        Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

3. Se você não tiver um grupo de recursos existente, crie um novo grupo de recursos com o comando **New-AzureRmResourceGroup**. Forneça o nome do grupo de recursos e o local necessários para sua solução.

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

4. Valide sua implantação antes de executá-la usando o cmdlet **Test-AzureRmResourceGroupDeployment**. Ao testar a implantação, forneça parâmetros exatamente como faria ao executar a implantação (mostrado na próxima etapa).

        Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

5. Para criar uma nova implantação para seu grupo de recursos, execute o comando **New-AzureRmResourceGroupDeployment** e forneça os parâmetros necessários. Os parâmetros incluirão um nome para sua implantação, o nome do seu grupo de recursos, o caminho ou a URL para o modelo criado e qualquer outro parâmetro necessário para seu cenário. Caso o parâmetro **Mode** não esteja especificado, o valor padrão de **Incremental** será usado.
   
     Você tem as três opções a seguir para fornecer valores de parâmetro:
   
     1. Use parâmetros embutidos.

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

     2. Use um objeto de parâmetro.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

     3. Usando um arquivo de parâmetro. Para obter informações sobre o arquivo de modelo, consulte [Arquivo de parâmetro](./#parameter-file).

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

     Depois de os recursos serem implantados por meio de um dos três métodos acima, você verá um resumo da implantação.

        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleResourceGroup
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2015 7:00:27 PM
        Mode              : Incremental
        ...

     Para executar uma implantação completa, defina **Mode** como **Complete**.

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -Mode Complete -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> 
        
     Você receberá uma solicitação para confirmar que deseja usar o modo Completo, que pode envolver a exclusão de recursos.
        
        Confirm
        Are you sure you want to use the complete deployment mode? Resources in the resource group 'ExampleResourceGroup' which are not
        included in the template will be deleted.
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

     Se o modelo incluir um parâmetro com um nome que corresponda a um dos parâmetros no comando para implantar o modelo (como incluir um parâmetro chamado **ResourceGroupName** no modelo que é igual ao parâmetro **ResourceGroupName** no cmdlet [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx)), você será solicitado a fornecer um valor para um parâmetro com o sufixo **FromTemplate** (como **ResourceGroupNameFromTemplate**). Em geral, você deve evitar essa confusão não dando aos parâmetros o mesmo nome dos parâmetros usados para operações de implantação.

6. Se você quiser registrar informações adicionais sobre a implantação que podem ajudar a solucionar possíveis erros de implantação, use o parâmetro **DeploymentDebugLogLevel**. Você pode especificar que o conteúdo da solicitação, o conteúdo da resposta ou ambos sejam registrados com a operação de implantação.

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
        
     Para saber mais sobre como usar este conteúdo de depuração para solucionar problemas de implantação, consulte [Solução de problemas de implantações de grupo de recursos com o Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md).
       
        
### Vídeo

Aqui está um vídeo que demonstra como trabalhar com modelos do Gerenciador de Recursos com o PowerShell.

[AZURE.VIDEO deploy-an-application-with-azure-resource-manager-template]


## Implantar com a CLI do Azure

Se você não utilizou anteriormente a CLI do Azures com o Gerenciamento de Recursos, consulte [Usando a CLI do Azure para Mac, Linux e Windows e Windows com o Gerenciamento de Recursos do Azure](xplat-cli-azure-resource-manager.md).

1. Faça logon em sua conta do Azure. Depois de fornecer suas credenciais, o comando retornará o resultado do seu logon.

        azure login
  
        ...
        info:    login command OK

2. Se você tiver várias assinaturas, forneça a ID da assinatura que deseja usar para implantação.

        azure account set <YourSubscriptionNameOrId>

3. Alternar para o módulo do Gerenciador de Recursos do Azure. Você receberá a confirmação do novo modo.

        azure config mode arm
   
        info:     New mode is arm

4. Se você não tiver um grupo de recursos existente, crie um novo grupo de recursos. Forneça o nome do grupo de recursos e o local necessários para sua solução. Um resumo do novo grupo de recursos é retornado.

        azure group create -n ExampleResourceGroup -l "West US"
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Valide sua implantação antes de executá-la usando o comando **azure group template validate**. Ao testar a implantação, forneça parâmetros exatamente como faria ao executar a implantação (mostrado na próxima etapa).

        azure group template validate -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup

5. Para criar uma nova implantação para seu grupo de recursos, execute o comando a seguir e ofereça os parâmetros necessários. Os parâmetros incluirão um nome para sua implantação, o nome do seu grupo de recursos, o caminho ou a URL para o modelo criado e qualquer outro parâmetro necessário para seu cenário.
   
     Você tem as três opções a seguir para fornecer valores de parâmetro:

     1. Use parâmetros embutidos e um modelo local. Cada parâmetro está no formato: `"ParameterName": { "value": "ParameterValue" }`. O exemplo a seguir mostra os parâmetros com caracteres de escape.

            azure group deployment create -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     2. Use parâmetros embutidos e um link para um modelo.

            azure group deployment create --template-uri <LinkToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     3. Use um arquivo de parâmetro. Para obter informações sobre o arquivo de modelo, consulte [Arquivo de parâmetro](./#parameter-file).
    
            azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     Depois de os recursos serem implantados por meio de um dos três métodos acima, você verá um resumo da implantação.
  
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        ...
        info:    group deployment create command OK

     Para executar uma implantação completa, defina **mode** como **Complete**.

        azure group deployment create --mode Complete -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

6. Se você quiser registrar informações adicionais sobre a implantação que podem ajudar a solucionar possíveis erros de implantação, use o parâmetro **debug-setting**. Você pode especificar que o conteúdo da solicitação, o conteúdo da resposta ou ambos sejam registrados com a operação de implantação.

        azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

## Implantar com a API REST
1. Definir [Parâmetros e cabeçalhos comuns](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common), incluindo tokens de autenticação.
2. Se você não tiver um grupo de recursos existente, crie um novo grupo de recursos. Forneça sua id de assinatura, o nome do novo grupo de recursos e local que você precisa para sua solução. Para obter mais informações, consulte [Criar um grupo de recursos](https://msdn.microsoft.com/library/azure/dn790525.aspx).

        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
   
3. Valide sua implantação antes de executá-la usando a operação [Validar uma implantação modelo](https://msdn.microsoft.com/library/azure/dn790547.aspx). Ao testar a implantação, forneça parâmetros exatamente como faria ao executar a implantação (mostrado na próxima etapa).

3. Criar um novo grupo de recursos. Forneça seu id de assinatura, o nome do grupo dos recursos para a serem implantados, o nome da implantação e o local do seu modelo. Para obter informações sobre o arquivo de modelo, consulte [Arquivo de parâmetro](./#parameter-file). Para obter mais informações sobre a API REST para criar um grupo de recursos, consulte [Criar uma implantação de modelo](https://msdn.microsoft.com/library/azure/dn790564.aspx). Observe que **mode** está definido como **Incremental**. Para executar uma implantação completa, defina **mode** como **Complete**.
    
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",
              }
            }
          }
   
      Se você desejar registrar o conteúdo da resposta, o conteúdo da solicitação ou ambos, inclua **debugSetting** na solicitação.

        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }


4. Obtém o status da implantação do modelo. Para obter mais informações, consulte [obter informações sobre uma implantação de modelo](https://msdn.microsoft.com/library/azure/dn790565.aspx).

          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## Implantação com o Visual Studio

Com o Visual Studio você pode criar um projeto do grupo de recursos e implantá-lo ao Azure por meio da interface do usuário. Selecione o tipo de recursos a serem incluídos em seu projeto e os recursos serão adicionados automaticamente ao modelo do Gerenciador de recursos. O projeto também fornece um script do PowerShell para implantar o modelo.

Para obter uma introdução ao uso do Visual Studio com grupos de recursos, consulte [Criando e implantando grupos de recursos do Azure por meio do Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## Faça a implantação com o portal

E adivinhe? Todos os aplicativos criados por meio do [portal](https://portal.azure.com/) têm suporte de um modelo do Gerenciador de Recursos do Azure! Ao simplesmente criar uma Máquina Virtual, Rede Virtual, Conta de Armazenamento, Serviço de Aplicativo ou banco de dados por meio do portal, você já está colhendo os benefícios do Gerenciador de recursos do Azure sem muito esforço. Basta selecionar o ícone **Novo** para estar a caminho da implantação de um aplicativo por meio do Gerenciador de Recursos do Azure.

![Novo](./media/resource-group-template-deploy/new.png)

Todas as implantações por meio do portal são validadas automaticamente antes da execução. Para saber mais sobre como usar o portal com o Gerenciador de Recursos do Azure, confira [Como usar o Portal do Azure para gerenciar os recursos do Azure](azure-portal/resource-group-portal.md).


## Arquivo de parâmetro.

Se você usar um arquivo de parâmetro para passar os valores de parâmetro para seu modelo durante a implantação, será necessário criar um arquivo JSON com um formato semelhante ao exemplo a seguir.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "webSiteName": {
                "value": "ExampleSite"
            },
            "webSiteHostingPlanName": {
                "value": "DefaultPlan"
            },
            "webSiteLocation": {
                "value": "West US"
            },
            "adminPassword": {
                "reference": {
                   "keyVault": {
                      "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
                   }, 
                   "secretName": "sqlAdminPassword" 
                }   
            }
       }
    }

O tamanho do arquivo de parâmetro não pode ser superior a 64 KB.

Para saber como definir os parâmetros no modelo, consulte [Criando modelos](../resource-group-authoring-templates/#parameters). Para obter detalhes sobre a referência a KeyVault para passar valores seguros, consulte [Passar valores seguros durante a implantação](resource-manager-keyvault-parameter.md)

## Próximas etapas
- Para obter um exemplo de como implantar recursos por meio da biblioteca de cliente do .NET, confira [Implantar recursos usando bibliotecas .NET e um modelo](virtual-machines/virtual-machines-windows-csharp-template.md)
- Para obter um exemplo detalhado de implantação de um aplicativo, confira [Provisionar e implantar microsserviços de forma previsível no Azure](app-service-web/app-service-deploy-complex-application-predictably.md)
- Para obter orientação sobre como implantar a solução em ambientes diferentes, confira [Ambientes de desenvolvimento e de teste no Microsoft Azure](solution-dev-test-environments.md).
- Para saber mais sobre as seções do modelo do Gerenciador de Recursos do Azure, confira [Criação de modelos](resource-group-authoring-templates.md)
- Para obter uma lista das funções que você pode usar em um modelo do Gerenciador de Recursos do Azure, confira [Funções do modelo](resource-group-template-functions.md)

 

<!---HONumber=AcomDC_0420_2016-->