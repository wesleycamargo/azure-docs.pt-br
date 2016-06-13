<properties
	pageTitle="Diferentes maneiras de criar uma VM do Windows | Microsoft Azure"
	description="Lista as diferentes maneiras de criar uma máquina virtual do Windows com o Gerenciador de Recursos."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="03/11/2016"
	ms.author="cynthn"/>

# Diferentes maneiras de criar uma máquina virtual do Windows com o Gerenciador de Recursos

O Azure oferece diferentes maneiras de criar uma máquina virtual, pois máquinas virtuais são adequadas para diferentes usuários e finalidades. Isso significa que você precisa tomar algumas decisões sobre a máquina virtual e como criá-la. Este artigo fornece um resumo dessas opções e links para instruções.

## Portal do Azure

Usar o portal do Azure é uma maneira fácil de testar uma máquina virtual, especialmente, se estiver se familiarizando com o Azure.

[Criar uma máquina virtual que executa o Windows usando o portal](virtual-machines-windows-hero-tutorial.md)

## PowerShell do Azure

Se preferir trabalhar em um shell de comando, será possível usar o Azure PowerShell.

- [Criar uma VM do Windows usando o PowerShell](virtual-machines-windows-ps-create.md)
- [Criar uma máquina virtual do Windows com um modelo do Gerenciador de Recursos](virtual-machines-windows-ps-template.md)

## Modelo

As máquinas virtuais exigem uma combinação de recursos (como conjuntos de disponibilidade e contas de armazenamento). Em vez de implantar e gerenciar cada recurso separadamente, é possível criar um modelo do Azure Resource Manager que implanta e provisiona todos os recursos em uma única operação coordenada.

- [Criar uma máquina virtual do Windows com um modelo do Gerenciador de Recursos](virtual-machines-windows-ps-template.md)

## Visual Studio

[Implantar recursos do Azure usando bibliotecas de computação, rede e armazenamento do .NET](virtual-machines-windows-csharp.md)

<!---HONumber=AcomDC_0601_2016-->