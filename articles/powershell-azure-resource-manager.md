<properties 
	pageTitle="Azure PowerShell com o Gerenciador de Recursos | Microsoft Azure" 
	description="Introdução ao uso do Azure PowerShell para implantar vários recursos como um grupo de recursos no Azure." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/17/2016" 
	ms.author="tomfitz"/>

# Usando o Azure PowerShell com o Gerenciador de Recursos do Azure

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)

O Gerenciador de Recursos do Azure introduz uma maneira completamente nova de pensar sobre os recursos do Azure. Em vez de criar e gerenciar recursos individuais, você começa imaginando uma solução inteira, como um blog, uma galeria de fotos, um portal do SharePoint ou um wiki. Use um modelo – uma representação declarativa da solução – para criar um grupo de recursos que contém todos os recursos que necessários para dar suporte à solução. Em seguida, você gerencia e implanta esse grupo de recursos como uma unidade lógica.

Neste tutorial, você aprenderá a usar o Azure PowerShell com o Gerenciador de Recursos do Azure. Apresentamos e explicamos o processo de criação e implantação de um grupo de recursos para um aplicativo hospedado do Azure com um banco de dados SQL, completo com todos os recursos que você precisa para oferecer suporte a ele.

## Pré-requisitos

Para concluir este tutorial, você precisará:

- Uma conta do Azure
  + Você pode [abrir uma conta do Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F): você recebe créditos que podem ser usados para experimentar serviços pagos do Azure e, mesmo após eles serem utilizados, você pode manter a conta e usar os serviços gratuitos do Azure, como os Sites. Seu cartão de crédito nunca será cobrado, a menos que você altere explicitamente suas configurações, solicitando esse tipo de cobrança.
  
  + Você pode [ativar benefícios para assinantes do MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): todos os meses, sua assinatura do MSDN concede créditos que podem ser usados para serviços pagos do Azure.
- Azure PowerShell 1.0. Para obter informações sobre esta versão e como instalá-la, confira [Como instalar e configurar o Azure PowerShell](powershell-install-configure.md).

Este tutorial foi criado para iniciantes do PowerShell, mas pressupõe que você compreende os conceitos básicos, como módulos, cmdlets e sessões.

## O que você implantará

Neste tutorial, você usará o Azure PowerShell para implantar um aplicativo Web e um banco de dados SQL. No entanto, esse aplicativo Web e a solução do banco de dados SQL é composta por vários tipos de recursos que funcionam juntos. Os recursos reais que você implantará são:

- SQL Server - para hospedar o banco de dados
- Banco de dados SQL - para armazenar os dados
- Regras de firewall - para permitir que o aplicativo Web se conecte ao banco de dados
- Plano de Serviço de Aplicativo - para definir os recursos e o custo do aplicativo Web
- Site - para executar o aplicativo Web
- Configuração da Web - para armazenar a cadeia de conexão no banco de dados 

## Obter ajuda para os cmdlets

Para obter ajuda detalhada sobre qualquer cmdlet que você vir neste tutorial, use o cmdlet Get-Help.

	Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda para o cmdlet Get-AzureRmResource, digite:

	Get-Help Get-AzureRmResource -Detailed

Para obter uma lista dos cmdlets no módulo de Recursos com uma sinopse da Ajuda, digite:

    PS C:\> Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

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

Para fazer logon em sua conta do Azure, use o cmdlet **Login-AzureRmAccount**.

    PS C:\> Login-AzureRmAccount

O cmdlet solicita as credenciais de logon para sua conta do Azure. Depois de entrar, ele baixa as configurações da conta para que elas estejam disponíveis para o Azure PowerShell.

As configurações de conta expiram, portanto, você precisa atualizá-las ocasionalmente. Para atualizar as configurações de conta, execute **Login-AzureRmAccount** novamente.

>[AZURE.NOTE] Os módulos do Gerenciador de Recursos requer o Login-AzureRmAccount. Um arquivo de configurações de publicação não é suficiente.

## Obter locais para tipos de recursos

Ao implantar um recurso, você deve especificar onde deseja hospedá-lo. Nem todas as regiões dão suporte a todos os tipos de recurso. Antes de implantar seu aplicativo Web e o banco de dados SQL, você deve descobrir quais são as regiões que dão suporte a esses tipos. Um grupo de recursos pode conter recursos localizados em regiões diferentes; no entanto, sempre que possível, você deve criar recursos no mesmo local para otimizar o desempenho. Em particular, convém certificar-se de que o seu banco de dados está no mesmo local que o aplicativo que o acessa.

Para obter os locais que dão suporte a cada tipo de recurso, você precisará usar o cmdlet **Get-AzureRmResourceProvider**. Primeiro, vamos ver o que esse comando retorna:

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}
    ...

