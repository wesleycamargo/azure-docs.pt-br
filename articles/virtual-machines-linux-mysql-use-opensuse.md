<properties urlDisplayName="Install MySQL" pageTitle="Instalar MySQL em uma máquina virtual com o OpenSUSE Linux no Azure" metaKeywords="Azure, MySQL" description="Aprenda a instalar MySQL em uma máquina virtual no Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="kathydav" />

# Instalar MySQL em uma máquina virtual com o OpenSUSE Linux no Azure

[MySQL][MySQL] é um popular banco de dados SQL livre.  Usando o [Portal de Gerenciamento do Azure][AzurePortal], você pode criar uma máquina virtual que executa o OpenSUSE Linux.Em seguida, você pode instalar e configurar um banco de dados MySQL na máquina virtual.

Este tutorial mostra:

- Como usar o Portal de Gerenciamento para criar uma máquina virtual OpenSUSE Linux por meio de uma imagem disponível pelo Azure.
- Conectar-se a à máquina virtual usando o SSH ou o PuTTY.
- Como instalar MySQL em uma máquina virtual.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## Criar uma máquina virtual que executa o OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

## Instalar e executar MySQL na máquina virtual

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

## Resumo
Neste tutorial, você aprendeu a criar uma máquina virtual com o OpenSUSE Linux e a se conectar remotamente usando SSH ou PuTTY.  Você também aprendeu como instalar e configurar o MySQL na máquina virtual Linux.  Para obter mais informações sobre o MySQL, consulte a [Documentação do MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com

<!--HONumber=35.1-->
