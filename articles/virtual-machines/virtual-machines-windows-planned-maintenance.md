<properties
	pageTitle="Manutenção planejada para VMs do Windows | Microsoft Azure"
	description="Compreenda o que é a manutenção planejada do Azure e como ela afeta suas máquinas virtuais do Windows em execução no Azure"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="drewm"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="drewm"/>

# Manutenção planejada para máquinas virtuais no Azure


Compreenda o que é a manutenção planejada do Azure e como ela pode afetar a disponibilidade de suas máquinas virtuais do Windows. Este artigo também está disponível para [máquinas virtuais Linux](virtual-machines-linux-planned-maintenance.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## Por que o Azure realiza manutenção planejada

O Microsoft Azure realiza atualizações periodicamente em todo o mundo para aumentar a confiabilidade, o desempenho e a segurança da infraestrutura de host subjacente a máquinas virtuais. Muitas dessas atualizações são realizadas sem nenhum impacto sobre as máquinas virtuais ou os Serviços de Nuvem, incluindo atualizações que preservam a memória.

Porém, algumas dessas atualizações exigem reinicialização das máquinas virtuais para que as atualizações necessárias à infraestrutura sejam aplicadas. As máquinas virtuais serão desligadas enquanto corrigimos a infraestrutura e, em seguida, elas serão reiniciadas.

Existem dois tipos de manutenção que podem afetar a disponibilidade das máquinas virtuais: planejada e não planejada. Esta página descreve como o Microsoft Azure realiza a manutenção planejada. Para obter mais informações sobre a manutenção não planejada, consulte [Compreender manutenção planejada X manutenção não planejada](virtual-machines-windows-manage-availability.md).

[AZURE.INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]

<!---HONumber=AcomDC_0427_2016-->