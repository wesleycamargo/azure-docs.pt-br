
<properties
	pageTitle="Use a CLI do Azure com o Gerenciador de Recursos | Microsoft Azure"
	description="Use a CLI (Interface de Linha de Comando) do Azure para implantar vários recursos como um grupo de recursos."
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="azure-resource-manager"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/20/2016"
	ms.author="danlep"/>

# Usar a CLI do Azure para Mac, Linux e Windows com o Gerenciador de Recursos do Azure

> [AZURE.SELECTOR]
- [CLI do Azure](xplat-cli-azure-resource-manager.md)
- [PowerShell do Azure](powershell-azure-resource-manager.md)



Este artigo apresenta como criar e gerenciar os recursos do Azure usando a CLI (Interface de Linha de Comando) do Azure no modo Azure Resource Manager.

>[AZURE.NOTE] Para criar e gerenciar os recursos do Azure na linha de comando, você precisará de uma assinatura do Azure ([conta do Azure gratuita aqui](https://azure.microsoft.com/free/)). Também precisará [instalar a CLI do Azure](xplat-cli-install.md) e [fazer logon para usar os recursos do Azure associados à sua conta](xplat-cli-connect.md). Se você tiver feito isso, você está pronto para continuar.

## Recursos do Azure

Use o Gerenciador de Recursos do Azure para criar e gerenciar um grupo de _recursos_ (entidades gerenciadas pelo usuário, como máquina virtual, servidor de banco de dados, banco de dados ou site) como uma unidade lógica única ou um _grupo de recursos_.

Uma vantagem do Azure Resource Manager é que você pode criar seus recursos do Azure de maneira _declarativa_: descrevendo a estrutura e as relações de um grupo implantável de recursos em *modelos* JSON. O modelo identifica os parâmetros que podem ser preenchidos em linha ao executar um comando ou armazenados em um arquivo de parâmetros JSON separado (JavaScript Object Notation). Isso permite criar facilmente novos recursos usando-se o mesmo modelo simplesmente fornecendo parâmetros diferentes. Por exemplo, um modelo que cria um site terá parâmetros para o nome do site, a região do site em que estará localizado e outros configurações comuns.

Quando um modelo é usado para modificar ou criar um grupo, uma _implantação_ é criada e, então, aplicada ao grupo. Para saber mais sobre o Gerenciador de Recursos do Azure, visite a [Visão Geral do Gerenciador de Recursos do Azure](resource-group-overview.md).

Depois de criar uma implantação, você pode gerenciar os recursos individuais de modo forçado na linha de comando, assim como faz no modelo de implantação clássico. Por exemplo, use os comandos da CLI no modo Gerenciador de Recursos para iniciar, parar ou excluir recursos como as [máquinas virtuais do Azure Resource Manager](./virtual-machines/virtual-machines-linux-cli-deploy-templates.md).

## Autenticação

Trabalhar com o Azure Resource Manager por meio da CLI do Azure atualmente requer que você se autentique no Microsoft Azure usando o `azure login` comando, em seguida, especificando uma conta gerenciada pelo Azure Active Directory - uma conta corporativa, de estudante (uma conta organizacional) ou uma conta da Microsoft. A autenticação usando um certificado instalado por meio de um arquivo .publishsettings não funcionará neste modo.

Para saber mais sobre a autenticação para o Microsoft Azure, consulte [Conectar-se a uma assinatura do Azure a partir da CLI do Azure](xplat-cli-connect.md).

>[AZURE.NOTE] Quando você usa uma conta gerenciada pelo Azure Active Directory, também pode usar o RBAC (Controle de Acesso Baseado no Azure Functions) para gerenciar o acesso e o uso dos recursos do Azure. Para obter detalhes, confira [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md).

## Definir o modo Gerenciador de Recursos

Como a CLI não está no modo Gerenciador de Recursos por padrão, use o comando a seguir para habilitar os comandos do Gerenciador de Recursos da CLI do Azure.

	azure config mode arm

## Encontrar os locais

A maioria dos comandos do Gerenciador de Recursos do Azure precisa de um local válido de onde criar ou localizar um recurso. Você pode encontrar todos os locais disponíveis para os diferentes recursos do Azure usando o comando a seguir.

	azure location list

Isto lista as regiões do Azure que estão disponíveis, como "Oeste dos EUA", "Leste dos EUA" e assim por diante. Para obter detalhes sobre os provedores de recursos disponíveis e os locais onde eles estão disponíveis, use o comando `azure provider list` seguido do comando `azure provider show`. Por exemplo, o comando a seguir lista os locais do serviço de Contêiner do Azure:

    azure provider show Microsoft.ContainerService 

## Criar um grupo de recursos

Um grupo de recursos é um agrupamento lógico de recursos, como recursos de computação, armazenamento e rede. Quase todos os comandos no modo Gerenciador de Recursos precisam de um grupo de recursos. Você pode criar um grupo de recursos na região Oeste dos EUA denominado _testRG_, por exemplo, usando o comando a seguir.

	azure group create -n "testRG" -l "West US"

Você implantará esse grupo de recursos *testRG* posteriormente quando usar um modelo para iniciar uma VM do Ubuntu. Depois de ter criado um grupo de recursos, você poderá adicionar recursos como máquinas virtuais e redes ou armazenamento.


## Usar modelos do grupo de recursos

### Localizar e configurar um modelo de grupo de recursos

Ao trabalhar com modelos, você poderá [criar seu próprio modelo](resource-group-authoring-templates.md) ou usar um dos [modelos QuickStart](https://azure.microsoft.com/documentation/templates/) contribuídos pela comunidade disponíveis também no [GitHub](https://github.com/Azure/azure-quickstart-templates).

A criação de um novo modelo está fora do escopo deste artigo. Portanto, para começar, usaremos o modelo _101-simple-vm-from-image_ disponível nos [modelos QuickStart](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux/). Por padrão, isso cria uma única máquina virtual Ubuntu 14.04.2-LTS em uma nova rede virtual com uma única sub-rede. Você só precisa especificar um grupo de recursos e alguns parâmetros a seguir para usar esse modelo:

* Nome de usuário de administrador para a VM = `adminUsername`
* Senha = `adminPassword`
* Nome de domínio para a VM = `dnsLabelPrefix`

>[AZURE.TIP] Estas etapas mostram apenas uma maneira de usar um modelo de VM com a CLI do Azure. Para obter outros exemplos, consulte [Implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de Recursos do Azure e a CLI do Azure](./virtual-machines/virtual-machines-linux-cli-deploy-templates.md)

1. Siga o link "Saiba mais com o GitHub" para baixar os arquivos azuredeploy.json e azuredeploy.parameters.json do [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux) para uma pasta de trabalho no computador local. (Selecione o formato _raw_ de cada arquivo no GitHub).

2. Abra o arquivo azuredeploy.parameters.json em um editor de texto e insira valores de parâmetro adequados para seu ambiente (deixando o valor **ubuntuOSVersion** inalterado).

	```
			{
			  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
			  "contentVersion": "1.0.0.0",
			  "parameters": {
			    "adminUsername": {
			      "value": "azureUser"
			    },
			    "adminPassword": {
			      "value": "GEN-PASSWORD"
			    },
			    "dnsLabelPrefix": {
			      "value": "GEN-UNIQUE"
			    },
			    "ubuntuOSVersion": {
			      "value": "14.04.2-LTS"
			    }
			  }
			}

	```
3.  Agora que os parâmetros de implantação foram modificados, você implantará a VM do Ubuntu no grupo de recursos *testRG* criado anteriormente. Escolha um nome para a implantação (*testRGDeploy* neste exemplo), em seguida, use o seguinte comando para iniciar.

	```
	azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json testRG testRGDeploy
	```

	A opção `-e` especifica o arquivo azuredeploy.parameters.json que você modificou na etapa anterior. A opção `-f` especifica o arquivo de modelo azuredeploy.json.

	Esse comando retornará OK assim que a implantação for carregada, mas antes da implantação ser aplicada aos recursos do grupo.

4. Para verificar o status da implantação, use o comando a seguir.

	```
	azure group deployment show testRG testRGDeploy
	```

	O **ProvisioningState** mostra o status da implantação.

	Se sua implantação for bem-sucedida, você verá uma saída semelhante ao seguinte.

		azure-cli@0.8.0:/# azure group deployment show testRG testRGDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Succeeded
		data:    Timestamp          : 
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ---------------------
		data:    adminUsername          String        MyUserName
		data:    adminPassword          SecureString  undefined
		data:    dnsLabelPrefix    String        MyDomainName
		data:    ubuntuOSVersion        String        14.04.2-LTS
		info:    group deployment show command OK

	>[AZURE.NOTE] Se você perceber que sua configuração não está correta e precisar parar uma implantação em execução há muito tempo, use o comando a seguir.
	>
	> `azure group deployment stop testRG testRGDeploy`
	>
	> Se você não fornecer um nome de implantação, um nome é criado automaticamente com base no nome do arquivo de modelo. Ele é retornado como parte da saída do comando `azure group create`.

	Agora você pode criar uma SSH para a VM usando o nome de domínio especificado. Quando se conectar à VM, você precisa usar um nome de domínio totalmente qualificado do formato `<domainName>.<region>.cloudapp.azure.com`, como `MyDomainName.westus.cloudapp.azure.com`.

5. Para exibir o grupo, use o comando a seguir.

		azure group show testRG

	Esse comando retorna informações sobre os recursos do grupo. Se você tiver vários grupos, use o comando `azure group list` para recuperar uma lista de nomes de grupo e use `azure group show` para exibir detalhes de um grupo específico.

Você também pode usar um modelo diretamente do [GitHub](https://github.com/Azure/azure-quickstart-templates) em vez de baixar um no computador. Para isso, transmita a URL para o arquivo azuredeploy.json do modelo em seu comando usando a opção **--template-uri**. Para obter a URL, abra azuredeploy.json no GitHub no modo _bruto_ e copie a URL que aparece na barra de endereços do navegador. Você pode usar essa URL diretamente para criar uma implantação usando um comando semelhante ao exemplo a seguir.

	azure group deployment create testRG testRGDeploy --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-linux/azuredeploy.json
Você precisará inserir os parâmetros de modelo necessários.

> [AZURE.NOTE] É importante abrir o modelo JSON no modo _bruto_. A URL que aparece na barra de endereços do navegador é diferente daquela que aparece no modo normal. Para abrir o arquivo no modo _raw_ ao exibir o arquivo no GitHub, no canto superior direito, clique em **Raw**.

## Trabalhar com recursos

Embora modelos permitam declarar alterações feitas na configuração que afetam todo o grupo, às vezes você precisa trabalhar com apenas um recurso específico. Você pode fazer isso usando os comandos `azure resource`.

> [AZURE.NOTE] Ao usar os comandos `azure resource` em vez do comando `list` , você deve especificar a versão da API do recurso com o qual estiver trabalhando usando o parâmetro `-o`. Se você não tiver certeza sobre a versão da API a ser usada, consulte o arquivo de modelo e localize o campo **apiVersion** do recurso.

1. Para listar todos os recursos em um grupo, use o comando a seguir.

		azure resource list testRG

2. Para exibir um recurso individual dentro do grupo, como a VM denominada *MyUbuntuVM*, use um comando semelhante ao seguinte.

		azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

	Observe o parâmetro **Microsoft.Compute/virtualMachines**. Isso indica o tipo do recurso sobre o qual você está solicitando informações. Ao examinar o arquivo de modelo baixado anteriormente, você observará que esse mesmo valor é usado para definir o tipo do recurso da máquina virtual descrito no modelo.

	Esse comando retorna informações relacionadas à máquina virtual.

3. Ao exibir detalhes em um recurso, costuma ser útil usar o parâmetro `--json`. Isso torna a saída mais legível já que alguns valores são estruturas aninhadas ou coleções. O exemplo a seguir demonstra o retorno dos resultados do comando **show** como um documento JSON.

		azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json

	>[AZURE.NOTE] Você pode salvar os dados JSON no arquivo usando o caractere &gt; para direcionar a saída para um arquivo. Por exemplo:
	>
	> `azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

4. Para excluir um recurso existente, use um comando como o seguinte.

		azure resource delete testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

## Exibir logs de grupo

Para exibir informações registradas em log sobre operações realizadas em um grupo, use o comando `azure group log show`. Por padrão, ele listará a última operação realizada no grupo. Para exibir todas as operações, use o parâmetro opcional `--all`. Para a última implantação, use `--last-deployment`. Para uma implantação específica, use `--deployment` e especifique o nome da implantação. O exemplo a seguir retorna um log de todas as operações realizadas no grupo *testRG*.

	azure group log show testRG --all
    
## Exportar um grupo de recursos como um modelo

Para um grupo de recursos existente, você pode exibir o modelo Resource Manager do grupo de recursos. A exportação do modelo oferece dois benefícios:

1. Você pode automatizar com facilidade as implantações futuras da solução, pois toda a infraestrutura está definida no modelo.

2. Você pode familiarizar-se com a sintaxe do modelo analisando o JSON que representa sua solução.

Usando a CLI do Azure, você pode exportar um modelo que representa o estado atual do seu grupo de recursos ou baixar o modelo que foi usado para uma determinada implantação.

* **Exportar o modelo para um grupo de recursos** - isso será útil quando você tiver feito alterações em um grupo de recursos e precisar recuperar a representação JSON de seu estado atual. No entanto, o modelo gerado contém apenas uma quantidade mínima de parâmetros e nenhuma variável. A maioria dos valores no modelo é embutida em código. Antes de implantar o modelo gerado, convém converter mais valores em parâmetros, para que você possa personalizar a implantação para ambientes diferentes.

    Para exportar o modelo de um grupo de recursos para um diretório local, execute o comando `azure group export` conforme mostrado no exemplo a seguir. (Substitua seu ambiente do sistema operacional por um diretório local apropriado).

        azure group export testRG ~/azure/templates/

* **Baixe o modelo para uma determinada implantação** – isso será útil quando você precisar exibir o modelo real que foi usado para implantar os recursos. O modelo incluirá todos os parâmetros e variáveis definidos para a implantação original. No entanto, se alguém em sua organização tiver feito alterações ao grupo de recursos que estejam fora da definição no modelo, esse modelo não representará o estado atual do grupo de recursos.

    Para baixar o modelo usado de uma implantação específica para um diretório local, execute o comando `azure group deployment template download`.

        azure group deployment template download TestRG testRGDeploy ~/azure/templates/downloads/
 
>[AZURE.NOTE] A exportação do modelo está na visualização e nem todos os tipos de recursos oferecem suporte atualmente para a exportação de um modelo. Ao tentar exportar um modelo, talvez você veja um erro afirmando que alguns recursos não foram exportados. Se for necessário, defina manualmente esses recursos em seu modelo depois de baixá-lo.

## Próximas etapas

* Para obter informações sobre como trabalhar com o Gerenciador de Recursos do Azure usando o Azure PowerShell, veja [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](powershell-azure-resource-manager.md).
* Para obter informações sobre como trabalhar com o Azure Resource Manager no portal do Azure, consulte [Usando o Portal do Azure para implantar e gerenciar os recursos do Azure](./azure-portal/resource-group-portal.md).

<!---HONumber=AcomDC_0427_2016-->