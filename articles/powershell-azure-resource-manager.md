<properties 
	pageTitle="Azure PowerShell com o Gerenciador de Recursos | Microsoft Azure" 
	description="Introdução ao uso do Azure PowerShell para implantar vários recursos como um grupo de recursos no Azure." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/11/2016" 
	ms.author="tomfitz"/>

# Usando o Azure PowerShell com o Azure Resource Manager

> [AZURE.SELECTOR]
- [PowerShell do Azure](powershell-azure-resource-manager.md)
- [CLI do Azure](xplat-cli-azure-resource-manager.md)

O Azure Resource Manager introduz uma maneira completamente nova de pensar sobre os recursos do Azure. Em vez de criar e gerenciar recursos individuais, você começa imaginando uma solução inteira, como um blog, uma galeria de fotos, um portal do SharePoint ou um wiki. Use um modelo – uma representação declarativa da solução – para criar um grupo de recursos que contém todos os recursos que necessários para dar suporte à solução. Em seguida, você gerencia e implanta esse grupo de recursos como uma unidade lógica.

Neste tutorial, você aprenderá a usar o Azure PowerShell com o Azure Resource Manager. Ele orienta você pelo processo de implantação de uma solução e ajuda a trabalhar com essa solução. Você usará o Azure PowerShell e um modelo do Resource Manager para implantar:

- SQL Server - para hospedar o banco de dados
- Banco de dados SQL - para armazenar os dados
- Regras de firewall - para permitir que o aplicativo Web se conecte ao banco de dados
- Plano de Serviço de Aplicativo - para definir os recursos e o custo do aplicativo Web
- Site - para executar o aplicativo Web
- Configuração da Web - para armazenar a cadeia de conexão no banco de dados 
- Regras de alerta - para monitorar o desempenho e erros
- Insights de aplicativo - para configurações de dimensionamento automático

## Pré-requisitos

Para concluir este tutorial, você precisará:

- Uma conta do Azure
  + Você pode [abrir uma conta do Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F): você recebe créditos que podem ser usados para experimentar serviços pagos do Azure e, mesmo após eles serem utilizados, você pode manter a conta e usar os serviços gratuitos do Azure, como os Sites. Seu cartão de crédito nunca será cobrado, a menos que você altere explicitamente suas configurações, solicitando esse tipo de cobrança.
  
  + Você pode [ativar benefícios para assinantes do MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): todos os meses, sua assinatura do MSDN concede créditos que podem ser usados para serviços pagos do Azure.
- Azure PowerShell 1.0. Para saber mais sobre esta versão e como instalá-la, confira [Como instalar e configurar o Azure PowerShell](powershell-install-configure.md).

Este tutorial foi criado para iniciantes do PowerShell, mas pressupõe que você compreende os conceitos básicos, como módulos, cmdlets e sessões.

## Obter ajuda para os cmdlets

Para obter ajuda detalhada sobre qualquer cmdlet que você vir neste tutorial, use o cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda para o cmdlet Get-AzureRmResource, digite:

    Get-Help Get-AzureRmResource -Detailed

Para obter uma lista dos cmdlets no módulo de Recursos com uma sinopse da Ajuda, digite:

    Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

A saída será semelhante ao seguinte extrato:

	Name                                   Synopsis
	----                                   --------
	Find-AzureRmResource                   Searches for resources using the specified parameters.
	Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
	Get-AzureRmADGroup                     Filters active directory groups.
	Get-AzureRmADGroupMember               Get a group members.
	...

Para obter ajuda completa sobre um cmdlet, digite um comando com o formato:

    Get-Help <cmdlet-name> -Full
  
## Faça logon em sua conta do Azure

Antes de trabalhar em sua solução, você deve fazer logon em sua conta.

Para fazer logon em sua conta do Azure, use o cmdlet **Add-AzureRmAccount**.

    Add-AzureRmAccount

O cmdlet solicita as credenciais de logon para sua conta do Azure. Depois de entrar, ele baixa as configurações da conta para que elas estejam disponíveis para o Azure PowerShell.

As configurações de conta expiram, portanto, você precisa atualizá-las ocasionalmente. Para atualizar as configurações da conta, execute **Add-AzureRmAccount** novamente.

>[AZURE.NOTE] Os módulos do Resource Manager exigem o cmdlet Add-AzureRmAccount. Um arquivo de configurações de publicação não é suficiente.

Se você tiver mais de uma assinatura, forneça a ID da assinatura que quer usar para implantação com o cmdlet **Set-AzureRmContext**.

    Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

## Criar um grupo de recursos

