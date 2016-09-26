<properties
	pageTitle="Configurando o Oracle Data Guard em VMs | Microsoft Azure"
	description="Acompanhe um tutorial para configurar e implementar o Oracle Data Guard em máquinas virtuais do Azure para alta disponibilidade e recuperação de desastres."
	services="virtual-machines-windows"
	authors="rickstercdn"
	manager="timlt"
	documentationCenter=""
	tags="azure-service-management"/>
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="09/06/2016"
	ms.author="rclaus" />

#Configurando o Oracle Data Guard para Azure


Este tutorial demonstra como configurar e implementar o Oracle Data Guard em um ambiente de Máquinas Virtuais do Azure para alta disponibilidade e recuperação de desastre. O tutorial concentra-se na replicação unidirecional para bancos de dados não Oracle RAC.

O Oracle Data Guard dá suporte à proteção de dados e recuperação de desastres para o banco de dados Oracle. É uma solução simples, pronta para uso e de alto desempenho para recuperação de desastres, proteção de dados e alta disponibilidade para todo o banco de dados Oracle.

Este tutorial presume que você já tem conhecimento teórico e prático sobre os conceitos de alta disponibilidade e recuperação de desastre do Oracle Database. Para obter informações, veja o [Site da Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html) e também o [Guia de administração e conceitos do Oracle Data Guard](https://docs.oracle.com/cd/E11882_01/server.112/e41134/toc.htm).

Além disso, o tutorial presume que você já tenha implementado os seguintes pré-requisitos:

- Você já examinou a seção de Considerações sobre alta disponibilidade e recuperação de desastre no tópico [Considerações diversas sobre imagens de máquinas virtuais do Oracle](virtual-machines-windows-classic-oracle-considerations.md). Observe que o Azure dá suporte a instâncias do Oracle Database autônomas, mas não a Oracle RAC (Oracle Real Application Clusters) ao mesmo tempo.


- Você criou duas VMs (Máquinas Virtuais) no Azure usando a mesma imagem da Oracle Enterprise Edition fornecida pela plataforma. Verifique se as Máquinas Virtuais estão no [mesmo serviço de nuvem](virtual-machines-windows-load-balance.md) e na mesma Rede Virtual para garantir que possam se acessar mutuamente por um endereço IP privado persistente. Além disso, é recomendável colocar as VMs no mesmo [conjunto de disponibilidade](virtual-machines-windows-manage-availability.md) para permitir que o Azure coloque-as em domínios de falha e domínios de atualização separados. O Oracle Data Guard só está disponível com o Oracle Database Enterprise Edition. Cada computador deve ter pelo menos 2 GB de memória e 5 GB de espaço em disco. Para obter as informações mais atualizadas sobre os tamanhos de VM fornecidos pela plataforma, veja [Tamanhos de máquina virtual para o Azure](virtual-machines-windows-sizes.md). Se você precisar de volume de disco adicional para suas VMs, pode anexar discos adicionais. Para obter informações, veja [Como anexar um disco de dados a uma máquina virtual](virtual-machines-windows-classic-attach-disk.md).



- Você definiu os nomes de Máquina Virtual como "Machine1" para a VM primária e "Machine2" para a VM em espera no portal clássico do Azure.

- Você definiu a variável de ambiente **ORACLE\_HOME** para apontar para o mesmo caminho de instalação raiz do Oracle nas Máquinas Virtuais primária e em espera, como `C:\OracleDatabase\product\11.2.0\dbhome_1\database`.

- Faça logon no Windows Server como um membro do grupo **Administradores** ou um membro do grupo **ORA\_DBA**.

Neste tutorial, você irá:

Implementar o ambiente de banco de dados físico em espera

1. Criar um banco de dados primário

2. Preparar o banco de dados primário para a criação de banco de dados em espera

	1. Habilitar registro em log forçado

	2. Criar um arquivo de senha

	3. Configurar um log de restauração em espera

	4. Habilitar arquivamento

	5. Definir parâmetros de inicialização do banco de dados primário

Criar um banco de dados físico em espera

1. Preparar um arquivo de parâmetro de inicialização para banco de dados em espera

2. Configurar o ouvinte e tnsnames para oferecer suporte a banco de dados primário e máquinas em espera

	1. Configurar listener.ora em ambos os servidores para manter entradas para ambos os bancos de dados

	2. Para manter entradas para os bancos de dados primários e de espera, configure tnsnames.ora nas máquinas virtuais primárias e em espera.

	3. Inicie o ouvinte e verifique tnsping nas duas máquinas virtuais para os dois serviços.

3. Inicializar a instância em espera no estado nomount

4. Use RMAN para clonar o banco de dados e criar um banco de dados em espera

5. Iniciar o banco de dados físico em espera no modo de recuperação gerenciado

6. Verifique o banco de dados físico em espera

> [AZURE.IMPORTANT] Este tutorial foi configurado e testado com a seguinte configuração de hardware e software:
>
>| | **Banco de dados primário** | **Banco de dados em espera** |
>|----------------------|-------------------------------------------|-------------------------------------------|
>| **Versão do Oracle** | Versão do Oracle11g Enterprise (11.2.0.4.0) | Versão do Oracle11g Enterprise (11.2.0.4.0) |
>| **Nome da máquina** | Machine1 | Machine2 |
>| **Sistema operacional** | Windows 2008 R2 | Windows 2008 R2 |
>| **SID do Oracle** | TEST | TEST\_STBY |
>| **Memória** | Mínimo de 2 GB | Mínimo de 2 GB |
>| **Espaço em disco** | Mínimo de 5 GB | Mínimo de 5 GB |

Para versões subsequentes do Oracle Database e do Oracle Data Guard, pode haver algumas alterações adicionais que você precisa implementar. Para obter as informações mais atualizadas específicas de versão, veja a documentação do [Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) e do [Oracle Database](http://www.oracle.com/us/corporate/features/database-12c/index.html) no site da Oracle.

##Implementar o ambiente de banco de dados físico em espera
### 1\. Criar um banco de dados primário

- Crie um banco de dados primário "TEST" na máquina virtual primária. Para obter informações, consulte Criando e configurando um Oracle Database.
- Para ver o nome do seu banco de dados, conecte-se ao banco de dados como o usuário SYS com a função SYSDBA no prompt de comando SQL*Plus e execute a instrução a seguir:

		SQL> select name from v$database;

		The result will display like the following:

		NAME
		---------
		TEST
- Em seguida, consulte os nomes dos arquivos de banco de dados no modo de exibição do sistema dba\_data\_files:

		SQL> select file_name from dba_data_files;
		FILE_NAME
		-------------------------------------------------------------------------------
		C:\ <YourLocalFolder>\TEST\USERS01.DBF
		C:\ <YourLocalFolder>\TEST\UNDOTBS01.DBF
		C:\ <YourLocalFolder>\TEST\SYSAUX01.DBF
		C:<YourLocalFolder>\TEST\SYSTEM01.DBF
		C:<YourLocalFolder>\TEST\EXAMPLE01.DBF

### 2\. Preparar o banco de dados primário para a criação de banco de dados em espera

Antes de criar um banco de dados em espera, é recomendável garantir que o banco de dados primário esteja configurado corretamente. A seguir está uma lista das etapas que você precisa executar:

1. Habilitar registro em log forçado
2. Criar um arquivo de senha
3. Configurar um log de restauração em espera
4. Habilitar arquivamento
5. Definir parâmetros de inicialização do banco de dados primário

#### Habilitar registro em log forçado

Para implementar um banco de dados em espera, é necessário habilitar “Registro em log forçado” no banco de dados primário. Essa opção garante que, mesmo que uma operação de 'nologging' seja feita, o registro em log forçado terá precedência e todas as operações serão registradas nos logs de restauração. Portanto, podemos garantir que tudo no banco de dados primário seja registrado e a replicação em espera inclua todas as operações no banco de dados primário. Para habilitar o registro em log forçado, execute a instrução de banco de dados de alteração:

	SQL> ALTER DATABASE FORCE LOGGING;

	Database altered.

#### Criar um arquivo de senha

Para poder enviar e aplicar os logs arquivados do servidor primário para o servidor em espera, a senha sys deve ser idêntica nos servidores primários e em espera. É por isso que você cria um arquivo de senha no banco de dados primário e copiá-o para o servidor em espera.

>[AZURE.IMPORTANT] Ao usar o Oracle Database 12c, há um novo usuário, **SYSDG**, que você pode usar para administrar o Oracle Data Guard. Para obter mais informações, veja [Alterações no Oracle Database Versão 12c](http://docs.oracle.com/database/121/UNXAR/release_changes.htm#UNXAR404).

Além disso, certifique-se de que o ambiente ORACLE\_HOME já esteja definido em Machine1. Caso contrário, defina-o como uma variável de ambiente usando a caixa de diálogo Variáveis de Ambiente. Para acessar essa caixa de diálogo, inicie o utilitário **Sistema** clicando duas vezes no ícone Sistema no **Painel de Controle**; em seguida, clique na guia **Avançado** e escolha **Variáveis de Ambiente**. Para definir as variáveis de ambiente, clique no botão **Novo** em **Variáveis de Sistema**. Depois de configurar as variáveis de ambiente, feche o prompt de comando do Windows existente e abra um novo.

Execute a seguinte instrução para trocar para o diretório Oracle\_Home, como C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\database.

	cd %ORACLE_HOME%\database

Em seguida, crie um arquivo de senha usando o utilitário de criação de arquivo de senha, [ORAPWD](http://docs.oracle.com/cd/B28359_01/server.111/b28310/dba007.htm). No mesmo prompt de comando do Windows em Machine1, execute o seguinte comando definindo o valor da senha como a senha de **SYS**:

	ORAPWD FILE=PWDTEST.ora PASSWORD=password FORCE=y

Esse comando cria um arquivo de senha, chamado de PWDTEST.ora, no diretório ORACLE\_HOME\\database. Você deve copiar esse arquivo manualmente para o diretório %ORACLE\_HOME%\\database em Machine2.

#### Configurar um log de restauração em espera

Em seguida, você precisa configurar um log de restauração em espera para que o primário possa receber corretamente a restauração quando ficar em espera. Criá-los previamente aqui também permite que os logs de restauração em espera sejam criados automaticamente na espera. É importante configurar SRL (logs de restauração em espera) com o mesmo tamanho que os logs de restauração online. O tamanho dos arquivos de log de restauração em espera atual deve corresponder exatamente ao tamanho dos arquivos de log de restauração online do banco de dados primário atual.

Execute a seguinte instrução no prompt de comando SQL*PLUS na Machine1. O arquivo v$logfile é um modo de exibição do sistema que contém informações sobre arquivos de log de restauração.

	SQL> select * from v$logfile;
	GROUP# STATUS  TYPE    MEMBER                                                       IS_
	---------- ------- ------- ------------------------------------------------------------ ---
	3         ONLINE  C:<YourLocalFolder>\TEST\REDO03.LOG               NO
	2         ONLINE  C:<YourLocalFolder>\TEST\REDO02.LOG               NO
	1         ONLINE  C:<YourLocalFolder>\TEST\REDO01.LOG               NO
	Next, query the v$log system view, displays log file information from the control file.
	SQL> select bytes from v$log;
	BYTES
	----------
	52428800
	52428800
	52428800


Observe que 52428800 é 50 megabytes.

Em seguida, na janela SQL*Plus, execute as seguintes instruções para adicionar um novo grupo de arquivo de log de restauração em espera a um banco de dados em espera e especifique um número que identifique o grupo usando a cláusula GROUP. Usar números de grupo pode facilitar a administração de grupos de arquivos de log de restauração em espera:

	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 4 'C:<YourLocalFolder>\TEST\REDO04.LOG' SIZE 50M;
	Database altered.
	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 5 'C:<YourLocalFolder>\TEST\REDO05.LOG' SIZE 50M;
	Database altered.
	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 6 'C:<YourLocalFolder>\TEST\REDO06.LOG' SIZE 50M;
	Database altered.

Então execute o seguinte modo de exibição do sistema para listar informações sobre restauração de arquivos de log. Essa operação também verifica se os grupos de arquivos de log de restauração em espera foram criados:

	SQL> select * from v$logfile;
	GROUP# STATUS  TYPE MEMBER IS_
	---------- ------- ------- --------------------------------------------- ---
	3         ONLINE C:<YourLocalFolder>\TEST\REDO03.LOG NO
	2         ONLINE C:<YourLocalFolder>\TEST\REDO02.LOG NO
	1         ONLINE C:<YourLocalFolder>\TEST\REDO01.LOG NO
	4         STANDBY C:<YourLocalFolder>\TEST\REDO04.LOG
	5         STANDBY C:<YourLocalFolder>\TEST\REDO05.LOG NO
	6         STANDBY C:<YourLocalFolder>\TEST\REDO06.LOG NO
	6 rows selected.

#### Habilitar arquivamento

Em seguida, habilite o arquivamento executando as seguintes instruções para colocar o banco de dados primário no modo ARCHIVELOG e habilitar o arquivamento automático. Você pode habilitar o modo de log do arquivo-morto montando o banco de dados e, em seguida, executando o comando archivelog.

Primeiro, faça logon como sysdba. No prompt de comando do Windows, execute:

	sqlplus /nolog

	connect / as sysdba

Em seguida, desligue o banco de dados no prompt de comando SQL*Plus:

	SQL> shutdown immediate;
	Database closed.
	Database dismounted.
	ORACLE instance shut down.

Em seguida, execute o comando de montagem de inicialização para montar o banco de dados. Isso garante que o Oracle associe a instância ao banco de dados especificado.

	SQL> startup mount;
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes
	Database mounted.

Em seguida, execute:

	SQL> alter database archivelog;
	Database altered.

Em seguida, execute a instrução Alter do banco de dados com a cláusula Open para disponibilizar o banco de dados para uso normal:

	SQL> alter database open;

	Database altered.

#### Definir parâmetros de inicialização do banco de dados primário

Para configurar o Data Guard, você precisa criar e configurar os parâmetros em espera em um arquivo regular (arquivo de parâmetro de inicialização de texto) primeiro. Quando o arquivo estiver pronto, você precisará convertê-lo para um arquivo de parâmetro do servidor (SPFILE).

Você pode controlar o ambiente do Data Guard usando os parâmetros no arquivo INIT. ORA. Ao seguir este tutorial, você precisa atualizar o banco de dados primário INIT.ORA para que ele possa conter as duas funções: primária ou espera.

	SQL> create pfile from spfile;
	File created.

Em seguida, você precisa editar o arquivo para adicionar os parâmetros em espera. Para fazer isso, abra o arquivo INITTEST.ORA no local de %ORACLE\_HOME%\\database. Em seguida, anexe as instruções a seguir ao arquivo INITTEST.ora. A convenção de nomenclatura para o arquivo INIT.ORA é INIT<YourDatabaseName>.ORA.

	db_name='TEST'
	db_unique_name='TEST'
	LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
	LOG_ARCHIVE_DEST_1= 'LOCATION=C:\OracleDatabase\archive   VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=TEST'
	LOG_ARCHIVE_DEST_2= 'SERVICE=TEST_STBY LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=TEST_STBY'
	LOG_ARCHIVE_DEST_STATE_1=ENABLE
	LOG_ARCHIVE_DEST_STATE_2=ENABLE
	REMOTE_LOGIN_PASSWORDFILE=EXCLUSIVE
	LOG_ARCHIVE_FORMAT=%t_%s_%r.arc
	LOG_ARCHIVE_MAX_PROCESSES=30
	# Standby role parameters --------------------------------------------------------------------
	fal_server=TEST_STBY
	fal_client=TEST
	standby_file_management=auto
	db_file_name_convert='TEST_STBY','TEST'
	log_file_name_convert='TEST_STBY','TEST'
	# ---------------------------------------------------------------------------------------------


O bloco de instrução anterior inclui dois itens de configuração importantes:
-	**LOG\_ARCHIVE\_CONFIG...:** as IDs de banco de dados exclusivas são definidas com essa instrução.
-	**LOG\_ARCHIVE\_DEST\_1...:** a localização da pasta de arquivo morto local é definida com essa instrução. Recomendamos criar um novo diretório para as necessidades de arquivamento de seu banco de dados e especificar a localização do arquivo morto local usando esta instrução explicitamente, em vez de usar a pasta padrão do Oracle %ORACLE\_HOME%\\database\\archive.
-	**LOG\_ARCHIVE\_DEST\_2 .... LGWR ASYNC...:** você define um processo de LGWR (gravador de log) assíncrono (LGWR) para coletar dados de restauração de transação e transmiti-los para destinos em espera. Aqui, o DB\_UNIQUE\_NAME especifica um nome exclusivo para o banco de dados no servidor em espera de destino.

Quando o novo arquivo de parâmetro estiver pronto, você precisa criar o spfile por meio dele.

Primeiro, desligue o banco de dados:

	SQL> shutdown immediate;

	Database closed.

	Database dismounted.

	ORACLE instance shut down.

Em seguida, execute o comando nomount de inicialização da seguinte maneira:

	SQL> startup nomount pfile='c:\OracleDatabase\product\11.2.0\dbhome_1\database\initTEST.ora';
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes

Agora, crie um spfile:

	SQL>create spfile frompfile='c:\OracleDatabase\product\11.2.0\dbhome\_1\database\initTEST.ora';

	File created.

Então desligue o banco de dados:

	SQL> shutdown immediate;

	ORA-01507: database not mounted

Então use o comando de inicialização para iniciar uma instância:

	SQL> startup;
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes
	Database mounted.
	Database opened.

##Criar um banco de dados físico em espera
Esta seção aborda as etapas que devem ser executadas em Machine2 para preparar o banco de dados físico em espera.

Primeiro, você precisa criar a área de trabalho remota para Machine2 por meio do portal clássico do Azure.

Em seguida, no servidor em espera (Machine2), crie todas as pastas necessárias para o banco de dados em espera, como C:\\<YourLocalFolder>\\TEST. Ao seguir este tutorial, certifique-se de que a estrutura de pasta corresponde à estrutura de pastas em Machine1 para manter todos os arquivos necessários, como controlfile, datafiles, redologfiles, udump, bdump e cdump. Além disso, defina as variáveis de ambiente ORACLE\_HOME e ORACLE\_BASE em Machine2. Caso contrário, defina-as como uma variável de ambiente usando a caixa de diálogo Variáveis de Ambiente. Para acessar essa caixa de diálogo, inicie o utilitário **Sistema** clicando duas vezes no ícone Sistema no **Painel de Controle**; em seguida, clique na guia **Avançado** e escolha **Variáveis de Ambiente**. Clique no botão **Novo** em **Variáveis de Sistema** para definir as variáveis de ambiente. Depois de configurar as variáveis de ambiente, você precisa fechar o prompt de comando do Windows existente e abrir um novo para ver as alterações.

Depois siga estas etapas:

1. Preparar um arquivo de parâmetro de inicialização para banco de dados em espera

2. Configurar o ouvinte e tnsnames para oferecer suporte a banco de dados primário e máquinas em espera

	1. Configurar listener.ora em ambos os servidores para manter entradas para ambos os bancos de dados

	2. Configurar tnsnames.ora nas máquinas virtuais primária e em espera para manter entradas para bancos de dados primários e em espera

	3. Inicie o ouvinte e verifique tnsping nas duas máquinas virtuais para os dois serviços.

3. Inicializar a instância em espera no estado nomount

4. Use RMAN para clonar o banco de dados e criar um banco de dados em espera

5. Iniciar o banco de dados físico em espera no modo de recuperação gerenciado

6. Verifique o banco de dados físico em espera

### 1\. Preparar um arquivo de parâmetro de inicialização para banco de dados em espera

Esta seção demonstra como preparar um arquivo de parâmetro de inicialização para o banco de dados em espera. Para fazer isso, primeiro copie o arquivo INITTEST.ORA de Machine1 para Machine2 manualmente. Você deve ser capaz de ver o arquivo INITTEST.ORA na pasta %ORACLE\_HOME%\\database em ambas as máquinas. Em seguida, modifique o arquivo INITTEST.ora na Machine2 para configurá-lo para a função de espera conforme especificado abaixo:

	db_name='TEST'
	db_unique_name='TEST_STBY'
	db_create_file_dest='c:\OracleDatabase\oradata\test_stby’
	db_file_name_convert=’TEST’,’TEST_STBY’
	log_file_name_convert='TEST','TEST_STBY'


	job_queue_processes=10
	LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
	LOG_ARCHIVE_DEST_1='LOCATION=c:\OracleDatabase\TEST_STBY\archives VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=’TEST'
	LOG_ARCHIVE_DEST_2='SERVICE=TEST LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE)
	LOG_ARCHIVE_DEST_STATE_1='ENABLE'
	LOG_ARCHIVE_DEST_STATE_2='ENABLE'
	LOG_ARCHIVE_FORMAT='%t_%s_%r.arc'
	LOG_ARCHIVE_MAX_PROCESSES=30


O bloco de instrução anterior inclui dois itens de configuração importantes:

-	***.LOG\_ARCHIVE\_DEST\_1:** você precisa criar manualmente a pasta c:\\OracleDatabase\\TEST\_STBY\\archives em Machine2.
-	***.LOG\_ARCHIVE\_DEST\_2:** esta é uma etapa opcional. Você define isso porque poderá ser necessário quando a máquina primária estiver em manutenção e o computador em espera se tornar um banco de dados primário.

Em seguida, você precisa iniciar a instância em espera. No servidor de banco de dados em espera, digite o seguinte comando em um prompt de comando do Windows para criar uma instância do Oracle criando um serviço Windows:

	oradim -NEW -SID TEST\_STBY -STARTMODE MANUAL

O comando **Oradim** cria uma instância do Oracle, mas não a inicia. Você pode encontrá-la no diretório C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\BIN.

##Configurar o ouvinte e tnsnames para oferecer suporte a banco de dados primário e máquinas em espera
Antes de criar um banco de dados em espera, você precisa certificar-se de que os bancos de dados primários e em espera em sua configuração possa se comunicar entre si. Para fazer isso, você precisa configurar o ouvinte e TNSNames manualmente ou usando o utilitário de configuração de rede NETCA. Essa é uma tarefa obrigatória quando você usa o utilitário RMAN (Gerenciador de Recuperação).

### Configurar listener.ora em ambos os servidores para manter entradas para ambos os bancos de dados

Crie a área de trabalho remota para Machine1 e edite o arquivo listener.ora conforme especificado abaixo. Quando você editar o arquivo listener.ora, certifique-se de que a abertura e o fechamento de parêntese alinhem-se na mesma coluna. Você pode encontrar o arquivo listener.ora na seguinte pasta: c:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\NETWORK\\ADMIN\\.

	# listener.ora Network Configuration File: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

	# Generated by Oracle configuration tools.

	SID_LIST_LISTENER =
	  (SID_LIST =
	    (SID_DESC =
	      (SID_NAME = test)
	      (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
	      (PROGRAM = extproc)
	      (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
	    )
	  )

	LISTENER =
	  (DESCRIPTION_LIST =
	    (DESCRIPTION =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
	    )
	  )

A seguir, crie a área de trabalho remota para a Machine2 e edite o arquivo listener.ora da seguinte maneira: arquivo de configuração de rede # listener.ora: C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\network\\admin\\listener.ora

	# Generated by Oracle configuration tools.

	SID_LIST_LISTENER =
	  (SID_LIST =
	    (SID_DESC =
	      (SID_NAME = test_stby)
	      (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
	      (PROGRAM = extproc)
	      (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
	    )
	  )

	LISTENER =
	  (DESCRIPTION_LIST =
	    (DESCRIPTION =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
	    )
	  )


### Configurar tnsnames.ora nas máquinas virtuais primária e em espera para manter entradas para bancos de dados primários e em espera

Crie a área de trabalho remota para Machine1 e edite o arquivo tnsnames.ora como especificado abaixo. Você pode encontrar o arquivo tnsnames.ora na seguinte pasta: c:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\NETWORK\\ADMIN\\.

	TEST =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test)
	    )
	  )

	TEST_STBY =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test_stby)
	    )
	  )

Crie a área de trabalho remota para Machine2 e edite o arquivo tnsnames.ora da seguinte maneira:

	TEST =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test)
	    )
	  )

	TEST_STBY =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test_stby)
	    )
	  )


