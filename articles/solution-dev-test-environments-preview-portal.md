<properties
   pageTitle="Ambientes de desenvolvimento e teste | Microsoft Azure"
   description="Saiba como usar modelos do Gerenciador de Recursos do Azure para criar e excluir com rapidez e consistência ambientes de desenvolvimento e teste."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="jimdial"
   manager="carolz"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/08/2015"
   ms.author="jdial"/>

# Ambientes de desenvolvimento e teste no Microsoft Azure

Aplicativos personalizados normalmente são implantados para vários ambientes de desenvolvimento e testes antes da implantação para produção. Quando ambientes são criados no local, recursos de computação são adquiridos ou alocados para cada ambiente para cada aplicativo. Os ambientes geralmente incluem vários máquinas físicas ou virtuais com configurações específicas que são implantadas manualmente ou com scripts de automação complexos. As implantações frequentemente levam horas e resultam em configurações inconsistentes entre ambientes.

## Cenário ##

Ao provisionar ambientes de desenvolvimento e teste no Microsoft Azure, você paga apenas pelos recursos que utiliza. Este artigo explica como você pode criar, manter e excluir com rapidez e consistência os ambientes de desenvolvimento e teste usando os modelos e arquivos de parâmetros do Gerenciador de Recursos do Azure, conforme ilustrado abaixo.

![Cenário](./media/solution-dev-test-environments-preview-portal/scenario.png)

Três ambientes de desenvolvimento e testes são mostrados acima. Cada um tem um aplicativo Web e um banco de dados SQL. Os nomes do aplicativo e do banco de dados em cada ambiente são diferentes. Este artigo explica como usar um modelo para implantar o mesmo recurso em ambientes e usar arquivos de parâmetros exclusivos para especificar configurações diferentes para os recursos nos ambientes.

Se você não estiver familiarizado com os conceitos do Gerenciador de Recursos do Azure, é recomendável que leia o artigo [Visão geral do Gerenciador de Recursos do Azure](resource-group-overview.md) antes de ler este artigo.

Convém percorrer primeiro as etapas deste artigo, conforme listado, sem ler qualquer um dos artigos referenciados, para obter rapidamente experiência com o uso de modelos do Gerenciador de Recursos do Azure. Sem dúvida, você terá muitas dúvidas ao percorrer as etapas. Após percorrer as etapas uma vez, você poderá obter respostas para a maioria de suas perguntas continuando a experimentar as etapas e lendo os artigos referenciados.

## Planejar o uso de recursos do Azure
Quando tiver um design de alto nível para seu aplicativo, você poderá definir:

- Quais recursos do Azure seu aplicativo incluirá. Você pode criar seu aplicativo e implantá-lo como um aplicativo Web do Azure com um banco de dados SQL do Azure. Você pode criar seu aplicativo em máquinas virtuais usando PHP e MySQL ou IIS e SQL Server ou outros componentes. O artigo [Comparação do Serviço de Aplicativo do Azure, serviços de nuvem e máquinas virtuais](app-service-web/choose-web-site-cloud-service-vm.md) o ajuda a decidir quais recursos do Azure pode desejar usar para seu aplicativo.
- A quais requisitos de nível de serviço, como disponibilidade, segurança e escala, seu aplicativo atenderá.

## Usando modelos do Gerenciador de Recursos do Azure
Um modelo do Gerenciador de Recursos do Azure define todos os recursos do Azure que seu aplicativo utiliza. Já existem vários modelos que você pode implantar diretamente no Portal de Visualização do Azure ou baixar, modificar e salvar em um sistema de controle de origem com o código do aplicativo. É possível que exista um modelo que inclui os recursos que você deseja usar com o aplicativo. Você pode encontrar uma lista de modelos disponíveis no repositório GitHub de [Modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/).

