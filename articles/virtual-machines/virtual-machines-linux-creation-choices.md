<properties
	pageTitle="Diferentes maneiras de criar uma VM do Linux | Microsoft Azure"
	description="Lista as diferentes maneiras de criar uma máquina virtual do Linux no Azure e fornece links para mais instruções"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="04/12/2016"
	ms.author="iainfou"/>

# Diferentes maneiras de criar uma máquina virtual do Linux com o Gerenciador de Recursos

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

O Azure oferece maneiras diferentes de criar uma VM que se adapte a usuários e finalidades diferentes. Este artigo resume essas diferenças e as opções que você pode utilizar para criar máquinas virtuais Linux.

Os modelos do Azure Resource Manager também fornecem um modo de criar e gerenciar uma máquina virtual e seus recursos diferentes como uma unidade de implantação lógica. Para saber mais sobre o Gerenciador de Recursos do Azure e como gerenciar recursos como uma unidade, confira o [visão geral](../resource-group-overview.md).

## Opções da ferramenta

### Shell de comando: CLI do Azure 

Na CLI, use a interface de linha de comando do Azure. Consulte estes tutoriais para usar a CLI do Azure:

* [Criar uma VM do Linux na CLI para desenvolvimento e teste](virtual-machines-linux-quick-create-cli.md) 

* [Criar uma VM do Linux protegida usando um modelo do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

### GUI: portal do Azure

A interface gráfica do usuário do [portal do Azure](https://portal.azure.com) é uma maneira fácil de testar uma máquina virtual, particularmente se você estiver começando a usar o Azure. Use o portal do Azure para criar a VM:

* [Criar uma máquina virtual executando o Linux usando o portal do Azure](virtual-machines-linux-portal-create.md) 

## Sistema operacional e opções de imagem

Escolha uma imagem com base no sistema operacional que você deseja executar. A Azure e seus parceiros oferecem muitas imagens, algumas delas incluem aplicativos e ferramentas. Ou use uma das suas próprias imagens.

### Imagens do Azure

Em todos os artigos acima, você pode facilmente usar uma imagem existente do Azure para criar uma máquina virtual e personalizá-la para a rede, para balanceamento de carga e muito mais. O portal oferece o Azure Marketplace para imagens fornecidas pelo Azure. Você pode obter listas semelhantes usando a linha de comando. Por exemplo, na CLI do Azure, execute `azure vm image list` para obter uma lista de todas as imagens disponíveis, por local e editor. Consulte [Navegar e selecionar imagens da máquina virtual do Azure com a CLI do Azure](virtual-machines-linux-cli-ps-findimage.md).

### Usar sua própria imagem

Use uma imagem com base em uma máquina virtual do Azure existente *capturando* essa VM ou carregue uma imagem de sua preferência, armazenada em um VHD (disco rígido virtual). Para obter mais informações, consulte:

* [Distribuições endossadas pelo Azure](virtual-machines-linux-endorsed-distros.md)

* [Informações para distribuições não endossadas](virtual-machines-linux-create-upload-generic.md)

* [Como capturar uma máquina virtual do Linux como um modelo do Gerenciador de Recursos](virtual-machines-linux-capture-image.md).

## Próximas etapas

* Experimente um dos tutoriais para criar uma VM do Linux por meio do [portal](virtual-machines-linux-portal-create.md), com a [CLI](virtual-machines-linux-quick-create-cli.md) ou usando um modelo do [Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md).

* Após criar uma VM do Linux, você poderá [adicionar um disco de dados](virtual-machines-linux-add-disk.md) facilmente.

<!---HONumber=AcomDC_0420_2016-->