### Inicie o ouvinte e verifique tnsping nas duas máquinas virtuais para os dois serviços.

Abra um novo prompt de comando do Windows na máquina virtual primária e na em espera e execute as instruções a seguir:

	C:\Users\DBAdmin>tnsping test

	TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:08
	Copyright (c) 1997, 2010, Oracle.  All rights reserved.
	Used parameter files:
	C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
	Used TNSNAMES adapter to resolve the alias
	Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))) (CONNECT_DATA = (SER
	VICE_NAME = test)))
	OK (0 msec)


	C:\Users\DBAdmin>tnsping test_stby

	TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:16
	Copyright (c) 1997, 2010, Oracle.  All rights reserved.
	Used parameter files:
	C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
	Used TNSNAMES adapter to resolve the alias
	Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))) (CONNECT_DATA = (SER
	VICE_NAME = test_stby)))
	OK (260 msec)


##Inicializar a instância em espera no estado nomount
Configurar o ambiente para dar suporte ao banco de dados em espera na máquina virtual em espera (MACHINE2).

Primeiro, copie o arquivo de senha da máquina primária (Machine1) para a máquina em espera (Machine2) manualmente. Isso é necessário, pois a senha **sys** deve ser idêntica nos dois computadores.

Em seguida, abra o prompt de comando do Windows em Machine2 e configure as variáveis de ambiente para apontar para o banco de dados em espera da seguinte maneira:

	SET ORACLE_HOME=C:\OracleDatabase\product\11.2.0\dbhome_1
	SET ORACLE_SID=TEST_STBY