Na lista, você verá uma pasta "[201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)". Como muitos aplicativos personalizados incluem um aplicativo Web e o banco de dados SQL, esse modelo é usado como um exemplo no restante deste artigo para ajudá-lo a entender como usar modelos. Está fora do escopo deste artigo explicar tudo o que esse modelo cria e configura, mas se você planeja usá-lo para criar ambientes reais em sua organização, convém entendê-lo plenamente lendo o artigo [Provisionar um aplicativo Web com um banco de dados SQL](app-service-web/app-service-web-arm-with-sql-database-provision.md).

> [AZURE.NOTE] Você pode implantar o modelo no Azure diretamente clicando no botão "Implantar no Azure" no artigo [Provisionar um aplicativo Web com um banco de dados SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/). Esse procedimento poderá ser útil para saber mais sobre modelos, mas isso não permite editar, controlar a versão e salvar os valores de parâmetro e modelo com o código do aplicativo. As etapas neste artigo explicam como você pode salvar e controlar a versão para seus valores de parâmetro e modelo com o código do aplicativo.

  **Etapa 1:** exiba o conteúdo do arquivo [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.json) na pasta 201-web-app-sql-database. Esse é o arquivo de modelo do Gerenciador de Recursos do Azure. No modo de exibição, clique no botão "[Bruto](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy.json)". Com o mouse, selecione todo o conteúdo desse arquivo e salve-o no computador como um arquivo chamado "TestApp1-Template.json".

No arquivo de modelo, você verá uma seção "recursos", que define os recursos do Azure criados pelo modelo. Entre outros tipos de recursos, o modelo cria recursos de [Aplicativo Web do Azure](app-service-web/app-service-web-overview.md) e [Banco de Dados SQL do Azure](sql-database/sql-database-technical-overview.md).

Você também verá uma seção "parâmetros", que define os parâmetros com os quais cada recurso pode ser configurado. Alguns dos parâmetros especificados no modelo têm propriedades "defaultValue", enquanto outros não as têm. Ao implantar recursos do Azure com um modelo, você deve fornecer valores para todos os parâmetros que não têm propriedades defaultValue. Se você não fornecer valores para parâmetros com propriedades defaultValue, será usado o valor especificado para o parâmetro defaultValue no modelo.

Um modelo define quais recursos do Azure são criados e os parâmetros com os quais cada recurso pode ser configurado. Normalmente, convém criar os mesmos recursos em cada ambiente de desenvolvimento e teste. Você pode saber mais sobre modelos e como criar seus próprios lendo o artigo [Práticas recomendadas criar modelos do Gerenciador de Recursos do Azure](best-practices-resource-manager-design-templates.md).

## Criar arquivos de parâmetros

Você provavelmente desejará ter os mesmos recursos do Azure criados em cada ambiente, mas pode querer que a configuração dos recursos seja diferente em cada ambiente. É aí que entram os arquivos de parâmetros.

  **Etapa 2:** exiba o conteúdo do arquivo [azuredeploy-parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy-parameters.json) na pasta 201-web-app-sql-database. Esse é o arquivo de parâmetros para o arquivo de modelo que você salvou na etapa 1. No modo de exibição, clique no botão "[Bruto](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy-parameters.json)". Com o mouse, selecione todo o conteúdo desse arquivo e salve-o em três arquivos separados no computador, com os seguintes nomes:

- TestApp1-Parameters-Development.json
- TestApp1-Parameters-Test.json
- TestApp1-Parameters-Pre-Production.json

  **Etapa 3:** usando qualquer editor de texto ou JSON, edite os arquivos de parâmetros dos ambientes de desenvolvimento e teste criados na etapa 2, substituindo os valores existentes nos arquivos pelos valores a seguir.

  --TestApp1-Parameters-Development.json--

| Parâmetro | Descrição |
|---|---|
| **siteName** | TestApp1Dev |
| **hostingPlanName** | TestApp1PlanDev |
| **siteLocation** | Centro dos EUA |
| **serverName** | testapp1dev |
| **serverLocation** | Centro dos EUA |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1dev |

