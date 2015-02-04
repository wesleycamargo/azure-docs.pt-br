<properties pageTitle="Usando o Windows PowerShell com o Gerenciador de Recursos" metaKeywords="ResourceManager, PowerShell, Azure PowerShell" description="Usar o Windows PowerShell para criar um grupo de recursos" metaCanonical="" services="" documentationCenter="" title="Using Windows PowerShell with Resource Manager" authors="stevenka; juneb" solutions="" manager="stevenka" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="powershell" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="stevenka; juneb" />

# Usando o Windows PowerShell com o Gerenciador de Recursos

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/powershell-azure-resource-manager.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/pt-br/documentation/articles/xplat-cli-azure-resource-manager.md" title="CLI entre plataformas">CLI entre plataformas</a></div>

O Gerenciador de Recursos introduz uma maneira completamente nova de pensar sobre os recursos do Azure. Em vez de criar e gerenciar recursos individuais, você começa imaginando um serviço complexo, como um blog, uma galeria de fotos, um portal do SharePoint ou um wiki. Você pode usar um modelo – um modelo de recurso do serviço-- para criar um grupo de recursos com os recursos que você precisa para oferecer suporte ao serviço. Em seguida, você pode gerenciar e implantar esse grupo de recursos como uma unidade lógica.

Neste tutorial, você aprenderá a usar o Windows PowerShell com o Gerenciador de Recursos no Microsoft Azure. Apresentamos e explicamos o processo de criação e implantação de um grupo de recursos para um site hospedado do Azure (ou aplicativo web) com um banco de dados SQL, completo com todos os recursos que você precisa para oferecer suporte a ele.

**Tempo estimado para conclusão:** 15 minutos

## Pré-requisitos

Para poder usar o Windows PowerShell com o Gerenciador de Recursos, você deve ter o seguinte:

-   Windows PowerShell, versão 3.0 ou 4.0. Para localizar a versão do Windows PowerShell, digite:`$PSVersionTable` e verifique se o valor de `PSVersion` é 3.0 ou 4.0. Para instalar uma versão compatível, consulte [Windows Management Framework 3.0][Windows Management Framework 3.0] ou [Windows Management Framework 4.0][Windows Management Framework 4.0].

-   PowerShell do Azure, versão 0.8.0 ou posterior. Para instalar a versão mais recente e associá-la à sua assinatura do Azure, consulte [Como instalar e configurar o PowerShell do Windows Azure (a página pode estar em inglês)][Como instalar e configurar o PowerShell do Windows Azure (a página pode estar em inglês)].

Este tutorial foi criado para iniciantes do Windows PowerShell, mas pressupõe que você compreende os conceitos básicos, como módulos, cmdlets e sessões. Para obter mais informações sobre o Windows PowerShell, consulte [Introdução ao PowerShell do Windows Azure (a página pode estar em inglês)][Introdução ao PowerShell do Windows Azure (a página pode estar em inglês)].

Para obter ajuda detalhada sobre qualquer cmdlet que você vir neste tutorial, use o cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda sobre o cmdlet Add-AzureAccount, digite:

    Get-Help Add-AzureAccount -Detailed

## Neste tutorial

-   [Sobre os módulos do PowerShell do Azure][Sobre os módulos do PowerShell do Azure]
-   [Criar um grupos de recursos][Criar um grupos de recursos]
-   [Gerenciar um grupo de recursos][Gerenciar um grupo de recursos]
-   [Solucionar problemas de um grupo de recursos][Solucionar problemas de um grupo de recursos]
-   [Próximas etapas][Próximas etapas]

## <span id="about"></span></a>Sobre os módulos do PowerShell do Azure

A partir da versão 0.8.0, a instalação do PowerShell do Azure inclui três módulos do Windows PowerShell:

-   **Azure**: inclui os cmdlets tradicionais para gerenciar recursos individuais, como contas de armazenamento, sites, bancos de dados, máquinas virtuais e serviços de mídia. Para obter mais informações, consulte [Cmdlets do Gerenciamento de Serviços do Azure (a página pode estar em inglês)][Cmdlets do Gerenciamento de Serviços do Azure (a página pode estar em inglês)].

