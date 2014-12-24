<properties urlDisplayName="Microsoft Azure Cross-Platform Command-Line Interface" pageTitle="Usando a interface de linha de comando entre plataformas do Microsoft Azure com o Gerenciador de Recursos" title =" Usando a interface de linha de comando entre plataformas do Microsoft Azure com o Gerenciador de Recursos "metaKeywords ="interface de linha de comando entre plataformas microsoft azure com Gerenciador de Recursos, gerenciador de recursos de linha de comando do microsoft azure, gerenciador de recursos de linha de comando do azure, gerenciador de recursos de cli do azure" description="Use the Microsoft Azure Cross-Platform Command-Line Interface with the Resource Manager" metaCanonical="http://www.windowsazure.com/pt-br/script/xplat-cli-intro" umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="need to identify contact" documentationCenter="" solutions="" authors="larryfr" services="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

#Usando a interface de linha de comando da plataforma cruzada do Microsoft Azure com o Gerenciador de Recursos

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/powershell-azure-resource-manager.md" title="Windows PowerShell">CLI de plataforma cruzada do</a><a href="/pt-br/documentation/articles/xplat-cli-azure-resource-manager.md" title="Cross-Platform CLI" class="current">Windows PowerShell</a></div>

Recentemente, apresentamos uma visualização do Gerenciador de Recursos, que é uma nova maneira de gerenciar o Microsoft Azure. Neste artigo, você aprenderá a usar a interface de linha de comando de plataforma cruzada do Azure (xplat-cli) para trabalhar com o Gerenciar de Recursos. 

>[WACOM.NOTE] O Gerenciador de Recursos está em visualização no momento e não oferece o mesmo nível de recursos de gerenciamento que o Gerenciamento de Serviços do Azure.

>[WACOM.NOTE] Se você ainda não tiver instalado e configurado o xplat cli, consulte [Instalar e configurar a interface de linha de comando entre plataformas do Microsoft Azure][xplatsetup] para obter mais etapas sobre como instalar, configurar e usar o xplat-cli.

##Gerenciador de Recursos

O Gerenciador de Recursos permite que você gerencie um grupo de _recursos_ (entidades gerenciadas pelo usuário, como um servidor de banco de dados, um banco de dados ou um site) como uma unidade lógica única ou um _grupo de recursos_. Por exemplo, um grupo de recursos pode conter um site e recursos de banco de dados SQL.

Para dar suporte a uma maneira mais declarativa de descrever alterações feitas em recursos em um grupo de recursos, o Gerenciador de Recursos usa *modelos*, que são documentos JSON. O idioma do modelo também permite descrever parâmetros que podem ser preenchidos embutidos durante a execução de um comando ou armazenados em um arquivo JSON separado. Isso permite criar facilmente novos recursos usando-se o mesmo modelo simplesmente fornecendo parâmetros diferentes. Por exemplo, um modelo que cria um site terá parâmetros para o nome do site, a região do site em que estará localizado e outros parâmetros comuns.

>[WACOM.NOTE] As especificações do idioma do modelo não são documentadas neste momento. Depois que a documentação estiver disponível, este tópico será atualizado para fornecer um link para a documentação de referência.
>
> No entanto, você pode usar o comando `azure group template download` para baixar e modificar modelos fornecidos pela Microsoft e por parceiros na galeria de modelos.

Quando um modelo é usado para modificar ou criar um grupo, uma _implantação_ é criada e, então, aplicada ao grupo.

##Autenticação

Atualmente, o trabalho com o Gerenciador de Recursos por meio do xplat-cli exige que você se autentique no Microsoft Azure usando uma conta organizacional. A autenticação com uma conta da Microsoft ou um certificado instalado por meio de um arquivo .publishsettings não funcionará.

Para obter mais informações sobre como se autenticar usando uma conta organizacional, consulte [Instalar e configurar a interface de linha de comando entre plataformas do Microsoft Azure][xplatsetup].

##Trabalhando com grupos e modelos

