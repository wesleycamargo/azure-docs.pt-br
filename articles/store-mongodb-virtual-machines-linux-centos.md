<properties linkid="store-mongodb-virtual-machines-linux-centos" urlDisplayName="Install MongoDB" pageTitle="nstall MongoDB on a virtual machine running CentOS Linux in Azure" metaKeywords="Azure, MongoDB" description="Learn how to install Mongo DB on a virtual machine in Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="bbenz, MSOpenTech" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="" ms.topic="article" ms.date="01/01/1900" ms.author="bbenz, MSOpenTech" />

# Instalar o MongoDB em uma máquina virtual que executa o CentOS Linux no Azure

[O MongoDB][O MongoDB] é um banco de dados NoSQL de código-fonte aberto e com alto desempenho. Usando o [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], você pode criar uma máquina virtual que executa o CentOS Linux da Galeria de Imagens. Em seguida, você pode instalar e configurar um banco de dados MongoDB na máquina virtual.

Você aprenderá a:

-   Usar o Portal de Gerenciamento para criar uma máquina virtual que executa o CentOS Linux da galeria.
-   Conectar-se a à máquina virtual usando o SSH ou o PuTTY.
-   Instalar o MongoDB na máquina virtual.

## Criar uma máquina virtual que executa o CentOS Linux

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

## Instalar e executar o MongoDB na máquina virtual

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

## Resumo

Neste tutorial, você aprendeu como criar uma máquina virtual do Linux e como conectar-se remotamente a ela usando o SSH ou o PuTTY. Você também aprendeu como instalar e configurar o MongoDB na máquina virtual do Linux. Para obter mais informações sobre o MongoDB, consulte [Documentação do MongoDB][Documentação do MongoDB].

  [O MongoDB]: http://www.mongodb.org/
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [create-and-configure-centos-vm-in-portal]: ../includes/create-and-configure-centos-vm-in-portal.md
  [install-and-run-mongo-on-centos-vm]: ../includes/install-and-run-mongo-on-centos-vm.md
  [Documentação do MongoDB]: http://www.mongodb.org/display/DOCS/Home