Em seguida, iniciar o banco de dados em espera no estado nomount e, em seguida, gere um spfile.

Inicie o banco de dados:

	SQL>shutdown immediate;

	SQL>startup nomount
	ORACLE instance started.

	Total System Global Area  747417600 bytes
	Fixed Size                  2179496 bytes
	Variable Size             473960024 bytes
	Database Buffers          264241152 bytes
	Redo Buffers                7036928 bytes


##Use RMAN para clonar o banco de dados e criar um banco de dados em espera
Você pode usar o utilitário RMAN (Gerenciador de Recuperação) para executar qualquer cópia de backup do banco de dados primário para criar o banco de dados físico em espera.

Crie a área de trabalho remota para a VM em espera (MACHINE2) e execute o utilitário RMAN especificando uma cadeia de conexão completa para as instâncias DESTINO (banco de dados primário, Machine1) e AUXILIAR (database em espera, Machine2).

>[AZURE.IMPORTANT] Não use a autenticação do sistema operacional, pois ainda não há nenhum banco de dados na máquina do servidor em espera.

	C:\> RMAN TARGET sys/password@test AUXILIARY sys/password@test_STBY

	RMAN>DUPLICATE TARGET DATABASE
	  FOR STANDBY
	  FROM ACTIVE DATABASE
	  DORECOVER
	    NOFILENAMECHECK;