--TestApp1-Parameters-Test.json--

| Parâmetro | Descrição |
|---|---|
| **siteName** | TestApp1Test |
| **hostingPlanName** | TestApp1PlanTest |
| **siteLocation** | Centro dos EUA |
| **serverName** | testapp1test |
| **serverLocation** | Centro dos EUA |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1test |

Na etapa 6, esses arquivos de parâmetros serão usados para criar configurações exclusivas para os recursos Aplicativo Web do Azure e Banco de Dados SQL do Azure nos ambientes de desenvolvimento e testes.

 **Etapa 4:** edite o arquivo de parâmetros TestApp1-Parameters-Pre-Production.json criado na etapa 2. Substitua todo o conteúdo do arquivo pelos itens abaixo:

	{
	  "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
	  "contentVersion" : "1.0.0.0",
	  "parameters" : {
	    "administratorLogin" : {
	      "value" : "testApp1Admin"
	    },
	    "administratorLoginPassword" : {
	      "value" : "testApp1XP49"
	    },
	    "databaseName" : {
	      "value" : "testapp1preprod"
	    },
	    "hostingPlanName" : {
	      "value" : "TestApp1PlanPreProd"
	    },
	    "serverLocation" : {
	      "value" : "Central US"
	    },
	    "serverName" : {
	      "value" : "testapp1preprod"
	    },
	    "siteLocation" : {
	      "value" : "Central US"
	    },
	    "siteName" : {
	      "value" : "TestApp1PreProd"
	    },
	    "sku" : {
	      "value" : "Standard"
	    },
		"requestedServiceObjectiveName" : {
		  "value" : "S1"
	    }
	  }
	}

No arquivo de parâmetros de pré-produção acima, os parâmetros **sku** e **requestedServiceObjectiveName** foram *adicionados*, enquanto não foram adicionados nos arquivos de parâmetros de desenvolvimento e testes. Isso ocorre porque há valores padrão especificados para esses parâmetros no modelo e, em ambientes de desenvolvimento e testes, os valores padrão são usados, mas no ambiente de pré-produção, são usados valores não padrão para esses parâmetros.

