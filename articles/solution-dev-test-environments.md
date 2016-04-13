<properties
   pageTitle="Ambientes de desenvolvimento e teste | Microsoft Azure"
   description="Saiba como usar modelos do Gerenciador de Recursos do Azure para criar e excluir com rapidez e consistência ambientes de desenvolvimento e teste."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/22/2016"
   ms.author="jdial"/>

# Ambientes de desenvolvimento e teste no Microsoft Azure

Aplicativos personalizados normalmente são implantados para vários ambientes de desenvolvimento e testes antes da implantação para produção. Quando ambientes são criados no local, recursos de computação são adquiridos ou alocados para cada ambiente para cada aplicativo. Os ambientes geralmente incluem vários máquinas físicas ou virtuais com configurações específicas que são implantadas manualmente ou com scripts de automação complexos. As implantações frequentemente levam horas e resultam em configurações inconsistentes entre ambientes.

## Cenário ##

Ao provisionar ambientes de desenvolvimento e teste no Microsoft Azure, você paga apenas pelos recursos que utiliza. Este artigo explica como você pode criar, manter e excluir com rapidez e consistência os ambientes de desenvolvimento e teste usando os modelos e arquivos de parâmetros do Gerenciador de Recursos do Azure, conforme ilustrado abaixo.

![Cenário](./media/solution-dev-test-environments/scenario.png)

Três ambientes de desenvolvimento e testes são mostrados acima. Cada um tem recursos de aplicativo Web e de Banco de Dados SQL que são especificados em um arquivo de modelo. Os nomes do aplicativo e do banco de dados em cada ambiente são diferentes e são especificados em arquivos de parâmetro exclusivos para cada ambiente.

Se você não estiver familiarizado com os conceitos do Gerenciador de Recursos do Azure, é recomendável que leia o artigo [Visão geral do Gerenciador de Recursos do Azure](resource-group-overview.md) antes de ler este artigo.

Convém percorrer primeiro as etapas deste artigo, conforme listado, sem ler qualquer um dos artigos referenciados, para obter rapidamente experiência com o uso de modelos do Gerenciador de Recursos do Azure. Após percorrer as etapas uma vez, você poderá obter respostas para a maioria das perguntas que surgiram continuando a experimentar as etapas e lendo os artigos citados.

## Planejar o uso de recursos do Azure
Quando tiver um design de alto nível para seu aplicativo, você poderá definir:

- Quais recursos do Azure seu aplicativo incluirá. Você pode criar seu aplicativo e implantá-lo como um aplicativo Web do Azure com um banco de dados SQL do Azure. Você pode criar seu aplicativo em máquinas virtuais usando PHP e MySQL ou IIS e SQL Server ou outros componentes. O artigo [Comparação do Serviço de Aplicativo do Azure, serviços de nuvem e máquinas virtuais](app-service-web/choose-web-site-cloud-service-vm.md) o ajuda a decidir quais recursos do Azure pode desejar usar para seu aplicativo.
- A quais requisitos de nível de serviço, como disponibilidade, segurança e escala, seu aplicativo atenderá.

## Baixar um modelo existente
Um modelo do Gerenciador de Recursos do Azure define todos os recursos do Azure que seu aplicativo utiliza. Já existem vários modelos que você pode implantar diretamente no Portal do Azure ou baixar, modificar e salvar em um sistema de controle de origem com o código do aplicativo. Conclua as etapas abaixo para baixar um modelo existente.