O ProviderNamespace representa uma coleção de tipos de recursos relacionados. Esses namespaces normalmente combinam bem com os serviços que você deseja criar no Azure. Se desejar usar um provedor de recursos listado como **Não registrado**, é possível registrar esse provedor de recursos executando o cmdlet **Register-AzureRmResourceProvider** e especificando o namespace do provedor a ser registrado. Provavelmente, o provedor de recursos que você usará neste tutorial já estará registrado em sua assinatura.

Você pode obter mais detalhes sobre um provedor especificando esse namespace:

    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql

    ProviderNamespace RegistrationState ResourceTypes                                 Locations
    ----------------- ----------------- -------------                                 ---------
    Microsoft.Sql     Registered        {operations}                                  {East US 2, South Central US, Cent...
    Microsoft.Sql     Registered        {locations}                                   {East US 2, South Central US, Cent...
    Microsoft.Sql     Registered        {locations/capabilities}                      {East US 2, South Central US, Cent...
    ...

Para limitar a saída dos locais com suporte de um tipo específico de recurso, como sites, use:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
A saída será semelhante a:

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

Os locais que você vê podem ser um pouco diferentes dos resultados anteriores. Os resultados pode ser diferentes, porque um administrador em sua organização criou uma política que limita quais regiões podem ser usadas em sua assinatura ou pode haver restrições relacionadas a políticas fiscais em seu país de residência.

Vamos executar o mesmo comando para o banco de dados:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql).ResourceTypes | Where-Object ResourceTypeName -eq servers).Locations
    East US 2
    South Central US
    Central US
    North Central US
    West US
    East US
    East Asia
    Southeast Asia
    Japan West
    Japan East
    North Europe
    West Europe
    Brazil South

Parece que esses recursos estão disponíveis em várias regiões. Para este tópico, usaremos **Oeste dos EUA**, mas você pode especificar qualquer uma das regiões com suporte.

## Criar um grupo de recursos

Esta seção do tutorial fornece orientações sobre o processo de criação de um grupo de recursos. O grupo de recursos servirá como um contêiner para todos os recursos em sua solução que compartilham o mesmo ciclo de vida. Posteriormente no tutorial, você implantará o aplicativo Web e o banco de dados SQL neste grupo de recursos.

Para criar um grupo de recursos, use o cmdlet **New-AzureRmResourceGroup**.

O comando usa o parâmetro **Name** para especificar um nome para o grupo de recursos e o parâmetro **Location** para especificar o local. Com base no que descobrimos na seção anterior, usaremos "Oeste dos Estados Unidos" para o local.

    PS C:\> New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

O grupo de recursos foi criado com êxito.


## Obter as versões de API disponíveis para os recursos

Ao implantar um modelo, você deve especificar uma versão de API a ser usada para criar o recurso. As versões disponíveis da API correspondem às versões das operações da API REST que são lançadas pelo provedor de recursos. À medida que os provedores de recursos habilitam novos recursos, eles lançarão novas versões da API REST. Portanto, a versão da API que você especificar em seu modelo afeta quais propriedades estão disponíveis para você durante a criação do modelo. Em geral, você deve selecionar a versão mais recente da API ao criar novos modelos. Para os modelos existentes, você pode decidir se deseja continuar usando uma versão de API que você sabe que não alterará sua implantação, ou se deseja atualizar o modelo para a versão mais recente para aproveitar os novos recursos.

Esta etapa pode parecer confusa, mas não é difícil descobrir as versões de API disponíveis para o recurso. Você usará novamente o comando **Get-AzureRmResourceProvider**.

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

Como você pode ver, essa API foi atualizada com frequência. Normalmente, os mesmos números de versão de API estarão disponíveis para todos os recursos em um provedor de recursos. A única exceção seria se um recurso foi adicionado ou removido em algum momento. Vamos supor que as mesmas versões de API estão disponíveis para o recurso do serverFarms. No entanto, você pode verificar novamente qualquer recurso que você acha que pode ter uma lista diferente de versões de API disponíveis.

Para o banco de dados, você verá:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql).ResourceTypes | Where-Object ResourceTypeName -eq servers/databases).ApiVersions
    2014-04-01-preview
    2014-04-01 

## Criar seu modelo

Este tópico não mostra como criar seu modelo nem discute a estrutura do modelo. Para obter essas informações, veja [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md). O modelo que você implantará é mostrado abaixo. Observe que o modelo usa as versões de API que você recuperou na seção anterior. Para garantir que todos os recursos residam na mesma região, usamos a expressão de modelo **resourceGroup().location** para usar o local do grupo de recursos.

Observe, ainda, a seção de parâmetros. Esta seção define os valores que você pode fornecer ao implantar os recursos. Você usará esses valores posteriormente neste tutorial.

