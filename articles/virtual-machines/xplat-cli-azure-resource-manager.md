<properties
	pageTitle="Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure | Microsoft Azure"
	description="Usando a CLI do Microsoft Azure para Mac, Linux e Windows com o Gerenciador de Recursos do Azure."
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="virtual-machines"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services"" ms.tgt\_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="06/09/2015" ms.author="danlep"/>

# Usar a CLI do Azure para MAC, Linux e Windows com o Gerenciador de Recursos do Azure

> [AZURE.SELECTOR]
- [Azure PowerShell](../powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)


Este artigo descreve como criar, gerenciar e excluir recursos e VMs do Azure usando a CLI do Azure para Mac, Linux e Windows usando o modo Gerenciador de Recursos do Azure.

>[AZURE.NOTE]Para criar e gerenciar recursos do Azure na linha de comando, você precisará de uma conta do Azure ([avaliação gratuita aqui](http://azure.microsoft.com/pricing/free-trial/)). Você também precisará [instalar a CLI do Azure](../xplat-cli-install.md), e [fazer logon para usar recursos do Azure associados à sua conta](../xplat-cli-connect.md). Se você tiver feito isso, você está pronto para continuar.

## Recursos do Azure

Use o Gerenciador de Recursos do Azure para gerenciar um grupo de _recursos_ (entidades gerenciadas pelo usuário, como máquina virtual, servidor de banco de dados, banco de dados ou site) como uma unidade lógica única ou um _grupo de recursos_. Você pode criar, gerenciar e excluir esses recursos imperativamente na linha de comando, assim como no modo asm.

Usando o modo Gerenciador de Recursos do Azure, você também pode gerenciar os recursos do Azure de maneira _declarativa_ descrevendo a estrutura e as relações de um grupo implantável de recursos em *modelos* JSON. O modelo descreve os parâmetros que podem ser preenchidos, ou embutidos durante a execução de um comando, ou armazenados em arquivo JSON azuredeploy-parameters.json separado. Isso permite criar facilmente novos recursos usando-se o mesmo modelo simplesmente fornecendo parâmetros diferentes. Por exemplo, um modelo que cria um site terá parâmetros para o nome do site, a região do site em que estará localizado e outros parâmetros comuns.

Quando um modelo é usado para modificar ou criar um grupo, uma _implantação_ é criada e, então, aplicada ao grupo. Para obter mais informações sobre o Gerenciador de Recursos do Azure, visite [Visão geral do Gerenciador de Recursos do Azure](../resource-group-overview.md).

## Autenticação

Trabalhar com o Gerenciador de Recursos do Azure por meio da CLI do Azure exige uma autenticação no Microsoft Azure usando uma conta corporativa ou de estudante. A autenticação usando um certificado instalado por meio de um arquivo .publishsettings não funcionará.

Para obter mais informações sobre a autenticação usando uma conta de trabalho ou escolar, consulte [Conectar-se a uma assinatura do Azure a partir da CLI do Azure](../xplat-cli-connect.md).

> [AZURE.NOTE]Como você usa uma conta de trabalho ou escolar, que é gerenciada pelo Active Directory do Azure, você também pode usar o controle de acesso de Azure Role-Based (RBAC) para gerenciar o acesso e uso de recursos do Azure. Para obter detalhes, consulte [Gerenciar e auditar o acesso a recursos](../resource-group-rbac.md)

## Definindo o modo Gerenciador de Recursos do Azure

Como o modo Gerenciador de Recursos do Azure não está habilitado por padrão, você deve usar o comando a seguir para habilitar os comandos do Gerenciador de Recursos da CLI do Azure.

	azure config mode arm

>[AZURE.NOTE]O modo do Gerenciador de Recursos do Azure e o modo do Gerenciamento de Serviços do Azure são mutuamente exclusivos. Ou seja, recursos criados em um modo não podem ser gerenciados no outro modo.

## Encontrando os locais

A maioria dos comandos do Gerenciador de Recursos do Azure precisa de um local válido de onde criar ou localizar um recurso. Você pode encontrar todos os locais disponíveis usando o comando a seguir.

	azure location list

Ele listará locais específicos para regiões como "Oeste dos EUA", "Leste dos EUA" e assim por diante.

## Criando grupo de recursos

Um grupo de recursos é um agrupamento lógico de rede, armazenamento e outros recursos. Quase todos os comandos no modo Gerenciador de Recursos do Azure precisam de um grupo de recursos. Você pode criar um grupo de recursos denominado _testrg_, por exemplo, usando o comando a seguir.

	azure group create -n "testrg" -l "West US"

Você pode começar a adicionar recursos a este grupo logo em seguida e usá-lo para configurar uma nova máquina virtual.

## Criando máquinas virtuais

Há duas maneiras para criar máquinas virtuais no modo Gerenciador de Recursos do Azure:

1. Usando os comandos individuais de CLI do Azure.
2. Usando modelos de grupo de recursos.

Certifique-se de criar pelo menos um grupo de recursos antes de iniciar qualquer um desses métodos.

### Usando os comandos individuais de CLI do Azure

Essa é a abordagem básica para configurar e criar uma máquina virtual de acordo com suas necessidades. No modo Gerenciador de Recursos do Azure, você precisará configurar alguns recursos obrigatórios, como a rede, para poder usar o comando **vm create**.

>[AZURE.NOTE]Se estiver criando recursos pela primeira vez na linha de comando para a sua assinatura, você poderá ser solicitado a fazer o registro para determinados provedores de recursos. Se isso acontecer, é fácil registrar o provedor e repetir o comando com falha novamente, conforme mostrado no próximo exemplo.
>
> `azure provider register Microsoft.Storage`
>
> Você pode descobrir a lista de provedores registrados para sua assinatura executando o comando a seguir.
>
> `azure provider list`


#### Criando um recurso de IP público.

Você precisará criar um IP público para que você possa SSH em sua nova máquina virtual para qualquer trabalho significativo. Criar um IP público é simples. O comando precisa de um grupo de recursos, um nome para o recurso IP público e um local, nessa ordem.

	azure network public-ip create "testrg" "testip" "westus"

#### Criando um recurso de Placa de interface de rede

A placa de interface de rede ou NIC precisa, primeiro, de uma sub-rede e uma rede virtual para ser criada. Crie uma rede virtual em um local e grupo de recursos específicos usando o comando **network vnet create**.

	azure network vnet create "testrg" "testvnet" "westus"

Em seguida, você pode criar uma sub-rede nessa rede virtual usando o comando **network vnet subnet create**.

	azure network vnet subnet create "testrg" "testvnet" "testsubnet"

Você deve ser capaz de criar uma NIC usando esses recursos com o comando **network nic create**.

	azure network nic create "testrg" "testnic" "westus" -k "testsubnet" -m "testvnet" -p "testip"

>[AZURE.NOTE]Embora seja opcional, é muito importante passar o nome do IP público como um parâmetro para o comando **network nic create**, pois isso associa o NIC para esse IP, que será posteriormente usado para SSH na máquina virtual criada usando este NIC.

Para obter mais informações sobre os comandos **network**, consulte a ajuda de linha de comando ou [Usando a CLI do Azure com o Gerenciamento de Recursos do Azure](azure-cli-arm-commands.md).

#### Localizando a imagem do sistema operacional

Atualmente, você pode localizar um sistema operacional somente com base no editor de imagem. Em outras palavras, você deve executar o comando a seguir para localizar uma lista de editores de imagem de sistema operacional no local desejado.

	azure vm image list-publishers "westus"

Em seguida, escolha um editor na lista e encontre a lista de imagens por esse editor executando o comando a seguir.

	azure vm image list "westus" "CoreOS"

Por fim, escolha uma imagem de sistema operacional na lista que se parece com o exemplo a seguir.

	info:    Executing command **vm image list**
	warn:    The parameters --offer and --sku if specified will be ignored
	+ Getting virtual machine image offers (Publisher: "Canonical" Location: "westus")
	data:    Publisher  Offer        Sku          Version          Location  Urn

	data:    ---------  -----------  -----------  ---------------  --------- ----------------------------------------
	data:    CoreOS     CoreOS       Alpha        475.1.0          westus    CoreOS:CoreOS:Alpha:475.1.0
	data:    CoreOS     CoreOS       Alpha        490.0.0          westus    CoreOS:CoreOS:Alpha:490.0.0

Salve o nome URN da imagem que deseja carregar na sua máquina virtual. Você o usará mais tarde no artigo.

#### Criando uma máquina virtual

Agora você está pronto para criar uma máquina virtual executando o comando **vm create** e passando as informações necessárias. É opcional passar o IP público neste estágio, pois a NIC já tem essas informações. O comando pode ser semelhante ao exemplo a seguir, onde _testvm_ é o nome da máquina virtual criada no grupo de recursos _testrg_.

	azure-cli@0.8.0:/# azure vm create "testrg" "testvm" "westus" "Linux" -Q "CoreOS:CoreOS:Alpha:660.0.0" -u "azureuser" -p "Pass1234!" -N "testnic"
	info:    Executing command vm create
	+ Looking up the VM "testvm"
	info:    Using the VM Size "Standard_A1"
	info:    The [OS, Data] Disk or image configuration requires storage account
	+ Retrieving storage accounts
	info:    Could not find any storage accounts in the region "westus", trying to create new one
	+ Creating storage account "cli02f696bbfda6d83414300" in "westus"
	+ Looking up the storage account cli02f696bbfda6d83414300
	+ Looking up the NIC "testnic"
	+ Creating VM "testvm"
	info:    vm create command OK

Você deve ser capaz de iniciar essa máquina virtual executando o comando a seguir.

	azure vm start "testrg" "testvm"

Em seguida, faça SSH nela usando o comando **ssh username@ipaddress**. Para pesquisar rapidamente o endereço IP do seu recurso IP público, use o comando a seguir.

	azure network public-ip show "testrg" "testip"

Gerenciar essa máquina virtual é fácil com os comandos **vm**. Para obter mais informações, visite [Usando a CLI do Azure com o Gerenciamento de Recursos do Azure](azure-cli-arm-commands.md).

### Atalho vm quick-create

O novo atalho **vm quick-create** remove a maioria das etapas do método obrigatório da criação da VM. Isso é útil quando você deseja experimentar a criação de máquinas virtuais simples ou se você não está preocupado(a) com as configurações de rede. É um comando interativo, e você precisa descobrir apenas a URN da imagem do sistema operacional antes de executá-lo.

	azure-cli@0.8.0:/# azure vm quick-create
	info:  Executing command vm quick-create
	Resource group name: CLIRG
	Virtual machine name: myqvm
	Location name: westus
	Operating system Type [Windows, Linux]: Linux
	ImageURN (format: "publisherName:offer:skus:version"): CoreOS:CoreOS:Alpha:660.0.0
	User name: azureuser
	Password: ********
	Confirm password: ********

A CLI do Azure criará uma máquina virtual com o tamanho da VM padrão. Ela também criará uma conta de armazenamento, uma NIC, uma rede virtual e sub-rede e um IP público. Você pode fazer SSH na máquina virtual usando o IP público quando ele é inicializado.

### Usando modelos de grupo de recursos

#### Localizando e configurando um modelo de grupo de recursos

1. Ao trabalhar com modelos, você pode criar o seu próprio modelo, ou usar um da Galeria de Modelos, ou usar os modelos disponíveis em [GitHub](https://github.com/azurermtemplates/azurermtemplates). Para começar, vamos usar um modelo chamado CoreOS.CoreOSStable.0.2.40-preview da Galeria de Modelos. Para listar os modelos disponíveis da galeria, use o comando a seguir. Como há milhares de modelos disponíveis, não se esqueça de paginar os resultados, ou usar **grep** ou **findstr** (no Windows), ou o comando de pesquisa de cadeia de caracteres favorito para localizar modelos interessantes. Como alternativa, você pode usar a opção **--json** e baixar a lista inteira no formato JSON para facilitar a pesquisa.

		azure group template list

	A resposta listará o publicador e o nome do modelo e parecerá semelhante ao exemplo a seguir (embora haja muito mais).

		data:    Publisher               Name
		data:    ----------------------------------------------------------------------------
		data:    CoreOS                  CoreOS.CoreOSStable.0.2.40-preview
		data:    CoreOS                  CoreOS.CoreOSAlpha.0.2.39-preview
		data:    SUSE                    SUSE.SUSELinuxEnterpriseServer12.2.0.36-preview
		data:    SUSE                    SUSE.SUSELinuxEnterpriseServer11SP3PremiumImage0.2.54-preview

2. Para exibir os detalhes do modelo, use o comando a seguir.

		azure group template show CoreOS.CoreOSStable.0.2.40-preview

	Isso retornará informações descritivas sobre o modelo. O modelo que estamos usando criará uma máquina virtual Linux.

3. Depois de selecionar um modelo, você poderá baixá-lo usando o comando a seguir.

		azure group template download CoreOS.CoreOSStable.0.2.40-preview

	O download de um modelo permite personalizá-lo para mais bem atender às suas necessidades. Por exemplo, adicionando outro recurso ao modelo.

	>[AZURE.NOTE]Se você modificar o modelo, use o comando `azure group template validate` para validar o modelo antes de usá-lo a fim de criar ou modificar um grupo de recursos existente.

4. Para configurar o modelo de grupo de recursos para seu uso, abra o arquivo de modelo em um editor de texto. Observe os **parâmetros** da coleção JSON na parte superior. Ela contém uma lista de parâmetros esperados por esse modelo para criar os recursos descritos pelo modelo. Alguns parâmetros podem ter valores padrão, enquanto outros especificam o tipo do valor ou um intervalo de valores permitidos.

	Ao usar um modelo, você pode fornecer parâmetros como parte dos parâmetros de linha de comando ou especificando um arquivo contendo os valores de parâmetro. Também é possível gravar nos campos de **valor** diretamente na seção **parâmetros** no modelo, embora isso tornaria o modelo totalmente associado a uma determinada implantação sem poder ser facilmente reutilizado. De qualquer maneira, os parâmetros devem estar no formato JSON e você deve fornecer seus próprios valores para as chaves que não têm valores padrão.

	Por exemplo, para criar um arquivo que contém os parâmetros para o modelo CoreOS.CoreOSStable.0.2.40-preview, use os dados a seguir para criar um arquivo chamado params.json. Substitua os valores usados neste exemplo com seus próprios valores. O **Local** deve especificar uma região do Azure próxima de você, como **Norte da Europa** ou **Centro-Sul dos Estados Unidos** (Este exemplo usa o **Oeste dos EUA**).

		{
		  "newStorageAccountName": {
			"value": "testStorage"
		  },
		  "newDomainName": {
			"value": "testDomain"
		  },
		  "newVirtualNetworkName": {
			"value": "testVNet"
		  },
		  "vnetAddressSpace": {
			"value": "10.0.0.0/11"
		  },
		  "hostName": {
			"value": "testHost"
		  },
		  "userName": {
			"value": "azureUser"
		  },
		  "password": {
			"value": "Pass1234!"
		  },
		  "location": {
			"value": "West US"
		  },
		  "hardwareSize": {
			"value": "Medium"
		  }
	    }

5. Depois de salvar o arquivo params.json, use o comando a seguir para criar um novo grupo de recursos com base no modelo. O parâmetro `-e` especifica o arquivo params.json criado na etapa anterior. Substitua **testRG** pelo nome do grupo que deseja usar, e **testDeploy** pelo nome da sua implantação. O local deve ser o mesmo que o especificado no seu arquivo de parâmetro do modelo params.json.

		azure group create "testRG" "West US" -f CoreOS.CoreOSStable.0.2.40-preview.json -d "testDeploy" -e params.json

	Esse comando retornará OK depois que a implantação for carregada, mas antes da implantação ser aplicada aos recursos do grupo. Para verificar o status da implantação, use o comando a seguir.

		azure group deployment show "testRG" "testDeploy"

	O **ProvisioningState** mostra o status da implantação.

	Se sua implantação foi bem-sucedida, você verá uma saída semelhante ao exemplo a seguir.

		azure-cli@0.8.0:/# azure group deployment show testRG testDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Running
		data:    Timestamp          : 2015-04-27T07:49:18.5237635Z
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ----------------
		data:    newStorageAccountName  String        testStorage
		data:    newDomainName          String        testDomain
		data:    newVirtualNetworkName  String        testVNet
		data:    vnetAddressSpace       String        10.0.0.0/11
		data:    hostName               String        testHost
		data:    userName               String        azureUser
		data:    password               SecureString  undefined
		data:    location               String        West US
		data:    hardwareSize           String        Medium
		info:    group deployment show command OK

	>[AZURE.NOTE]Se você perceber que sua configuração não está correta e precisar parar uma implantação em execução há muito tempo, use o comando a seguir.
	>
	> `azure group deployment stop "testRG" "testDeploy"`
	>
	> Se você não fornecer um nome de implantação, será criado automaticamente um com base no nome do arquivo de modelo. Ele será retornado como parte da saída do comando `azure group create`.

6. Para exibir o grupo, use o comando a seguir.

		azure group show "testRG"

	Esse comando retorna informações sobre os recursos do grupo. Se tiver vários grupos, você poderá usar o comando `azure group list` para recuperar uma lista de nomes de grupo e, em seguida, usar `azure group show` para exibir detalhes de um grupo específico.

7. Você também pode usar os modelos mais recentes diretamente no GitHub, em vez de baixar a biblioteca de modelos. Abra [GitHub.com](http://www.github.com) e procure por AzureRmTemplates. Selecione o repositório AzureRmTemplates e procure por quaisquer modelos que você achar interessantes, por exemplo, _101-simple-vm-from-image_. Se você clicar no modelo, verá que ele contém azuredeploy.json, entre outros arquivos. Este é o modelo que você deseja usar em seu comando usando uma opção **--template-url**. Abra-o no modo _bruto_ e copie a URL que aparece na barra de endereços do navegador. Você pode usar essa URL diretamente para criar uma implantação, em vez de baixar uma biblioteca de modelos, usando um comando semelhante ao exemplo a seguir.

		azure group deployment create "testDeploy" -g "testResourceGroup" --template-uri https://raw/githubusercontent.com/azurermtemplates/azurermtemplates/master/101-simple-vm-from-image/azuredeploy.json

	> [AZURE.NOTE]É importante abrir o modelo json no modo _bruto_. A URL que aparece na barra de endereços do navegador é diferente daquela que aparece no modo normal. Para abrir o arquivo no modo _raw_ ao exibir o arquivo no GitHub, no canto superior direito, clique em **Raw**.

#### Trabalhando com recursos

Embora modelos permitam declarar alterações feitas na configuração que afetam todo o grupo, às vezes você precisa trabalhar com apenas um recurso específico. Você pode fazer isso usando os comandos `azure resource`.

> [AZURE.NOTE]Ao usar os comandos `azure resource` em vez do comando `list` , você deve especificar a versão da API do recurso com o qual estiver trabalhando usando o parâmetro `-o`. Se você não tiver certeza sobre a versão da API a ser usada, consulte o arquivo de modelo e localize o campo **apiVersion** do recurso.

1. Para listar todos os recursos em um grupo, use o comando a seguir.

		azure resource list "testRG"

2. Para exibir recursos individuais, dentro do grupo, use o comando a seguir.

		azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01"

	Observe o parâmetro **Microsoft.ClassicCompute/virtualMachines**. Isso indica o tipo do recurso sobre o qual você está solicitando informações. Ao examinar o arquivo de modelo baixado anteriormente, você observará que esse mesmo valor é usado para definir o tipo do recurso da máquina virtual descrito no modelo.

	Esse comando retorna informações relacionadas à máquina virtual.

3. Durante a exibição de detalhes sobre um recurso, costuma ser útil usar o parâmetro `--json`, pois isso torna a saída mais legível, uma vez que alguns valores são estruturas aninhadas ou coleções. O exemplo a seguir demonstra o retorno dos resultados do comando **show** como um documento JSON.

		azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01" --json

	>[AZURE.NOTE]Você pode salvar os dados JSON em arquivo usando o caractere para enviar a saída ao arquivo. Por exemplo:
	>
	> `azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01" --json > myfile.json`

4. Para excluir um recurso existente, use o comando a seguir.

		azure resource delete "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01"

## Registro em log

Para exibir informações registradas em log sobre operações realizadas em um grupo, use o comando `azure group log show`. Por padrão, ele listará a última operação realizada no grupo. Para exibir todas as operações, use o parâmetro opcional `--all`. Para a última implantação, use `--last-deployment`. Para uma implantação específica, use `--deployment` e especifique o nome da implantação. O exemplo a seguir retorna um log de todas as operações realizadas no grupo MyGroup.

	azure group log show mygroup --all

## Próximas etapas

* Para obter informações sobre como usar a CLI (Interface de Linha de Comando) do Azure , consulte [Instalar e configurar a CLI do Azure][clisetup].
* Para obter informações sobre como trabalhar com o Gerenciador de Recursos do Azure usando o Azure PowerShell, consulte [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](../powershell-azure-resource-manager.md).
* Para obter informações sobre como trabalhar com o Gerenciador de Recursos do Azure no Portal do Azure, consulte [Usando grupos de recursos para gerenciar os recursos do Azure][psrm]

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[portal]: https://manage.windowsazure.com/
[clisetup]: ../xplat-cli.md
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

<!---HONumber=July15_HO5-->