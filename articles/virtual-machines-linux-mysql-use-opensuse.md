<properties urlDisplayName="Install MongoDB" pageTitle="Instalar o MongoDB em uma m&aacute;quina virtual que executa o CentOS Linux no Azure" metaKeywords="Azure, MongoDB" description="Saiba como instalar o Mongo DB em uma m&aacute;quina virtual no Azure." metaCanonical="" services="" documentationCenter="" title="Instalar o MongoDB em uma m&aacute;quina virtual que executa o CentOS Linux no Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Instalar MySQL em uma máquina virtual com o OpenSUSE Linux no Azure

[MySQL][MySQL] é um banco de dados SQL fonte aberto popular. Usando o [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], você pode criar uma máquina virtual que executa o OpenSUSE Linux da Galeria de Imagens. Em seguida, você pode instalar e configurar um banco de dados MySQL na máquina virtual.

Neste tutorial, você irá aprender:

-   Como usar o Portal de GErenciamento para criar uma máquina virtual OpenSUSE Linux a partir da galeria.
-   Conectar-se a à máquina virtual usando o SSH ou o PuTTY.
-   Como instalar MySQL em uma máquina virtual.

## Crie uma assinatura do Azure.

Se você não tiver uma conta do Azure, será possível se inscrever em uma [conta gratuita][conta gratuita]. Após a inscrição, siga estas etapas para continuar esse tutorial.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## Criar uma máquina virtual que executa o OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

## Instalar e executar MySQL na máquina virtual

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

## Resumo

Neste tutorial, você aprendeu a criar uma máquina virtual com o OpenSUSE Linux e a se conectar remotamente usando SSH ou PuTTY. Você também aprendeu como instalar e configurar o MySQL na máquina virtual Linux. Para obter mais informações sobre o MySQL, consulte [Documentação do MySQL][Documentação do MySQL].

  [MySQL]: http://www.mysql.com
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [conta gratuita]: http://azure.microsoft.com
  [Documentação do MySQL]: http://dev.mysql.com/doc/