-   **AzureResourceManager**: inclui cmdlets para criar, gerenciar e implantar os recursos do Azure em um grupo de recursos. Para obter mais informações, consulte [Cmdlets do Gerenciador de Recursos do Azure (a página pode estar em inglês)][Cmdlets do Gerenciador de Recursos do Azure (a página pode estar em inglês)].

-   **AzureProfile**: inclui cmdlets comuns aos dois módulos, como Add-AzureAccount, Get-AzureSubscription e Switch-AzureMode. Para obter mais informações, consulte [Cmdlets de perfil do Azure (a página pode estar em inglês)][Cmdlets de perfil do Azure (a página pode estar em inglês)].

> [WACOM.NOTE] O módulo do Gerenciador de recursos do Azure está no modo de visualização. Ele pode não fornecer os mesmos recursos de gerenciamento que o módulo do Azure.

Os módulos do Azure e do Gerenciador de Recursos do Azure não foram projetados para serem usados na mesma sessão do Windows PowerShell. Para facilitar a alternância entre eles, adicionamos um novo cmdlet, **Switch-AzureMode**, ao módulo de Perfil do Azure.

Por padrão, quando você usa o PowerShell do Azure, os cmdlets nos módulos do Azure são importados por padrão. Para alternar para o módulo do Gerenciador de Recursos do Azure, use o cmdlet Switch-AzureMode. Ele remove o módulo do Azure da sessão e importa os módulos do Gerenciador de Recursos do Azure e do Perfil do Azure.

Para alternar para o módulo AzureResourceManager, digite:

    PS C:PS C:\> Switch-AzureMode -Name AzureResourceManagergt; Switch-AzureMode -Name AzureResourceManager

Para alternar de volta para o módulo do Azure, digite:

    PS C:PS C:\> Switch-AzureMode -Name AzureServiceManagementgt; Switch-AzureMode -Name AzureServiceManagement

Por padrão, o Switch-AzureMode afeta apenas a sessão atual. Para tornar a opção eficaz em todas as sessões do Windows PowerShell, use o parâmetro **Global** do Switch-AzureMode.

Para obter ajuda com o cmdlet Switch-AzureMode, digite: `Get-Help Switch-AzureMode` ou consulte [Switch-AzureMode][Switch-AzureMode].

Para obter uma lista dos cmdlets no módulo AzureResourceManager com uma sinopse da Ajuda, digite:

    PS C:\> Get-Command -Module AzureResourceManager | Get-Help | Format-Table Name, Synopsis

    Name                                   Synopsis
    ----                                   --------
    Get-AzureLocation                      Gets the Azure data center locations and the resource types that they support
    Get-AzureResource                      Gets Azure resources
    Get-AzureResourceGroup                 Gets Azure resource groups
    Get-AzureResourceGroupDeployment       Gets the deployments in a resource group.
    Get-AzureResourceGroupGalleryTemplate  Gets resource group templates in the gallery
    Get-AzureResourceGroupLog              Gets the deployment log for a resource group
    New-AzureResource                      Creates a new resource in a resource group
    New-AzureResourceGroup                 Creates an Azure resource group and its resources
    New-AzureResourceGroupDeployment       Add an Azure deployment to a resource group.
    Remove-AzureResource                   Deletes a resource
    Remove-AzureResourceGroup              Deletes a resource group.
    Save-AzureResourceGroupGalleryTemplate Saves a gallery template to a JSON file
    Set-AzureResource                      Changes the properties of an Azure resource.
    Stop-AzureResourceGroupDeployment      Cancels a resource group deployment
    Test-AzureResourceGroupTemplate        Detects errors in a resource group template or template parameters

Para obter ajuda completa sobre um cmdlet, digite um comando com o formato:

    Get-Help <cmdlet-name> -Full

Por exemplo,

    Get-Help Get-AzureLocation -Full

# <span id="create"></span></a> Criar um grupos de recursos

Esta seção do tutorial fornece orientações sobre o processo de criação e implantação de um grupo de recursos para um site com um banco de dados SQL.

Você não precisa ser um especialista em Azure, SQL, sites ou gerenciamento de recursos para realizar essa tarefa. Os modelos fornecem um modelo de grupo de recursos com todos os recursos que podem ser necessários. E, como estamos usando o Windows PowerShell para automatizar as tarefas, você pode usar esse processo como um modelo para criar scripts de tarefas de grande escala.

