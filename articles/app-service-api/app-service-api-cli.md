<properties
	pageTitle="Os aplicativos de API e CLI do Azure | Microsoft Azure"
	description="Como usar a Interface de linha de comando (CLI) do Microsoft Azure para Mac, Linux e Windows com aplicativos de API do Azure."
	editor="jimbe"
	manager="wpickett"
	documentationCenter=""
	authors="tdykstra"
	services="app-service\api"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/20/2015"
	ms.author="tdykstra"/>

# A Interface de linha de comando (CLI) do Azure e os aplicativos de API

Este artigo mostra como criar, gerenciar e excluir aplicativos de API no Serviço de Aplicativo do Azure, usando a Interface de linha de comando (CLI) do Azure para Mac, Linux e Windows.

## Pré-requisitos

Este artigo pressupõe que você instalou a CLI do Azure e sabe como executar tarefas básicas. Para obter uma introdução a CLI, consulte [Instalar e configurar a CLI do Azure](../xplat-cli.md).

> [AZURE.NOTE]As instruções para [conectar-se a uma assinatura do Azure](../xplat-cli-connect.md) oferecem duas alternativas: fazer logon usando uma conta de trabalho ou escolar e baixar um arquivo *. publishsettings*. Para aplicativos de API, o método de autenticação de arquivo *. publishsettings* não funcionará. Isso ocorre porque você precisa usar o modo de Gerenciamento de recursos (apresentado na próxima seção) para trabalhar com aplicativos de API e o método de autenticação de arquivo *. publishsettings* não funciona com o Gerenciador de recursos.

## Habilitar o modo de Gerenciamento de recursos

A CLI pode ser usada no modo [Gerenciamento de serviço (asm)](../virtual-machines/virtual-machines-command-line-tools.md) ou no modo [Gerenciamento de recursos (arm)](../xplat-cli-azure-resource-manager.md). Para aplicativos de API, você precisa usar o modo de Gerenciamento de recursos. Como o modo `arm` não está habilitado por padrão, use o comando `config mode arm` para habilitá-lo.

	azure config mode arm

## Lista os comandos disponíveis para trabalhar com aplicativos de API

Para ver todos os comandos disponíveis no momento para trabalhar com aplicativos de API, execute o comando `apiapp`.

	azure apiapp

## Lista todos os aplicativos de API em um assinatura ou grupo de recursos

Para listar todos os aplicativos de API que você tem em sua assinatura, execute o comando `apiapp list` sem parâmetros.

	azure apiapp list

A lista mostra o grupo de recursos, nome do aplicativo de API, ID do pacote e a URL de cada aplicativo de API.

	info:    Executing command apiapp list
	info:    Listing ApiApps
	data:    Resource Group  Name           Package Id        Url                                                                       
	data:    --------------  -------------  ----------------  --------------------------------------------------------------------------
	data:    mygroup         SimpleDropbox  Microsoft.ApiApp  https://microsoft-apiappf1bbba377c6d4aa1f03146cadd6.azurewebsites.net
	info:    apiapp list command OK

Para limitar a lista para um grupo de recursos especificado, adicione o parâmetro de grupo (`-g`).

	azure apiapp list -g <resource group name>

Por exemplo:

	azure apiapp list -g mygroup

Para adicionar a versão do aplicativo de API e informações de nível de acesso à lista, adicione o parâmetro de detalhes (`-d`).

	azure apiapp list -d

A saída `list` com os campos adicionais se parece com este exemplo:

	info:    Executing command apiapp list
	info:    Listing ApiApps
	data:    Resource Group  Name           Package Id     Version  Auth                 Url                                                                       
	data:    --------------  -------------  -------------  -------  -------------------  --------------------------------------------------------------------------
	data:    mygroup         SimpleDropbox  SimpleDropbox  1.0.0    PublicAuthenticated  https://microsoft-apiappf1bbba377cbd2a3aa1f03146c6.azurewebsites.net
	info:    apiapp list command OK

### Lista detalhes sobre um aplicativo de API

Para listar detalhes para um aplicativo de API, use o comando `apiapp show` com o grupo (`-g`) e os parâmetros de nome (`-n`) do aplicativo de API.

	azure apiapp show -g <resource group name> -n <API app name>

Por exemplo:

	azure apiapp show -g mygroup -n SimpleDropbox

A saída se parece com esta:

	info:    Executing command apiapp show
	info:    Getting ApiApp
	data:    Name:              SimpleDropbox
	data:    Location:          West US
	data:    Resource Group:    mygroup
	data:    Package Id:        SimpleDropbox
	data:    Package Version:   1.0.0
	data:    Update Policy:     Disabled
	data:    Access Level:      PublicAuthenticated
	data:    Hosting site name: microsoft-apiappf1bbba377c6d4bd2a36cadd6
	data:    Gateway name:      SD1aeb4ae60b7cb4f3d966dfa43b6607f30
	info:    apiapp show command OK