Você pode copiar o modelo e salvá-lo localmente como um arquivo .json. Neste tutorial, vamos supor que ele foi salvo em c:\\Azure\\Templates\\azuredeploy.json, mas você pode salvá-lo em qualquer local conveniente e com um nome que faça sentido de acordo com suas necessidades.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            },
            "serverName": {
                "type": "string"
            },
            "databaseName": {
                "type": "string"
            },
            "administratorLogin": {
                "type": "string"
            },
            "administratorLoginPassword": {
                "type": "securestring"
            }
        },
        "variables": {
            "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "name": "[parameters('serverName')]",
                "type": "Microsoft.Sql/servers",
                "location": "[resourceGroup().location]",
                "apiVersion": "2014-04-01",
                "properties": {
                    "administratorLogin": "[parameters('administratorLogin')]",
                    "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
                    "version": "12.0"
                },
                "resources": [
                    {
                        "name": "[parameters('databaseName')]",
                        "type": "databases",
                        "location": "[resourceGroup().location]",
                        "apiVersion": "2014-04-01",
                        "dependsOn": [
                            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                        ],
                        "properties": {
                            "edition": "Basic",
                            "collation": "SQL_Latin1_General_CP1_CI_AS",
                            "maxSizeBytes": "1073741824",
                            "requestedServiceObjectiveName": "Basic"
                        }
                    },
                    {
                        "name": "AllowAllWindowsAzureIps",
                        "type": "firewallrules",
                        "location": "[resourceGroup().location]",
                        "apiVersion": "2014-04-01",
                        "dependsOn": [
                            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                        ],
                        "properties": {
                            "endIpAddress": "0.0.0.0",
                            "startIpAddress": "0.0.0.0"
                        }
                    }
                ]
            },
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "tags": {
                    "team": "webdev"
                },
                "dependsOn": [
                    "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
                ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
                "resources": [
                    {
                        "name": "web",
                        "type": "config",
                        "apiVersion": "2015-08-01",
                        "dependsOn": [
                            "[concat('Microsoft.Web/Sites/', variables('siteName'))]"
                        ],
                        "properties": {
                            "connectionStrings": [
                                {
                                    "ConnectionString": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('serverName'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('databaseName'), ';User Id=', parameters('administratorLogin'), '@', parameters('serverName'), ';Password=', parameters('administratorLoginPassword'), ';')]",
                                    "Name": "DefaultConnection",
                                    "Type": 2
                                }
                            ]
                        }
                    }
                ]
            }
        ]
    }


## Implantar o modelo

Você tem o grupo de recursos e o seu modelo, portanto agora está pronto para implantar a infraestrutura definida no modelo para o grupo de recursos. Implante recursos com o cmdlet **New-AzureRmResourceGroupDeployment**. A sintaxe básica é semelhante a essa:

    PS C:\> New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -TemplateFile c:\Azure\Templates\azuredeploy.json

Especifique o grupo de recursos e o local do modelo. Se o seu modelo não for local, você poderá usar o parâmetro **-TemplateUri** e especificar um URI para o modelo. Você pode definir o parâmetro **-Mode** como **Incremental** ou **Complete**. Por padrão, o Gerenciador de Recursos executa uma atualização incremental durante a implantação; portanto, não é essencial definir **-Mode** quando quiser a opção **Incremental**. Para entender as diferenças entre esses modos de implantação, consulte [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).

###Parâmetros de modelo dinâmico

Se estiver familiarizado com o PowerShell, você saberá que pode percorrer os parâmetros disponíveis para um cmdlet digitando um sinal de subtração (-) e, em seguida, pressionando a tecla TAB. Essa mesma funcionalidade também funciona com os parâmetros definidos no modelo. Assim que você digitar o nome do modelo, o cmdlet buscará o modelo, o analisará e adicionará dinamicamente os parâmetros do modelo ao comando. Isso facilita muito a especificação dos valores de parâmetros do modelo. E, se você esquecer um valor de parâmetro necessário, o PowerShell solicitará o valor.

Veja abaixo o comando completo com os parâmetros incluídos. Você pode fornecer seus próprios valores para os nomes dos recursos.

    PS C:\> New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -TemplateFile c:\Azure\Templates\azuredeploy.json -hostingPlanName freeplanwest -serverName exampleserver -databaseName exampledata -administratorLogin exampleadmin

Quando você digitar o comando, será solicitado que você forneça o parâmetro obrigatório ausente, **administratorLoginPassword**. Além disso, quando você digitar a senha, o valor da cadeia de caracteres segura será obscurecido. Essa estratégia elimina o risco de fornecer uma senha em texto sem formatação.

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