## Etapa 1: Alternar ao gerenciador de recursos do Azure

1.  Inicie o Windows PowerShell. Você pode usar qualquer programa de host que desejar, como o Windows PowerShell ou o Windows PowerShell ISE.

2.  Use o cmdlet **Switch-AzureMode** para importar os cmdlets nos módulos AzureResourceManager e AzureProfile.

    `PS C:PS C:\>Switch-AzureMode AzureResourceManager`gt;Switch-AzureMode AzureResourceManager</code>

3.  Para adicionar sua conta do Azure à sessão do Windows PowerShell, use o cmdlet **Add-AzureAccount**.

    `PS C:PS C:\> Add-AzureAccount`gt; Add-AzureAccount</code>

O cmdlet solicita um endereço de e-mail e senha. Em seguida, ele baixa as configurações da conta para que estejam disponíveis para o Windows PowerShell.

As configurações de conta expiram, portanto, você precisa atualizá-las ocasionalmente. Para atualizar as configurações de conta, execute **Add-AzureAccount** novamente.

> [WACOM.NOTE] O módulo AzureResourceManager requer o Add-AzureAccount. Um arquivo de configurações de publicação não é suficiente.

## Etapa 2: Selecione um modelo de galeria

Há várias maneiras de criar um grupo de recursos e seus recursos, mas a maneira mais fácil é usar um modelo de grupo de recursos. Um *modelo de grupo de recursos* é a cadeia de caracteres JSON que define os recursos em um grupo de recursos. A cadeia de caracteres inclui espaços reservados chamados "parâmetros" para valores definidos pelo usuário, como nomes e tamanhos.

O Azure hospeda uma galeria de modelos de grupos de recursos e você pode criar seus próprios modelos a partir do zero ou editando um modelo da galeria. Neste tutorial, usaremos um modelo da galeria.

Para procurar um modelo na galeria de modelos de grupos de recursos do Azure, use o cmdlet **Get-AzureResourceGroupGalleryTemplate**.

No prompt do Windows Powershell, digite:

    PS C:PS C:\> Get-AzureResourceGroupGalleryTemplategt; Get-AzureResourceGroupGalleryTemplate

O cmdlet retorna uma lista de modelos da galeria com as propriedades Publisher e Identity. Você usa a propriedade **Identity** para identificar o modelo nos comandos.

    Publisher       Identity
    ---------       --------
    Ghost           Ghost.Ghost.0.1.0-preview1
    Joomla          Joomla.Joomla.0.1.0-preview1
    Microsoft       Microsoft.ASPNETEmptySite.0.1.0-preview1
    Microsoft       Microsoft.ASPNETstarterSite.0.1.0-preview1
    Microsoft       Microsoft.Bakery.0.1.0-preview1
    Microsoft       Microsoft.Boilerplate.0.1.0-preview1
    ...

DICA: para recuperar o último comando, pressione a tecla de seta para cima.

O modelo Microsoft.WebSiteSQLDatabase.0.1.0-preview1 parece ser interessante. Para obter mais informações sobre um modelo da galeria, use o parâmetro **Identity**. O valor do parâmetro Identity é a identidade do modelo.

    PS C:PS C:\> Get-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.1.0-preview1gt; Get-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.1.0-preview1

O cmdlet retorna um objeto com muito mais informações sobre o modelo, incluindo uma descrição.

    <p>Windows Azure Websites offers secure and flexible development, 
    deployment and scaling options for any sized web application. Leverage 
    your existing tools to create and deploy applications without the hassle 
    of managing infrastructure.</p>

Esse modelo parece que atende às nossas necessidades. Vamos salvá-lo no disco e examiná-lo mais de perto.

## Etapa 3: Examine o modelo

Vamos salvar o modelo em um arquivo JSON no disco. Essa etapa não é obrigatória, mas facilita a exibição do modelo. Para salvar o modelo, use o cmdlet **Save-AzureResourceGroupGalleryTemplate**. Use o parâmetro **Identity** para especificar o modelo, e o parâmetro **Path** para especificar um caminho no disco.

