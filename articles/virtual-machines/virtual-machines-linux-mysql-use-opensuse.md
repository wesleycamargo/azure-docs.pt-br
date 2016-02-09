<properties
	pageTitle="Instalar MySQL em uma VM do Linux OpenSUSE no Microsoft Azure"
	description="Aprenda a instalar MySQL em uma máquina virtual no Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="cynthn"/>

# Instalar MySQL em uma máquina virtual com o OpenSUSE Linux no Azure

O [MySQL][MySQL] é um popular banco de dados SQL de software livre. Este tutorial mostra como criar uma máquina virtual com o OpenSUSE Linux e instalar o MySQL.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


<br>


## Criar uma máquina virtual que executa o OpenSUSE Linux

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## Instalar e executar MySQL na máquina virtual

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## Próximas etapas
Para obter detalhes sobre o MySQL, consulte a [Documentação do MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com

<!---HONumber=AcomDC_0128_2016-->