1. Pesquise nos modelos existentes em [Modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/) no repositório GitHub. Na lista, você verá uma pasta "[201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)". Como muitos aplicativos personalizados incluem um aplicativo Web e o banco de dados SQL, esse modelo é usado como um exemplo no restante deste artigo para ajudá-lo a entender como usar modelos. Está fora do escopo deste artigo explicar tudo o que esse modelo cria e configura, mas se você planeja usá-lo para criar ambientes reais em sua organização, convém entendê-lo plenamente lendo o artigo [Provisionar um aplicativo Web com um banco de dados SQL](app-service-web/app-service-web-arm-with-sql-database-provision.md).
2. Clique no arquivo [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.json) na pasta 201-web-app-sql-database para seu conteúdo. Esse é o arquivo de modelo do Gerenciador de Recursos do Azure. 
3. No modo de exibição, clique no botão "[Bruto](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy.json)". 
4. Com o mouse, selecione o conteúdo deste arquivo e salve-o no computador como um arquivo chamado "TestApp1-Template.json". 
5. Examine o conteúdo do modelo e observe o seguinte:
 - Seção **Recursos**: esta seção define os tipos de recursos do Azure criados por este modelo. Entre outros tipos de recursos, o modelo cria recursos de [Aplicativo Web do Azure](app-service-web/app-service-web-overview.md) e [Banco de Dados SQL do Azure](sql-database/sql-database-technical-overview.md). Se você preferir executar e gerenciar servidores Web e SQL em máquinas virtuais, você poderá usar os modelos "[iis-2vm-sql-1vm](https://github.com/Azure/azure-quickstart-templates/tree/master/iis-2vm-sql-1vm)"ou"[lamp-app](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app)", mas as instruções neste artigo se baseiam no modelo [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database).
 - Seção **Parâmetros**: esta seção define os parâmetros com os quais cada recurso pode ser configurado. Alguns dos parâmetros especificados no modelo têm propriedades "defaultValue", enquanto outros não as têm. Ao implantar os recursos do Azure com um modelo, você deve fornecer valores para todos os parâmetros que não têm as propriedades defaultValue especificadas no modelo. Se você não fornecer valores para parâmetros com propriedades defaultValue, será usado o valor especificado para o parâmetro defaultValue no modelo.

Um modelo define quais recursos do Azure são criados e os parâmetros com os quais cada recurso pode ser configurado. Você pode saber mais sobre modelos e como criar seus próprios lendo o artigo [Práticas recomendadas criar modelos do Gerenciador de Recursos do Azure](best-practices-resource-manager-design-templates.md).

## Baixar e personalizar um arquivo de parâmetro existente

Apesar de você provavelmente querer ter os *mesmos* recursos do Azure criados em cada ambiente, é possível que também queira que a configuração dos recursos seja *diferente* em cada ambiente. É aí que entram os arquivos de parâmetros. Crie arquivos de parâmetro contendo valores exclusivos em cada ambiente usando as etapas abaixo.

1. Veja o conteúdo do arquivo [azuredeploy-parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.parameters.json) na pasta 201-web-app-sql-database. Esse é o arquivo de parâmetros para o arquivo de modelo que você salvou na etapa anterior. 
2. No modo de exibição, clique no botão "[Bruto](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy.parameters.json)". 
3. Com o mouse, selecione o conteúdo deste arquivo e salve-o em três arquivos separados no computador, com os seguintes nomes:
 - TestApp1-Parameters-Development.json
 - TestApp1-Parameters-Test.json
 - TestApp1-Parameters-Pre-Production.json

3. Usando qualquer editor de texto ou JSON, edite o arquivo de parâmetros do ambiente de Desenvolvimento criado na Etapa 3, substituindo os valores listados à direita dos valores de parâmetro no arquivo pelos *valores* listados à direita dos **parâmetros** abaixo: 
 - **siteName**: *TestApp1DevApp*
 - **hostingPlanName**: *TestApp1DevPlan*
 - **siteLocation**: *EUA Central*
 - **serverName**: *testapp1devsrv*
 - **serverLocation**: *EUA Central*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *substitua pela sua senha*
 - **databaseName**: *testapp1devdb*

4. Usando qualquer editor de texto ou JSON, edite o arquivo de parâmetros do ambiente de Teste criado na Etapa 3, substituindo os valores listados à direita dos valores de parâmetro no arquivo pelos *valores* listados à direita dos **parâmetros** abaixo:
 - **siteName**: *TestApp1TestApp*
 - **hostingPlanName**: *TestApp1TestPla* 
 - **siteLocation**: *EUA Central*
 - **serverName**: *testapp1testsrv*
 - **serverLocation**: *EUA Central*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *substitua pela sua senha*
 - **databaseName**: *testapp1testdb*