## Criar um aplicativo de API

Há duas maneiras de criar um aplicativo de API. Você pode usar comandos CLI obrigatórios para criar recursos do Azure individualmente, ou você pode usar sintaxe declarativa em um modelo para definir todos os recursos necessários juntos e implantar o modelo com um comando CLI. Para a abordagem declarativa, consulte [Próximas etapas](#next-steps).

Para criar um aplicativo de API usando a abordagem imperativa, execute as seguintes etapas:

1. Escolha um local válido
1. Crie ou localize um grupo de recursos para usar
2. Crie ou localize um plano de Serviço de Aplicativo para usar
4. Criar o Aplicativo de API

### Escolha um local válido

Quando você cria um grupo de recursos, você precisa especificar um local. Aqui estão alguns dos locais válidos para aplicativos de API.

* Leste dos EUA
* Oeste dos EUA
* Centro-Sul dos Estados Unidos
* Norte da Europa
* Ásia Oriental
* Leste do Japão
* Europa Ocidental
* Sudeste Asiático
* Oeste do Japão
* Centro-Norte dos EUA
* Centro dos EUA
* Sul do Brasil
* Leste dos EUA 2

Para obter uma lista completa e atualizada dos locais, use o comando `location list` e consulte a linha do provedor de recursos `Microsoft.AppService/apiapps`.

	azure location list

Aqui está um exemplo de saída do comando `location list`.

	info:    Executing command location list
	info:    Getting Resource Providers
	data:    Name                                                                Location                                                                                                                                                   
	data:    ------------------------------------------------------------------  -----------------------------------------------------------------------------------------------------------------------------------------------------------
	data:    Microsoft.ApiManagement/service                                     East US,North Central US,South Central US,West US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West,Brazil South                     
	data:    Microsoft.AppService/apiapps                                        East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
	data:    Microsoft.AppService/appIdentities                                  East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
	info:    location list command OK

### Crie ou localize um grupo de recursos para usar

Para criar um grupo de recursos use o comando `group create` com os parâmetros de nome (`n`) e local (`l`).

	azure group create -n <name> -l <location>

Por exemplo:

	azure group create -n "mygroup" -l "West US"

Para localizar um grupo de recursos existente, use o comando `group list` e, em seguida, escolha um grupo de recursos em um local válido para aplicativos de API.

	azure group list

### Crie ou localize um plano de Serviço de Aplicativo para usar

Para criar um plano de Serviço de Aplicativo, use o comando `resource create` e use o parâmetro de tipo de recurso (`-r`) para especificar que o tipo de recurso que você deseja criar é um plano de Serviço de Aplicativo.

	azure resource create -g <resource group> -n <app service plan name> -r "Microsoft.Web/serverfarms" -l <location> -o <api version> -p "{"sku": {"tier": "<pricing tier>"}, "numberOfWorkers" : <number of workers>, "workerSize": "<worker size>"}"
	
Por exemplo:

	azure resource create -g mygroup -n myplan -r "Microsoft.Web/serverfarms" -l "West US" -o "2015-06-01" -p "{"sku": {"tier": "Standard"}, "numberOfWorkers" : 1, "workerSize": "Small"}"

A cadeia de caracteres JSON para o parâmetro `properties`(`-p`) é necessário devido a algumas alterações recentes na API REST; no futuro o parâmetro `-p` será opcional.

O comando de exemplo especifica a última versão de API a partir da data que este artigo foi escrito. Para verificar se está disponível uma versão posterior, use o comando `provider show` e consulte a matriz `apiVersions` para o objeto `sites` na matriz `resourceTypes`.

	azure provider show -n Microsoft.Web --json
   
Aqui está um exemplo do objeto `sites` na saída do comando.

	{
	  "resourceTypes": [
	    {
	      "apiVersions": [
	        "2015-06-01",
	        "2015-05-01",
	        "2015-04-01",
	        "2014-04-01"
	      ],
	      "locations": [
	        "East Asia",
	        "East US",
	        "Japan East",
	        "Japan West",
	        "North Europe",
	        "West Europe",
	        "West US",
	        "Southeast Asia",
	        "Central US",
	        "East US 2"
	      ],
	      "properties": {},
	      "name": "sites"
	    }

Para listar os planos de Serviço de Aplicativo existentes, use o comando `resource list` e especifique o plano de Serviço de Aplicativo como o tipo de recurso usando o parâmetro `-r`.

	azure resource list -r Microsoft.Web/serverfarms

A saída se parece com esta:

	info:    Executing command resource list
	info:    Listing resources
	data:    Id                                                                                                                                           Name             Resource Group          Type                       Parent  Location  Tags
	data:    -------------------------------------------------------------------------------------------------------------------------------------------  ---------------  ----------------------  -------------------------  ------  --------  ----
	data:    /subscriptions/aeb4ae60-b7cb-4f3d-966d-fa43b6607f30/resourceGroups/ContosoAdsGroup/providers/Microsoft.Web/serverFarms/ContosoAdsPlan        ContosoAdsPlan   ContosoAdsGroup         Microsoft.Web/serverFarms          westus    null
	info:    resource list command OK

### Criar um aplicativo vazio de API (personalizado)

Para criar um aplicativo de API vazio (aquele que você irá escrever o código para você mesmo), use o comando `apiapp create` e especificar o pacote do Nuget `Microsoft.ApiApp` (parâmetro `-u`).

	azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u Microsoft.ApiApp

Por exemplo:

	azure apiapp create -g mygroup -n newapiapp -p myplan -u Microsoft.ApiApp

A saída relata o progresso enquanto o aplicativo de API é criado.

	info:    Executing command apiapp create
	info:    Checking resource group and app service plan
	info:    Getting package metadata
	info:    Creating deployment
	info:    Waiting for deployment completion
	info:    Deployment started:
	data:    Subscription Id: aeb4ae60-b7cb-4f3d-966d-fa43b6607f30
	data:    Resource Group:  mygroup
	data:    Deployment Name: AppServiceDeployment_f67fa710-d565-43cf-8c9c-
	                          d515dd2eb903
	data:    Correlation Id:  a7894287-c585-46d5-96a4-feac4b2f48c6
	data:    Timestamp:       2015-07-21T23:20:12.8858274Z
	data:    
	data:    Operation           State         Status        Resource                                          
	data:    ------------------- ------------- ------------- --------------------------------------------------
	data:    E8D88DA720375394    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a
	data:    FC31834D2E73D10E    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/providers/Microsoft.Resources/links/apiApp
	data:    C5B48DAF91306BB4    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/siteextensions/Microsoft.ApiApp
	data:    F4B943428026A1B2    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp
	data:    4B3A935892415369    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp/providers/Microsoft.Resources/links/apiAppSite
	info:    Deployment complete with status: Succeeded
	info:    apiapp create command OK

#### Criar um aplicativo de API do Marketplace

Para obter uma lista de pacotes de aplicativos de API disponíveis no Marketplace, use o comando `group template list` e especifique os aplicativos de API usando o parâmetro da categoria (`-c`).

	azure group template list -c apiapps

A saída se parece com esta:

	info:    Executing command group template list
	info:    Listing gallery resource group templates
	data:    Publisher      Name                                                  
	data:    -------------  ------------------------------------------------------
	data:    Microsoft      Microsoft.Template.1.0.1                              
	data:    microsoft_com  microsoft_com.MicrosoftSharePointConnector.0.2.2      
	data:    microsoft_com  microsoft_com.FTPConnector.0.2.2                      
	info:    group template list command OK

Para criar um aplicativo de API a partir do Marketplace use o comando `apiapap create` e especifique o nome do aplicativo de API que você deseja criar, usando o parâmetro do pacote do NuGet (`-u`).

	azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u <marketplace name>

O valor a ser usado para o parâmetro `-u` é a seção do meio do nome do marketplace. Por exemplo, para microsoft\_com. MicrosoftSqlConnector.0.2.2, o comando é semelhante a:

	azure apiapp create -g mygroup -n mysqlconnector -p myplan -u MicrosoftSqlConnector
	
Se quaisquer parâmetros de modelo de aplicativo de API forem necessários, como nomes de servidor e cadeias de conexão para um conector SQL, os dados necessários serão solicitados. Você tem a opção de usar o `--parameters` ou `--parameters-file` para passar os valores de parâmetro. Para obter mais informações sobre arquivos de parâmetros, consulte [Provisionar um aplicativo de API com um novo gateway](app-service-api-arm-new-gateway-provision.md).

## Próximas etapas

Este artigo mostra como usar os comandos de CLI do Azure individuais para trabalhar com aplicativos de API personalizados ou aplicativos de API que você criar a partir dos modelos do Marketplace. Para obter informações sobre como usar modelos personalizados para automatizar a criação de aplicativos de API, consulte estes recursos:

* [Provisionar um aplicativo de API com um gateway existente](app-service-api-arm-existing-gateway-provision.md)
* [Provisionar um aplicativo de API com um novo gateway](app-service-api-arm-new-gateway-provision.md)

Para obter mais informações sobre como usar os utilitários de linha de comando do Azure com o Gerenciador de recursos do Azure, consulte estes recursos:
 
* [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](../xplat-cli-azure-resource-manager.md).
* [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](../powershell-azure-resource-manager.md)
 

<!---HONumber=06-->