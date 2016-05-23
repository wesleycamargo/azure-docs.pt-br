<properties
	pageTitle="Manutenção planejada para VMs Linux | Microsoft Azure"
	description="Compreenda o que é a manutenção planejada do Azure e como ela afeta suas máquinas virtuais Linux em execução no Azure"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="drewm"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="drewm"/>

# Manutenção planejada para máquinas virtuais Linux no Azure

Compreenda o que é a manutenção planejada do Azure e como ela pode afetar a disponibilidade de suas máquinas virtuais Linux. Este artigo também está disponível para [máquinas virtuais do Windows](virtual-machines-windows-planned-maintenance.md).

Este artigo fornece um panorama do processo de manutenção planejada do Azure. Se estiver tentando entender por que sua VM foi reinicializada, você poderá [ler esta postagem de blog detalhando a exibição de logs de reinicialização da VM](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Por que o Azure realiza manutenção planejada

O Microsoft Azure realiza atualizações periodicamente em todo o mundo para aumentar a confiabilidade, o desempenho e a segurança da infraestrutura de host subjacente a máquinas virtuais. Muitas dessas atualizações são realizadas sem nenhum impacto sobre as máquinas virtuais ou os Serviços de Nuvem, incluindo atualizações que preservam a memória.

Porém, algumas dessas atualizações exigem reinicialização das máquinas virtuais para que as atualizações necessárias à infraestrutura sejam aplicadas. As máquinas virtuais serão desligadas enquanto corrigimos a infraestrutura e, em seguida, elas serão reiniciadas.

Existem dois tipos de manutenção que podem afetar a disponibilidade das máquinas virtuais: planejada e não planejada. Esta página descreve como o Microsoft Azure realiza a manutenção planejada. Para obter mais informações sobre a manutenção não planejada, consulte [Compreender manutenção planejada X manutenção não planejada](virtual-machines-linux-manage-availability.md).

[AZURE.INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]

<!---HONumber=AcomDC_0511_2016-->