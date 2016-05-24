<properties
	pageTitle="Configurar pontos de extremidade em uma VM do Linux clássica | Microsoft Azure"
	description="Saiba como configurar pontos de extremidade no portal clássico do Azure para permitir a comunicação com uma máquina virtual do Linux no Azure"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="cynthn"/>

# Como configurar pontos de extremidade em uma máquina virtual clássica no Azure

Todas as máquinas virtuais do Linux criadas no Azure usando o modelo de implantação clássico podem se comunicar automaticamente com outras máquinas virtuais no mesmo serviço de nuvem ou rede virtual por um canal de rede privada. No entanto, os computadores na Internet ou outras redes virtuais requerem pontos de extremidade para direcionar o tráfego de rede de entrada para uma máquina virtual. Este artigo também está disponível para [máquinas virtuais do Windows](virtual-machines-windows-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.

Ao criar uma máquina virtual Linux no portal clássico do Azure, em geral, um ponto de extremidade para o SSH (Secure Shell) é criado para você automaticamente. Você pode configurar pontos de extremidade adicionais criando a máquina virtual ou posteriormente, conforme a necessidade.
 

[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## Próximas etapas

* Também é possível criar um ponto de extremidade de VM usando a [Interface de Linha de Comando do Azure](../virtual-machines-command-line-tools.md). Execute o comando **azure vm endpoint create**.

* Se você criou uma máquina virtual no modelo de implantação do Gerenciador de Recursos, é possível usar a CLI do Azure no modo do Gerenciador de Recursos para [criar grupos de segurança de rede](../virtual-network/virtual-networks-create-nsg-arm-cli.md) a fim de controlar o tráfego para a VM.

<!---HONumber=AcomDC_0511_2016-->