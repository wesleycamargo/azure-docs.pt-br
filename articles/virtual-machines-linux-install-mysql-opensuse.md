<properties linkid="manage-linux-common-task-mysql-virtual-machine" urlDisplayName="Install MySQL" pageTitle="Install MySQL on a Linux virtual machine in Azure" metaKeywords="Azure vm OpenSUSE, Linux vm" description="Learn how to create an Azure virtual machine with OpenSUSE Linux, and then use SSH or PuTTY to install MySQL." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running OpenSUSE Linux in Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt"></tags>

# Instalar MySQL em uma máquina virtual com o OpenSUSE Linux no Azure

[MySQL][] é um banco de dados SQL fonte aberto popular. Usando o [Portal de Gerenciamento do Azure][], você pode criar uma máquina virtual que executa o OpenSUSE Linux da Galeria de Imagens. Em seguida, você pode instalar e configurar um banco de dados MySQL na máquina virtual.

Neste tutorial, você irá aprender:

-   Como usar o Portal de GErenciamento para criar uma máquina virtual OpenSUSE Linux a partir da galeria.

-   Conectar-se a à máquina virtual usando o SSH ou o PuTTY.

-   Como instalar MySQL em uma máquina virtual.

## Criar uma máquina virtual que executa o OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal][]]

## Instalar e executar MySQL na máquina virtual

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm][]]

## Resumo

Neste tutorial, você aprendeu a criar uma máquina virtual que executa o OpenSUSE Linux e conectar-se remotamente a ela usando o SSH ou o PuTTY. Você também aprendeu como instalar e configurar o MySQL na máquina virtual Linux. Para obter mais informações sobre o MySQL, consulte [Documentação do MySQL][].

  [MySQL]: http://www.mysql.com
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [create-and-configure-opensuse-vm-in-portal]: ../includes/create-and-configure-opensuse-vm-in-portal.md
  [install-and-run-mysql-on-opensuse-vm]: ../includes/install-and-run-mysql-on-opensuse-vm.md
  [Documentação do MySQL]: http://dev.mysql.com/doc/
