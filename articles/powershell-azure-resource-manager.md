<properties 
	pageTitle="Usando o Azure PowerShell com o Gerenciador de Recursos do Azure" 
	description="Use o Azure PowerShell para implantar vários recursos como um grupo de recursos no Azure." 
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
	ms.topic="article" 
	ms.date="07/15/2015" 
	ms.author="tomfitz"/>

# Usando o Azure PowerShell com o Gerenciador de Recursos do Azure

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)

O Gerenciador de Recursos do Azure introduz uma maneira completamente nova de pensar sobre os recursos do Azure. Em vez de criar e gerenciar recursos individuais, você começa imaginando um serviço complexo, como um blog, uma galeria de fotos, um portal do SharePoint ou um wiki. Você pode usar um modelo – um modelo de recurso do serviço-- para criar um grupo de recursos com os recursos que você precisa para oferecer suporte ao serviço. Em seguida, você pode gerenciar e implantar esse grupo de recursos como uma unidade lógica.

Neste tutorial, você aprenderá a usar o Azure PowerShell com o Gerenciador de Recursos no Microsoft Azure. Apresentamos e explicamos o processo de criação e implantação de um grupo de recursos para um aplicativo hospedado do Azure com um banco de dados SQL, completo com todos os recursos que você precisa para oferecer suporte a ele.

## Pré-requisitos

Para concluir este tutorial, você precisa ter o Azure PowerShell versão 0.8.0 ou posterior. Para instalar a versão mais recente e associá-la à sua assinatura do Azure, consulte [Como instalar e configurar o PowerShell do Azure](powershell-install-configure.md).