1. Como o Gerenciador de Recursos está em visualização, os comandos xplat-cli que funcionam com ele não permanecem habilitados por padrão. Use o comando a seguir para habilitar os comandos.

		braço do modo de configuração do Azure

	>[WACOM.NOTE]  O modo do Gerenciador de Recursos e o modo do Gerenciamento de Serviços do Azure são mutuamente excludentes. Ou seja, recursos criados em um modo não podem ser gerenciados no outro modo.

2. Ao trabalhar com modelos, você pode criar seu próprio ou usar um da Galeria de Modelos. Para listar os modelos disponíveis da galeria, use o comando a seguir.

		azure group template list

	A resposta listará o editor e o nome do modelo, além de ser semelhante à resposta a seguir.

		data:    Publisher               Name
		data:    ----------------------------------------------------------------------------
		data:    Microsoft               Microsoft.WebSite.0.1.0-preview1
		data:    Microsoft               Microsoft.PHPStarterKit.0.1.0-preview1
		data:    Microsoft               Microsoft.HTML5EmptySite.0.1.0-preview1
		data:    Microsoft               Microsoft.ASPNETEmptySite.0.1.0-preview1
		data:    Microsoft               Microsoft.WebSiteMySQLDatabase.0.1.0-preview1

3. Para exibir detalhes de um modelo que criará um site do Azure, use o comando a seguir.

		azure group template show Microsoft.WebSiteSQLDatabase.0.1.0-preview1

	Isso retornará informações descritivas sobre o modelo.

4. Depois de selecionar um modelo, você poderá baixá-lo usando o comando a seguir.

		azure group template download Microsoft.WebSiteSQLDatabase.0.1.0-preview1

	O download de um modelo permite personalizá-lo para atender melhor às suas necessidades. Por exemplo, adicionando outro recurso ao modelo.

	>[WACOM.NOTE] Se você modificar o modelo, use o comando `azure group template validate` para validar o modelo antes de usá-lo a fim de criar ou modificar um grupo de recursos existente.

5. Abra o arquivo de modelo em um editor de texto. Observe a coleção **parâmetros** na parte superior. Ela contém uma lista de parâmetros esperados por esse modelo para criar os recursos descritos pelo modelo. Alguns parâmetros, como **sku**, têm valores padrão, e outros apenas especificam o tipo de valor, como **siteName** Ao usar um modelo, você pode fornecer parâmetros como parte dos parâmetros de linha de comando ou especificando um arquivo contendo os valores de parâmetro. De qualquer maneira, os parâmetros devem estar no formato JSON.

	Para criar um arquivo contendo parâmetros para o modelo Microsoft.WebSiteSQLDatabase.0.1.0 preview1, use os seguintes dados e crie um arquivo chamado **params.json**. Substitua valores que comecem com **My** como **MyWebSite** por seus próprios valores. O **siteLocation** deve especificar uma região do Azure próxima de você, como **Norte da Europa** ou **Centro Sul dos Estados Unidos**.

		{
		  "siteName": {
		    "value": "MyWebSite"
		  },
		  "hostingPlanName": {
		    "value": "MyHostingPlan"
		  },
		  "siteLocation": {
		    "value": "North Europe"
		  },
		  "serverName": {
		    "value": "MySQLServer"
		  },
		  "serverLocation": {
		    "value": "North Europe"
		  },
		  "administratorLogin": {
		    "value": "MySQLAdmin"
		  },
		  "administratorLoginPassword": {
		    "value": "MySQLAdminPassword"
		  },
		  "databaseName": {
		    "value": "MySQLDB"
		  }
		}

1. Depois de salvar o arquivo **params.json**, use o comando a seguir para criar um novo grupo de recursos com base no modelo. O parâmetro `-e` especifica o arquivo **params.json** criado na etapa anterior.

		azure group create MyGroupName "MyDataCenter" -y Microsoft.WebSiteSQLDatabase.0.1.0-preview1 -d MyDeployment -e params.json

	Substitua o **MyGroupName** pelo nome do grupo que você deseja usar e **MyDataCenter** pelo valor **siteLocation** especificado no modelo.

	>[WACOM.NOTE]  Esse comando retornará OK depois que a implantação for carregada, mas antes da implantação ser aplicada aos recursos do grupo. Para verificar o status da implantação, use o comando a seguir.
	>
	> `azure group deployment show MyGroupName MyDeployment`
	> 
	> O **ProvisioningState** mostra o status da implantação.
	> 
	> Se você perceber que sua configuração não está correta e precisar parar uma implantação em execução há muito tempo, use o comando a seguir.
	> 
	> `azure group deployment stop MyGroupName MyDeployment`
	> 
	> Se você não fornecer um nome de implantação, será criado automaticamente um com base no nome do arquivo de modelo. Ele será retornado como parte da saída do comando `azure group create`.

