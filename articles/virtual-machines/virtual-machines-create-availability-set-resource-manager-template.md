<properties 
	pageTitle="Criar um conjunto de disponibilidade usando modelos do Gerenciador de Recursos do Azure" 
	description="Descreve como usar o modelo de conjunto de disponibilidade e inclui a sintaxe do modelo" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="kathydav"/>

# Criar um conjunto de disponibilidade usando modelos do Gerenciador de Recursos do Azure

Você pode criar facilmente um conjunto de disponibilidade para uma máquina virtual usando o PowerShell do Azure ou a CLI (Linha de Comando do Azure) e um modelo do Gerenciador de Recursos. Este modelo cria um conjunto de disponibilidade.
 
Antes de mergulhar de cabeça, verifique se você tem o PowerShell e a CLI do Azure configurados e prontos.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up](../../includes/xplat-getting-set-up.md)]


## Criar um conjunto de disponibilidade usando um modelo do Gerenciador de Recursos

Siga estas etapas para criar um conjunto de disponibilidade para uma máquina virtual usando um modelo do Gerenciador de Recursos no repositório de modelos do Github com o PowerShell do Azure.

### Etapa 1: baixar o arquivo JSON

Designe uma pasta local como a localização dos arquivos do modelo JSON e crie-a (por exemplo, C:\Azure\Templates\availability).

Substitua o nome de pasta e copie e execute estes comandos.

	$folderName="<folder name, such as C:\Azure\Templates\availability>"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-2-FDs-no-resource-loops/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath) 

### Etapa 2: reunir os detalhes dos parâmetros necessários

Quando usar um modelo, você terá que fornecer detalhes como local, nome do conjunto e assim por diante. Para descobrir quais parâmetros são necessários para um modelo, siga um destes procedimentos:

- Examine a lista de parâmetros [aqui](http://azure.microsoft.com/documentation/templates/201-2-vms-2-FDs-no-resource-loops/).
- Abra o arquivo JSON em uma ferramenta ou editor de texto de sua escolha. Procure a seção "parâmetros" na parte superior do arquivo, que lista o conjunto de parâmetros necessários ao modelo para configurar a máquina virtual. 

Reúna as informações necessárias para tê-las à disposição para inserção. Quando você executar o comando para implantar o modelo, receberá uma solicitação para fornecer informações.

### Etapa 3: criar o conjunto de disponibilidade.

As seções a seguir mostram como usar o PowerShell do Azure ou a CLI do Azure para fazer isso.

### Usar PowerShell do Azure

Preencha o nome da implantação do Azure, o nome do grupo de recursos, o local do Azure e a pasta para o arquivo JSON salvo e, em seguida, execute estes comandos.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\availability>" 
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Ao executar o comando **New-AzureResourceGroupDeployment**, você será solicitado a fornecer os valores para os parâmetros na seção **"parameters"** do arquivo JSON. Depois que você fizer isso, o comando criará o grupo de recursos e o conjunto de disponibilidade.

Aqui está um exemplo do conjunto de comandos do PowerShell para o modelo.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$folderName="C:\Azure\Templates[thing]"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Você verá algo semelhante ao item a seguir.

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	dnsNameForPublicIP: 131.107.89.211
	adminUserName: WebAdmin1
	adminPassword: *******
	vmSourceImageName: a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd
	...

Para remover esse grupo de recursos e todos os seus recursos (a conta de armazenamento, a máquina virtual e a rede virtual), use este comando.

	Remove-AzureResourceGroup –Name "<resource group name>"


## Usar a CLI do Azure

Siga estas etapas para criar o conjunto de disponibilidade usando um modelo do Gerenciador de Recursos no repositório de modelos do Github com um comando da CLI do Azure.

	azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-2-FDs-no-resource-loops/azuredeploy.json





 

<!---HONumber=58_postMigration-->