Este tutorial foi criado para iniciantes do PowerShell, mas pressupõe que você compreende os conceitos básicos, como módulos, cmdlets e sessões. Para obter mais informações sobre o Windows PowerShell, consulte [Introdução ao PowerShell do Windows Azure (a página pode estar em inglês)](http://technet.microsoft.com/library/hh857337.aspx).

Para obter ajuda detalhada sobre qualquer cmdlet que você vir neste tutorial, use o cmdlet Get-Help.

	Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda sobre o cmdlet Add-AzureAccount, digite:

	Get-Help Add-AzureAccount -Detailed

## Sobre os módulos do PowerShell do Azure
A partir da versão 0.8.0, a instalação do Azure PowerShell inclui mais que um módulo do Windows PowerShell: Você deve decidir se deseja usar os comandos que estão disponíveis no módulo do Azure ou o módulo do Gerenciador de recursos do Azure. Para facilitar a alternância entre eles, adicionamos um novo cmdlet, **Switch-AzureMode**, ao módulo de Perfil do Azure.

Por padrão, quando você usa o PowerShell do Azure, os cmdlets nos módulos do Azure são importados por padrão. Para alternar para o módulo do Gerenciador de Recursos do Azure, use o cmdlet Switch-AzureMode. Ele remove o módulo do Azure da sessão e importa os módulos do Gerenciador de Recursos do Azure e do Perfil do Azure.

Para alternar para o módulo AzureResourceManager, digite:

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Para alternar de volta para o módulo do Azure, digite:

    PS C:\> Switch-AzureMode -Name AzureServiceManagement

Por padrão, o Switch-AzureMode afeta apenas a sessão atual. Para tornar a opção eficaz em todas as sessões do PowerShell, use o parâmetro **Global** do Switch-AzureMode.

Para obter ajuda com o cmdlet Switch-AzureMode, digite: `Get-Help Switch-AzureMode` ou consulte [Switch-AzureMode](http://go.microsoft.com/fwlink/?LinkID=394398).
  
Para obter uma lista dos cmdlets no módulo AzureResourceManager com uma sinopse da Ajuda, digite:

    PS C:\> Get-Command -Module AzureResourceManager | Get-Help | Format-Table Name, Synopsis

A saída será semelhante ao seguinte extrato:

	Name                                   Synopsis
	----                                   --------
	Add-AlertRule                          Adds or updates an alert rule of either metric, event, o...
	Add-AzureAccount                       Adds the Azure account to Windows PowerShell
	Add-AzureEnvironment                   Creates an Azure environment
	Add-AzureKeyVaultKey                   Creates a key in a vault or imports a key into a vault.
        ...

Para obter ajuda completa sobre um cmdlet, digite um comando com o formato:

	Get-Help <cmdlet-name> -Full

Por exemplo,

	Get-Help Get-AzureLocation -Full

Para o conjunto completo de comandos do Gerenciador de Recursos do Azure, consulte [Cmdlets do Gerenciador de Recursos do Azure](http://go.microsoft.com/fwlink/?LinkID=394765).
  
## Criar um grupos de recursos

Esta seção do tutorial fornece orientações sobre o processo de criação e implantação de um grupo de recursos para um aplicativo com um banco de dados SQL.

Você não precisa ser um especialista em Azure, SQL, aplicativos ou gerenciamento de recursos para realizar essa tarefa. Os modelos fornecem um modelo de grupo de recursos com todos os recursos que podem ser necessários. E, como estamos usando o Windows PowerShell para automatizar as tarefas, você pode usar esse processo como um modelo para criar scripts de tarefas de grande escala.

### Etapa 1: Alternar para o Gerenciador de Recursos do Azure 
1. Iniciar o PowerShell. Você pode usar qualquer programa de host que desejar, como o Azure PowerShell ou o Windows PowerShell ISE.

2. Use o cmdlet **Switch-AzureMode** para importar os cmdlets nos módulos AzureResourceManager e AzureProfile.

        PS C:\> Switch-AzureMode AzureResourceManager

3. Para adicionar sua conta do Azure à sessão do Windows PowerShell, use o cmdlet **Add-AzureAccount**.

        PS C:\> Add-AzureAccount

O cmdlet solicita as credenciais de logon para sua conta do Azure. Depois de entrar, ele baixa as configurações da conta para que estejam disponíveis para o Windows PowerShell.

As configurações de conta expiram, portanto, você precisa atualizá-las ocasionalmente. Para atualizar as configurações de conta, execute **Add-AzureAccount** novamente.

>[AZURE.NOTE]O módulo AzureResourceManager requer o Add-AzureAccount. Um arquivo de configurações de publicação não é suficiente.

### Etapa 2: Selecione um modelo de galeria

Há várias maneiras de criar um grupo de recursos e seus recursos, mas a maneira mais fácil é usar um modelo de grupo de recursos. Um *modelo de grupo de recursos* é a cadeia de caracteres JSON que define os recursos em um grupo de recursos. A cadeia de caracteres inclui espaços reservados chamados "parâmetros" para valores definidos pelo usuário, como nomes e tamanhos.

O Azure hospeda uma galeria de modelos de grupos de recursos e você pode criar seus próprios modelos a partir do zero ou editando um modelo da galeria. Neste tutorial, usaremos um modelo da galeria.

Para ver todos os modelos na galeria de modelos de grupo de recursos do Azure, use o cmdlet **Get-AzureResourceGroupGalleryTemplate**; no entanto, esse comando retorna um grande número de modelos. Para ver um número mais fácil de gerir de modelos, especifique um parâmetro do publicador.

Na solicitação do PowerShell, digite:
    
    PS C:\> Get-AzureResourceGroupGalleryTemplate -Publisher Microsoft

O cmdlet retorna uma lista de modelos da galeria que tenham a Microsoft como publicador. Você usa a propriedade **Identity** para identificar o modelo nos comandos.

O modelo Microsoft.WebSiteSQLDatabase.0.2.6-preview parece ser interessante. Quando você executa o comando, a versão do modelo pode ser um pouco diferente porque foi lançada uma nova versão. Use a versão mais recente do modelo. Para obter mais informações sobre um modelo da galeria, use o parâmetro **Identity**. O valor do parâmetro Identity é a identidade do modelo.

    PS C:\> Get-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview

O cmdlet retorna um objeto com muito mais informações sobre o modelo, incluindo um resumo e uma descrição.

Esse modelo parece que atende às nossas necessidades. Vamos salvá-lo no disco e examiná-lo mais de perto.

### Etapa 3: Examinar o modelo

Vamos salvar o modelo em um arquivo JSON no disco. Essa etapa não é obrigatória, mas facilita a exibição do modelo. Para salvar o modelo, use o cmdlet **Save-AzureResourceGroupGalleryTemplate**. Use o parâmetro **Identity** para especificar o modelo, e o parâmetro **Path** para especificar um caminho no disco.

O Save-AzureResourceGroupGalleryTemplate salva o modelo e retorna o caminho e um nome de arquivo para o arquivo de modelo JSON.

	PS C:\> Save-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview -Path C:\Azure\Templates\New_WebSite_And_Database.json

	Path
	----
	C:\Azure\Templates\New_WebSite_And_Database.json


Você pode exibir o arquivo de modelo em um editor de texto, como o Bloco de Notas. Cada modelo possui uma seção de **parâmetros** e uma seção de **recursos**.

A seção **parameters** do modelo é uma coleção dos parâmetros que são definidos em todos os recursos. Inclui valores de propriedade que pode fornecer ao configurar o seu grupo de recursos.

    "parameters": {
      "siteName": {
        "type": "string"
      },
      "hostingPlanName": {
        "type": "string"
      },
      "siteLocation": {
        "type": "string"
      },
      ...
    }

Alguns parâmetros têm um valor padrão. Ao usar o modelo, você não precisa fornecer valores para esses parâmetros. Se você não especificar um valor, o valor padrão será usado.

    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    },

Quando os parâmetros tiverem valores enumerados, os valores válidos serão listados com o parâmetro. Por exemplo, o parâmetro **sku** pode assumir valores Free, Shared, Basic ou Standard. Se você não especificar um valor para o parâmetro **sku**, ele usará o valor padrão, Free.

    "sku": {
      "type": "string",
      "allowedValues": [
        "Free",
        "Shared",
        "Basic",
        "Standard"
      ],
      "defaultValue": "Free"
    },


Observe que o parâmetro **administratorLoginPassword** usa uma cadeia de caracteres segura, não texto sem formatação. Quando você fornece um valor para uma cadeia de caracteres segura, o valor é obscurecido.

	"administratorLoginPassword": {
      "type": "securestring"
    },

A seção **resources** do modelo lista os recursos que o modelo cria. Esse modelo cria um servidor de banco de dados SQL e um banco de dados SQL, um farm de servidores e um site e várias configurações de gerenciamento.
  
A definição de cada recurso inclui suas propriedades, como nome, tipo, local e parâmetros de valores definidos pelo usuário. Por exemplo, esta seção do modelo define o banco de dados SQL. Inclui parâmetros para o nome do banco de dados ([parameters('databaseName')]), o local do servidor de banco de dados [parameters('serverLocation')]e a propriedade de agrupamento [parameters('collation')].

    {
        "name": "[parameters('databaseName')]",
        "type": "databases",
        "location": "[parameters('serverLocation')]",
        "apiVersion": "2.0",
        "dependsOn": [
          "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
        ],
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveId": "[parameters('requestedServiceObjectiveId')]"
        }
    },


Estamos quase prontos para usar o modelo, mas antes disso, precisamos encontrar locais para cada um dos recursos.

### Etapa 4: Obter locais para tipos de recurso

A maioria dos modelos pedirá que você especifique um local para cada um dos recursos em um grupo de recursos. Cada recurso está localizado em um data center do Azure, mas nem todos os data centers do Azure oferecem suporte a todos os tipos de recurso.

Selecione qualquer local que ofereça suporte ao tipo de recurso. Você não precisa criar todos os recursos em um grupo de recursos no mesmo local; no entanto, sempre que possível, deverá criar os recursos no mesmo local para otimizar o desempenho. Em particular, convém certificar-se de que o seu banco de dados está no mesmo local que o aplicativo que o acessa.

Para obter os locais que oferecem suporte a cada tipo de recurso, use o cmdlet **Get-AzureLocation**. Para limitar a saída para um tipo específico de recurso, como ResourceGroup, use:

    Get-AzureLocation | Where-Object Name -eq "ResourceGroup" | Format-Table Name, LocationsString -Wrap

A saída será semelhante a:

    Name                                 LocationsString
    ----                                 ---------------
    ResourceGroup                        East Asia, South East Asia, East US, West US, North
                                         Central US, South Central US, Central US, North Europe,
                                         West Europe

Agora, temos as informações de que precisamos para criar o grupo de recursos.

### Etapa 5: Criar um grupo de recursos
 
Nesta etapa, vamos usar o modelo de grupo de recursos para criar o grupo de recursos. Para referência, abra o arquivo New\_WebSite\_And\_Database.json no disco e siga-o. O arquivo de modelo pode ser muito útil para determinar os valores de parâmetro para passar, como o ApiVersion correto para um recurso.

Para criar um grupo de recursos, use o cmdlet **New-AzureResourceGroup**.

O comando usa o parâmetro **Name** para especificar um nome para o grupo de recursos e o parâmetro **Location** para especificar o local. Use a saída de **Get-AzureLocation** para selecionar um local para o grupo de recursos. Ele usa o parâmetro **GalleryTemplateIdentity** para especificar o modelo da galeria.

	PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview
            ....

Assim que você digitar o nome do modelo, o New-AzureResourceGroup buscará o modelo, analisará e adicionará os parâmetros do modelo ao comando dinamicamente. Isso facilita muito a especificação dos valores de parâmetros do modelo. E, se você esquecer um valor de parâmetro necessário, o Windows PowerShell solicitará o valor.

**Parâmetros de Modelo Dinâmico**

Para obter os parâmetros, digite um sinal de subtração (-) para indicar um nome de parâmetro e, em seguida, pressione a tecla TAB. Ou, digite as primeiras letras de um nome de parâmetro, como siteName e, em seguida, pressione a tecla TAB.

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -si<TAB>

O PowerShell conclui o nome do parâmetro. Para percorrer os nomes dos parâmetros, pressione TAB repetidamente.

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName 

Digite um nome para o site e repita o processo de TAB para cada um dos parâmetros. Os parâmetros com um valor padrão são opcionais. Para aceitar o valor padrão, omita o parâmetro do comando.

Quando um parâmetro do modelo tem valores enumerados, como o parâmetro sku neste modelo, pressione a tecla TAB para percorrer os valores do parâmetro.

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku <TAB>

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Basic<TAB>

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Free<TAB>

Aqui está um exemplo de um comando New-AzureResourceGroup que especifica os parâmetros necessários do modelo e o parâmetro **Verbose** comum. Observe que o **administratorLoginPassword** foi omitido.

	PS C:\> New-AzureResourceGroup -Name TestRG -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -hostingPlanName TestPlan -siteLocation "East Asia" -serverName testserver -serverLocation "East Asia" -administratorLogin Admin01 -databaseName TestDB -Verbose

Quando você digitar o comando, será solicitado que você forneça o parâmetro obrigatório ausente, **administratorLoginPassword**. Além disso, quando você digitar a senha, o valor da cadeia de caracteres segura será obscurecido. Essa estratégia elimina o risco de fornecer uma senha em texto sem formatação.

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	administratorLoginPassword: **********

O **New-AzureResourceGroup** retorna o grupo de recursos que criou e implantou.

Em apenas algumas etapas, criamos e implantamos os recursos necessários para um site complexo. O modelo da galeria forneceu quase todas as informações de que precisávamos para realizar essa tarefa. Além disso, a tarefa é facilmente automatizada.

## Obter informações sobre os seus grupos de recursos

Depois de criar um grupo de recursos, você pode usar os cmdlets no módulo AzureResourceManager para gerenciar o seu grupo de recursos.

- Para obter todos os grupos de recursos na sua assinatura, use o cmdlet Get-AzureResourceGroup:

		PS C:\>Get-AzureResourceGroup

		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
		
		...

- Para obter os recursos no grupo de recursos, use o cmdlet **Get-AzureResource** e seu parâmetro ResourceGroupName. Sem parâmetros, o Get-AzureResource obtém todos os recursos na sua assinatura do Azure.

		PS C:\> Get-AzureResource -ResourceGroupName TestRG
		
		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		
		Resources         :
				Name                   Type                          Location
				----                   ------------                  --------
				ServerErrors-TestSite  microsoft.insights/alertrules         eastasia
	        	TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
	        	TestSite               microsoft.insights/components         centralus
	         	testserver             Microsoft.Sql/servers                 eastasia
	        	TestDB                 Microsoft.Sql/servers/databases       eastasia
	        	TestPlan               Microsoft.Web/serverFarms             eastasia
	        	TestSite               Microsoft.Web/sites                   eastasia
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## Adicionar a um grupo de recursos

- Para adicionar um recurso ao grupo de recursos, use o cmdlet **New-AzureResource**. Esse comando adiciona um novo site ao grupo de recursos TestRG. Este comando é um pouco mais complexo, porque não usa um modelo. 

        PS C:\>New-AzureResource -Name TestSite2 -Location "North Europe" -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01 -PropertyObject @{"name" = "TestSite2"; "siteMode"= "Limited"; "computeMode" = "Shared"}

- Para adicionar uma nova implantação baseada em modelo ao grupo de recursos, use o comando **AzureResourceGroupDeployment**.

		PS C:\>New-AzureResourceGroupDeployment ` 
		-ResourceGroupName TestRG `
		-GalleryTemplateIdentity Microsoft.WebSite.0.2.6-preview `
		-siteName TestWeb2 `
		-hostingPlanName TestDeploy2 `
		-siteLocation "North Europe" 

## Mover um recurso

Você pode mover os recursos existentes para um novo grupo de recursos. Para obter exemplos, consulte [Mover recursos para um novo grupo de recursos ou uma nova assinatura](resource-group-move-resources.md).

## Excluir um grupo de recursos

- Para excluir um recurso do grupo de recursos, use o cmdlet **Remove-AzureResource**. Esse cmdlet exclui o recurso, mas não exclui o grupo de recursos.

	Este comando remove o site TestSite2 do grupo de recursos TestRG.

		Remove-AzureResource -Name TestSite2 -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01

- Para excluir um grupo de recursos, use o cmdlet **Remove-AzureResourceGroup**. Este cmdlet exclui o grupo de recursos e seus recursos.

		PS C:\ps-test> Remove-AzureResourceGroup -Name TestRG
		
		Confirm
		Are you sure you want to remove resource group 'TestRG'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## Solucionar problemas de um grupo de recursos
Ao testar os cmdlets nos módulos do AzureResourceManager, há probabilidade de você encontrar erros. Use as dicas desta seção para resolvê-los.

### Evitando erros

O módulo AzureResourceManager inclui cmdlets que ajudam você a evitar erros.


- **Get-AzureLocation**: este cmdlet obtém os locais que dão suporte a cada tipo de recurso. Antes de inserir um local para um recurso, use esse cmdlet para verificar se o local oferece suporte ao tipo de recurso.


- **Test-AzureResourceGroupTemplate**: teste o modelo e o parâmetro do modelo antes de usá-los. Digite um modelo personalizado ou da galeria e os valores dos parâmetros do modelo que você pretende usar. Este cmdlet testa se o modelo é internamente consistente e se o conjunto de valores do parâmetro corresponde ao modelo.



### Corrigindo erros

- **Get-AzureResourceGroupLog**: este cmdlet obtém as entradas no log para cada implantação do grupo de recursos. Se algo der errado, comece examinando os logs de implantação. 

- **Verbose e Debug**: os cmdlets no módulo AzureResourceManager chamam APIs REST que executam o trabalho real. Para ver as mensagens que as APIs retornam, defina a variável $DebugPreference como "Continue" e use o parâmetro comum Verbose em seus comandos. As mensagens geralmente fornecem pistas importantes sobre a causa de qualquer falha.

- **Suas credenciais do Azure não foram configuradas ou expiraram**: para atualizar as credenciais em sua sessão do Windows PowerShell, use o cmdlet Add-AzureAccount. As credenciais em um arquivo de configurações de publicação não são suficientes para os cmdlets no módulo AzureResourceManager.


## Próximas etapas

- Para saber mais sobre a criação de modelos do Gerenciador de Recursos do Azure, consulte [Criando modelos do Gerenciador de Recursos do Azure](./resource-group-authoring-templates.md).
- Para saber mais sobre como implantar modelos, consulte [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](./resource-group-template-deploy.md)
- Para obter um exemplo detalhado da implantação de um projeto, consulte [Implantar microsserviços de maneira previsível no Azure](app-service-web/app-service-deploy-complex-application-predictably.md).

<!---HONumber=Oct15_HO3-->