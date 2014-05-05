<properties linkid="manage-linux-common-task-mongodb-virtual-machine" urlDisplayName="Instale o MongoDB" pageTitle="Instale o MongoDB em uma máquina virtual do Linux no Azure" metaKeywords="vm do CentOS no Azure, vm do Linux no Azure, vm do Linux, MongoDB no Linux" description="Saiba como criar uma máquina virtual do Azure com o CentOS Linux e usar o SSH ou PuTTY para instalar o MongoDB." metaCanonical="" services="virtual-machines" documentationCenter="" title="Instale o MongoDB em uma máquina virtual que executa o CentOS Linux no Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />





#Instale o MongoDB em uma máquina virtual com o CentOS Linux no Azure

[O MongoDB](http://www.mongodb.org/) é um banco de dados NoSQL de código-fonte aberto popular e com alto desempenho. Usando o Portal de Gerenciamento do Azure, você pode criar uma máquina virtual com o CentOS Linux a partir da Galeria de Imagens. Em seguida, você pode instalar e configurar um banco de dados MongoDB na máquina virtual.

Você aprenderá:

- Usar o Portal de Gerenciamento para selecionar e instalar uma máquina virtual do Linux com CentOS Linux a partir da galeria.

- Conectar-se a à máquina virtual usando o SSH ou PuTTY.
- Instalar o MongoDB na máquina virtual.


##Criar uma máquina virtual com o CentOS Linux

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

## Anexar um disco de dados

[WACOM.INCLUDE [attach-data-disk-centos-vm-in-portal](../includes/attach-data-disk-centos-vm-in-portal.md)]

##Instalar e executar o MongoDB na máquina virtual

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

##Resumo

Neste tutorial, você aprendeu como criar uma máquina virtual do Linux e como conectar-se remotamente a ela usando o SSH ou o PuTTY. Você também aprendeu como instalar e configurar o MongoDB na máquina virtual do Linux. Para obter mais informações sobre o MongoDB, consulte a [Documentação do MongoDB](http://www.mongodb.org/display/DOCS/Home).