O motivo pelo qual valores não padrão são usados para esses parâmetros no ambiente de pré-produção é testar valores para esses parâmetros que talvez você prefira para seu ambiente de produção. Esses parâmetros se relacionam aos [planos de hospedagem de aplicativos Web](https://azure.microsoft.com/pricing/details/app-service/) do Azure, à **sku** e ao [banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/) do Azure ou ao **requestedServiceObjectiveName** que são usados pelo aplicativo. Skus e nomes de objetivo de serviço diferentes têm custos e recursos diferentes e dão suporte a métricas de nível de serviço diferentes.

A tabela a seguir lista os valores padrão para os parâmetros especificados no modelo e os valores que são usados em vez dos valores padrão no arquivo de parâmetros de pré-produção.

| Parâmetro | Valor padrão | Valor do arquivo de parâmetros |
|---|---|---|
| **sku** | Grátis | Standard |
| **requestedServiceObjectiveName** | S0 | S1 |

## Criar ambientes
Todos os recursos do Azure devem ser criados em um [grupo de recursos do Azure](azure-portal/resource-group-portal.md). Grupos de recursos o habilitam a agrupar recursos do Azure para que eles possam ser gerenciados coletivamente. [Permissões](./active-directory/role-based-access-control-configure.md) podem ser atribuídas a grupos de recursos, para que pessoas específicas em sua organização possam criar, modificar, excluir ou exibi-los, juntamente com os recursos neles. Alertas e informações de cobrança de recursos no grupo de recursos podem ser exibidos no [Portal de Visualização do Azure](https://portal.azure.com). Grupos de recursos são criados em um [local](https://azure.microsoft.com/regions/) do Azure. Neste artigo, todos os recursos são criados no local Centro dos Estados Unidos. Ao começar a criar ambientes reais, você poderá escolher o local que melhor atende às suas necessidades.

  **Etapa 5:** crie grupos de recursos para os ambientes de desenvolvimento e testes usando um dos métodos a seguir. Ambos os métodos obterão exatamente o mesmo resultado.

  **Método 1:** CLI (interface de linha de comando) do Azure

  Verifique se você tem a CLI [instalada](xplat-cli-install.md) em um computador com Windows, OS X ou Linux e se você [conectou](xplat-cli-connect.md) sua ID organizacional à sua assinatura do Azure. Na linha de comando da CLI, digite o comando a seguir para criar o grupo de recursos para o ambiente de desenvolvimento.

	azure group create "TestApp1-Development" "Central US"

  O comando retornará o seguinte se tiver êxito:

	info:    Executing command group create
	+ Getting resource group TestApp1-Development
	+ Creating resource group TestApp1-Development
	info:    Created resource group TestApp1-Development
	data:    Id:                  /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development
	data:    Name:                TestApp1-Development
	data:    Location:            centralus
	data:    Provisioning State:  Succeeded
	data:    Tags: null
	data:
	info:    group create command OK

  Para criar o grupo de recursos para o ambiente de testes, digite o seguinte comando:

	azure group create "TestApp1-Test" "Central US"

  Para criar o grupo de recursos para o ambiente de pré-produção, digite o seguinte comando:

	azure group create "TestApp1-Pre-Production" "Central US"

  **Método 2:** PowerShell

  Verifique se você tem o PowerShell instalado em um computador com o Windows e conectado à sua assinatura, conforme detalhado no artigo [Como instalar e configurar o Azure PowerShell](powershell-install-configure.md). Em um prompt de comando do PowerShell, digite o comando a seguir para criar o grupo de recursos no ambiente de desenvolvimento. Se estiver usando o Azure PowerShell 1.0 Preview, o comando será **New-AzureRmResourceGroup**, como mostrado abaixo. Se estiver usando uma versão do Azure PowerShell anterior ao 1.0 Preview, o comando será **New-AzureResourceGroup**.

	New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

  O comando retornará o seguinte se tiver êxito:

	WARNING: The output object of this cmdlet will be modified in a future release.


	ResourceGroupName : TestApp1-Development
	Location          : centralus
	ProvisioningState : Succeeded
	Tags              : 
	Permissions       : 
	                    Actions  NotActions
	                    =======  ==========
	                    *                  
	                    
	ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

  Para criar o grupo de recursos para o ambiente de testes, digite o seguinte comando:

	New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

  Para criar o grupo de recursos para o ambiente de pré-produção, digite o seguinte comando:

	New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

 **Etapa 6:** implante recursos do Azure nos grupos de recursos de cada ambiente usando o arquivo de modelo do aplicativo e os arquivos de parâmetro de cada ambiente, com um dos métodos a seguir. Ambos os métodos obterão exatamente o mesmo resultado.

  **Método 1:** CLI (Interface de Linha de Comando) do Azure

  Na linha de comando da CLI, digite o comando a seguir para implantar recursos no grupo de recursos criado para o ambiente de desenvolvimento, substituindo [path] pelo caminho para os arquivos que você salvou nas etapas anteriores.

	azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

  O comando retornará o seguinte se tiver êxito:

	info:    Executing command group deployment create
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "Deployment1"
	+ Waiting for deployment to complete
	data:    DeploymentName     : Deployment1
	data:    ResourceGroupName  : TestApp1-Development
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : XXXX-XX-XXT20:20:23.5202316Z
	data:    Mode               : Incremental
	data:    Name                           Type          Value
	data:    -----------------------------  ------------  ----------------------------
	data:    siteName                       String        TestApp1Dev
	data:    hostingPlanName                String        TestApp1PlanDev
	data:    siteLocation                   String        Central US
	data:    sku                            String        Free
	data:    workerSize                     String        0
	data:    serverName                     String        testapp1dev
	data:    serverLocation                 String        Central US
	data:    administratorLogin             String        testapp1Admin
	data:    administratorLoginPassword     SecureString  undefined
	data:    databaseName                   String        testapp1dev
	data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
	data:    edition                        String        Standard
	data:    maxSizeBytes                   String        1073741824
	data:    requestedServiceObjectiveName  String        S0
	info:    group deployment create command OKx

  Se o comando não for bem-sucedido, resolva as mensagens de erro e tente novamente. Problemas comuns são o uso de valores de parâmetros que não obedecem às restrições de nomenclatura de recursos do Azure. Outras dicas de solução de problemas podem ser encontradas no artigo [Solucionando problemas de implantações de grupos de recursos no Azure](virtual-machines/resource-group-deploy-debug.md).

  Na linha de comando da CLI, digite o comando a seguir para implantar recursos no grupo de recursos criado para o ambiente de testes, substituindo [path] pelo caminho para os arquivos que você salvou nas etapas anteriores.

	azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

  Na linha de comando da CLI, digite o comando a seguir para implantar recursos no grupo de recursos criado para o ambiente de pré-produção, substituindo [path] pelo caminho para os arquivos que você salvou nas etapas anteriores.

	azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
  **Método 2:** PowerShell

  Em um prompt de comando do PowerShell, digite o comando a seguir para implantar recursos no grupo de recursos criado para o ambiente de desenvolvimento, substituindo [path] pelo caminho para os arquivos que você salvou nas etapas anteriores. Se estiver usando o Azure PowerShell 1.0 Preview, o comando será **New-AzureRmResourceGroupDeployment**, como mostrado abaixo. Se estiver usando uma versão do Azure PowerShell anterior ao 1.0 Preview, o comando será **New-AzureResourceGroupDeployment**.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

  O comando retornará o seguinte se tiver êxito:

	DeploymentName    : Deployment1
	ResourceGroupName : TestApp1-Development
	ProvisioningState : Succeeded
	Timestamp         : XX/XX/XXXX 2:44:48 PM
	Mode              : Incremental
	TemplateLink      : 
	Parameters        : 
	                    Name             Type                       Value     
	                    ===============  =========================  ==========
	                    siteName         String                     TestApp1Dev
	                    hostingPlanName  String                     TestApp1PlanDev
	                    siteLocation     String                     Central US
	                    sku              String                     Free      
	                    workerSize       String                     0         
	                    serverName       String                     testapp1dev
	                    serverLocation   String                     Central US
	                    administratorLogin  String                     testapp1Admin
	                    administratorLoginPassword  SecureString                         
	                    databaseName     String                     testapp1dev
	                    collation        String                     SQL_Latin1_General_CP1_CI_AS
	                    edition          String                     Standard  
	                    maxSizeBytes     String                     1073741824
	                    requestedServiceObjectiveName  String                     S0        
	                    
	Outputs           :

  Se o comando não for bem-sucedido, resolva as mensagens de erro e tente novamente. Problemas comuns são o uso de valores de parâmetros que não obedecem às restrições de nomenclatura de recursos do Azure. Outras dicas de solução de problemas podem ser encontradas no artigo [Solucionando problemas de implantações de grupos de recursos no Azure](virtual-machines/resource-group-deploy-debug.md).

  Em um prompt de comando do PowerShell, digite o comando a seguir para implantar recursos no grupo de recursos criado para o ambiente de testes, substituindo [path] pelo caminho para os arquivos que você salvou nas etapas anteriores.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  Em um prompt de comando do PowerShell, digite o comando a seguir para implantar recursos no grupo de recursos criado para o ambiente de pré-produção, substituindo [path] pelo caminho para os arquivos que você salvou nas etapas anteriores.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

Os arquivos de modelos e parâmetros podem ter o controle de versão e ser mantidos com o código do aplicativo e em um sistema de controle de origem. Você também pode salvar os comandos anteriores em arquivos de script e salvá-los com seu código.

## Manter ambientes
Durante o desenvolvimento, a configuração dos recursos do Azure em diferentes ambientes pode ser alterada inconsistentemente de forma intencional ou acidental. Isso pode causar problemas desnecessários e resolução de problemas durante o ciclo de desenvolvimento do aplicativo.

  **Etapa 7:** altere os ambientes. Abra o [Portal de Visualização do Azure](https://portal.azure.com) e entre nele com a mesma conta que você usou para concluir as etapas acima. Conforme mostrado na figura abaixo, clique em Procurar Tudo --> Grupos de Recursos (talvez seja necessário rolar para baixo na folha Procurar para ver os grupos de recursos). Você verá todos os três grupos de recursos criados usando um dos métodos das etapas anteriores. Clique no grupo de recursos TestApp1-Development, conforme mostrado abaixo.

  ![Portal](./media/solution-dev-test-environments-preview-portal/portal1.png)

  Depois de clicar no grupo de recursos TestApp1-Development, você verá a folha que lista os recursos criados pelo modelo na implantação de grupo de recursos concluída em uma etapa anterior. Exclua o recurso de aplicativo Web TestApp1Dev clicando em TestApp1Dev-->Excluir, conforme mostrado abaixo.

  ![Portal](./media/solution-dev-test-environments-preview-portal/portal2.png)

  Clique em "Sim" quando o portal exibir uma mensagem perguntando se você tem certeza de que deseja excluir o recurso. O conteúdo do grupo de recursos agora é diferente do que deveria ser. Além disso, você pode experimentar excluir vários recursos de vários grupos de recursos ou até mesmo alterar definições de configuração para alguns dos recursos.

> [AZURE.NOTE] Em vez de usar o Portal de Visualização do Azure para excluir um recurso de um grupo de recursos, você pode usar o comando [Remove-AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) do PowerShell ou o comando “azure resource delete” da CLI para realizar a mesma tarefa.

  **Etapa 8:** implante novamente os ambientes nos grupos de recursos usando os mesmos comandos que você usou na Etapa 6, mas substitua “Deployment1” por “Deployment2”. Conforme mostrado na seção Resumo da imagem abaixo, você verá que todos os recursos do modelo existem no grupo de recursos TestApp1-Development novamente. Uma das vantagens da implantação de seus ambientes com modelos do Gerenciador de Recursos do Azure é que você pode facilmente reimplantar os ambientes de volta para um estado conhecido a qualquer momento.

  ![Portal](./media/solution-dev-test-environments-preview-portal/portal3.png)

  Se clicar no texto em "Última implantação" na imagem, você verá uma folha que mostra o histórico de implantação para o grupo de recursos. Como usou o nome "Deployment1" para a primeira implantação e "Deployment2" para a segunda, você terá duas entradas. Clicar em uma implantação exibirá uma folha que mostra os resultados para cada implantação.

## Excluir ambientes
Quando tiver concluído o trabalho em um ambiente, convém excluí-lo para não incorrer em cobranças de uso de recursos do Azure que você não está usando. Excluir ambientes é ainda mais fácil do que criá-los. Nas etapas anteriores, grupos de recursos individuais do Azure foram criados para cada ambiente. Quando você exclui um grupo de recursos, todos os recursos que ele contém também são excluídos. Tendo isso em mente, um dos métodos a seguir excluirá os ambientes (grupos de recursos), junto com todos os recursos do Azure dentro deles que você implantou anteriormente.

  **Etapa 9:** exclua os ambientes usando um dos métodos abaixo. Ambos os métodos obterão exatamente o mesmo resultado.

  **Método 1: CLI do Azure**

  Em um prompt da CLI, digite o seguinte:

	azure group delete "TestApp1-Development"

  O comando retornará o seguinte se você digitar "y" quando for solicitado:

	info:    Executing command group delete
	Delete resource group TestApp1-Development? [y/n] y
	+ Deleting resource group TestApp1-Development
	info:    group delete command OK

  Em um prompt da CLI, digite o seguinte para excluir os ambientes restantes:

	azure group delete "TestApp1-Test"
	azure group delete "TestApp1-Pre-Production"
  
  **Método 2:** PowerShell

  Se estiver usando o Azure PowerShell 1.0 Preview, o comando para excluir o grupo de recursos será **Remove-AzureRmResourceGroup**, como mostrado abaixo. Se estiver usando uma versão do Azure PowerShell anterior ao 1.0 Preview, o comando será **Remove-AzureResourceGroup**. Em um prompt do PowerShell, digite o seguinte:

	Remove-AzureRmResourceGroup -Name TestApp1-Development

  O comando retornará o seguinte se você digitar "y" quando for solicitado:

	Confirm
	Are you sure you want to remove resource group 'TestApp1-Development'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

  Em um prompt do PowerShell, digite o seguinte para excluir os ambientes restantes:

	Remove-AzureRmResourceGroup -Name TestApp1-Test
	Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

Independentemente do método que você usar, depois de concluir a execução de comandos, os grupos de recursos e todos os recursos que eles continham deixarão de existir, e você não incorrerá em despesas de cobrança para os recursos.

Para minimizar as despesas de utilização de recursos do Azure incorridas durante o desenvolvimento de aplicativos, você pode usar a [Automação do Azure](automation/automation-intro.md) para agendar trabalhos que:

- Param máquinas virtuais ao fim de cada dia e as reiniciam no início de cada dia.
- Excluir ambientes inteiros ao fim de cada dia e recriá-los no início de cada dia.
 
Agora que viu como é fácil criar, manter e excluir os ambientes de desenvolvimento e teste, você pode aprender mais sobre o que acabou de fazer experimentando as etapas acima e lendo as referências contidas neste artigo.

## Próximas etapas

- [Delegar controle administrativo](role-based-access-control-configure.md) para recursos diferentes em cada ambiente atribuindo usuários ou grupos do AD do Microsoft Azure a funções específicas que têm a capacidade de executar um subconjunto de operações nos recursos do Azure.
- [Atribua marcas](resource-group-using-tags.md) aos grupos de recursos de cada ambiente e/ou aos recursos individuais. Você pode adicionar uma marca de "Ambiente" aos grupos de recursos e definir seu valor para corresponder aos nomes de seu ambiente. As marcas podem ser particularmente úteis quando você precisar organizar os recursos de gerenciamento ou de cobrança.
- Monitore alertas e cobrança de recursos do grupo de recursos no [Portal de Visualização do Azure](https://portal.azure.com).

## Recursos adicionais

- [Criar e implantar modelos do Gerenciador de Recursos do Azure no Visual Studio](http://msdn.microsoft.com/library/azure/Dn872471.aspx) com o SDK 2.6 do Azure instalado.
- Crie seu aplicativo usando o [Visual Studio Enterprise](https://www.visualstudio.com/products/visual-studio-enterprise-vs), o [Código do Visual Studio](http://www.visualstudio.com/products/code-vs) ou a [Matriz da Web](http://www.microsoft.com/web/webmatrix/).
- [Implantar um aplicativo Web](app-service-web/web-sites-deploy.md) para os ambientes que você criou.
- Use o [Release Management para Visual Studio](http://msdn.microsoft.com/Library/vs/alm/Release/overview) para criar pipelines de implantação gerenciados e contínuos, a fim de liberar com rapidez, facilidade e frequência.
- Solicite um convite para a visualização do [Laboratório de Desenvolvimento/Teste do Azure](https://azure.microsoft.com/campaigns/devtest-lab/). Ele o habilita a gerenciar ambientes de laboratório de desenvolvimento e teste usando modelos e configurar cotas e políticas para uso em sua organização.

<!---HONumber=AcomDC_0128_2016-->