Se o modelo incluir um parâmetro com um nome que corresponda a um dos parâmetros no comando para implantar o modelo (como incluir um parâmetro chamado **ResourceGroupName** no modelo que é igual ao parâmetro **ResourceGroupName** no cmdlet [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx)), você será solicitado a fornecer um valor para um parâmetro com o sufixo **FromTemplate** (como **ResourceGroupNameFromTemplate**). Em geral, você deve evitar essa confusão não dando aos parâmetros o mesmo nome que os parâmetros usados para operações de implantação.

O comando é executado e retorna mensagens conforme os recursos são criados. Por fim, você pode ver o resultado de sua implantação.

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 10/16/2015 12:55:50 AM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    hostingPlanName  String                     freeplanwest
                    serverName       String                     exampleserver
                    databaseName     String                     exampledata
                    administratorLogin  String                  exampleadmin
                    administratorLoginPassword  SecureString

    Outputs           :

Em apenas algumas etapas, criamos e implantamos os recursos necessários para um site complexo.

## Obter informações sobre os seus grupos de recursos

Depois de criar um grupo de recursos, você pode usar os cmdlets no módulo do Gerenciador de Recursos para gerenciar seu grupo de recursos.

- Para obter todos os grupos de recursos em sua assinatura, use o cmdlet **Get-AzureRmResourceGroup**:

		PS C:\> Get-AzureRmResourceGroup

		ResourceGroupName : TestRG1
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
		
		...

      Se você quiser obter determinado grupo de recursos, forneça o parâmetro **Name**.
      
          PS C:\> Get-AzureRmResourceGroup -Name TestRG1

- Para obter os recursos no grupo de recursos, use o cmdlet **Find-AzureRmResource** e seu parâmetro **ResourceGroupNameContains**. Sem parâmetros, o Find-AzureRmResource obtém todos os recursos em sua assinatura do Azure.

        PS C:\> Find-AzureRmResource -ResourceGroupNameContains TestRG1
		
        Name              : exampleserver
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/tfserver10
        ResourceName      : exampleserver
        ResourceType      : Microsoft.Sql/servers
        Kind              : v12.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
        ...
	        
- O modelo acima inclui uma marca em um recurso. Você pode aplicar marcas para organizar os recursos de modo lógico em sua assinatura. Você usa os comandos **Find-AzureRmResource** e **Find-AzureRmResourceGroup** comandos para consultar os recursos por marcas.

        PS C:\> Find-AzureRmResource -TagName team

        Name              : ExampleSiteuxq53xiz5etmq
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/ExampleSiteuxq53xiz5etmq
        ResourceName      : ExampleSiteuxq53xiz5etmq
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      Há muito mais que você pode fazer com marcas. Para obter mais informações, veja [Usando marcas para organizar os recursos do Azure](resource-group-using-tags.md).

## Adicionar a um grupo de recursos

Para adicionar um recurso ao grupo de recursos, use o cmdlet **New-AzureRmResource**. No entanto, adicionar um recurso dessa maneira pode causar confusão futura, pois o novo recurso não existe no modelo. Se você implantou novamente o modelo antigo, você implantaria uma solução incompleta. Se estiver realizando implantações com frequência, você achará mais fácil e mais confiável adicionar o novo recurso ao seu modelo e implantá-lo novamente.

## Mover um recurso

Você pode mover os recursos existentes para um novo grupo de recursos. Para obter exemplos, confira [Mover recursos para um novo grupo de recursos ou uma nova assinatura](resource-group-move-resources.md).

## Excluir um grupo de recursos

- Para excluir um recurso do grupo de recursos, use o cmdlet **Remove-AzureRmResource**. Esse cmdlet exclui o recurso, mas não exclui o grupo de recursos.

	Este comando remove o site TestSite do grupo de recursos TestRG.

		Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- Para excluir um grupo de recursos, use o cmdlet **Remove-AzureRmResourceGroup**. Este cmdlet exclui o grupo de recursos e seus recursos.

		PS C:\> Remove-AzureRmResourceGroup -Name TestRG1
		
		Confirm
		Are you sure you want to remove resource group 'TestRG1'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y



## Próximas etapas

- Para saber mais sobre a criação de modelos do Gerenciador de Recursos, confira [Criando Modelos do Gerenciador de Recursos do Azure](./resource-group-authoring-templates.md).
- Para saber mais sobre como implantar modelos, confira [Implantar um aplicativo com o Modelo do Gerenciador de Recursos do Azure](./resource-group-template-deploy.md).
- Para ver um exemplo detalhado da implantação de um projeto, confira [Implantar microsserviços de maneira previsível no Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Para saber mais sobre como solucionar problemas de uma implantação com falha, confira [Solucionando problemas de implantações de grupos de recursos no Azure](./virtual-machines/resource-group-deploy-debug.md).

<!---HONumber=AcomDC_0218_2016-->