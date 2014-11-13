<properties urlDisplayName="Install MySQL" pageTitle="Instalar o MySQL em uma m&aacute;quina virtual Linux no Azure" metaKeywords="Azure vm OpenSUSE, Linux vm" description="Saiba como criar uma m&aacute;quina virtual do Azure com o OpenSUSE Linux e usar o SSH ou o PuTTY para instalar MySQL." metaCanonical="" services="virtual-machines" documentationCenter="" title="Instalar MySQL em uma m&aacute;quina virtual com o OpenSUSE Linux no Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Instalar MySQL em uma máquina virtual com o OpenSUSE Linux no Azure

[MySQL][MySQL] é um banco de dados SQL fonte aberto popular. Usando o [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], você pode criar uma máquina virtual que executa o OpenSUSE Linux da Galeria de Imagens. Em seguida, você pode instalar e configurar um banco de dados MySQL na máquina virtual.

Neste tutorial, você irá aprender:

-   Como usar o Portal de GErenciamento para criar uma máquina virtual OpenSUSE Linux a partir da galeria.

-   Conectar-se a à máquina virtual usando o SSH ou o PuTTY.

-   Como instalar MySQL em uma máquina virtual.

## Criar uma máquina virtual que executa o OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

## Instalar e executar MySQL na máquina virtual

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

## Resumo

Neste tutorial, você aprendeu a criar uma máquina virtual que executa o OpenSUSE Linux e conectar-se remotamente a ela usando o SSH ou o PuTTY. Você também aprendeu como instalar e configurar o MySQL na máquina virtual Linux. Para obter mais informações sobre o MySQL, consulte [Documentação do MySQL][Documentação do MySQL].

  [MySQL]: http://www.mysql.com
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Documentação do MySQL]: http://dev.mysql.com/doc/