Antes de implantar os recursos em sua assinatura, você deve criar um grupo de recursos que conterá os recursos.

Para criar um grupo de recursos, use o cmdlet **New-AzureRmResourceGroup**.

O comando usa o parâmetro **Name** para especificar um nome para o grupo de recursos e o parâmetro **Location** para especificar o local. Com base no que descobrimos na seção anterior, usaremos "Oeste dos Estados Unidos" para o local.

    New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
A saída será semelhante a:

    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

O grupo de recursos foi criado com êxito.

## Implantar sua solução

Este tópico não mostra como criar seu modelo nem discute a estrutura do modelo. Para saber como fazer isso, confira [Criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md) e [Passo a passo do modelo do Resource Manager](resource-manager-template-walkthrough.md). Você implantará o modelo pré-definido em [Provisionar um aplicativo Web com um Banco de Dados SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) a partir dos [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/).

Você tem o grupo de recursos e o seu modelo, portanto agora está pronto para implantar a infraestrutura definida no modelo para o grupo de recursos. Implante recursos com o cmdlet **New-AzureRmResourceGroupDeployment**. O modelo especifica muitos valores padrão que usaremos para que você não precise fornecer valores para esses parâmetros. A sintaxe básica é semelhante a essa:

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -administratorLogin exampleadmin -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

Especifique o grupo de recursos e o local do modelo. Se o modelo for um arquivo local, use o parâmetro **-TemplateFile** e especifique o caminho até o modelo. Você pode definir o parâmetro **-Mode** como **Incremental** ou **Complete**. Por padrão, o Gerenciador de Recursos executa uma atualização incremental durante a implantação; portanto, não é essencial definir **-Mode** quando quiser a opção **Incremental**. Para entender as diferenças entre esses modos de implantação, consulte [Implantar um aplicativo com o modelo do Azure Resource Manager](resource-group-template-deploy.md).

###Parâmetros de modelo dinâmico

Se estiver familiarizado com o PowerShell, você saberá que pode percorrer os parâmetros disponíveis para um cmdlet digitando um sinal de subtração (-) e, em seguida, pressionando a tecla TAB. Essa mesma funcionalidade também funciona com os parâmetros definidos no modelo. Assim que você digitar o nome do modelo, o cmdlet buscará o modelo, o analisará e adicionará dinamicamente os parâmetros do modelo ao comando. Isso facilita muito a especificação dos valores de parâmetros do modelo.

Quando você digitar o comando, será solicitado que você forneça o parâmetro obrigatório ausente, **administratorLoginPassword**. Além disso, quando você digitar a senha, o valor da cadeia de caracteres segura será obscurecido. Essa estratégia elimina o risco de fornecer uma senha em texto sem formatação.

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

Se o modelo incluir um parâmetro com um nome que corresponde a um dos parâmetros incluídos no comando para implantar o modelo (como incluir um parâmetro chamado **ResourceGroupName** no modelo que é igual ao parâmetro **ResourceGroupName** no cmdlet [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx)), será solicitado que você forneça um valor para um parâmetro com **FromTemplate** pós-fixado (como **ResourceGroupNameFromTemplate**). Em geral, você deve evitar essa confusão não dando aos parâmetros o mesmo nome que os parâmetros usados para operações de implantação.

O comando é executado e retorna mensagens conforme os recursos são criados. Por fim, você pode ver o resultado de sua implantação.

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 4/11/2016 7:26:11 PM
    Mode              : Incremental
    TemplateLink      :
                Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json
                ContentVersion : 1.0.0.0
    Parameters        :
                Name             Type                       Value
                ===============  =========================  ==========
                skuName          String                     F1
                skuCapacity      Int                        1
                administratorLogin  String                  exampleadmin
                administratorLoginPassword  SecureString
                databaseName     String                     sampledb
                collation        String                     SQL_Latin1_General_CP1_CI_AS
                edition          String                     Basic
                maxSizeBytes     String                     1073741824
                requestedServiceObjectiveName  String       Basic

    Outputs           :
                Name             Type                       Value
                ===============  =========================  ==========
                siteUri          String                     websites5wdai7p2k2g4.azurewebsites.net
                sqlSvrFqdn       String                     sqlservers5wdai7p2k2g4.database.windows.net
                    
    DeploymentDebugLogLevel :

Em apenas algumas etapas, criamos e implantamos os recursos necessários para um site complexo.

### Informações de depuração do log

