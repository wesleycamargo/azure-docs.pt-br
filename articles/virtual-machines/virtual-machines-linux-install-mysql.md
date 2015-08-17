<properties
	pageTitle="Como instalar o MySQL no Azure"
	description="Saiba como instalar a pilha do MySQL em uma VM (máquina virtual) Linux no Azure. Você pode instalar no Ubuntu ou no CentOS."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/12/2015"
	ms.author="mingzhan"/>


#Como instalar o MySQL no Azure


Neste tópico, supõe-se que o leitor já tenha uma conta do Azure. Caso não a tenha, é recomendável inscrever-se visitando o [Azure](http://azure.microsoft.com).



##Criando uma imagem de VM no Microsoft Azure.
Aqui, criaremos uma nova VM no portal de gerenciamento do Microsoft Azure.
###Gerar a "Chave de Autenticação SSH"
Precisaremos da chave SSH para acessar o portal do Azure.


- Baixe e instale o puttygen [daqui](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 
- Execute puttygen.exe.
- Clique no botão "Gerar" para gerar chaves.


 ![imagem](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p01.png)
 
- Depois que a chave for gerada, ela será mostrada conforme indicado abaixo. 
 
 ![imagem](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p02.png)

- Copie a chave pública e salve-a em um arquivo chamado "publicKey.key". Apenas não clique no botão "Salvar a chave pública", porque o formato da chave pública salva é diferente da chave pública que desejamos.
- Clique no botão "Salvar chave privada" e salve-a como "privateKey.ppk". 

###Logon no Portal do Azure

Vá para https://portal.azure.com/ e faça logon.

###Criar uma VM do Linux

Clique em "NOVO" na parte inferior esquerda, crie uma imagem seguindo as instruções e escolha a imagem do Linux de acordo com suas necessidades. Aqui, escolho Ubuntu 14.04 como exemplo.

  ![imagem](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p03.png)

###Configurar o NOME DO HOST

Para "NOME DO HOST:" essa é a URL que você pode usar para acessar a máquina virtual. Basta especificar o nome DNS, por exemplo, "mysqlnode1", e o Azure gerará a URL como "mysqlnode1.cloudapp.net". Para "CHAVE DE AUTENTICAÇÃO SSH": essa é a chave pública gerada por puttygen. Você precisa copiá-la do conteúdo no arquivo "publicKey.key".

  ![imagem](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p04.png)
  

##Abrir a porta padrão de MySQL
Os pontos de extremidade no Microsoft Azure consistem em um protocolo, juntamente com uma porta pública e uma porta privada. A porta privada é a que o serviço escuta no computador local. A porta pública é a que o serviço escuta externamente. A porta 3306 é o número de porta padrão, que será ser escutado pelo MySQL. Clique em "PROCURAR" ⇒ "MÁQUINA VIRTUAL" e clique na imagem que você criou.
 
   ![imagem](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p05.png)


##Conectar-se à imagem criada
Você pode escolher qualquer ferramenta SSH para se conectar à sua máquina virtual. Aqui, usamos Putty como exemplo.
 

- Primeiro, baixe o Putty. A URL de download do Putty está aqui.
- Depois de baixar o Putty, clique no arquivo executável "PUTTY. EXE". Configure-o da maneira a seguir.


     O "nome do Host (ou endereço IP)" é a URL como "NOME DNS" quando você cria uma imagem.
     
     A “Porta” que podemos escolher é a 22. Essa é a porta padrão dos serviços SSH.

   ![imagem](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p06.png)
 
- Antes de selecionar Abrir, clique em Conexão > SSH > guia Autenticação para procurar o arquivo que foi gerado pelo Puttygen e contém a chave privada. Consulte a captura de tela abaixo do campo para preencher.

   ![imagem](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p07.png)
 
- Em seguida, clique em "Abrir". Você poderá ser alertado por uma caixa de mensagem informando que a máquina que você conectar poderá não ser a máquina desejada. Se você tiver configurado o nome DNS e o número de porta corretamente, clique em "Sim".
  
 ![imagem](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p08.png)

- Depois disso, você verá o que é indicado a seguir. 
 
 ![imagem](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p09.png)


##Instalar o MySQL em sua máquina virtual
Há suporte a três métodos de instalação para MySQL: pacotes binários, pacotes rpm e pacotes de origem. Para considerações de desempenho, usaremos um pacote rpm para o MySQL 5.6 como um exemplo para este artigo. O desempenho do MySQL5.6 é significativamente superior ao do MySQL5.5. Mais informações podem ser encontradas [aqui](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).


###Como instalar o MySQL5.6 no Ubuntu ou Debian
Usaremos o Ubuntu 14.04 LTS como exemplo para este artigo.

- Etapa 1: instalar o MySQL Server 5.6

    Instalar mysql-server 5.6 com o comando apt-get

              # azureuser@mysqlnode1:~$ sudo apt-get update
              # azureuser@mysqlnode1:~$ sudo apt-get -y install mysql-server-5.6

    Durante a instalação, você verá uma caixa de diálogo pop-up solicitando que defina a senha raiz do MySQL. Você precisará especificar a nova senha raiz do usuário do MySQL. Abaixo está a captura de tela.

 ![imagem](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p10.png)

    Confirm the password again when it is asked.

 ![imagem](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p11.png)
 
- Etapa 2: logon no MySQL Server

    Após a conclusão da instalação do MySQL Server, o serviço MySQL é iniciado automaticamente. Você pode fazer logon no MySQL Server com a raiz do usuário. Para fazer logon MySQL Server, use o comando abaixo. Ele solicitará a senha raiz de mysql que você definiu durante a instalação do mysql server.

             # azureuser@mysqlnode1:~$ mysql -uroot -p

- Etapa 3: verificar o serviço MySQL na VM
    
    Após o logon, verifique se o serviço MySQL está em execução. Você pode usar os comentários a seguir para iniciar/reiniciar o serviço.

    (a) Para obter o status do serviço MySQL

             #sudo service mysql status

    (b) Para iniciar o serviço MySQL

             #sudo service mysql start

    (b) Para interromper o serviço MySQL

             #sudo service mysql stop

    (b) Para reiniciar o serviço MySQL

             #sudo service mysql restart


###Como instalar o MySQL na família de sistemas operacionais Redhat ou Oracle Linux
- Etapa 1: adicionar o repositório MySQL Yum. Para obter permissões de raiz, execute o comando: 

            #sudo su -
            #[root@azureuser ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm 
            #[root@azureuser ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm 

- Etapa 2: selecionar uma série de versão
 
            #[root@azureuser ~]# vim /etc/yum.repos.d/mysql-community.repo

    Essa é uma entrada típica para o sub-repositório de uma série de versão no arquivo:

        # *Enable to use MySQL 5.6*

        [mysql56-community]
        name=MySQL 5.6 Community Server

        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/

        enabled=1

        gpgcheck=1

        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

- Etapa 3: instalar o MySQL com Yum. Instale o MySQL com o seguinte comando:

           #[root@azureuser ~]#yum install mysql-community-server 

    Isso instala o pacote para o servidor MySQL, bem como outros pacotes necessários.

- Etapa 4: verificar o estado de execução do MySQL

    Você pode verificar o status do MySQL Server com o seguinte comando:
   
           #[root@azureuser ~]#service mysqld status

    Você pode verificar se a porta padrão do MySQL Server está em execução:

           #[root@azureuser ~]#netstat  –tunlp|grep 3306

- Etapa 5: iniciando e parando o MySQL Server

    Inicie o MySQL Server com o seguinte comando:

           #[root@azureuser ~]#service mysqld start

    Pare o MySQL Server com o seguinte comando:

           #[root@azureuser ~]#service mysqld stop

    Para definir o MySQL para iniciar quando o sistema for inicializado, execute o seguinte comando:

           #[root@azureuser ~]#chkconfig mysqld on


###Como instalar o MySQL no Suse Linux

- Etapa 1: instalar o MySQL Server

    Para elevar as permissões, execute o comando:

           #sudo su -

    Instale o MySQL executando o seguinte comando:

           #mysql-test:~ # zypper update

           #mysql-test:~ # zypper install mysql-server mysql-devel mysql

- Etapa 2: verificar o estado de execução do MySQL

    Você pode verificar o status do MySQL Server com o seguinte comando:

           #mysql-test:~ # rcmysql status

    Você pode verificar se a porta padrão do MySQL Server está em execução:

           #mysql-test:~ # netstat  –tunlp|grep 3306

- Etapa 3: iniciando e parando o MySQL Server

    Inicie o MySQL Server com o seguinte comando:

           #mysql-test:~ # rcmysql start

    Pare o MySQL Server com o seguinte comando:

           #mysql-test:~ # rcmysql stop

    Para definir o MySQL para iniciar quando o sistema for inicializado, execute o seguinte comando:

           #mysql-test:~ # insserv mysql
 

<!---HONumber=August15_HO6-->