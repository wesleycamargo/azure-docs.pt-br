<properties
	pageTitle="Implantar farms do SharePoint com os modelos do ARM | Microsoft Azure"
	description="Implante facilmente um farm de três ou nove servidores do SharePoint com modelos do Gerenciador de Recursos e o Portal de Visualização do Azure, Azure PowerShell ou CLI do Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="06/29/2015"
	ms.author="davidmu"/>

# Implantar farms do SharePoint com os modelos do Gerenciador de Recursos do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo aborda como criar um recurso com o modelo de implantação do Gerenciador de Recursos. Você não pode criar esse recurso com o modelo de implantação clássica.

Use as instruções neste artigo para implantar um novo farm de três ou nove servidores do SharePoint Server 2013 usando os modelos de Gerenciador de Recursos.

## Implantar um farm de três servidores do SharePoint

Para um farm básico do SharePoint Server 2013, um modelo do Gerenciador de Recursos cria três máquinas virtuais em uma nova rede virtual em três sub-redes diferentes.

![](./media/virtual-machines-workload-template-sharepoint/three-server-sharepoint-farm.png)

Você pode executar o modelo com o portal de visualização do Azure, o Azure PowerShell ou CLI do Azure.

### Portal de visualização do Azure

Para implantar essa carga de trabalho usando um modelo do Gerenciador de Recursos e o portal de visualização do Azure, clique [aqui](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsharepoint-three-vm%2Fazuredeploy.json).

![](./media/virtual-machines-workload-template-sharepoint/azure-portal-template.png)

1.	Para o painel **Modelo**, clique em **Salvar**.
2.	Clique em **Parâmetros**. No painel **Parâmetros**, insira novos valores, selecione os valores permitidos ou aceite os valores padrão e, em seguida, clique em **OK**.
3.	Se necessário, clique em **Assinatura** e selecione a assinatura correta do Azure.
4.	Clique em **Grupo de recursos** e selecione um grupo de recursos existente. Como alternativa, clique em **Ou criar novo** para criar um novo para essa carga de trabalho.
5.	Se necessário, clique em **Local do grupo de recursos** e selecione o local correto do Azure.
6.	Se for necessário, clique em **Termos legais** para rever os termos e o contrato de utilização do modelo.
7.	Clique em **Criar**.

Dependendo do modelo, pode levar algum tempo para o Azure criar a carga de trabalho. Ao concluir, você terá um novo farm de três servidores do SharePoint em seu grupo de recursos novos ou existentes.

### Azure PowerShell