O Save-AzureResourceGroupGalleryTemplate salva o modelo e retorna o caminho e um nome de arquivo para o arquivo de modelo JSON.

    PS C:\> Save-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 -Path D:\Azure\Templates

    Path
    ----
    D:\Azure\Templates\Microsoft.WebSite.0.1.0-preview1.json

Você pode exibir o arquivo de modelo em um editor de texto, como o Bloco de Notas. Cada modelo possui uma seção **resources** e uma seção **parameters**.

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
            "edition": "Web",
            "collation": "[parameters('collation')]",
            "maxSizeBytes": "1073741824"
          }
        },

A seção **parameters** do modelo é uma coleção dos parâmetros que são definidos em todos os recursos. Inclui as propriedades databaseName, serverLocation e collation.

    "parameters": {
    ...    

    "serverLocation": {
      "type": "string"
    }, 
    ...

    "databaseName": {
      "type": "string"
    },
    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    }

Alguns parâmetros têm um valor padrão. Ao usar o modelo, você não precisa fornecer valores para esses parâmetros. Se você não especificar um valor, o valor padrão será usado.

    "collation": {
          "type": "string",
          "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
        }

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

Estamos quase prontos para usar o modelo, mas antes disso, precisamos encontrar locais para cada um dos recursos.

## Etapa 4: Obter locais para tipos de recursos

A maioria dos modelos pedirá que você especifique um local para cada um dos recursos em um grupo de recursos. Cada recurso está localizado em um data center do Azure, mas nem todos os data centers do Azure oferecem suporte a todos os tipos de recurso.

Selecione qualquer local que ofereça suporte ao tipo de recurso. Os recursos em um grupo de recursos não precisam estar no mesmo local e não precisam estar no mesmo local que o grupo de recursos ou a assinatura.

Para obter os locais que oferecem suporte a cada tipo de recurso, use o cmdlet **Get-AzureLocation**. Este é um extrato da saída. (Essa saída pode ser diferente da sua. Os detalhes são passíveis de mudança ao longo do tempo ao longo do tempo.)

    Name                                 Locations
    ----                                 ---------
    ResourceGroup                        East Asia, South East Asia, East US, West US, North Central US,
                                         South Central US, Central US, North Europe, West Europe

    Microsoft.Sql/servers/databases      Brazil South, Central US, East Asia, East US, East US 2, Japan
                                         East, Japan West, North Central US, North Europe, South Central US,
                                         Southeast Asia, West Europe, West US

    Microsoft.Web/serverFarms            Brazil South, East Asia, East US, Japan East, Japan West, North
                                         Central US, North Europe, West Europe, West US

    Microsoft.Web/sites                  Brazil South, East Asia, East US, Japan East, Japan West, North
                                         Central US, North Europe, West Europe, West US

Agora, temos as informações de que precisamos para criar o grupo de recursos.

## Etapa 5: Criar um grupos de recursos

Nesta etapa, vamos usar o modelo de grupo de recursos para criar o grupo de recursos. Para referência, abra o arquivo JSON Microsoft.WebSiteSQLDatabase.0.1.0-preview1 no disco e siga-o.

Para criar um grupo de recursos, use o cmdlet **New-AzureResourceGroup**.

O comando usa o parâmetro **Name** para especificar um nome para o grupo de recursos e o parâmetro **Location** para especificar o local. Use a saída de **Get-AzureLocation** para selecionar um local para o grupo de recursos. Ele usa o parâmetro **GalleryTemplateIdentity** para especificar o modelo da galeria.

    PS C:\> New-AzureResourceGroup ` 
            -Name TestRG1 `
            -Location "East Asia" `
            -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 `
            ....

Assim que você digitar o nome do modelo, o New-AzureResourceGroup buscará o modelo, analisará e adicionará os parâmetros do modelo ao comando dinamicamente. Isso facilita muito a especificação dos valores de parâmetros do modelo. E, se você esquecer um valor de parâmetro necessário, o Windows PowerShell solicitará o valor.

**Parâmetros de modelo dinâmico**

Para obter os parâmetros, digite um sinal de subtração (-) para indicar um nome de parâmetro e, em seguida, pressione a tecla TAB. Ou, digite as primeiras letras de um nome de parâmetro, como siteName e, em seguida, pressione a tecla TAB.

        PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -si<TAB>

