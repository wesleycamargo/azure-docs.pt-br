<properties 
	pageTitle="Executando um cluster MariaDB (MySQL) no Azure" 
	description="Criar um cluster MariaDB + Galera MySQL nas máquinas virtuais do Azure" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="sabbour" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure-services" 
	ms.date="12/3/2014" 
	ms.author="v-ahsab" 
	ms.prod="azure"/>

<!--The next line, with one pound sign at the beginning, is the page title--> 
# Cluster MariaDB (MySQL) - tutorial do Azure

<p>Estamos criando um cluster [Galera](http://galeracluster.com/products/) multimestre de [MariaDBs](https://mariadb.org/en/about/), uma substituição robusta, escalável e confiável para MySQL, para trabalhar em um ambiente altamente disponível nas Máquinas Virtuais do Azure.</p>

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

+ [Visão geral da arquitetura]
+ [Criação do modelo]
+ [Criação do cluster]
+ [Balanceamento de carga do cluster]
+ [Validação do cluster]


## Visão geral da arquitetura

Este tópico executa as seguintes etapas:

1. Criar um cluster de 3 nós
2. Separar os discos de dados do disco do sistema operacional
3. Criar os discos de dados na configuração RAID-0/striped para aumentar o IOPS
4. Usar o balanceador de carga do Azure para balancear a carga dos três nós
5. Para minimizar o trabalho repetitivo, crie uma imagem de VM com MariaDB+Galera e use-a para criar as outras VMs do cluster.

![Architecture](./media/virtual-machines-mariadb-cluster/Setup.png)

> [AZURE.NOTE]  Este tópico usa as ferramentas da [CLI do Azure], certifique-se de baixá-las e conectá-las à sua assinatura do Azure de acordo com as instruções. Se você precisar de uma referência para os comandos disponíveis na CLI do Azure, confira este link para a [referência de comandos da CLI do Azure]. Você também precisará [criar uma chave SSH para autenticação] e anotar o **local do arquivo .pem**.


## Criação do modelo

### Infraestrutura

1. Crie um grupo de afinidade para manter os recursos juntos

		azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"
        
2. Criar uma rede virtual

		azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet

3. Crie uma conta de armazenamento para hospedar todos os nossos discos. Observe que você não deve colocar mais de 40 discos muito usados na mesma conta de armazenamento para evitar atingir o limite de 20.000 IOPS da conta de armazenamento. Nesse caso, estamos longe deste número de forma que vamos armazenar tudo na mesma conta para manter a simplicidade

		azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster

3. Localizar o nome da imagem da Máquina Virtual CentOS 7

		azure vm image list | findstr CentOS        
this will output something like `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`. Use the name in the following step.

4. Crie o modelo de VM substituindo **/path/to/key.pem** pelo caminho onde você armazenou a chave SSH .pem gerada

		azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser
        
5. Anexe discos de dados de 4 x 500 GB à VM para uso na configuração RAID

		FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd        

6. SSH dentro da VM de modelo que você criou em **mariadbhatemplate.cloudapp.net:22** e conecte-se usando sua chave privada.

### Software

1. Obtenha a raiz

        sudo su
        
2. Instale o suporte RAID:

     - Instalar mdadm
        
        		yum install mdadm
                
     - Criar a configuração RAID0/stripe com um sistema de arquivos EXT4
        
				mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
				mdadm --detail --scan >> /etc/mdadm.conf
				mkfs -t ext4 /dev/md0
         
     - Criar o diretório de ponto de montagem
         
				mkdir /mnt/data
                
     - Recuperar o UUID do dispositivo RAID recém-criado
        
				blkid | grep /dev/md0
    
     - Edit /etc/fstab
        
        		vi /etc/fstab
        
     - Adicionar o dispositivo lá para habilitar a montagem automática na reinicialização, substituindo o UUID com o valor obtido do comando **blkid** antes
        
        		UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2
        	
     - Monte a nova partição
        
        		mount /mnt/data
            
3. Instale MariaDB:    
		
     - Criar o arquivo MariaDB.repo: 
        
              	vi /etc/yum.repos.d/MariaDB.repo
        
     - Preencha-o com o conteúdo abaixo      
        
				[mariadb]
				name = MariaDB
				baseurl = http://yum.mariadb.org/10.0/centos7-amd64
				gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
				gpgcheck=1
    
     - Remover o posfixo existente e mariadb-libs para evitar conflitos
    
    		yum remove postfix mariadb-libs-*
            
     - Instalar MariaDB com Galera
    
    		yum install MariaDB-Galera-server MariaDB-client galera
   
4. Mova o diretório de dados MySQL para o dispositivo de blocos RAID

     - Copie o diretório atual do MySQL para o novo local e remova o diretório antigo
    
    		cp -avr /var/lib/mysql /mnt/data  
    		rm -rf /var/lib/mysql
      
     - Definir as permissões no novo diretório adequadamente
        
        	chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/  
            
     - Criar um symlink apontando o diretório antigo para o novo local na partição do RAID
    
    		ln -s /mnt/data/mysql /var/lib/mysql   

5. Como [SELinux interferirá nas operações de cluster](http://galeracluster.com/documentation-webpages/configuration.html#selinux), é necessário desabilitá-lo para a sessão atual (até que surja uma versão compatível). Editar `/etc/selinux/config` para desabilitá-lo para reinicializações subsequentes:
    	
	        setenforce 0
    
       then editing `/etc/selinux/config` to set `SELINUX=permissive`
       
6. Validar a execução do MySQL

    - Iniciar o MySQL
    
    		service mysql start
            
    - Proteger a instalação do MySQL, definir a senha raiz, remova usuários anônimos, desabilitando o logon de raiz remoto e remover o banco de dados de teste
    		
            mysql_secure_installation
            
    - Criar um usuário no banco de dados para as operações de cluster e, opcionalmente, seus aplicativos
   
			mysql -u root -p
			GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
            exit
   
   - Parar o MySQL
   
			service mysql stop
            
7. Crie espaço reservado para configuração

	- Edite a configuração do MySQL para criar um espaço reservado para as configurações de cluster. Não substitua o **`<Vairables>`** ou o remova agora. Isso acontecerá depois de criarmos uma VM com base neste modelo.
	
			vi /etc/my.cnf.d/server.cnf
			
	- Edite a seção **[galera]** e limpe-a
	
	- Edite a seção **[mariadb]**
	
			wsrep_provider=/usr/lib64/galera/libgalera_smm.so 
            binlog_format=ROW
            wsrep_sst_method=rsync
			bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
			default_storage_engine=InnoDB
            innodb_autoinc_lock_mode=2
			
            wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
            #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
            #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
            #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
			#wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server

8. Abra as portas necessárias no firewall (usando FirewallD no CentOS 7)
	
	- MySQL: `firewall-cmd --zone=public --add-port=3306/tcp --permanent`
    - GALERA: `firewall-cmd --zone=public --add-port=4567/tcp --permanent`
    - GALERA IST: `firewall-cmd --zone=public --add-port=4568/tcp --permanent`
    - RSYNC: `firewall-cmd --zone=public --add-port=4444/tcp --permanent`
    - Recarregue o firewall: `firewall-cmd --reload`
	
9.  Otimize o sistema para o desempenho. Consulte este artigo em [estratégia de ajuste de desempenho] para obter mais detalhes

	- Edite o arquivo de configuração de MySQL novamente
	
			vi /etc/my.cnf.d/server.cnf
		
	- Edite a seção **[mariadb]** e acrescente o seguinte
	
	> [AZURE.NOTE] É recomendável que o **innodb\_buffer\_pool_size** seja 70% da memória da VM. Ele foi definido como 2,45 GB aqui para a VM do Azure Médio com 3,5 GB de RAM.
	
	        innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70% of physical memory.
            innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
            max_connections = 5000 # A larger value will give the server more time to recycle idled connections
            innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
            innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
            innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
			query_cache_size = 0
			
10. Pare o MySQL, desabilite o serviço MySQL para que não seja executado na inicialização, de modo a evitar o estrago do cluster ao adicionar um novo nó e desprovisionar a máquina.

		service mysql stop
        chkconfig mysql off
		waagent -deprovision	
		
11. Capture a VM por meio do portal. (Atualmente, as ferramentas da [emissão #1268 na CLI do Azure] descrevem o fato de que as imagens capturadas pelas ferramentas xplat-cli não capturam os discos de dados anexados.)

	- Desligue a máquina por meio do portal
    - Clique em Capturar, especifique o nome da imagem como **mariadb-galera-image**, forneça uma descrição e marque a opção "I have run waagent".
	![Capture the Virtual Machine](./media/virtual-machines-mariadb-cluster/Capture.png)
	![Capture the Virtual Machine](./media/virtual-machines-mariadb-cluster/Capture2.PNG)
	
## Criação do cluster
 
Crie 3 VMs a partir do modelo recém-criado e, em seguida, configure e inicie o cluster.

1. Crie a primeira VM do CentOS 7 a partir da imagem **mariadb-galera-image** da imagem que você criou, fornecendo o nome da rede virtual **mariadbvnet** e a sub-rede **mariadb**, tamanho da máquina **Médio**, passando o nome do Serviço de Nuvem para que seja **mariadbha** (ou qualquer nome que você desejar para ser acessado pelo mariadbha.cloudapp.net), configurando o nome dessa máquina para que seja **mariadb1** e o nome de usuário para **azureuser**, e habilitando o acesso SSH e passando o arquivo .pem do certificado SSH e substituindo **/path/to/key.pem** pelo caminho onde você armazenou chave SSH .pem gerada.

	> [AZURE.NOTE] Os comandos a seguir são divididos em várias linhas para maior clareza, mas você deve inserir cada um como uma única linha.

		azure vm create 
        --virtual-network-name mariadbvnet
        --subnet-names mariadb 
        --availability-set clusteravset
		--vm-size Medium 
		--ssh-cert "/path/to/key.pem" 
		--no-ssh-password 
		--ssh 22 
		--vm-name mariadb1 
		mariadbha mariadb-galera-image azureuser 

2. Crie 2 mais Máquinas Virtuais ao _conectá-las_ ao serviço de nuvem **mariadbha** atualmente criado, alterando o **nome da VM**, bem como a **porta SSH** para uma porta exclusiva para que não entre em conflito com as outras VMs no mesmo Serviço de Nuvem.

		azure vm create		
        --virtual-network-name mariadbvnet
        --subnet-names mariadb 
        --availability-set clusteravset
		--vm-size Medium
		--ssh-cert "/path/to/key.pem"
		--no-ssh-password
		--ssh 23
		--vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
e para MariaDB3

		azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb 
        --availability-set clusteravset
		--vm-size Medium
		--ssh-cert "/path/to/key.pem"
		--no-ssh-password
		--ssh 24
		--vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser
		
3. Você precisará obter o endereço IP interno de cada uma das 3 VMs para a próxima etapa:

	![Getting IP address](./media/virtual-machines-mariadb-cluster/IP.png)

4. SSH dentro das 3 VMs e edite o arquivo de configuração em cada

		sudo vi /etc/my.cnf.d/server.cnf
		
	removendo **'wsrep_cluster_name'** e **'wsrep_cluster_address'**, removendo o **#** do início e validando se são, de fato, o que você deseja.
    Além disso, substitua **'<ServerIP>'** em **'wsrep_node_address'** e **'<NodeName>'** em **'wsrep_node_name'** com o endereço IP e o nome da VM, respectivamente, removendo essas linhas também.
	
5. Inicie o cluster em MariaDB1 e deixe-o executando na inicialização

		sudo service mysql bootstrap
        chkconfig mysql on
	
6. Inicie o MySQL em MariaDB2 e MariaDB3 e deixe-o executando na inicialização

		sudo service mysql start
        chkconfig mysql on
		
## Balanceamento de carga do cluster
Quando você criou as VMs clusterizadas, você as adicionou em um conjunto de disponibilidade chamado **clusteravset** para garantir que elas fossem colocados em diferentes domínios de falha e atualização e que o Azure nunca faça manutenção em todos as máquinas ao mesmo tempo. Essa configuração atende aos requisitos para ser suportada pelo Contrato de Nível de Serviço do Azure (SLA).

Agora você deve usar o balanceador de carga do Azure para balancear as solicitações entre os nossos 3 nós.

Execute os comandos a seguir em sua máquina usando a CLI do Azure.
A estrutura dos parâmetros de comando é: `azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

	azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306
	
Por fim, já que a CLI define o intervalo de sondagem do balanceador de carga para 15 segundos (que podem ser bastante longos), altere-o no portal em **Pontos de extremidade** para qualquer uma das VMs

![Edit endpoint](./media/virtual-machines-mariadb-cluster/Endpoint.PNG)

em seguida, clique em Reconfigurar o Conjunto com Balanceamento de Carga e continue

![Reconfigure Load Balanced Set](./media/virtual-machines-mariadb-cluster/Endpoint2.PNG)

em seguida, altere o intervalo de sondagem para 5 segundos e salve

![Change Probe Interval](./media/virtual-machines-mariadb-cluster/Endpoint3.PNG)

## Validação do cluster

O trabalho pesado está feito. Agora, o cluster deve ser acessível em 'mariadbha.cloudapp.net:3306', onde ocorrerá as solicitações de balanceador de carga e de rota entre as 3 VMs de forma fluente e eficaz.

Use seu cliente MySQL favorito para conectar-se ou apenas conecte-se a partir de uma das VMs para verificar se o cluster está funcionando.

	 mysql -u cluster -h mariadbha.cloudapp.net -p
	 
Em seguida, crie um novo banco de dados e preencha-o com alguns dados

	CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
	INSERT INTO TestTable (value)  VALUES ('Value1');
	INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;
	
Resultará na tabela abaixo

	+----+--------+
	| id | value  |
	+----+--------+
	|  1 | Value1 |
	|  4 | Value2 |
	+----+--------+
	2 rows in set (0.00 sec)	
	
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

Neste artigo, você criou um cluster MariaDB + Galera de 3 nós altamente disponível nas Máquinas Virtuais do Azure executando o CentOS 7. As VMs tiveram a carga balanceada com o balanceador de carga do Azure.

Talvez você queira dar uma olhada em [outra maneira de clusterizar o MySQL no Linux] e nas formas de [otimizar e testar o desempenho do MySQL nas VMs Linux do Azure].

<!--Anchors-->
[Visão geral da arquitetura]: #architecture-overview
[Criação do modelo]: #creating-the-template
[Criação do cluster]: #creating-the-cluster
[Balanceamento de carga do cluster]: #load-balancing-the-cluster
[Validação do cluster]: #validating-the-cluster
[Próximas etapas]: #next-steps

<!--Image references-->

<!--Link references-->
[Azure CLI]: http://azure.microsoft.com/documentation/articles/xplat-cli/
[Azure CLI command reference]: http://azure.microsoft.com/documentation/articles/command-line-tools/
[create an SSH key for authentication]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[performance tuning strategy]: http://azure.microsoft.com/documentation/articles/virtual-machines-linux-optimize-mysql-perf/
[otimizar e testar o desempenho do MySQL nas VMs Linux do Azure]:http://azure.microsoft.com/documentation/articles/virtual-machines-linux-optimize-mysql-perf/
[issue #1268 in the Azure CLI]:https://github.com/Azure/azure-xplat-cli/issues/1268
[outra maneira de clusterizar o MySQL no Linux]: http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-cluster/
\<!--HONumber=42-->