##Iniciar o banco de dados físico em espera no modo de recuperação gerenciado
Este tutorial demonstra como criar um banco de dados em espera. Para obter informações sobre como criar um banco de dados lógico em espera, consulte a documentação do Oracle.

Abra o prompt de comando SQL*Plus e habilite o Data Guard na máquina virtual em espera ou no servidor (MACHINE2) da seguinte maneira:

	SHUTDOWN IMMEDIATE;
	STARTUP MOUNT;
	ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;

Quando você abre o banco de dados em espera no modo **MONTAGEM**, o envio de log de arquivo morto continua e o processo de recuperação gerenciado continua aplicando o log no banco de dados em espera. Isso garante que o banco de dados em espera permaneça atualizado com o banco de dados primário. Observe que o banco de dados em espera não pode ser acessado para fins de relatório durante esse tempo.

Quando você abre o banco de dados em espera no modo **SOMENTE LEITURA**, o envio de log do arquivo morto continua. Mas o processo de recuperação gerenciado para. Isso faz com que o banco de dados em espera se torne cada vez mais desatualizado até que o processo de recuperação gerenciado seja retomado. Você pode acessar o banco de dados em espera para fins de relatório durante esse tempo, mas os dados podem não refletir as alterações mais recentes.

Em geral, recomendamos manter o banco de dados em espera no modo **MONTAGEM** para manter os dados no banco de dados em espera atualizados em caso de falha do banco de dados primário. No entanto, você pode manter o banco de dados em espera no modo **SOMENTE LEITURA** para fins de relatório, dependendo dos requisitos de seu aplicativo. As etapas a seguir demonstram como habilitar o Data Guard no modo somente leitura usando SQL*Plus:

	SHUTDOWN IMMEDIATE;
	STARTUP MOUNT;
	ALTER DATABASE OPEN READ ONLY;


