<properties
	pageTitle="Criar uma máquina virtual personalizada que executa o Windows no Azure"
	description="Aprenda a criar uma máquina virtual personalizada que executa o Windows no Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>


<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="kathydav"/>

#Criar uma máquina virtual personalizada que executa o Windows no Azure

Uma máquina virtual *personalizada* é simplesmente uma máquina virtual que você cria usando a opção **Da Galeria** porque isso lhe dá mais opções de configuração do que a opção **Criação Rápida**. Essas opções incluem:

- Conectar a máquina virtual a uma rede virtual.
- Instalar o Agente de VM e extensões, como para antimalware.
- Adicionar a máquina virtual ao serviço de nuvem existente.
- Adicionar a máquina virtual a uma conta de armazenamento existente.
- Adicionar a máquina virtual a um conjunto de disponibilidade.

> [AZURE.IMPORTANT]Se você quiser que sua máquina virtual use uma rede virtual para que possa se conectar às suas máquinas virtuais diretamente pelo nome do host ou estabelecer conexões entre locais, use o método Da Galeria e especifique a rede virtual ao criar a máquina virtual. Uma máquina virtual pode ser configurada para ingressar em uma rede virtual somente quando você criar a máquina virtual. Para mais detalhes sobre redes virtuais, consulte [Visão geral da rede virtual do Azure](virtual-networks-overview.md).

##Para criar a máquina virtual

[AZURE.INCLUDE [virtual-machines-create-windowsvm](../../includes/virtual-machines-create-windowsvm.md)]

<!----HONumber=August15_HO7-->