Ao implantar um modelo, você pode registrar informações adicionais sobre a solicitação e a resposta especificando o parâmetro **-DeploymentDebugLogLevel** ao executar o cmdlet **New-AzureRmResourceGroupDeployment**. Essas informações podem ajudar você a solucionar erros de implantação. O valor padrão é **Nenhum**, o que significa que nenhum conteúdo de solicitação ou resposta será registrado. Você pode especificar o registro em log do conteúdo da solicitação, da resposta ou de ambos. Para saber mais sobre como solucionar problemas de implantação e como registrar informações de depuração , confira [Solução de problemas de implantações de grupo de recursos com o Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md). O exemplo a seguir registra o conteúdo da solicitação e da resposta da implantação.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -DeploymentDebugLogLevel All -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

> [AZURE.NOTE] Ao especificar o parâmetro DeploymentDebugLogLevel, considere com atenção o tipo de informação que você está passando durante a implantação. Ao registrar em log as informações sobre a solicitação ou resposta, você pode potencialmente expor dados confidenciais que são recuperados por meio de operações de implantação.


## Obter informações sobre os seus grupos de recursos

Depois de criar um grupo de recursos, você pode usar os cmdlets no módulo do Gerenciador de Recursos para gerenciar seu grupo de recursos.

- Para obter um grupo de recursos em sua assinatura, use o cmdlet **Get-AzureRmResourceGroup**:

		Get-AzureRmResourceGroup -ResourceGroupName TestRG1
	
	Ele retornará as seguintes informações:

		ResourceGroupName : TestRG1
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
		
		...

	Se você não especificar um nome para o grupo de recursos, o cmdlet retornará todos os grupos de recursos em sua assinatura.

- Para obter os recursos no grupo de recursos, use o cmdlet **Find-AzureRmResource** e seu parâmetro **ResourceGroupNameContains**. Sem parâmetros, o Find-AzureRmResource obtém todos os recursos em sua assinatura do Azure.

        Find-AzureRmResource -ResourceGroupNameContains TestRG1
	
     Isso retorna uma lista de recursos formatada da seguinte maneira:
		
        Name              : sqlservers5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/sqlservers5wdai7p2k2g4
        ResourceName      : sqlservers5wdai7p2k2g4
        ResourceType      : Microsoft.Sql/servers
        Kind              : v2.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
        Tags              : {System.Collections.Hashtable}
        ...
	        
- Você pode usar marcas para organizar os recursos de forma lógica em sua assinatura e para recuperar os recursos com os cmdlets **Find-AzureRmResource** e **Find-AzureRmResourceGroup**.

        Find-AzureRmResource -TagName displayName -TagValue Website

        Name              : webSites5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/webSites5wdai7p2k2g4
        ResourceName      : webSites5wdai7p2k2g4
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      Há muito mais que você pode fazer com marcas. Para obter mais informações, veja [Usando marcas para organizar os recursos do Azure](resource-group-using-tags.md).

## Adicionar a um grupo de recursos

Para adicionar um recurso ao grupo de recursos, use o cmdlet **New-AzureRmResource**. No entanto, adicionar um recurso dessa maneira pode causar confusão futura, pois o novo recurso não existe no modelo. Se você implantou novamente o modelo antigo, você implantaria uma solução incompleta. Se estiver realizando implantações com frequência, você achará mais fácil e mais confiável adicionar o novo recurso ao seu modelo e implantá-lo novamente.

## Mover um recurso

Você pode mover os recursos existentes para um novo grupo de recursos. Para obter exemplos, confira [Mover recursos para um novo grupo de recursos ou uma nova assinatura](resource-group-move-resources.md).

## Exportar modelo

Para um grupo de recursos existente (implantado por meio do PowerShell ou com um dos outros métodos, por exemplo, o portal), você pode exibir o modelo do Resource Manager do grupo de recursos. A exportação do modelo oferece dois benefícios:

1. Você pode automatizar com facilidade as implantações futuras da solução, pois toda a infraestrutura está definida no modelo.

2. Você pode se familiarizar com a sintaxe do modelo analisando o JSON (JavaScript Object Notation) que representa sua solução.

Por meio do PowerShell, você pode gerar um modelo que representa o estado atual de seu grupo de recursos ou recuperar o modelo que foi usado para uma determinada implantação.

A exportação do modelo para um grupo de recursos é útil quando você tiver feito alterações em um grupo de recursos e precisar recuperar a representação do JSON de seu estado atual. No entanto, o modelo gerado contém apenas uma quantidade mínima de parâmetros e nenhuma variável. A maioria dos valores no modelo é embutida em código. Antes de implantar o modelo gerado, convém converter mais valores em parâmetros, para que você possa personalizar a implantação para ambientes diferentes.