##Verifique o banco de dados físico em espera
Esta seção demonstra como verificar a configuração de alta disponibilidade como um administrador.

Abra a janela de prompt de comando SQL*Plus e consulte o log de restauração em arquivo-morto na máquina virtual em espera (Machine2):

	SQL> show parameters db_unique_name;

	NAME                                TYPE       VALUE
	------------------------------------ ----------- ------------------------------
	db_unique_name                      string     TEST_STBY

	SQL> SELECT NAME FROM V$DATABASE

	SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

	SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
	----------------  ---------------  --------------- ------------
	45                    23-FEB-14   23-FEB-14   YES
	45                    23-FEB-14   23-FEB-14   NO
	46                    23-FEB-14   23-FEB-14   NO
	46                    23-FEB-14   23-FEB-14   YES
	47                    23-FEB-14   23-FEB-14   NO
	47                    23-FEB-14   23-FEB-14   NO

Abra a janela do prompt de comando SQL*Plus e troque os arquivos de log na máquina principal (Machine1):

	SQL> alter system switch logfile;
	System altered.

	SQL> archive log list
	Database log mode              Archive Mode
	Automatic archival             Enabled
	Archive destination            C:\OracleDatabase\archive
	Oldest online log sequence     69
	Next log sequence to archive   71
	Current log sequence           71

