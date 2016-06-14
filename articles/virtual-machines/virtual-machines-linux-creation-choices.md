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

O Azure oferece maneiras diferentes de criar uma VM usando o modelo de implantação do Resource Manager para se adaptar a diferentes usuários e finalidades. Este artigo resume essas diferenças e as opções que você pode fazer para criar máquinas virtuais (VMs) do Linux.

## Opções da ferramenta

### Shell de comando: CLI do Azure 

Na CLI, use a interface de linha de comando do Azure. Você pode ler mais sobre [como instalar a CLI do Azure](../xplat-cli-install.md) via npm, o contêiner do Docker, ou instalar um script. Os tutoriais a seguir fornecem exemplos sobre como usar a CLI do Azure:

* [Criar uma VM do Linux na CLI do Azure para desenvolvimento e teste](virtual-machines-linux-quick-create-cli.md) 

* [Criar uma VM do Linux protegida usando um modelo do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

* [Criar uma VM Linux a partir do zero usando a CLI do Azure](virtual-machines-linux-create-cli-complete.md)

### GUI: portal do Azure

A interface gráfica do usuário do [portal do Azure](https://portal.azure.com) é uma maneira fácil de experimentar uma VM, especialmente se você estiver começando a usar o Azure, uma vez que não há nada para instalar em seu sistema. Use o portal do Azure para criar a VM:

* [Criar uma máquina virtual executando o Linux usando o portal do Azure](virtual-machines-linux-quick-create-portal.md) 

## Sistema operacional e opções de imagem

Nos dois métodos, você escolhe uma imagem com base no sistema operacional que deseja executar. A Azure e seus parceiros oferecem muitas imagens, algumas delas incluem aplicativos e ferramentas pré-instalados. Ou você pode carregar uma de suas próprias imagens.

### Imagens do Azure

Em todos os artigos acima, você pode usar facilmente uma imagem existente do Azure para criar uma VM e personalizá-la para a rede, o balanceamento de carga e muito mais. O portal oferece o Azure Marketplace para imagens fornecidas pelo Azure. Você pode obter listas semelhantes usando a linha de comando. Por exemplo, na CLI do Azure, execute `azure vm image list` para obter uma lista de todas as imagens disponíveis, por local e editor. Consulte [Navegar e selecionar imagens da máquina virtual do Azure com a CLI do Azure](virtual-machines-linux-cli-ps-findimage.md) para obter exemplos sobre como navegar e usar as imagens disponíveis.

### Usar sua própria imagem

Se você precisar de personalizações específicas, poderá usar uma imagem com base em uma VM do Azure existente *capturando* essa VM ou carregar uma imagem própria, armazenada em um disco rígido virtual (VHD). Para obter mais informações sobre as distribuições com suporte e como usar suas próprias imagens, confira os artigos a seguir:

* [Distribuições endossadas pelo Azure](virtual-machines-linux-endorsed-distros.md)

* [Informações para distribuições não endossadas](virtual-machines-linux-create-upload-generic.md)

* [Como capturar uma máquina virtual do Linux como um modelo do Resource Manager](virtual-machines-linux-capture-image.md).

## Próximas etapas

* Experimente um dos tutoriais para criar uma VM do Linux a partir do [portal](virtual-machines-linux-quick-create-portal.md) com a [CLI](virtual-machines-linux-quick-create-cli.md) ou usando um modelo do [Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md).

* Após criar uma VM do Linux, você poderá [adicionar um disco de dados](virtual-machines-linux-add-disk.md) facilmente.

* Etapas rápidas para [redefinir uma senha ou chaves SSH e gerenciar os usuários](virtual-machines-linux-using-vmaccess-extension.md)

<!---HONumber=AcomDC_0608_2016-->