O Windows PowerShell conclui o nome do parâmetro. Para percorrer os nomes dos parâmetros, pressione TAB repetidamente.

        PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName 

Digite um nome para o site e repita o processo de TAB para cada um dos parâmetros. Os parâmetros com um valor padrão são opcionais. Para aceitar o valor padrão, omita o parâmetro do comando.

Quando um parâmetro do modelo tem valores enumerados, como o parâmetro sku neste modelo, pressione a tecla TAB para percorrer os valores do parâmetro.

        PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName TestSite -sku <TAB>

        PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName TestSite -sku Free<TAB>

        PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName TestSite -sku Basic<TAB>

Aqui está um exemplo de um comando New-AzureResourceGroup que especifica os parâmetros necessários do modelo e o parâmetro **Verbose** comum. Observe que o **administratorLoginPassword** foi omitido. (O backtick (\`) é o caractere de continuação de linha do Windows PowerShell.)

    PS C:\> New-AzureResourceGroup 
    -Name TestRG `
    -Location "East Asia" `
    -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 `
    -siteName TestSite `
    -hostingPlanName TestPlan `
    -siteLocation "North Europe" `
    -serverName testserver `
    -serverLocation "West US" `
    -administratorLogin Admin01 `
    -databaseName TestDB `
    -Verbose

Quando você digitar o comando, será solicitado que você forneça o parâmetro obrigatório ausente, **administratorLoginPassword**. Além disso, quando você digitar a senha, o valor da cadeia de caracteres segura será obscurecido. Essa estratégia elimina o risco de fornecer uma senha em texto sem formatação.

    cmdlet New-AzureResourceGroup at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: **********

O **New-AzureResourceGroup** retorna o grupo de recursos que criou e implantou. Esta é a saída do comando, incluindo a saída detalhada.

    VERBOSE: 3:47:30 PM - Create resource group 'TestRG' in location 'East Asia'
    VERBOSE: 3:47:30 PM - Template is valid.
    VERBOSE: 3:47:31 PM - Create template deployment 'Microsoft.WebSiteSQLDatabase.0.1.0-preview1'
    using template https://gallerystoreprodch.blob.core.windows.net/prod-microsoft-windowsazure-gallery/8D6B920B-10F4-4B5A-B3DA-9D398FBCF3EE.PUBLICGALLERYITEMS.MICROSOFT.WEBSITESQLDATABASE.0.1.0-PREVIEW1/DeploymentTemplates/Website_NewHostingPlan_SQL_NewDB-Default.json.
    VERBOSE: 3:47:43 PM - Resource Microsoft.Sql/servers 'testserver' provisioning status is succeeded
    VERBOSE: 3:47:43 PM - Resource Microsoft.Web/serverFarms 'TestPlan' provisioning status is
    succeeded
    VERBOSE: 3:47:47 PM - Resource Microsoft.Sql/servers/databases 'testserver/TestDB' provisioning status is succeeded
    VERBOSE: 3:47:47 PM - Resource microsoft.insights/autoscalesettings 'TestPlan-TestRG'
    provisioning status is succeeded
    VERBOSE: 3:47:47 PM - Resource Microsoft.Sql/servers/firewallrules
    'testserver/AllowAllWindowsAzureIps' provisioning status is succeeded
    VERBOSE: 3:47:50 PM - Resource Microsoft.Web/Sites 'TestSite' provisioning status is succeeded
    VERBOSE: 3:47:54 PM - Resource Microsoft.Web/Sites/config 'TestSite/web' provisioning status is succeeded
    VERBOSE: 3:47:54 PM - Resource microsoft.insights/alertrules 'ServerErrors-TestSite' provisioning
    status is succeeded
    VERBOSE: 3:47:57 PM - Resource microsoft.insights/components 'TestSite' provisioning status is succeeded


    ResourceGroupName : TestRG
    Location          : eastasia
    ProvisioningState : Succeeded
    Resources         :
                    Name                   Type                                  Location
                    =====================  ====================================  =========
                    ServerErrors-TestSite  microsoft.insights/alertrules         eastus
                    TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
                    TestSite               microsoft.insights/components         centralus
                    testserver             Microsoft.Sql/servers                 westus
                    TestDB                 Microsoft.Sql/servers/databases       westus
                    TestPlan               Microsoft.Web/serverFarms             westus
                    TestSite               Microsoft.Web/sites                   westus

Em apenas algumas etapas, criamos e implantamos os recursos necessários para um site complexo.
O modelo da galeria forneceu quase todas as informações de que precisávamos para realizar essa tarefa.
Além disso, a tarefa é facilmente automatizada.

# <span id="manage"></span></a> Gerenciar um Grupo de Recursos

Depois de criar um grupo de recursos, você pode usar os cmdlets no módulo AzureResourceManager para gerenciar o grupo de recursos, alterá-lo, adicionar recursos e removê-lo.

-   Para obter os grupos de recursos na sua assinatura, use o cmdlet **Get-AzureResourceGroup**:

        PS C:>Get-AzureResourceGroup

        ResourceGroupName : TestRG
        Location          : eastasia
        ProvisioningState : Succeeded
        Resources         :
                        Name                   Type                                  Location
                        =====================  ====================================  =========
                        ServerErrors-TestSite  microsoft.insights/alertrules         eastus
                        TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
                        TestSite               microsoft.insights/components         centralus
                        testserver             Microsoft.Sql/servers                 westus
                        TestDB                 Microsoft.Sql/servers/databases       westus
                        TestPlan               Microsoft.Web/serverFarms             westus
                        TestSite               Microsoft.Web/sites                   westus

-   Para obter os recursos no grupo de recursos, use o cmdlet **GetAzureResource** e seu parâmetro ResourceGroupName. Sem parâmetros, o Get-AzureResource obtém todos os recursos na sua assinatura do Azure.

        PS C:\> Get-AzureResource -ResourceGroupName TestRG

        Name                   ResourceType                          Location
        ----                   ------------                          --------
        ServerErrors-TestSite  microsoft.insights/alertrules         eastus
        TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
        TestSite               microsoft.insights/components         centralus
        testserver             Microsoft.Sql/servers                 westus
        TestDB                 Microsoft.Sql/servers/databases       westus
        TestPlan               Microsoft.Web/serverFarms             westus
        TestSite               Microsoft.Web/sites                   westus

-   Para adicionar um recurso ao grupo de recursos, use o cmdlet **New-AzureResource**. Esse comando adiciona um novo site ao grupo de recursos TestRG. Este comando é um pouco mais complexo, porque não usa um modelo.

        PS C:\>New-AzureResource -Name TestSite2 `
        -Location "North Europe" `
        -ResourceGroupName TestRG `
        -ResourceType "Microsoft.Web/sites" `
        -ApiVersion 2004-04-01 `
        -PropertyObject @{"name" = "TestSite2"; "siteMode"= "Limited"; "computeMode" = "Shared"}

-   Para adicionar uma nova implantação baseada em modelo ao grupo de recursos, use o comando **AzureResourceGroupDeployment**.

        PS C:\>New-AzureResourceGroupDeployment ` 
        -ResourceGroupName TestRG `
        -GalleryTemplateIdentity Microsoft.WebSite.0.1.0-preview1 `
        -siteName TestWeb2 `
        -hostingPlanName TestDeploy2 `
        -siteMode Limited `
        -computeMode Dedicated `
        -siteLocation "North Europe" `
        -subscriptionID "9b14a38b-4b93-4554-8bb0-3cefb47abcde" `
        -resourceGroup TestRG

-   Para excluir um recurso do grupo de recursos, use o cmdlet **Remove-AzureResource**. Esse cmdlet exclui o recurso, mas não exclui o grupo de recursos.

    Este comando remove o site TestSite2 do grupo de recursos TestRG.

        Remove-AzureResource -Name TestSite2 `
            -Location "North Europe" `
            -ResourceGroupName TestRG `
            -ResourceType "Microsoft.Web/sites" `
            -ApiVersion 2004-04-01

-   Para excluir um grupo de recursos, use o cmdlet **Remove-AzureResourceGroup**. Este cmdlet exclui o grupo de recursos e seus recursos.

        PS C:\ps-test> Remove-AzureResourceGroup -Name TestRG

        Confirm
        Are you sure you want to remove resource group 'TestRG'
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

# <span id="troubleshoot"></span></a> Solucionar problemas de um grupo de recursos

Ao testar os cmdlets nos módulos do AzureResourceManager, há probabilidade de você encontrar erros. Use as dicas desta seção para resolvê-los.

## Evitando erros

O módulo AzureResourceManager inclui cmdlets que ajudam você a evitar erros.

-   **Get-AzureLocation**: este cmdlet obtém os locais que oferecem suporte a cada tipo de recurso. Antes de inserir um local para um recurso, use esse cmdlet para verificar se o local oferece suporte ao tipo de recurso.

-   **Test-AzureResourceGroupTemplate**: teste o modelo e o parâmetro do modelo antes de usá-los. Digite um modelo personalizado ou da galeria e os valores dos parâmetros do modelo que você pretende usar. Este cmdlet testa se o modelo é internamente consistente e se o conjunto de valores do parâmetro corresponde ao modelo.

## Corrigindo erros

-   **Get-AzureResourceGroupLog**: este cmdlet obtém as entradas no log para cada implantação do grupo de recursos. Se algo der errado, comece examinando os logs de implantação.

-   **Verbose e Debug**: os cmdlets no módulo AzureResourceManager chamam APIs REST que executam o trabalho real. Para ver as mensagens que as APIs retornam, defina a variável $DebugPreference como "Continue" e use o parâmetro comum Verbose em seus comandos. As mensagens geralmente fornecem pistas importantes sobre a causa de qualquer falha.

-   **Suas credenciais do Azure não foram configuradas ou expiraram**: para atualizar as credenciais em sua sessão do Windows PowerShell, use o cmdlet Add-AzureAccount. As credenciais em um arquivo de configurações de publicação não são suficientes para os cmdlets no módulo AzureResourceManager.

# <span id="next"></span></a>Próximas etapas

Para obter mais informações sobre como usar o Windows PowerShell com o Gerenciador de Recursos:

-   [Cmdlets do Gerenciador de Recursos do Azure (a página pode estar em inglês)][1]: saiba usar os cmdlets no módulo AzureResourceManager.
-   [Usando grupos de recursos para gerenciar os recursos do Azure][Usando grupos de recursos para gerenciar os recursos do Azure]: Saiba como criar e gerenciar grupos de recursos no Portal de Gerenciamento do Azure.
-   [Usando a interface de linha de comando da plataforma cruzada do Microsoft Azure com o Gerenciador de Recursos][Usando a interface de linha de comando da plataforma cruzada do Microsoft Azure com o Gerenciador de Recursos]: Saiba como criar e gerenciar os grupos de recursos com as ferramentas de linha de comando que funcionam em muitas plataformas de sistemas operacionais.
-   [Blog do Azure][Blog do Azure]: obtenha informações sobre os novos recursos no Azure.
-   [Blog do Windows PowerShell][Blog do Windows PowerShell]: obtenha informações sobre os novos recursos do Windows PowerShell.
-   [Blog "Hey, Scripting Guy!" Blog][Blog "Hey, Scripting Guy!" Blog]: obtenha dicas reais e truques da comunidade.do Windows PowerShell.

  [Windows Management Framework 3.0]: http://www.microsoft.com/pt-br/download/details.aspx?id=34595
  [Windows Management Framework 4.0]: http://www.microsoft.com/pt-br/download/details.aspx?id=40855
  [Sobre os módulos do PowerShell do Azure]: #about
  [Criar um grupos de recursos]: #create
  [Gerenciar um grupo de recursos]: #manage
  [Solucionar problemas de um grupo de recursos]: #troubleshoot
  [Próximas etapas]: #next
  [Switch-AzureMode]: http://go.microsoft.com/fwlink/?LinkID=394398
  [Usando grupos de recursos para gerenciar os recursos do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/azure-preview-portal-using-resource-groups
  [Usando a interface de linha de comando da plataforma cruzada do Microsoft Azure com o Gerenciador de Recursos]: http://www.windowsazure.com/pt-br/documentation/articles/xplat-cli-azure-resource-manager/
  [Blog do Azure]: http://blogs.msdn.com/windowsazure
  [Blog do Windows PowerShell]: http://blogs.msdn.com/powershell
  [Blog "Hey, Scripting Guy!" Blog]: http://blogs.technet.com/b/heyscriptingguy/
