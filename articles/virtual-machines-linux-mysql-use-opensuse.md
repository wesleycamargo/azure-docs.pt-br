<properties linkid="virtual-machines-linux-mysql-use-opensuse" urlDisplayName="Instalar o MongoDB" pageTitle="nstalar MongoDB em uma máquina virtual em execução CentOS Linux no Azure" metaKeywords="Azure, MongoDB" description="aprender a instalar Mongo DB em uma máquina virtual no Azure." metaCanonical="" services="" documentationCenter="" title="O MongoDB instalar em uma máquina virtual em execução CentOS Linux no Azure" authors="" solutions="" manager="" editor="" />

# Instalar MySQL em uma máquina virtual com o OpenSUSE Linux no Azure

[MySQL][MySQL] é um código-fonte aberto popular, o Banco de Dados SQL.  Usando o [Portal de Gerenciamento Azure][AzurePortal], você pode criar uma máquina virtual com o OpenSUSE Linux de galeria de imagens.  Em seguida, você pode instalar e configurar um banco de dados MySQL na máquina virtual.

Neste tutorial, você irá aprender:

- Como usar o Portal de Gerenciamento para criar uma máquina virtual OpenSUSE Linux a partir da galeria.
- Como conectar-se a à máquina virtual usando o SSH ou PuTTY.
- Como instalar MySQL em uma máquina virtual.

## Inscreva-se para o recurso de visualização de máquinas virtuais

Você precisará inscrever-se para o recurso de visualização de máquinas virtuais do Azure para criar uma máquina virtual. Você também pode assinar uma conta de avaliação gratuita se não possuir uma conta Azure.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## Crie uma máquina virtual que executa OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

##Instalar e executar MySQL em uma máquina virtual

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

##Resumo de neste tutorial, você aprendeu a criar uma máquina virtual com o OpenSUSE Linux e se conectem remotamente usando SSH ou acabamento.  Você também aprendeu como instalar e configurar o MySQL na máquina virtual Linux.  Para obter mais informações sobre o MySQL, consulte [Documentação do MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com

