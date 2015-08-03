<properties
	pageTitle="Instalar MySQL em uma máquina virtual com o OpenSUSE Linux no Azure"
	description="Aprenda a instalar MySQL em uma máquina virtual no Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="mysql"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/22/2015"
	ms.author="kathydav"/>

# Instalar MySQL em uma máquina virtual com o OpenSUSE Linux no Azure

O [MySQL][MySQL] é um popular banco de dados SQL de software livre. Este tutorial mostra:

- Como usar o [Portal de Gerenciamento do Azure][AzurePortal] para criar uma máquina virtual OpenSUSE Linux por meio de uma imagem disponível pelo Azure.
- Conectar-se a à máquina virtual usando o SSH ou o PuTTY.
- Como instalar MySQL em uma máquina virtual.

[AZURE.INCLUDE [antares-iaas-signup-iaas](../../includes/antares-iaas-signup-iaas.md)]

## Criar uma máquina virtual que executa o OpenSUSE Linux

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

##Instalar e executar MySQL na máquina virtual

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

##Resumo
Neste tutorial, você aprendeu a criar uma máquina virtual com o OpenSUSE Linux e a se conectar remotamente usando SSH ou PuTTY. Você também aprendeu a instalar e configurar o MySQL na máquina virtual Linux. Para obter mais informações sobre o MySQL, consulte [Documentação do MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com

<!---HONumber=July15_HO4-->