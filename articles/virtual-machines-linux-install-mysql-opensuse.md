<properties linkid="manage-linux-common-task-mysql-virtual-machine" urlDisplayName="Instalar MySQL" pageTitle="Instalar MySQL em uma máquina virtual Linux no Azure" metaKeywords="Azure vm OpenSUSE, vm Linux" description="Saiba como criar uma máquina virtual do Azure com o OpenSUSE Linux e usar o SSH ou o PuTTY para instalar MySQL." metaCanonical="" services="virtual-machines" documentationCenter="" title="Instalar MySQL em uma máquina virtual que executa o OpenSUSE Linux no Azure" authors="" solutions="" manager="" editor="" />




# Instalar MySQL em uma máquina virtual com o OpenSUSE Linux no Azure

[MySQL](http://www.mysql.com) é um banco de dados SQL fonte aberto popular. Usando o [Portal de Gerenciamento do Azure][AzurePreviewPortal], você pode criar uma máquina virtual que executa o OpenSUSE Linux da Galeria de Imagens.  Em seguida, você pode instalar e configurar um banco de dados MySQL na máquina virtual.

Neste tutorial, Você aprenderá a:

- Como usar o Portal de GErenciamento para criar uma máquina virtual OpenSUSE Linux a partir da galeria.

- Conectar-se a à máquina virtual usando o SSH ou o PuTTY.

- Como instalar MySQL em uma máquina virtual.

##Criar uma máquina virtual que executa o OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

##Instalar e executar MySQL na máquina virtual

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

##Resumo

Neste tutorial, você aprendeu a criar uma máquina virtual que executa o OpenSUSE Linux e conectar-se remotamente a ela usando o SSH ou o PuTTY. Você também aprendeu como instalar e configurar o MySQL na máquina virtual Linux. Para obter mais informações sobre o MySQL, consulte [Documentação do MySQL](http://dev.mysql.com/doc/).

[AzurePreviewPortal]: http://manage.windowsazure.com

