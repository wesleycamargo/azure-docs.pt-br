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
	ms.date="01/06/2016"
	ms.author="cynthn"/>

# Como configurar pontos de extremidade em uma máquina virtual clássica do Linux no Azure

Todas as máquinas virtuais do Linux criadas no Azure usando o modelo de implantação clássico podem se comunicar automaticamente com outras máquinas virtuais no mesmo serviço de nuvem ou rede virtual por um canal de rede privada. No entanto, os computadores na Internet ou outras redes virtuais requerem pontos de extremidade para direcionar o tráfego de rede de entrada para uma máquina virtual. Se desejar obter informações sobre pontos de extremidade em VMs do Windows, consulte [How to set up endpoints on a classic Windows virtual machine in Azure (Como configurar pontos de extremidade em uma máquina virtual Windows clássica no Azure)](virtual-machines-windows-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.

Quando você cria uma máquina virtual do Linux no portal clássico do Azure, pontos de extremidade comuns, como os para SSH (Secure Shell), geralmente são criados para você automaticamente. Você pode configurar pontos de extremidade adicionais criando a máquina virtual ou posteriormente, conforme a necessidade.
 

[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## Próximas etapas

* Você também pode criar um ponto de extremidade de VM usando a Interface de linha de comando do Azure no [Modo de Gerenciamento de Serviço](../virtual-machines-command-line-tools.md). Execute o comando **azure vm endpoint create**.

<!-----------HONumber=AcomDC_0330_2016-->