A exportação do modelo para uma implantação específica é útil quando você precisa exibir o modelo real usado para implantar os recursos. O modelo incluirá todos os parâmetros e variáveis definidos para a implantação original. No entanto, se alguém em sua organização tiver feito alterações ao grupo de recursos que estejam fora da definição no modelo, esse modelo não representará o estado atual do grupo de recursos.

> [AZURE.NOTE] O recurso de exportação do modelo está em versão de visualização, e nem todos os tipos de recursos oferecem suporte à exportação de um modelo. Ao tentar exportar um modelo, talvez você veja um erro afirmando que alguns recursos não foram exportados. Se for necessário, defina manualmente esses recursos em seu modelo depois de baixá-lo.

###Exportar modelo do grupo de recursos

Para ver o modelo de um grupo de recursos, execute o cmdlet **Export-AzureRmResourceGroup**.

    Export-AzureRmResourceGroup -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\TestRG1.json
    
###Baixar o modelo da implantação

Para baixar o modelo usado para uma implantação específica, execute o cmdlet **Save-AzureRmResourceGroupDeploymentTemplate**.

    Save-AzureRmResourceGroupDeploymentTemplate -DeploymentName azuredeploy -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\azuredeploy.json

## Excluir recursos ou grupo de recursos

- Para excluir um recurso do grupo de recursos, use o cmdlet **Remove-AzureRmResource**. Esse cmdlet exclui o recurso, mas não exclui o grupo de recursos.

	Esse comando remove o site TestSite do grupo de recursos TestRG1.

		Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- Para excluir um grupo de recursos, use o cmdlet **Remove-AzureRmResourceGroup**. Este cmdlet exclui o grupo de recursos e seus recursos.

		Remove-AzureRmResourceGroup -Name TestRG1
		
	Você receberá uma solicitação para confirmar a exclusão.
		
		Confirm
		Are you sure you want to remove resource group 'TestRG1'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

## Script de implantação

Os exemplos de implantação anteriores neste tópico mostraram apenas os cmdlets individuais necessários para implantar recursos no Azure. O exemplo a seguir mostra um script de implantação que cria o grupo de recursos e implanta os recursos.

    <#
      .SYNOPSIS
      Deploys a template to Azure
      
      .DESCRIPTION
      Deploys an Azure Resource Manager template

      .PARAMETER subscriptionId
      The subscription id where the template will be deployed.

      .PARAMETER resourceGroupName
      The resource group where the template will be deployed. Can be the name of an existing or a new resource group.

      .PARAMETER resourceGroupLocation
      Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.

      .PARAMETER deploymentName
      The deployment name.

      .PARAMETER templateFilePath
      Optional, path to the template file. Defaults to template.json.

      .PARAMETER parametersFilePath
      Optional, path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
    #>

    param(
      [Parameter(Mandatory=$True)]
      [string]
      $subscriptionId,

      [Parameter(Mandatory=$True)]
      [string]
      $resourceGroupName,

      [string]
      $resourceGroupLocation,

      [Parameter(Mandatory=$True)]
      [string]
      $deploymentName,

      [string]
      $templateFilePath = "template.json",

      [string]
      $parametersFilePath = "parameters.json"
    )

    #******************************************************************************
    # Script body
    # Execution begins here 
    #******************************************************************************
    $ErrorActionPreference = "Stop"

    # sign in
    Write-Host "Logging in...";
    Add-AzureRmAccount;

    # select subscription
    Write-Host "Selecting subscription '$subscriptionId'";
    Set-AzureRmContext -SubscriptionID $subscriptionId;

    #Create or check for existing resource group
    $resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
    if(!$resourceGroup)
    {
      Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
      if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
      }
      Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
      New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
    }
    else{
      Write-Host "Using existing resource group '$resourceGroupName'";
    }

    # Start the deployment
    Write-Host "Starting deployment...";
    if(Test-Path $parametersFilePath) {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
    } else {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
    }

## Próximas etapas

- Para saber mais sobre a criação de modelos do Gerenciador de Recursos, confira [Criando Modelos do Azure Resource Manager](./resource-group-authoring-templates.md).
- Para saber mais sobre como implantar modelos, confira [Implantar um aplicativo com o Modelo do Azure Resource Manager](./resource-group-template-deploy.md).
- Para ver um exemplo detalhado da implantação de um projeto, confira [Implantar microsserviços de maneira previsível no Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Para saber mais sobre como solucionar problemas de uma implantação com falha, confira [Solucionando problemas de implantações de grupos de recursos no Azure](./resource-manager-troubleshoot-deployments-powershell.md).

<!---HONumber=AcomDC_0525_2016-->