3. Para exibir o grupo, use o comando a seguir.

		azure group show MyGroupName

	Esse comando retorna informações sobre os recursos do grupo. Se tiver vários grupos, você poderá usar o comando `azure group list` para recuperar uma lista de nomes de grupo e, em seguida, usar `azure group show` para exibir detalhes de um grupo específico.

##Trabalhando com recursos

Embora modelos permitam declarar alterações feitas na configuração que afetam todo o grupo, às vezes você precisa trabalhar com apenas um recurso específico. Você pode fazer isso usando o comando `azure resource`.

> [WACOM.NOTE] Ao usar os comandos `azure resource` que não sejam o comando `list`, você deve especificar a versão da API do recurso com que está trabalhando usando o parâmetro `-o`. Se você não tiver certeza sobre a versão da API a ser usada, consulte o arquivo de modelo e localize o campo **apiVersion** do recurso.

1. Para listar todos os recursos em um grupo, use o comando a seguir.

		azure resource list MyGroupName

1. Para exibir recursos individuais, como o site, dentro do grupo, use o comando a seguir.

		azure resource show MyGroupName MyWebSiteName Microsoft.Web/sites -o "2014-04-01"

	Observe o parâmetro **Microsoft.Web/sites**. Isso indica o tipo do recurso sobre o qual você está solicitando informações. Se examinar o arquivo de modelo baixado anteriormente, você observará que esse mesmo valor é usado para definir o tipo do recurso do site descrito no modelo.

	Esse comando retorna informações relacionadas ao site. Por exemplo, o campo **hostNames** deve conter a URL do site. Use-o com o navegador para verificar se o site está funcionando.

2. Durante a exibição de detalhes sobre um recurso, costuma ser útil usar o parâmetro `--json`, pois isso torna a saída mais legível, uma vez que alguns valores são estruturas aninhadas ou coleções. O exemplo a seguir demonstra o retorno dos resultados do comando show como um documento JSON.

		azure resource show MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01" --json

	>[WACOM.NOTE] Você pode salvar os dados JSON em arquivo usando o caractere &gt; para enviar a saída ao arquivo. Por exemplo:
	>
	> `azure resource show MyGroupName MyWebSite Micrsoft.Web/sites --json > myfile.json`

3. Para excluir um recurso existente, use o comando a seguir.

		azure resource delete MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01"

##Registro em log

Para exibir informações registradas em log sobre operações realizadas em um grupo, use o comando `azure group log show`. Por padrão, ele listará a última operação realizada no grupo. Para exibir todas as operações, use o parâmetro opcional `--all`. Para a última implantação, use `--last-deployment`. Para uma implantação específica, use `--deployment` e especifique o nome da implantação. O exemplo a seguir retorna um log de todas as operações realizadas no grupo 'MyGroup'.

	azure group log show mygroup --all

##Próximas etapas

* Para obter mais informações sobre como usar a interface de linha de comando da entre plataformas do Azure, consulte [Instalar e configurar a interface de linha de comando dentre plataformas do Microsoft Azure][xplatsetup].
* Para obter informações sobre como trabalhar com o Gerenciador de Recursos usando o Microsoft Azure PowerShell, consulte [Guia de introdução usando o Windows PowerShell com o Gerenciador de Recursos][psrm]

[signuporg]: http://www.windowsazure.com/pt-br/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/pt-br/library/jj573650#createAzureTenant
[portal]: https://manage.windowsazure.com/
[xplatsetup]: /pt-br/documentation/articles/xplat-cli/
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760
