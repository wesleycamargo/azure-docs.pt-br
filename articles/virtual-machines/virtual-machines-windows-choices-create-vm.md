<properties
	pageTitle="Diferentes maneiras de criar uma VM do Windows | Microsoft Azure"
	description="Lista as diferentes maneiras de criar uma máquina virtual do Windows com o Gerenciador de Recursos."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="index-page"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="10/22/2015"
	ms.author="cynthn"/>

# Diferentes maneiras de criar uma máquina virtual do Windows com o Gerenciador de Recursos

O Azure oferece diferentes maneiras de criar uma máquina virtual, pois máquinas virtuais são adequadas para diferentes usuários e finalidades. Isso significa que você precisa tomar algumas decisões sobre a máquina virtual e como criá-la. Este artigo fornece um resumo dessas opções e links para instruções.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássica.

Os modelos do Gerenciador de Recursos do Azure foram introduzidos recentemente como uma maneira de criar e gerenciar uma máquina virtual e seus recursos diferentes, como uma unidade lógica de implantação. Instruções para essa abordagem são incluídas abaixo, quando disponíveis. Para saber mais sobre o Gerenciador de Recursos do Azure e como gerenciar recursos como uma unidade, consulte a [Visão geral][].

## Opções da ferramenta

### GUI: Portal de visualização do Azure

A interface gráfica do usuário do portal do Azure é uma maneira fácil de testar uma máquina virtual, especialmente se você estiver começando com o Azure. Usar o Portal de visualização do Azure para criar uma máquina virtual:

[Criar uma máquina virtual executando o Windows][]

### Shell de comando: CLI do Azure ou Azure PowerShell

Se você preferir trabalhar em um shell de comando, escolha entre a interface de linha de comando (CLI) do Azure para usuários do Mac e Linux ou o Azure PowerShell, que tem os cmdlets para o Azure e um console personalizado.

Para a CLI do Azure, consulte:

- [Comandos equivalentes do Gerenciador de Recursos e do Gerenciamento de Serviços para operações de máquina virtual com a CLI do Azure para Mac, Linux e Windows][].
- [Implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de Recursos do Azure e a CLI do Azure][]

Para o Azure PowerShell, consulte:

- [Criar uma VM do Windows com o Gerenciador de Recursos e o PowerShell][]
- [Criar e pré-configurar uma máquina virtual do Windows com o Gerenciador de Recursos e o Azure PowerShell][]
- [Implantar e gerenciar máquinas virtuais usando modelos do PowerShell e do Gerenciador de Recursos do Azure][]
- [Criar uma máquina virtual do Windows com um modelo do Gerenciador de Recursos e o PowerShell][]

### Ambiente de desenvolvimento: Visual Studio

[Implantar recursos do Azure usando bibliotecas de computação, rede e armazenamento do .NET][]

## Sistema operacional e opções de imagem

Escolha uma imagem com base no sistema operacional que você deseja executar. A Azure e seus parceiros oferecem muitas imagens, algumas delas incluem aplicativos e ferramentas. Ou use uma das suas próprias imagens. Localize a imagem da plataforma de que você precisa para seu aplicativo usando as informações em: [Navegar e selecionar imagens de máquina virtual do Azure com o Windows PowerShell e a CLI do Azure][].

<!-- LINKS -->
[Visão geral]: ../resource-group-overview.md

[Criar uma máquina virtual executando o Windows]: virtual-machines-windows-tutorial.md

[Comandos equivalentes do Gerenciador de Recursos e do Gerenciamento de Serviços para operações de máquina virtual com a CLI do Azure para Mac, Linux e Windows]: xplat-cli-azure-manage-vm-asm-arm.md
[Implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de Recursos do Azure e a CLI do Azure]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Criar e pré-configurar uma máquina virtual do Windows com o Gerenciador de Recursos e o Azure PowerShell]: virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md
[Implantar e gerenciar máquinas virtuais usando modelos do PowerShell e do Gerenciador de Recursos do Azure]: virtual-machines-deploy-rmtemplates-powershell.md
[Criar uma VM do Windows com o Gerenciador de Recursos e o PowerShell]: virtual-machines-create-windows-powershell-resource-manager.md
[Criar uma máquina virtual do Windows com um modelo do Gerenciador de Recursos e o PowerShell]: virtual-machines-create-windows-powershell-resource-manager-template-simple.md


[Navegar e selecionar imagens de máquina virtual do Azure com o Windows PowerShell e a CLI do Azure]: resource-groups-vm-searching.md
[Implantar recursos do Azure usando bibliotecas de computação, rede e armazenamento do .NET]: virtual-machines-arm-deployment.md

[Sign in to the virtual machine]: virtual-machines-log-on-windows-server.md

[Base configuration test environment]: virtual-machines-base-configuration-test-environment.md

[Azure hybrid cloud test environments]: virtual-machines-hybrid-cloud-test-environments.md

<!---HONumber=Nov15_HO1-->