Antes de começar, verifique se você tem a versão correta do Azure PowerShell instalada, faça logon e alterne para o novo modo do Gerenciador de Recursos. Para obter detalhes, clique [aqui](virtual-machines-deploy-rmtemplates-powershell.md#setting-up-powershell-for-resource-manager-templates).

Preencha o nome de uma implantação do Azure, um novo nome do Grupo de Recursos e um local de datacenter do Azure no seguinte conjunto de comandos. Remova tudo que estiver entre aspas, incluindo os caracteres < and >.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Aqui está um exemplo.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Em seguida, execute o bloco de comando no prompt do Azure PowerShell.

Ao executar o comando **New-AzureResourceGroupDeployment**, você receberá uma solicitação para fornecer os valores para uma série de parâmetros. Depois de especificar todos os valores de parâmetro, o **New-AzureResourceGroupDeployment** criará e configurará as máquinas virtuais.

Quando a execução do modelo for concluída, você terá um novo farm de três servidores do SharePoint em seu novo grupo de recursos.

### CLI do Azure

Antes de começar, verifique se você tem a versão correta do Azure CLI instalada, faça logon e alterne para o novo modo do Gerenciador de Recursos. Para obter detalhes, clique [aqui](virtual-machines-deploy-rmtemplates-azure-cli.md#getting-ready).

Primeiro, crie um novo grupo de recursos. Use o seguinte comando e especifique o nome do grupo e o local do datacenter do Azure no qual você deseja implantar.

	azure group create <group name> <location>

Em seguida, use o seguinte comando e especifique o nome do novo grupo de recursos e o nome de uma implantação do Azure.

	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json <group name> <deployment name>

Aqui está um exemplo.

	azure group create sp3serverfarm eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json sp3serverfarm spdevtest

Ao executar o comando **azure group deployment create**, você receberá uma solicitação para fornecer os valores para uma série de parâmetros. Quando você tiver especificado todos os valores de parâmetro, o Azure criará e configurará as máquinas virtuais.

Agora você tem um novo farm de três servidores do SharePoint em seu novo grupo de recursos.

## Implantar um farm de nove servidores do SharePoint

Para um farm do SharePoint Server 2013 de alta disponibilidade, um modelo do Gerenciador de Recursos cria nove máquinas virtuais em uma nova rede virtual em quatro sub-redes diferentes.

![](./media/virtual-machines-workload-template-sharepoint/nine-server-sharepoint-farm.png)

### Portal de visualização do Azure

Para implantar essa carga de trabalho usando um modelo do Gerenciador de Recursos e o portal de visualização do Azure, clique [aqui](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsharepoint-server-farm-ha%2Fazuredeploy.json).

![](./media/virtual-machines-workload-template-sharepoint/azure-portal-template.png)

1.	Para o painel **Modelo**, clique em **Salvar**.
2.	Clique em **Parâmetros**. No painel **Parâmetros**, insira novos valores, selecione os valores permitidos ou aceite os valores padrão e, em seguida, clique em **OK**.
3.	Se necessário, clique em **Assinatura** e selecione a assinatura correta do Azure.
4.	Clique em **Grupo de recursos** e selecione um grupo de recursos existente. Como alternativa, clique em **Ou criar novo** para criar um novo para essa carga de trabalho.
5.	Se necessário, clique em **Local do grupo de recursos** e selecione o local correto do Azure.
6.	Se for necessário, clique em **Termos legais** para rever os termos e o contrato de utilização do modelo.
7.	Clique em **Criar**.

Dependendo do modelo, pode levar algum tempo para o Azure criar a carga de trabalho. Ao concluir, você terá um novo farm de nove servidores do SharePoint em seu grupo de recursos novos ou existentes.

### Azure PowerShell

Antes de começar, verifique se você tem a versão correta do Azure PowerShell instalada, faça logon e alterne para o novo modo do Gerenciador de Recursos. Para obter detalhes, clique [aqui](virtual-machines-deploy-rmtemplates-powershell.md#setting-up-powershell-for-resource-manager-templates).

Preencha o nome de uma implantação do Azure, um novo nome do Grupo de Recursos e um local de datacenter do Azure no seguinte conjunto de comandos. Remova tudo que estiver entre aspas, incluindo os caracteres < and >.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Aqui está um exemplo.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Em seguida, execute o bloco de comando no prompt de comando do Azure PowerShell.

Ao executar o comando **New-AzureResourceGroupDeployment**, você receberá uma solicitação para fornecer os valores para uma série de parâmetros. Depois de especificar todos os valores de parâmetro, o **New-AzureResourceGroupDeployment** criará e configurará as máquinas virtuais.

Quando a execução do modelo for concluída, você terá um novo farm de nove servidores do SharePoint em seu novo grupo de recursos.

### CLI do Azure

Antes de começar, verifique se você tem a versão correta do Azure CLI instalada, faça logon e alterne para o novo modo do Gerenciador de Recursos. Para obter detalhes, clique [aqui](virtual-machines-deploy-rmtemplates-azure-cli.md#getting-ready).

Primeiro, crie um novo grupo de recursos. Use o seguinte comando e especifique o nome do grupo e o local do datacenter do Azure no qual você deseja implantar.

	azure group create <group name> <location>

Em seguida, use o seguinte comando e especifique o nome do novo grupo de recursos e o nome de uma implantação do Azure.

	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json <group name> <deployment name>

Aqui está um exemplo.

	azure group create sphaserverfarm eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json sphaserverfarm spdevtest

Ao executar o comando **azure group deployment create**, você receberá uma solicitação para fornecer os valores para uma série de parâmetros. Quando você tiver especificado todos os valores de parâmetro, o Azure criará e configurará as máquinas virtuais.

Quando a execução do modelo for concluída, você terá, então, um novo farm de nove servidores do SharePoint Server 2013 em seu novo grupo de recursos.


## Recursos adicionais

[Implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de Recursos do Azure e o Azure PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

[Computação do Azure, provedores de Rede e Armazenamento no Gerenciador de Recursos do Azure](virtual-machines-azurerm-versus-azuresm.md)

[Visão geral do Gerenciador de Recursos do Azure](../resource-group-overview.md)

[Implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de Recursos do Azure e a CLI do Azure](virtual-machines-deploy-rmtemplates-azure-cli.md)

[Documentação de máquinas virtuais](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Como instalar e configurar o Azure PowerShell](../install-configure-powershell.md)

<!---HONumber=Sept15_HO4-->