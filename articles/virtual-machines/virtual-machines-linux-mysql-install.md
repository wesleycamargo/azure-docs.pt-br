<properties
	pageTitle="Configurar MySQL em uma VM do Linux | Microsoft Azure "
	description="Saiba como instalar a pilha do MySQL em uma máquina virtual do Linux (sistema operacional da família RedHat ou Ubuntu) no Azure"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/01/2016"
	ms.author="mingzhan"/>


#Como instalar o MySQL no Azure


Neste artigo, você aprenderá a instalar e configurar o MySQL em uma máquina virtual do Azure que executa o Linux.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


##Instalar o MySQL em sua máquina virtual

> [AZURE.NOTE] Você já deve ter uma máquina virtual do Microsoft Azure executando o Linux para concluir este tutorial. Consulte o [Tutorial de VM Linux do Azure](virtual-machines-linux-quick-create-cli.md) para criar e configurar uma VM Linux com `mysqlnode` como o nome da VM e `azureuser` como usuário antes de continuar.

Nesse caso, use a porta 3306 como a porta do MySQL.

Conecte-se à VM Linux criada via putty. Se esta for a primeira vez que você usa a VM Linux Azure, consulte como usar putty para se conectar a uma VM Linux [aqui](virtual-machines-linux-mac-create-ssh-keys.md).

Usaremos o pacote do repositório para instalar o MySQL5.6 como um exemplo neste artigo. Na verdade, o MySQL5.6 demonstra melhoria de desempenho superior ao MySQL5.5. Mais informações [aqui](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).


###Como instalar o MySQL5.6 no Ubuntu
Usaremos uma VM do Linux com o Ubuntu do Azure aqui.

- Etapa 1: instalar MySQL Server 5.6 Alternar para o usuário `root`:

            #[azureuser@mysqlnode:~]sudo su -

    Instalar o mysql-server 5.6:

            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6

    Durante a instalação, uma janela de caixa de diálogo abrirá para solicitar que você defina a senha raiz do MySQL abaixo e será necessário definir a senha aqui.

    ![imagem](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p1.png)


    Insira a senha novamente para confirmar.

    ![imagem](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p2.png)

- Etapa 2: logon no MySQL Server

    Quando terminar a instalação do MySQL Server, o serviço do MySQL será iniciado automaticamente. Você pode fazer o logon no MySQL Server com o usuário `root`. Use o comando abaixo para fazer logon e insira a senha.

             #[root@mysqlnode ~]# mysql -uroot -p

- Etapa 3: gerenciar o serviço MySQL em execução

    (a) Obter o status do serviço MySQL

             #[root@mysqlnode ~]# service mysql status

    (b) Iniciar o serviço MySQL

             #[root@mysqlnode ~]# service mysql start

    (b) Parar o serviço MySQL

             #[root@mysqlnode ~]# service mysql stop

    (b) Reiniciar o serviço MySQL

             #[root@mysqlnode ~]# service mysql restart


###Como instalar o MySQL na família de SO do Red Hat como CentOS e Oracle Linux
Usaremos uma VM do Linux com CentOS ou Oracle Linux aqui.

- Etapa 1: adicione o Comutador do repositório MySQL Yum para o usuário `root`:

            #[azureuser@mysqlnode:~]sudo su -

    Baixe e instale o pacote de versão do MySQL:

            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm

- Etapa 2: edite o arquivo abaixo para habilitar o repositório do MySQL para baixar o pacote MySQL5.6.

            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo

    Atualize cada valor deste arquivo conforme indicado abaixo:

        # *Enable to use MySQL 5.6*

        [mysql56-community]
        name=MySQL 5.6 Community Server

        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/

        enabled=1

        gpgcheck=1

        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

- Etapa 3: instale o MySQL por meio do repositório de MySQL Instalar MySQL:

           #[root@mysqlnode ~]#yum install mysql-community-server

    O pacote RPM MySQL e todos os pacotes relacionados serão instalados.

- Etapa 4: gerenciar o serviço MySQL em execução

    (a) Verifique o status do serviço do MySQL Server:

           #[root@mysqlnode ~]#service mysqld status

    (b) Verifique se a porta padrão do MySQL Server está em execução:

           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306


    (c) Inicie o MySQL Server:

           #[root@mysqlnode ~]#service mysqld start

    (d) Pare o MySQL Server:

           #[root@mysqlnode ~]#service mysqld stop

    (e) Defina o MySQL para iniciar durante a inicialização do sistema:

           #[root@mysqlnode ~]#chkconfig mysqld on


###Como instalar o MySQL no Suse Linux
Usaremos a VM do Linux com OpenSUSE aqui.

- Etapa 1: baixar e instalar o MySQL Server

    Alterne para o usuário `root` com o comando abaixo:

           #sudo su -

    Baixe e instale o pacote de MySQL:

           #[root@mysqlnode ~]# zypper update

           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql

- Etapa 2: gerenciar o serviço MySQL em execução

    (a) Verifique o status do MySQL Server:

           #[root@mysqlnode ~]# rcmysql status

    (b) Verifique a porta padrão do MySQL Server:

           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306


    (c) Inicie o MySQL Server:

           #[root@mysqlnode ~]# rcmysql start

    (d) Pare o MySQL Server:

           #[root@mysqlnode ~]# rcmysql stop

    (e) Defina o MySQL para iniciar durante a inicialização do sistema:

           #[root@mysqlnode ~]# insserv mysql

###Próxima etapa
Encontre mais informações e uso sobre o MySQL [aqui](https://www.mysql.com/).

<!---HONumber=AcomDC_0824_2016-->