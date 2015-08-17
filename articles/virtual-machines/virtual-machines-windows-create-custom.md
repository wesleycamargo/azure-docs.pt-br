<properties
	pageTitle="Criar uma máquina virtual personalizada que executa o Windows no Azure"
	description="Aprenda a criar uma máquina virtual personalizada que executa o Windows no Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>


<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="kathydav"/>

#Criar uma máquina virtual personalizada que executa o Windows no Azure

Uma máquina virtual (VM) *personalizada* é simplesmente uma VM que você cria usando a opção **Da Galeria** porque isso lhe dá mais opções de configuração do que a opção **Criação Rápida**. Essas opções incluem:

- Conectar a VM a uma rede virtual.
- Instalar o Agente de VM e extensões, como para antimalware.
- Adicionar a VM a um serviço de nuvem existente.
- Adicionar a VM a uma conta de armazenamento existente.
- Adicionar a VM a um conjunto de disponibilidade.

> [AZURE.IMPORTANT]Se você quiser que sua VM use uma rede virtual para que possa se conectar às suas máquinas virtuais diretamente pelo nome do host ou estabelecer conexões entre locais, especifique a rede virtual ao criar a VM. Uma máquina virtual poderá ser configurada para ingressar em uma rede virtual somente quando você criar a VM. Para mais detalhes sobre redes virtuais, consulte [Visão geral da rede virtual do Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

##Para criar a VM

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

<!---HONumber=August15_HO6-->