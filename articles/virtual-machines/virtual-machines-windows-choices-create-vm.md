<properties
	pageTitle="Diferentes maneiras de criar uma máquina virtual Windows"
	description="Lista as diferentes maneiras de criar uma máquina virtual Windows e fornece links para instruções."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="index-page"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="09/15/2015"
	ms.author="cynthn"/>

# Diferentes maneiras de criar uma máquina virtual Windows

O Azure oferece diferentes maneiras de criar uma máquina virtual, pois máquinas virtuais são adequadas para diferentes usuários e finalidades. Isso significa que você precisa tomar algumas decisões sobre a máquina virtual e como criá-la. Este artigo fornece um resumo dessas opções e links para instruções.

Os modelos do Gerenciador de Recursos do Azure foram introduzidos recentemente como uma maneira de criar e gerenciar uma máquina virtual e seus recursos diferentes, como uma unidade lógica de implantação. Instruções para essa abordagem são incluídas abaixo, quando disponíveis. Para saber mais sobre o Gerenciador de Recursos do Azure e como gerenciar recursos como uma unidade, consulte a [Visão geral][].

## Opções da ferramenta

### GUI: Portal do Azure ou Portal de Visualização do Azure

A interface gráfica do usuário do portal do Azure é uma maneira fácil de testar uma máquina virtual, especialmente se você estiver começando com o Azure. Use o Portal do Azure ou o Portal de Visualização do Azure para criar a máquina virtual:

[Criar uma máquina virtual executando o Windows][]

### Shell de comando: CLI do Azure ou Azure PowerShell

Se você preferir trabalhar em um shell de comando, escolha entre a interface de linha de comando (CLI) do Azure para usuários do Mac e Linux ou o PowerShell do Azure, que tem os cmdlets do Windows PowerShell para Azure e um console personalizado.

Para a CLI do Azure, consulte [Comandos equivalentes do Gerenciador de Recursos e do Gerenciamento de Serviços para operações de máquina virtual com a CLI do Azure para Mac, Linux e Windows][]. Para usar um modelo, consulte [Implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de Recursos do Azure e o CLI do Azure][].

Para o Azure PowerShell, consulte [Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais Windows][] Para usar um modelo, consulte [Implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de Recursos do Azure e o PowerShell][]. Para criar máquinas virtuais na pilha do Gerenciamento de Serviços, consulte [Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais Windows][].

### Ambiente de desenvolvimento: Visual Studio

[Criar uma máquina virtual para um site com o Visual Studio][]

[Implantar recursos do Azure usando bibliotecas de computação, rede e armazenamento do .NET][]

## Sistema operacional e opções de imagem

Escolha uma imagem com base no sistema operacional que você deseja executar. A Azure e seus parceiros oferecem muitas imagens, algumas delas incluem aplicativos e ferramentas. Ou use uma das suas próprias imagens.

### Imagens do Azure

Estas instruções mostram como usar uma imagem do Azure para criar uma máquina virtual personalizada com opções de rede, balanceamento de carga e muito mais. Consulte [Como criar uma máquina virtual personalizada que executa o Windows][].

### Usar sua própria imagem

Use uma imagem baseada em uma máquina virtual existente do Azure *capturando* essa maquina virtual ou carregue uma imagem de sua preferência, armazenada em um disco rígido virtual (VHD):

- [Como capturar uma máquina virtual Windows para usar como imagem][]
- [Criar e carregar um VHD do Windows Server no Azure][]

## Próximas etapas

[Faça logon na máquina virtual][]

[Anexar um disco de dados][]

## Recursos adicionais
[Ambiente de teste de configuração básica][]

[Ambientes de teste de nuvem híbrida do Azure][]

<!-- LINKS -->
[Visão geral]: ../resource-group-overview.md

[Criar uma máquina virtual executando o Windows]: virtual-machines-windows-tutorial.md

[Comandos equivalentes do Gerenciador de Recursos e do Gerenciamento de Serviços para operações de máquina virtual com a CLI do Azure para Mac, Linux e Windows]: xplat-cli-azure-manage-vm-asm-arm.md
[Implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de Recursos do Azure e o CLI do Azure]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Create and preconfigure a Windows virtual machine with Resource Manager and Azure PowerShell]: virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md
[Implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de Recursos do Azure e o PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais Windows]: virtual-machines-ps-create-preconfigure-windows-vms.md
[Como criar uma máquina virtual personalizada que executa o Windows]: virtual-machines-windows-create-custom.md

[Como capturar uma máquina virtual Windows para usar como imagem]: virtual-machines-capture-image-windows-server.md

[Criar e carregar um VHD do Windows Server no Azure]: virtual-machines-create-upload-vhd-windows-server.md


[Criar uma máquina virtual para um site com o Visual Studio]: virtual-machines-dotnet-create-visual-studio-powershell.md
[Implantar recursos do Azure usando bibliotecas de computação, rede e armazenamento do .NET]: virtual-machines-arm-deployment.md

[Faça logon na máquina virtual]: virtual-machines-log-on-windows-server.md

[Anexar um disco de dados]: storage-windows-attach-disk.md

[Ambiente de teste de configuração básica]: virtual-machines-base-configuration-test-environment.md

[Ambientes de teste de nuvem híbrida do Azure]: virtual-machines-hybrid-cloud-test-environments.md

<!---HONumber=Sept15_HO3-->