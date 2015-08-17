<properties
	pageTitle="Crie uma máquina virtual personalizada no Azure"
	description="Saiba como criar uma máquina virtual personalizada no Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="kathydav"/>

#Como criar uma máquina virtual personalizada

Uma máquina virtual *personalizada* é simplesmente uma máquina virtual que você cria usando a opção **Da Galeria** porque isso lhe dá mais opções de configuração do que a opção **Criação Rápida**. Essas opções incluem:

- Conectar a máquina virtual a uma rede virtual.
- Instalar o agente de máquina Virtual do Azure e extensões de máquina Virtual do Azure, como para antimalware.
- Adicionar a máquina virtual a serviços de nuvem existentes.
- Adicionar a máquina virtual a uma conta de armazenamento existente.
- Adicionar a máquina virtual a um conjunto de disponibilidade.

**Importante**: se você quiser que sua máquina virtual use uma rede virtual para que possa se conectar às suas máquinas virtuais diretamente pelo nome do host ou estabelecer conexões entre locais, especifique a rede virtual ao criar a máquina virtual. Uma máquina virtual pode ser configurada para ingressar em uma rede virtual somente quando você criar a máquina virtual. Para mais detalhes sobre redes virtuais, consulte [Visão geral da rede virtual do Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

<!---HONumber=06-->