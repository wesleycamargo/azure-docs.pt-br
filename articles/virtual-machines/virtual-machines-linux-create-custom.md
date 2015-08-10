<properties
	pageTitle="Criar uma máquina virtual personalizada que executa Linux no Azure"
	description="Aprenda a criar uma máquina virtual personalizada que executa Linux no Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="dkshir"/>

# Como criar uma máquina virtual personalizada que executa Linux no Azure

Este tópico descreve como criar uma máquina virtual *personalizada* usando a CLI do Azure para Mac, Linux e Windows no modo de Gerenciamento de Serviços do Azure. Vamos usar uma imagem do Linux das **IMAGENS ** disponíveis no Azure. Os comandos da CLI do Azure oferecem estas opções de configuração, entre outras:

- Conectar a VM a uma rede virtual
- Adicionar a VM a um serviço de nuvem existente
- Adicionar a VM a uma conta de armazenamento existente
- Adicionar a VM a um conjunto de disponibilidade ou local.

> [AZURE.IMPORTANT]Se você quiser que sua máquina virtual use uma rede virtual para que possa se conectar às suas máquinas virtuais diretamente pelo nome do host ou estabelecer conexões entre locais, use o método Da Galeria e especifique a rede virtual ao criar a máquina virtual. Uma máquina virtual pode ser configurada para ingressar em uma rede virtual somente quando você criar a máquina virtual. Para mais detalhes sobre redes virtuais, consulte [Visão geral da rede virtual do Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

<p/>
[AZURE.INCLUDE [service-management-pointer-to-resource-manager](../../includes/service-management-pointer-to-resource-manager.md)]

- [Criar uma máquina virtual que executa Linux](virtual-machines-linux-tutorial.md)


## Como criar uma máquina virtual Linux com Gerenciamento de Serviços do Azure

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-LinuxVM.md)]

<!---HONumber=July15_HO5-->