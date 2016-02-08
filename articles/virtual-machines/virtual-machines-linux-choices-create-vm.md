<properties
	pageTitle="Diferentes maneiras de criar uma VM do Linux | Microsoft Azure"
	description="Lista as diferentes maneiras de criar uma máquina virtual do Linux no Azure e fornece links para mais instruções."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="01/20/2016"
	ms.author="dkshir"/>

# Diferentes maneiras de criar uma Máquina Virtual Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

O Azure oferece maneiras diferentes de criar uma VM que se adapte a usuários e finalidades diferentes. Este artigo resume essas diferenças e as opções que você pode utilizar para criar máquinas virtuais Linux.

Os modelos do Gerenciador de Recursos do Azure também podem ser usados para criar e gerenciar uma máquina virtual e seus recursos diferentes, como uma unidade lógica de implantação. As instruções para essa abordagem são incluídas onde aplicável. Para saber mais sobre o Gerenciador de Recursos do Azure e como gerenciar recursos como uma unidade, confira o [visão geral][].

## Opções da ferramenta

### GUI: O portal clássico do Azure ou portal do Azure

A interface gráfica do usuário do portal é uma maneira fácil de experimentar uma máquina virtual, especialmente se você estiver iniciando no Azure. Use o [portal clássico do Azure](https://manage.windowsazure.com) ou o [portal do Azure](https://portal.azure.com) para criar a VM. Para obter instruções gerais, consulte [Criar uma máquina virtual personalizada][] e selecione qualquer imagem do Linux na **Galeria**. Observe que o [portal clássico do Azure](https://manage.windowsazure.com) cria máquinas virtuais usando o modelo de implantação clássica.

### Shell de comando: CLI do Azure ou PowerShell do Azure

Se você preferir trabalhar em um shell de comando, escolha entre a interface de linha de comando (CLI) do Azure para usuários do Mac, Linux e Windows, ou do Azure PowerShell, que tem os cmdlets do Windows PowerShell para Azure e um console personalizado.

Para criar máquinas virtuais no modelo de implantação do Gerenciador de Recursos usando a CLI do Azure, confira a seção [Criar uma máquina virtual que executa o Linux][]. Siga as guias ou seletores de artigo sobre este artigo para ler as instruções usando o Azure PowerShell e os Modelos.

Para o modelo clássico de implantação, confira as seções [Criar uma máquina virtual Linux personalizada usando a CLI do Azure](virtual-machines-linux-create-custom.md) e [Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Linux][].


### Ambiente de desenvolvimento: Visual Studio

O Visual Studio também dá suporte para criar máquinas virtuais do Azure. Para o modelo clássico de implantação, leia [Criar uma máquina virtual para um site com o Visual Studio][]. Para o modelo de implantação do Gerenciador de Recursos, leia [Implantar recursos do Azure usando bibliotecas de computação, rede e armazenamento do .NET][].


## Sistema operacional e opções de imagem

Escolha uma imagem com base no sistema operacional que você deseja executar. A Azure e seus parceiros oferecem muitas imagens, algumas delas incluem aplicativos e ferramentas. Ou use uma das suas próprias imagens.


### Imagens do Azure

Em todos os artigos acima, você pode facilmente usar uma imagem existente do Azure para criar uma máquina virtual e personalizá-la para a rede, para balanceamento de carga e muito mais. Os portais fornecem uma galeria ou lista de imagens para as imagens fornecidas do Azure. Você pode obter listas semelhantes usando a linha de comando. Por exemplo, na CLI do Azure, execute `azure vm image list` para obter uma lista de todas as imagens disponíveis, por local e editor.


### Usar sua própria imagem

Use uma imagem com base em uma máquina virtual do Azure existente *capturando* essa VM ou carregue uma imagem de sua preferência, armazenada em um disco rígido virtual (VHD). Para o modelo clássico de implantação, confira [Como capturar uma máquina virtual do Linux para usar como um modelo com a CLI][] e [Criando e carregando um disco rígido virtual que contém o sistema operacional Linux][]. Para o modelo de implantação do Gerenciador de Recursos, confira [Como capturar uma máquina virtual do Linux como um modelo do Gerenciador de Recursos](virtual-machines-linux-capture-image-resource-manager.md).

## Próximas etapas

[Fazer logon na máquina virtual][]

[Anexar um disco de dados][]

## Recursos adicionais

[Ambiente de teste de configuração básica][]

[Ambientes de teste de nuvem híbrida do Azure][]

[Comandos equivalentes do Gerenciador de Recursos e de Gerenciamento de Serviços para operações de VM com a CLI do Azure para Mac, Linux e Windows][]

<!-- LINKS -->
[visão geral]: ../resource-group-overview.md

[Create a Virtual Machine Running Windows]: virtual-machines-windows-tutorial.md
[Create a Virtual Machine Running Linux]: virtual-machines-linux-tutorial.md

[Comandos equivalentes do Gerenciador de Recursos e de Gerenciamento de Serviços para operações de VM com a CLI do Azure para Mac, Linux e Windows]: xplat-cli-azure-manage-vm-asm-arm.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and the Azure CLI]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Linux]: virtual-machines-ps-create-preconfigure-linux-vms.md

[How to Create a Custom Virtual Machine Running Linux in Azure]: virtual-machines-linux-create-custom.md
[Como capturar uma máquina virtual do Linux para usar como um modelo com a CLI]: virtual-machines-linux-capture-image.md

[Criando e carregando um disco rígido virtual que contém o sistema operacional Linux]: virtual-machines-linux-create-upload-vhd.md

[Criar uma máquina virtual para um site com o Visual Studio]: virtual-machines-dotnet-create-visual-studio-powershell.md
[Implantar recursos do Azure usando bibliotecas de computação, rede e armazenamento do .NET]: virtual-machines-arm-deployment.md

[Fazer logon na máquina virtual]: virtual-machines-linux-how-to-log-on.md

[Anexar um disco de dados]: virtual-machines-linux-how-to-attach-disk.md

[Ambiente de teste de configuração básica]: virtual-machines-base-configuration-test-environment.md
[Ambientes de teste de nuvem híbrida do Azure]: virtual-machines-hybrid-cloud-test-environments.md

[Criar uma máquina virtual que executa o Linux]: virtual-machines-linux-tutorial.md
[Criar uma máquina virtual personalizada]: virtual-machines-create-custom.md

<!---HONumber=AcomDC_0128_2016-->