Consulte o log de restauração em arquivo-morto na máquina virtual em espera (Machine2):

	SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

	SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
	----------------  ---------------  --------------- ------------
	45                    23-FEB-14   23-FEB-14   YES
	46                    23-FEB-14   23-FEB-14   YES
	47                    23-FEB-14   23-FEB-14   YES
	48                    23-FEB-14   23-FEB-14   YES

	49                    23-FEB-14   23-FEB-14   YES
	50                    23-FEB-14   23-FEB-14   IN-MEMORY

Verifique se há qualquer lacuna na máquina virtual em espera (Machine2):

	SQL> SELECT * FROM V$ARCHIVE_GAP;
	no rows selected.

Outro método de verificação pode ser fazer failover para o banco de dados em espera e, em seguida, testar se é possível realizar failback para o banco de dados primário. Para ativar o banco de dados em espera como um banco de dados primário, use as instruções a seguir:

	SQL> ALTER DATABASE RECOVER MANAGED STANDBY DATABASE FINISH;
	SQL> ALTER DATABASE ACTIVATE STANDBY DATABASE;

Se você não tiver habilitado o flashback no banco de dados primário original, é recomendável remover o banco de dados primário original e recriar um banco de dados em espera.

Recomendamos habilitar o banco de dados de flashback nos bancos de dados primário e em espera. Quando ocorre um failover, o banco de dados primário pode ser atualizado de volta para o momento antes do failover e rapidamente convertido em um banco de dados em espera.

##Recursos adicionais
[Imagens de Máquina Virtual Oracle para Azure](virtual-machines-windows-classic-oracle-images.md)

<!---HONumber=AcomDC_0914_2016-->