
<properties
	pageTitle="Use a CLI do Azure com o Gerenciador de Recursos | Microsoft Azure"
	description="Use a CLI do Azure para Mac, Linux e Windows para implantar vários recursos como um grupo de recursos."
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="azure-resource-manager"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/19/2016"
	ms.author="danlep"/>

# Usar a CLI do Azure para Mac, Linux e Windows com o Gerenciador de Recursos do Azure

> [AZURE.SELECTOR]
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)



Este artigo descreve como criar e gerenciar recursos do Azure e usando a Interface da Linha de Comando do Azure (CLI) para Mac, Linux e Windows no modo Gerenciador de Recursos do Azure.

>[AZURE.NOTE]Para criar e gerenciar recursos do Azure na linha de comando, você precisará de uma conta do Azure ([avaliação gratuita aqui](http://azure.microsoft.com/pricing/free-trial/)). Você também precisará [instalar a CLI do Azure](xplat-cli-install.md), e [fazer logon para usar recursos do Azure associados à sua conta](xplat-cli-connect.md). Se você tiver feito isso, você está pronto para continuar.

## Recursos do Azure

Use o Gerenciador de Recursos do Azure para criar e gerenciar um grupo de _recursos_ (entidades gerenciadas pelo usuário, como máquina virtual, servidor de banco de dados, banco de dados ou site) como uma unidade lógica única ou um _grupo de recursos_.

Uma vantagem do Gerenciador de Recursos do Azure é que você pode criar seus recursos do Azure de maneira _declarativa_: descrevendo a estrutura e as relações de um grupo implantável de recursos em *modelos* JSON. O modelo identifica os parâmetros que podem ser preenchidos ou embutidos durante a execução de um comando, ou armazenados em arquivo JSON azuredeploy-parameters.json separado. Isso permite criar facilmente novos recursos usando-se o mesmo modelo simplesmente fornecendo parâmetros diferentes. Por exemplo, um modelo que cria um site terá parâmetros para o nome do site, a região do site em que estará localizado e outros configurações comuns.

Quando um modelo é usado para modificar ou criar um grupo, uma _implantação_ é criada e, então, aplicada ao grupo. Para saber mais sobre o Gerenciador de Recursos do Azure, visite a [Visão Geral do Gerenciador de Recursos do Azure](resource-group-overview.md).

Depois de criar uma implantação, você pode gerenciar os recursos individuais imperativamente na linha de comando, assim como no modelo de implantação clássico (Gerenciamento de Serviço). Por exemplo, use comandos de CLI do Gerenciador de Recursos do Azure para iniciar, parar ou excluir recursos como [máquinas virtuais do Gerenciador de Recursos do Azure](virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md).

## Autenticação

Trabalhar com o Gerenciador de Recursos do Azure por meio da CLI do Azure exige uma autenticação no Microsoft Azure usando uma conta corporativa ou de estudante (uma conta organizacional) ou uma conta da Microsoft (começando pela CLI versão 0.9.10). A autenticação usando um certificado instalado por meio de um arquivo .publishsettings não funcionará neste modo.

Para saber mais sobre a autenticação para o Microsoft Azure, consulte [Conectar-se a uma assinatura do Azure a partir da CLI do Azure](xplat-cli-connect.md).

>[AZURE.NOTE]Quando você usa uma conta corporativa ou de estudante, que é gerenciada pelo Active Directory do Azure, você também pode usar o controle de acesso baseado em função (RBAC) do Azure para gerenciar o acesso e o uso de recursos do Azure. Para obter detalhes, confira [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md).

## Definir o modo Gerenciador de Recursos do Azure

Como o modo Gerenciador de Recursos do Azure não está habilitado por padrão, use o comando a seguir para habilitar os comandos do Gerenciador de Recursos da CLI do Azure.

	azure config mode arm

>[AZURE.NOTE]O modo do Gerenciador de Recursos do Azure e o modo do Gerenciamento de Serviços do Azure são mutuamente exclusivos. Ou seja, recursos criados em um modo não podem ser gerenciados no outro modo.

## Encontrar os locais

A maioria dos comandos do Gerenciador de Recursos do Azure precisa de um local válido de onde criar ou localizar um recurso. Você pode encontrar todos os locais disponíveis para os diferentes recursos do Azure usando o comando a seguir.

	azure location list

Lista os recursos do Azure e as regiões do Azure nas quais eles estão disponíveis, como "Oeste dos EUA", "Leste dos EUA" e assim por diante.

## Criar um grupo de recursos

Um grupo de recursos é um agrupamento lógico de rede, armazenamento e outros recursos. Quase todos os comandos no modo Gerenciador de Recursos do Azure precisam de um grupo de recursos. Você pode criar um grupo de recursos denominado _testRG_, por exemplo, usando o comando a seguir.

	azure group create -n "testRG" -l "West US"

Você implantará a esse grupo de recursos "testRG" posteriormente quando usar um modelo para iniciar uma VM do Ubuntu. Depois de ter criado um grupo de recursos, você pode adicionar recursos, como máquinas virtuais e redes ou armazenamento.


## Usar modelos do grupo de recursos

### Localizar e configurar um modelo de grupo de recursos

Ao trabalhar com modelos, você poderá [criar o seu próprio modelo](resource-group-authoring-templates.md) ou usar um dos modelos da [Galeria de Modelos](https://azure.microsoft.com/documentation/templates/) que estão disponíveis também no [GitHub](https://github.com/Azure/azure-quickstart-templates).

A criação de um novo modelo está além do escopo deste artigo e, portanto, para começarmos usaremos o modelo _101-simple-vm-from-image_ disponível na [Galeria de Modelos](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux/). Por padrão, isso cria uma única máquina virtual do Ubuntu 14.04.2-LTS em uma nova rede virtual com uma única sub-rede na região Oeste dos EUA. Você só precisa especificar os seguintes parâmetros para usar este modelo:

* Um nome de usuário de administrador para a VM = `adminUsername`
* Uma senha = `adminPassword`
* Um nome de domínio para a VM = `dnsLabelPrefix`

>[AZURE.TIP]Estas etapas mostram apenas uma maneira de usar um modelo de VM com a CLI do Azure. Para obter outros exemplos, consulte [Implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de Recursos do Azure e a CLI do Azure](virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md)

1. Siga o link "Saiba mais com o GitHub" para baixar os arquivos azuredeploy.json e azuredeploy.parameters.json do GitHub para uma pasta de trabalho no computador local. (Selecione o formato _não processado_ de cada arquivo no GitHub).

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
3.  Agora que os parâmetros de implantação foram sido modificados, você implantará a VM do Ubuntu no grupo de recursos criado anteriormente. Escolha um nome para a implantação e, em seguida, use o seguinte comando para iniciá-lo.

	```
	azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json testRG testRGdeploy
	```

	Esse exemplo cria uma implantação chamada _testRGDeploy_, que é implantada no grupo de recursos _testRG_. A opção `-e` especifica o arquivo azuredeploy.parameters.json que você modificou na etapa anterior. A opção `-f` especifica o arquivo de modelo azuredeploy.json.

	Esse comando retornará OK assim que a implantação for carregada, mas antes da implantação ser aplicada aos recursos do grupo.

4. Para verificar o status da implantação, use o comando a seguir.

	```
	azure group deployment show "testRG" "testRGDeploy"
	```

	O **ProvisioningState** mostra o status da implantação.

	Se sua implantação for bem-sucedida, você verá uma saída semelhante ao seguinte.

		azure-cli@0.8.0:/# azure group deployment show testRG testDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Running
		data:    Timestamp          : 2015-10-26T16:15:29.5562024Z
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ---------------------
		data:    newStorageAccountName  String        MyStorageAccount
		data:    adminUsername          String        MyUserName
		data:    adminPassword          SecureString  undefined
		data:    dnsNameForPublicIP     String        MyDomainName
		data:    ubuntuOSVersion        String        14.04.2-LTS
		info:    group deployment show command OK

	>[AZURE.NOTE]Se você perceber que sua configuração não está correta e precisar parar uma implantação em execução há muito tempo, use o comando a seguir.
	>
	> `azure group deployment stop "testRG" "testDeploy"`
	>
	> Se você não fornecer um nome de implantação, um nome é criado automaticamente com base no nome do arquivo de modelo. Ele é retornado como parte da saída do comando `azure group create`.

	Agora você pode criar uma SSH para a VM usando o nome de domínio especificado. Quando se conectar à VM, você precisa usar um nome de domínio totalmente qualificado do formato `<domainName>.<region>.cloudapp.azure.com`, como `MyDomainName.westus.cloudapp.azure.com`.

5. Para exibir o grupo, use o comando a seguir.

		azure group show "testRG"

	Esse comando retorna informações sobre os recursos do grupo. Se você tiver vários grupos, use o comando `azure group list` para recuperar uma lista de nomes de grupo e use `azure group show` para exibir detalhes de um grupo específico.

Você também pode usar um modelo diretamente do [GitHub](https://github.com/Azure/azure-quickstart-templates) em vez de baixar um no computador. Para isso, transmita a URL para o arquivo azuredeploy.json do modelo em seu comando usando a opção **--template-url**. Para obter a URL, abra azuredeploy.json no GitHub no modo _bruto_ e copie a URL que aparece na barra de endereços do navegador. Você pode usar essa URL diretamente para criar uma implantação usando um comando semelhante ao exemplo a seguir.

	azure group deployment create "testDeploy" testResourceGroup --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-linux/azuredeploy.json
Você precisará inserir os parâmetros de modelo necessários.

> [AZURE.NOTE]É importante abrir o modelo JSON no modo _bruto_. A URL que aparece na barra de endereços do navegador é diferente daquela que aparece no modo normal. Para abrir o arquivo no modo _raw_ ao exibir o arquivo no GitHub, no canto superior direito, clique em **Raw**.

## Trabalhando com recursos

Embora modelos permitam declarar alterações feitas na configuração que afetam todo o grupo, às vezes você precisa trabalhar com apenas um recurso específico. Você pode fazer isso usando os comandos `azure resource`.

> [AZURE.NOTE]Ao usar os comandos `azure resource` em vez do comando `list` , você deve especificar a versão da API do recurso com o qual estiver trabalhando usando o parâmetro `-o`. Se você não tiver certeza sobre a versão da API a ser usada, consulte o arquivo de modelo e localize o campo **apiVersion** do recurso.

1. Para listar todos os recursos em um grupo, use o comando a seguir.

		azure resource list "testRG"

2. Para exibir um recurso individual dentro do grupo, use um comando como o seguinte.

		azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

	Observe o parâmetro **Microsoft.Compute/virtualMachines**. Isso indica o tipo do recurso sobre o qual você está solicitando informações. Ao examinar o arquivo de modelo baixado anteriormente, você observará que esse mesmo valor é usado para definir o tipo do recurso da máquina virtual descrito no modelo.

	Esse comando retorna informações relacionadas à máquina virtual.

3. Ao exibir detalhes em um recurso, costuma ser útil usar o parâmetro `--json`. Isso torna a saída mais legível já que alguns valores são estruturas aninhadas ou coleções. O exemplo a seguir demonstra o retorno dos resultados do comando **show** como um documento JSON.

		azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json

	>[AZURE.NOTE]Você pode salvar os dados JSON em arquivo usando o caractere para enviar a saída ao arquivo. Por exemplo:
	>
	> `azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

4. Para excluir um recurso existente, use um comando como o seguinte.

		azure resource delete "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

## Registro em log

Para exibir informações registradas em log sobre operações realizadas em um grupo, use o comando `azure group log show`. Por padrão, ele listará a última operação realizada no grupo. Para exibir todas as operações, use o parâmetro opcional `--all`. Para a última implantação, use `--last-deployment`. Para uma implantação específica, use `--deployment` e especifique o nome da implantação. O exemplo a seguir retorna um log de todas as operações realizadas no grupo *MyGroup*.

	azure group log show MyGroup --all

## Próximas etapas

* Para obter informações sobre como trabalhar com o Gerenciador de Recursos do Azure usando o Azure PowerShell, veja [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](powershell-azure-resource-manager.md).
* Para obter informações sobre como trabalhar com o Gerenciador de Recursos do Azure no Portal do Azure, confira [Usando grupos de recursos para gerenciar os recursos do Azure][psrm].

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

<!---HONumber=AcomDC_0121_2016-->