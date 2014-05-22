<properties linkid="store-mongodb-virtual-machines-linux-centos" urlDisplayName="Instalar o MongoDB" pageTitle="Instalar o MongoDB em uma máquina virtual que executa o CentOS Linux no Azure" metaKeywords="Azure, MongoDB" description="Saiba como instalar o Mongo DB em uma máquina virtual no Azure." metaCanonical="" services="" documentationCenter="" title="Instalar o MongoDB instalar em uma máquina virtual que executa o CentOS Linux no Azure" authors="" solutions="" manager="" editor="" />

# Instalar o MongoDB em uma máquina virtual que executa o CentOS Linux no Azure

O [MongoDB][MongoDB] é um banco de dados NoSQL de código-fonte aberto e com alto desempenho.  Usando o [Portal de Gerenciamento do Azure][AzurePortal], você pode criar uma máquina virtual que executa o CentOS Linux da Galeria de Imagens.  Em seguida, você pode instalar e configurar um banco de dados MongoDB na máquina virtual.

Você aprenderá a:

- Usar o Portal de Gerenciamento para criar uma máquina virtual que executa o CentOS Linux da galeria.
- Conectar-se a à máquina virtual usando o SSH ou o PuTTY.
- Instalar o MongoDB na máquina virtual.

## Inscrever-se para o recurso de visualização de máquinas virtuais

Você precisará inscrever-se para o recurso de visualização de máquinas virtuais do Azure para criar uma máquina virtual. Você também pode assinar uma conta de avaliação gratuira se não possuir uma conta Azure.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## Criar uma máquina virtual que executa o CentOS Linux

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

##Instalar e executar o MongoDB na máquina virtual

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

##Resumo
Neste tutorial, você aprendeu a criar uma máquina virtual Linux e conectar-se remotamente a ela usando o SSH ou o PuTTY.  Você também aprendeu como instalar e configurar o MongoDB na máquina virtual do Linux.  Para obter mais informações sobre o MongoDB, consulte [Documentação do MongoDB][MongoDocs].

[MongoDocs]: http://www.mongodb.org/display/DOCS/Home
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com

