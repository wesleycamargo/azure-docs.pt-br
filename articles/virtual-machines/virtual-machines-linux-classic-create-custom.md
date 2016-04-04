<properties
	pageTitle="Criar uma VM do Linux | Microsoft Azure"
	description="Saiba como criar uma máquina virtual personalizada com o modelo de implantação clássico executando o sistema operacional Linux."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/14/2015"
	ms.author="dkshir"/>

# Como criar uma VM personalizada do Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[Modelo do Gerenciador de Recursos](virtual-machines-linux-cli-create.md).

Este tópico descreve como criar uma máquina virtual *personalizada* com a CLI do Azure usando o modelo de implantação clássico. Usaremos uma imagem do Linux das **IMAGENS** disponíveis no Azure. Os comandos da CLI do Azure oferecem estas opções de configuração, entre outras:

- Conectar a VM a uma rede virtual
- Adicionar a VM a um serviço de nuvem existente
- Adicionar a VM a uma conta de armazenamento existente
- Adicionar a VM a um conjunto de disponibilidade ou local.

> [AZURE.IMPORTANT] Se você quiser que sua máquina virtual use uma rede virtual para que possa se conectar às suas máquinas virtuais diretamente pelo nome do host ou estabelecer conexões entre locais, use o método Da Galeria e especifique a rede virtual ao criar a máquina virtual. Uma máquina virtual pode ser configurada para ingressar em uma rede virtual somente quando você criar a máquina virtual. Para mais detalhes sobre redes virtuais, consulte [Visão geral da rede virtual do Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).


## Como criar uma máquina virtual do Linux usando o modelo de implantação clássica

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-linuxvm.md)]

<!----HONumber=AcomDC_0323_2016-->