5. Usando qualquer editor de texto ou JSON, edite o arquivo de parâmetro de Pré-produção que você criou na Etapa 3. Substitua todo o conteúdo do arquivo pelos itens abaixo:

	    {
    	  "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    	  "contentVersion" : "1.0.0.0",
    	  "parameters" : {
    	"administratorLogin" : {
    	  "value" : "testApp1Admin"
    	},
    	"administratorLoginPassword" : {
    	  "value" : "replace with your password"
    	},
    	"databaseName" : {
    	  "value" : "testapp1preproddb"
    	},
    	"hostingPlanName" : {
    	  "value" : "TestApp1PreProdPlan"
    	},
    	"serverLocation" : {
    	  "value" : "Central US"
    	},
    	"serverName" : {
    	  "value" : "testapp1preprodsrv"
    	},
    	"siteLocation" : {
    	  "value" : "Central US"
    	},
    	"siteName" : {
    	  "value" : "TestApp1PreProdApp"
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

O motivo pelo qual valores não padrão são usados para esses parâmetros no ambiente de Pré-produção é testar valores para esses parâmetros que talvez você prefira para seu ambiente de Produção, para que eles também possam ser testados. Esses parâmetros se relacionam aos [planos de hospedagem de aplicativos Web](https://azure.microsoft.com/pricing/details/app-service/) do Azure, à **sku** e ao [banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/) do Azure ou ao **requestedServiceObjectiveName** que são usados pelo aplicativo. Skus e nomes de objetivo de serviço diferentes têm custos e recursos diferentes e dão suporte a métricas de nível de serviço diferentes.

A tabela a seguir lista os valores padrão para os parâmetros especificados no modelo e os valores que são usados em vez dos valores padrão no arquivo de parâmetros de pré-produção.

| Parâmetro | Valor padrão | Valor do arquivo de parâmetros |
|---|---|---|
| **sku** | Grátis | Standard |
| **requestedServiceObjectiveName** | S0 | S1 |

## Criar ambientes
Todos os recursos do Azure devem ser criados em um [grupo de recursos do Azure](./azure-portal/resource-group-portal.md). Grupos de recursos o habilitam a agrupar recursos do Azure para que eles possam ser gerenciados coletivamente. [Permissões](./active-directory/role-based-access-built-in-roles.md) podem ser atribuídas a grupos de recursos, para que pessoas específicas em sua organização possam criar, modificar, excluir ou exibi-los, juntamente com os recursos neles. Alertas e informações de cobrança de recursos no grupo de recursos podem ser exibidos no [Portal do Azure](https://portal.azure.com). Grupos de recursos são criados em uma [região](https://azure.microsoft.com/regions/) do Azure. Neste artigo, todos os recursos são criados na região EUA Central. Ao começar a criar ambientes reais, você poderá escolher a região que melhor atende às suas necessidades.

Crie grupos de recursos para cada ambiente usando qualquer um dos métodos abaixo. Todos os métodos obterão o mesmo resultado.

###CLI (Interface de Linha de Comando) do Azure.

Verifique se você tem a CLI [instalada](xplat-cli-install.md) em um computador com Windows, OS X ou Linux e se você [conectou](xplat-cli-connect.md) sua [conta do Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (também chamada de conta corporativa ou de estudante) à sua assinatura do Azure. Na linha de comando da CLI, digite o comando a seguir para criar o grupo de recursos para o ambiente de desenvolvimento.

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

###PowerShell

Verifique se você tem o Azure PowerShell 1.01 ou superior instalado em um computador com Windows e se conectou sua [conta do Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (também chamada de conta corporativa ou de estudante) à sua assinatura, conforme detalhado no artigo [Como instalar e configurar o Azure PowerShell](powershell-install-configure.md). Em um prompt de comando do PowerShell, digite o comando a seguir para criar o grupo de recursos no ambiente de desenvolvimento.

	New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

O comando retornará o seguinte se tiver êxito:

	ResourceGroupName : TestApp1-Development
	Location          : centralus
	ProvisioningState : Succeeded
	Tags              :
	ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

Para criar o grupo de recursos para o ambiente de testes, digite o seguinte comando:

	New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

Para criar o grupo de recursos para o ambiente de pré-produção, digite o seguinte comando:

	New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

###Portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta do [Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (também chamada de corporativa ou de estudante). Clique em Novo-->Gerenciamento--> Grupo de recursos e insira "TestApp1-Development" na caixa de nome do Grupo de recursos, escolha sua assinatura e selecione "EUA Central" na caixa de local do Grupo de recursos, conforme mostra a imagem abaixo. ![Portal](./media/solution-dev-test-environments/rgcreate.png)
2. Clique no botão Criar para criar o grupo de recursos.
3. Clique em Procurar, role a lista para baixo até Grupos de recursos e clique em Grupos de recursos, conforme exibido abaixo. ![Portal](./media/solution-dev-test-environments/rgbrowse.png) 
4. Depois de clicar em Grupos de recursos, você verá a folha Grupos de recursos com seu novo grupo de recursos. ![Portal](./media/solution-dev-test-environments/rgview.png)
5. Crie os grupos de recursos TestApp1-Test e TestApp1-Pre-Production da mesma maneira que criou o grupo de recursos TestApp1-Development acima.

##Implantar recursos nos ambientes

Implante os recursos do Azure nos grupos de recursos de cada ambiente usando o arquivo de modelo da solução e dos arquivos de parâmetro de cada ambiente usando um dos métodos a seguir. Os dois métodos obterão o mesmo resultado.

###CLI (Interface de Linha de Comando) do Azure.

Na linha de comando da CLI, digite o comando a seguir para implantar recursos no grupo de recursos criado para o ambiente de desenvolvimento, substituindo [path] pelo caminho para os arquivos que você salvou nas etapas anteriores.

	azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

Após ver uma mensagem de "Aguardando conclusão da implantação" por alguns minutos, o comando retornará o seguinte se tiver êxito:

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
	data:    siteName                       String        TestApp1DevApp
	data:    hostingPlanName                String        TestApp1DevPlan
	data:    siteLocation                   String        Central US
	data:    sku                            String        Free
	data:    workerSize                     String        0
	data:    serverName                     String        testapp1devsrv
	data:    serverLocation                 String        Central US
	data:    administratorLogin             String        testapp1Admin
	data:    administratorLoginPassword     SecureString  undefined
	data:    databaseName                   String        testapp1devdb
	data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
	data:    edition                        String        Standard
	data:    maxSizeBytes                   String        1073741824
	data:    requestedServiceObjectiveName  String        S0
	info:    group deployment create command OKx

Se o comando não for bem-sucedido, resolva as mensagens de erro e tente novamente. Problemas comuns são o uso de valores de parâmetros que não obedecem às restrições de nomenclatura de recursos do Azure. Outras dicas de solução de problemas podem ser encontradas no artigo [Solucionando problemas de implantações de grupos de recursos no Azure](./resource-manager-troubleshoot-deployments-cli.md).

Na linha de comando da CLI, digite o comando a seguir para implantar recursos no grupo de recursos criado para o ambiente de testes, substituindo [path] pelo caminho para os arquivos que você salvou nas etapas anteriores.

	azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

Na linha de comando da CLI, digite o comando a seguir para implantar recursos no grupo de recursos criado para o ambiente de pré-produção, substituindo [path] pelo caminho para os arquivos que você salvou nas etapas anteriores.

	azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
###PowerShell

Em um prompt de comando do Azure PowerShell (versão 1.01 ou superior), digite o comando a seguir para implantar recursos no grupo de recursos criado para o ambiente de Desenvolvimento, substituindo [path] pelo caminho até os arquivos que você salvou nas etapas anteriores.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

Após ver um cursor piscando por alguns minutos, o comando retornará o seguinte, se tiver êxito:

	DeploymentName    : Deployment1
	ResourceGroupName : TestApp1-Development
	ProvisioningState : Succeeded
	Timestamp         : XX/XX/XXXX 2:44:48 PM
	Mode              : Incremental
	TemplateLink      : 
	Parameters        : 
	                    Name             Type                       Value     
	                    ===============  =========================  ==========
	                    siteName         String                     TestApp1DevApp
	                    hostingPlanName  String                     TestApp1DevPlan
	                    siteLocation     String                     Central US
	                    sku              String                     Free      
	                    workerSize       String                     0         
	                    serverName       String                     testapp1devsrv
	                    serverLocation   String                     Central US
	                    administratorLogin  String                     testapp1Admin
	                    administratorLoginPassword  SecureString                         
	                    databaseName     String                     testapp1devdb
	                    collation        String                     SQL_Latin1_General_CP1_CI_AS
	                    edition          String                     Standard  
	                    maxSizeBytes     String                     1073741824
	                    requestedServiceObjectiveName  String                     S0        
	                    
	Outputs           :

  Se o comando não for bem-sucedido, resolva as mensagens de erro e tente novamente. Problemas comuns são o uso de valores de parâmetros que não obedecem às restrições de nomenclatura de recursos do Azure. Outras dicas de solução de problemas podem ser encontradas no artigo [Solucionando problemas de implantações de grupos de recursos no Azure](./resource-manager-troubleshoot-deployments-powershell.md).

  Em um prompt de comando do PowerShell, digite o comando a seguir para implantar recursos no grupo de recursos criado para o ambiente de testes, substituindo [path] pelo caminho para os arquivos que você salvou nas etapas anteriores.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  Em um prompt de comando do PowerShell, digite o comando a seguir para implantar recursos no grupo de recursos criado para o ambiente de pré-produção, substituindo [path] pelo caminho para os arquivos que você salvou nas etapas anteriores.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

Os arquivos de modelos e parâmetros podem ter o controle de versão e ser mantidos com o código do aplicativo e em um sistema de controle de origem. Você também pode salvar os comandos anteriores em arquivos de script e salvá-los com seu código.

> [AZURE.NOTE] Você pode implantar o modelo no Azure diretamente clicando no botão "Implantar no Azure" no artigo [Provisionar um aplicativo Web com um banco de dados SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/). Esse procedimento poderá ser útil para saber mais sobre modelos, mas ele não permitirá que você edite, controle a versão e salve os valores de parâmetro e modelo com o código do aplicativo. Não há mais detalhes sobre esse método neste artigo.

## Manter ambientes
Durante o desenvolvimento, a configuração dos recursos do Azure em diferentes ambientes pode ser alterada inconsistentemente de forma intencional ou acidental. Isso pode causar problemas desnecessários e resolução de problemas durante o ciclo de desenvolvimento do aplicativo.

1. Altere os ambientes abrindo o [Portal do Azure](https://portal.azure.com). 
2. Entre nele com a mesma conta que você usou para concluir as etapas acima. 
3. Conforme mostra a imagem abaixo, clique em Procurar --> Grupos de Recursos (talvez seja necessário rolar para baixo para ver Grupos de recursos).![Portal](./media/solution-dev-test-environments/rgbrowse.png)
4. Depois de clicar nos Grupos de recursos na imagem acima, você verá a folha Grupos de recursos e os três grupos de recursos que você criou em uma etapa anterior, conforme mostra a imagem abaixo. Clique no grupo de recursos TestApp1-Desenvolvimento e você verá a folha que lista os recursos criados pelo modelo na implantação de grupo de recursos TestApp1-Desenvolvimento concluída em uma etapa anterior. Exclua o recurso de aplicativo Web TestApp1DevApp clicando em TestApp1DevApp na folha Grupo de recursos do TestApp1-Development e, em seguida, clicando em Excluir na folha do aplicativo Web de TestApp1DevApp. ![Portal](./media/solution-dev-test-environments/portal2.png)
5. Clique em "Sim" quando o portal exibir uma mensagem perguntando se você tem certeza de que deseja excluir o recurso. Fechar a folha do Grupo de recursos TestApp1-Development e abri-la novamente faz com que o aplicativo Web que acabou de ser excluído não apareça mais. O conteúdo do grupo de recursos agora é diferente do que deveria ser. Além disso, você pode experimentar excluir vários recursos de vários grupos de recursos ou até mesmo alterar definições de configuração para alguns dos recursos. Em vez de usar o Portal do Azure para excluir um recurso de um grupo de recursos, você pode usar o comando [Remove-AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) do PowerShell ou o comando “azure resource delete” da CLI para realizar a mesma tarefa.
6. Para obter todos os recursos e as configurações que devem colocar os grupos de recursos de volta ao estado normal, implante novamente os ambientes nos grupos de recursos usando os mesmos comandos que você usou na seção [Implantar recursos nos ambientes](#deploy-resources-to-environments), mas substitua "Implantação1" por "Implantação2".
7.  Conforme mostra a seção Resumo da folha TestApp1-Development na imagem mostrada na Etapa 4, você verá que o aplicativo Web excluído no portal na etapa anterior existe novamente, assim como quaisquer outros recursos que você decidiu excluir. Se você alterou a configuração de qualquer um dos recursos, também perceberá que foram reconfigurados para os valores normais nos arquivos de parâmetro. Uma das vantagens da implantação de seus ambientes com modelos do Gerenciador de Recursos do Azure é que você pode facilmente reimplantar os ambientes de volta para um estado conhecido a qualquer momento.
8. Se clicar no texto em "Última implantação" na imagem abaixo, você verá uma folha que mostra o histórico de implantação para o grupo de recursos. Como usou o nome "Deployment1" para a primeira implantação e "Deployment2" para a segunda, você terá duas entradas. Clicar em uma implantação exibirá uma folha que mostra os resultados para cada implantação.![Portal](./media/solution-dev-test-environments/portal3.png)



## Excluir ambientes
Quando tiver concluído o uso de um ambiente, convém excluí-lo para não incorrer em cobranças de uso de recursos do Azure que você não está usando. Excluir ambientes é ainda mais fácil do que criá-los. Nas etapas anteriores, foram criados Grupos de recursos individuais do Azure para cada ambiente e, em seguida, os recursos foram implantados em grupos de recursos.

Exclua os ambientes usando um dos métodos abaixo. Todos os métodos obterão o mesmo resultado.

### CLI do Azure

Em um prompt da CLI, digite o seguinte:

	azure group delete "TestApp1-Development"

Quando receber a solicitação, digite y e pressione enter para remover o ambiente de Desenvolvimento e todos os seus recursos. Após alguns minutos, o comando retornará o seguinte:

	info:    group delete command OK

Em um prompt da CLI, digite o seguinte para excluir os ambientes restantes:

	azure group delete "TestApp1-Test"
	azure group delete "TestApp1-Pre-Production"
  
### PowerShell

Em um prompt de comando do Azure PowerShell (versão 1.01 ou superior), digite o comando abaixo para excluir o grupo de recursos e todo o seu conteúdo.

	Remove-AzureRmResourceGroup -Name TestApp1-Development

Quando receber a solicitação, se você tiver certeza de que deseja remover o grupo de recursos, digite y e pressione enter.

Digite o seguinte para excluir os ambientes restantes:

	Remove-AzureRmResourceGroup -Name TestApp1-Test
	Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

### Portal do Azure

1. No Portal do Azure, navegue até Grupos de recursos, como você fez nas etapas anteriores. 
2. Selecione o grupo de recursos TestApp1-Development e, em seguida, clique em Excluir na folha do grupo de recursos TestApp1-Development. Uma nova folha será exibida. Insira o nome do grupo de recursos e clique no botão Excluir. ![Portal](./media/solution-dev-test-environments/rgdelete.png)
3. Exclua os grupos de recursos TestApp1-Teste e TestApp1-Pré-produção da mesma maneira que excluiu o grupo de recursos TestApp1-Desenvolvimento.

Independentemente do método usado, os grupos de recursos e todos os recursos que eles continham deixarão de existir, e você não incorrerá em despesas de cobrança para os recursos.

Para minimizar as despesas de utilização de recursos do Azure incorridas durante o desenvolvimento de aplicativos, você pode usar a [Automação do Azure](automation/automation-intro.md) para agendar trabalhos que:

- Param máquinas virtuais ao fim de cada dia e as reiniciam no início de cada dia.
- Excluir ambientes inteiros ao fim de cada dia e recriá-los no início de cada dia.
 
Agora que viu como é fácil criar, manter e excluir os ambientes de desenvolvimento e teste, você pode aprender mais sobre o que acabou de fazer experimentando as etapas acima e lendo as referências contidas neste artigo.

## Próximas etapas

- [Delegar controle administrativo](./active-directory/role-based-access-control-configure.md) para recursos diferentes em cada ambiente atribuindo usuários ou grupos do AD do Microsoft Azure a funções específicas que têm a capacidade de executar um subconjunto de operações nos recursos do Azure.
- [Atribua marcas](resource-group-using-tags.md) aos grupos de recursos de cada ambiente e/ou aos recursos individuais. Você pode adicionar uma marca de "Ambiente" aos grupos de recursos e definir seu valor para corresponder aos nomes de seu ambiente. As marcas podem ser particularmente úteis quando você precisar organizar os recursos de gerenciamento ou de cobrança.
- Monitore alertas e cobrança de recursos do grupo de recursos no [Portal do Azure](https://portal.azure.com).

<!-----------HONumber=AcomDC_0330_2016-->