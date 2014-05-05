<properties linkid="manage-windows-common-task-mongodb-vm" urlDisplayName="Instalar o MongoDB" pageTitle="Instalar o MongoDB em uma máquina virtual do Windows Server" metaKeywords="mv do Azure, Azure MongoDB, desktop remoto do Azure" description="Aprenda como criar uma máquina virtual do Azure com o Windows Server 2008 R2 e, em seguida, use o desktop remoto para instalar o MongoDB." metaCanonical="" services="virtual-machines" documentationCenter="" title="Instalar o MongoDB em uma máquina virtual que executa o Windows Server no Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />




#Instalar o MongoDB em uma máquina virtual que executa o Windows Server no Azure

O [MongoDB][MongoDB] é um banco de dados NoSQL de código-fonte aberto e com alto desempenho.  Usando o [Portal de Gerenciamento Azure][AzurePreviewPortal], você pode criar uma máquina virtual que executa o Windows Server da galeria de imagens.  Em seguida, você pode instalar e configurar um banco de dados MongoDB na máquina virtual.

Neste tutorial, você irá aprender:

- Como usar o Portal de Gerenciamento para criar uma máquina virtual do Windows Server a partir da galeria.
- Como conectar-se à máquina virtual usando o desktop remoto.
- Como instalar o MongoDB na máquina virtual.

## Criar uma máquina virtual que executa Windows Server 2008 R2

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

## Anexar um disco de dados

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

## Instalar e executar o MongoDB na máquina virtual 

[WACOM.INCLUDE [install-and-run-mongo-on-win2k8-vm](../includes/install-and-run-mongo-on-win2k8-vm.md)]

##Resumo
Neste tutorial você aprendeu como criar uma máquina virtual Windows Server e conectar-se remotamente a ela.  Você também aprendeu como instalar e configurar o MongoDB na máquina virtual Windows. Para obter mais informações sobre o MongoDB, consulte [Documentação do MongoDB][MongoDocs].

[MongoDocs]: http://www.mongodb.org/display/DOCS/Home
[MongoDB]: http://www.mongodb.org/
[AzurePreviewPortal]: http://manage.windowsazure.com

