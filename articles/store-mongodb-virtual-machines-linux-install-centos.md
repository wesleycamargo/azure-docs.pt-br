<properties linkid="manage-linux-common-task-mongodb-virtual-machine" urlDisplayName="Install MongoDB" pageTitle="Install MongoDB on a Linux virtual machine in Azure" metaKeywords="Azure vm CentOS, Azure vm Linux, Linux vm, Linux MongoDB" description="Learn how to create an Azure virtual machine with CentOS Linux, and then use SSH or PuTTY to install MongoDB." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Instalar o MongoDB em uma máquina virtual que executa o CentOS Linux no Azure

[O MongoDB][O MongoDB] é um banco de dados NoSQL de código-fonte aberto e com alto desempenho. Usando o Portal de Gerenciamento do Azure, você pode criar uma máquina virtual com o CentOS Linux a partir da Galeria de Imagens. Em seguida, você pode instalar e configurar um banco de dados MongoDB na máquina virtual.

Você aprenderá a:

-   Usar o Portal de Gerenciamento para selecionar e instalar uma máquina virtual do Linux com CentOS Linux a partir da galeria.

-   Conectar-se a à máquina virtual usando o SSH ou o PuTTY.
-   Instalar o MongoDB na máquina virtual.

## Criar uma máquina virtual que executa o CentOS Linux

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

## Anexar um disco de dados

[WACOM.INCLUDE [attach-data-disk-centos-vm-in-portal](../includes/attach-data-disk-centos-vm-in-portal.md)]

## Instalar e executar o MongoDB na máquina virtual

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

## Resumo

Neste tutorial, você aprendeu como criar uma máquina virtual do Linux e como conectar-se remotamente a ela usando o SSH ou o PuTTY. Você também aprendeu como instalar e configurar o MongoDB na máquina virtual do Linux. Para obter mais informações sobre o MongoDB, consulte [Documentação do MongoDB][Documentação do MongoDB].

  [O MongoDB]: http://www.mongodb.org/
  [create-and-configure-centos-vm-in-portal]: ../includes/create-and-configure-centos-vm-in-portal.md
  [attach-data-disk-centos-vm-in-portal]: ../includes/attach-data-disk-centos-vm-in-portal.md
  [install-and-run-mongo-on-centos-vm]: ../includes/install-and-run-mongo-on-centos-vm.md
  [Documentação do MongoDB]: http://www.mongodb.org/display/DOCS/Home
