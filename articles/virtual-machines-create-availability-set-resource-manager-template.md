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
	ms.date="04/20/2015" 
	ms.author="kathydav"/>

# Criar um conjunto de disponibilidade usando modelos do Gerenciador de Recursos do Azure

Você pode criar facilmente um conjunto de disponibilidade para uma máquina virtual usando o PowerShell do Azure ou o xplat-cli e um modelo do Gerenciador de Recursos. Este modelo cria um conjunto de disponibilidade.
 
Antes de começar, verifique se você tem o PowerShell do Azure e o xplat-cli configurados e prontos.

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up](../includes/xplat-getting-set-up.md)]


## [fazer algo] com um modelo do Gerenciador de Recursos usando o PowerShell do Azure

Siga estas etapas para [fazer algo] usando um modelo do Gerenciador de Recursos no repositório de modelos do Github com o PowerShell do Azure.

### Etapa 1: baixar o arquivo JSON

Designe uma pasta local como a localização dos arquivos do modelo JSON e crie-a \(por exemplo, C:\\Azure\\Templates\[item\]\).

Substitua o nome de pasta e copie e execute estes comandos.

	$folderName="<folder name, such as C:\Azure\Templates\[thing]>"
	$webclient = New-Object System.Net.WebClient
	$url = "[Writers: add the URL to the RAW version of the target template in GitHub]"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath) 

### Etapa 2: \(opcional\) exibir os parâmetros

Ao [fazer algo] com um modelo, você deve especificar um conjunto de parâmetros de configuração. Para ver os parâmetros que você precisa especificar para o modelo em um arquivo JSON local antes de executar o comando para criar a máquina virtual, abra o arquivo JSON em um editor de texto ou ferramenta de sua escolha. Procure a seção "parâmetros" na parte superior do arquivo, que lista o conjunto de parâmetros necessários ao modelo para configurar a máquina virtual. Aqui está a seção **"parâmetros"** do modelo azuredeploy.json:

[Observação para escritores: cole na seção "parâmetros" do arquivo azuredeploy.json e formate como código.]

### Etapa 3: obter [informações necessárias para concluir o modelo].

[Observação para escritores: seção opcional para obter valores de parâmetros, se necessário.]

### Etapa 4: [fazer algo] com o modelo.

Preencha o nome da implantação do Azure, o nome do grupo de recursos, o local do Azure e a pasta para o arquivo JSON salvo e, em seguida, execute estes comandos.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\[thing]>" 
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Ao executar o comando **New-AzureResourceGroupDeployment**, você será solicitado a fornecer os valores para os parâmetros na seção **"parâmetros"** do arquivo JSON. Depois que você fizer isso, o comando criará o grupo de recursos e o conjunto de disponibilidade.

Aqui está um exemplo do conjunto de comandos do PowerShell para o modelo.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$folderName="C:\Azure\Templates\[thing]"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Você verá algo semelhante ao item a seguir.

[Observação para escritores: cole na exibição do PowerShell para os primeiros parâmetros solicitados, substituindo isto:]

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	dnsNameForPublicIP: 131.107.89.211
	adminUserName: WebAdmin1
	adminPassword: *******
	vmSourceImageName: a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd
	...

Para remover esse grupo de recursos e todos os seus recursos \(a conta de armazenamento, a máquina virtual e a rede virtual\), use este comando.

	Remove-AzureResourceGroup –Name "<resource group name>"


## [fazer algo] com um modelo do Gerenciador de Recursos usando o xplat-cli

Siga estas etapas para [fazer algo] usando um modelo do Gerenciador de Recursos no repositório de modelos do Github com comandos do xplat-cli.

### Etapa 1: baixar o arquivo JSON para o modelo.

Designe uma pasta local como a localização dos arquivos do modelo JSON e crie-a \(por exemplo, C:\\Azure\\Templates\[item\]\).

Preencha o nome da pasta e execute estes comandos.

[comandos do xplat para baixar o arquivo de modelo]

### Etapa 2: \(opcional\) exibir os parâmetros do modelo.

Ao [fazer algo] com um modelo, você deve especificar um conjunto de parâmetros de configuração. Para ver os parâmetros que você precisa especificar para o modelo em um arquivo JSON local antes de executar o comando para criar a máquina virtual, abra o arquivo JSON em um editor de texto ou ferramenta de sua escolha. Procure a seção "parâmetros" na parte superior do arquivo, que lista o conjunto de parâmetros necessários ao modelo para configurar a máquina virtual. Aqui está a seção **"parâmetros"** do modelo azuredeploy.json:

[Observação para escritores: cole na seção "parâmetros" do arquivo azuredeploy.json e formate como código.]

### Etapa 3: obter [informações necessárias para concluir o modelo].

[Observação para escritores: seção opcional para obter valores de parâmetros, se necessário.]

### Etapa 4: [fazer algo] com o modelo.

Preencha as \[informações necessárias} e, em seguida, execute estes comandos.

[comandos do xplat para executar o arquivo de modelo]

[explicação de como o xplat executa o modelo]


Aqui está um exemplo de um comando do xplat-cli definido para o modelo.

[exemplo de comando do xplat]

Você verá algo semelhante ao item a seguir.

[Observação para escritores: cole na exibição do xplat para os primeiros parâmetros solicitados]


Para remover este grupo de recursos e todos os seus recursos \([itens no grupo de recursos]\), use este comando.

[comando do xplat]




<!--HONumber=52-->
