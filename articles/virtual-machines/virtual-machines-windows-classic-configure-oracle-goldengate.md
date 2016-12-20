---
title: Configurando o Oracle GoldenGate nas VMs | Microsoft Docs
description: "Acompanhe um tutorial para configurar e implementar o Oracle GoldenGate em VMs do Windows Server do Azure para alta disponibilidade e recuperação de desastres."
services: virtual-machines-windows
author: rickstercdn
manager: timlt
documentationcenter: 
tags: azure-service-management
ms.assetid: 85ca238c-5925-4c5a-86c7-08338587b8c3
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/06/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 0530f382846099e4a81c32dff0812b5c688bc5f8


---
# <a name="configuring-oracle-goldengate-for-azure"></a>Configurando o Oracle GoldenGate para o Azure
Este tutorial demonstra como configurar Oracle GoldenGate para o ambiente de máquinas virtuais do Azure para alta disponibilidade e recuperação de desastres. O tutorial tem como foco a [replicação bidirecional](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_about_gg.htm) de bancos de dados não RAC da Oracle e requer que os dois sites estejam ativos.

O Oracle GoldenGate dá suporte para a distribuição e a integração de dados. Permite configurar uma solução de sincronização e distribuição de dados por meio da configuração de replicação Oracle-Oracle e fornece uma solução flexível de alta disponibilidade. O Oracle GoldenGate suplementa o Oracle Data Guard com os recursos de replicação para permitir a distribuição de informações em toda a empresa e atualizações e migrações sem tempo de inatividade. Para obter informações detalhadas, veja [Usando o Oracle GoldenGate com o Oracle Data Guard](http://docs.oracle.com/cd/E11882_01/server.112/e17157/unplanned.htm).

O Oracle GoldenGate contém os seguintes componentes principais: Extract, bomba de dados, Replicat, arquivos de trilhas ou extração, Pontos de verificação, Manager e Collector. Para ter replicação bidirecional entre dois sites, você precisa criar e iniciar todos os componentes em ambos os sites. Para obter informações detalhadas sobre a arquitetura do Oracle GoldenGate, confira o [Guia do Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html).

Neste tutorial, presumimos que você já tenha conhecimento teórico e prático sobre os conceitos de Alta Disponibilidade e Recuperação de Desastres do Orace Database, bem como do [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html). Para obter mais informações, confira o [Site da Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html).

Além disso, o tutorial presume que você já tenha implementado os seguintes pré-requisitos:

* Você já examinou a seção Considerações sobre Alta Disponibilidade e Recuperação de Desastre no tópico [Imagens de Máquinas Virtuais do Oracle - Considerações Diversas](virtual-machines-windows-classic-oracle-considerations.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) . Observe que o Azure dá suporte a instâncias de banco de dados Oracle autônomas, mas não a Oracle RAC (Oracle Real Application Clusters) ao mesmo tempo.
* Você baixou o software Oracle GoldenGate no site de [Downloads da Oracle](http://www.oracle.com/us/downloads/index.html) . Você selecionou o pacote de produto Oracle Fusion Middleware – Data Integration. Em seguida, você selecionou Oracle GoldenGate no Oracle v11.2.1 Media Pack para Microsoft Windows x64 (64 bits) para um banco de dados Oracle 11g. Em seguida, baixe V11.2.1.0.3 de GoldenGate Oracle para Oracle 11g 64 bits no Windows 2008 (64 bits).
* Você criou duas VMs (Máquinas Virtuais) no Azure usando a Oracle Enterprise Edition no Windows Server. Verifique se as Máquinas Virtuais estão no [mesmo serviço de nuvem](virtual-machines-linux-load-balance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e na mesma [Rede Virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para garantir que elas possam acessar umas às outros no endereço IP privado persistente.
* Você definiu os nomes da Máquina Virtual como "MachineGG1" para o Site A e "MachineGG2" para o Site B no portal clássico do Azure.
* Você criou bancos de dados de teste "TestGG1" no Site A e "TestGG2" no Site B.
* Você faça logon no servidor do Windows como um membro do grupo Administradores ou membro do grupo **ORA_DBA**.

Neste tutorial, você irá:

1. Configurar o banco de dados no Site A e no Site B  
   
   1. Executar o carregamento de dados inicial
2. Preparar o Site A e o Site B para replicação de banco de dados
3. Criar todos os objetos necessários para dar suporte à replicação de DDL
4. Configurar o GoldenGate Manager no Site A e no Site B
5. Criar os processos Extrair Grupo e Bomba de Dados no Site A e no Site B
   
   1. Criar os processos Extrair e Bomba de dados no Site A
   2. Criar uma tabela de ponto de verificação GoldenGate no Site B
   3. Adicionar REPLICAT no Site B
   4. Crie os processos Extrair e Bomba de Dados no Site B
   5. Criar uma tabela de ponto de verificação GoldenGate no Site A
   6. Adicionar REPLICAT no Site A
   7. Adicionar trandata no Site A e no Site B
   8. Inicie os processos Extrair e Bomba de dados no Site A
   9. Inicie os processos Extrair e Bomba de Dados no Site B
   10. Iniciar o processo de REPLICAT no Site A
   11. Iniciar o processo de REPLICAT no Site B
6. Verifique o processo de replicação bidirecional

> [!IMPORTANT]
> Este tutorial foi configurado e testado com a seguinte configuração de software:
> 
> |  | **Banco de dados do site A** | **Banco de dados do site B** |
> | --- | --- | --- |
> | **Versão do Oracle** |Oracle11g Versão 2 – (11.2.0.1) |Oracle11g Versão 2 – (11.2.0.1) |
> | **Nome da máquina** |MachineGG1 |MachineGG2 |
> | **Sistema operacional** |Windows 2008 R2 |Windows 2008 R2 |
> | **SID do Oracle** |TESTGG1 |TESTGG2 |
> | **Esquema de replicação** |SCOTT |SCOTT |
> 
> 

Para versões subsequentes do Oracle Database e do Oracle GoldenGate, pode haver algumas alterações adicionais que você precisa implementar. Para obter informações mais atualizadas e específicas da versão , consulte a documentação do [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) e do [Banco de Dados Oracle](http://www.oracle.com/us/corporate/features/database-12c/index.html) no site da Oracle. Por exemplo, para um banco de dados de origem versão 11.2.0.4 e posteriores, a captura de DDL é executada pelo servidor logmining de forma assíncrona e não requer a instalação de nenhum gatilho, tabela ou outro objetos de banco de dados especial. Atualizações do Oracle GoldenGate podem ser realizadas sem interromper os aplicativos do usuário. O uso de um gatilho DDL e objetos de suporte é necessário quando a extração estiver no modo integrado com um banco de dados de origem Oracle 11g anterior à versão 11.2.0.4. Para obter orientações detalhadas, veja [Instalando e configurando o Oracle GoldenGate para o Oracle Database](http://docs.oracle.com/goldengate/1212/gg-winux/GIORA.pdf).

## <a name="1-setup-database-on-site-a-and-site-b"></a>1. Configurar o banco de dados no Site A e no Site B
Esta seção explica como executar os pré-requisitos de banco de dados no Site A e no Site B. Você deve executar todas as etapas desta seção nos dois sites: A e B.

Primeiro, crie a área de trabalho remota para o Site A e o Site B através do portal clássico do Azure. Abra um prompt de comando do Windows e crie um diretório inicial para os arquivos de instalação do Oracle GoldenGate:

    mkdir C:\OracleGG

Em seguida, descompacte e instale o software Oracle GoldenGate nessa pasta. Após essa etapa, você pode iniciar o interpretador de comando do software GoldenGate (GGSCI) executando o seguinte comando:

    C:\OracleGG\.\ggsci

É possível usar [GGSCI](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_gettingstarted.htm) para executar vários comandos que configuram, controlam e monitoram o Oracle GoldenGate.

Em seguida, execute o seguinte comando para criar todas as subpastas do pacote de instalação:

    GGSCI (Hostname) 1> CREATE SUBDIRS

Execute o seguinte comando para sair do prompt de comando GGSCI:

    GGSCI (Hostname) 1> EXIT

Então, você precisa criar um usuário de banco de dados, que será usado pelos processos de Extração e Replicação do Oracle GoldenGate Manager. Observe que você pode criar usuários individuais para cada processo ou configurar apenas um usuário comum. Neste tutorial, podemos criar um usuário, que é chamado de ggate. Em seguida, concedemos a esse usuário os privilégios necessários. Observe que você deve realizar as seguintes operações no Site A e no Site B.

Abra a janela de comando Mais \* do SQL nos Sites A e B com privilégios de administrador do banco de dados usando o **SYSDBA**, como:

    Enter username: / as sysdba

E execute:

    SQL> create tablespace ggs_data   datafile 'c:\OracleDatabase\oradata\<DBNAME>\<DBNAME>ggs_data01.dbf' size 200m;
    SQL> create user ggate identified by ggate default tablespace ggs_data  temporary tablespace temp;
          grant connect, resource to ggate;
          grant select any dictionary, select any table to ggate;
          grant create table to ggate;
          grant flashback any table to ggate;
          grant execute on dbms_flashback to ggate;
          grant execute on utl_file to ggate;
          grant create any table to ggate;
          grant insert any table to ggate;
          grant update any table to ggate;
          grant delete any table to ggate;
          grant drop any table to ggate;

Em seguida, localize o arquivo .ORA\<DatabaseSID\> do INIT na pasta do banco de dados \_HOME%\\ do %ORACLE nos Sites A e B, e acrescente os seguintes parâmetros do banco de dados ao INITTEST.ora:

    UNDO\_MANAGEMENT=AUTO
    UNDO\_RETENTION=86400

Para obter uma lista completa de todos os comandos do GGSCI do Oracle GoldenGate, veja a [Referência do Oracle GoldenGate para Windows](http://docs.oracle.com/goldengate/1212/gg-winux/GWURF/ggsci_commands.htm).

### <a name="perform-initial-data-load"></a>Executar o carregamento de dados inicial
Você pode executar o carregamento de dados inicial do banco de dados seguindo vários métodos. Por exemplo, você pode usar o [Carregamento Direto do Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_initsync.htm) ou utilitários regulares de Exportação e Importação para exportar dados da tabela do Site A para o Site B.

Para demonstrar o processo de replicação do Oracle GoldenGate, este tutorial demonstra como criar uma tabela no Site A e no Site B usando os comandos a seguir.

Primeiro, abra a janela de comando Mais \* do SQL e execute o seguinte comando para criar uma tabela de inventário nos bancos de dados dos Sites A e B:

    create table scott.inventory
    (prod_id number,
    prod_category varchar2(20),
    qty_in_stock number,
    last_dml timestamp default systimestamp);

Em seguida, adicione uma restrição para a tabela recém-criada em bancos de dados do Site A e do Site B:

    alter table scott.inventory add constraint pk_inventory primary key (prod_id) ;

Em seguida, conceda privilégios sobre a nova tabela de inventário para o usuário ggate no Site A e no Site b:

    grant all on scott.inventory to ggate;

Em seguida, crie e habilite um gatilho de banco de dados, INVENTORY_CDR_TRG, na tabela recém-criada para certificar-se de que todas as transações para a nova tabela serão registradas se o usuário não for ggate. Execute essa operação no Site A e no Site B.

    CREATE OR REPLACE TRIGGER INVENTORY_CDR_TRG
    BEFORE UPDATE
    ON SCOTT.INVENTORY
    REFERENCING NEW AS New OLD AS Old
    FOR EACH ROW
    BEGIN
    IF SYS_CONTEXT ('USERENV', 'SESSION_USER') != 'GGATE'
    THEN
    :NEW.LAST_DML := SYSTIMESTAMP;
    END IF;
    END;
    /


## <a name="2-prepare-site-a-and-site-b-for-database-replication"></a>2. Preparar o Site A e o Site B para replicação de banco de dados
Esta seção explica como preparar o Site A e o Site B para replicação de banco de dados. Você deve executar todas as etapas desta seção nos dois sites: A e B.

Primeiro, crie a área de trabalho remota para o Site A e o Site B através do portal clássico do Azure. Alterne o banco de dados para o modo archivelog usando a janela Comando SQL*Plus:

    sql>shutdown immediate
    sql>startup mount
    sql>alter database archivelog;
    sql>alter database open;


Em seguida, habilite o [registro em log suplementar](http://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm) mínimo da seguinte maneira:

    SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

Em seguida, prepare o banco de dados para dar suporte à replicação de DDL (linguagem de definição de dados):

    SQL> alter system set recyclebin=off scope=spfile;

Em seguida, desligue e reinicie o banco de dados:

    sql>shutdown immediate
    sql>startup


## <a name="3-create-all-necessary-objects-to-support-ddl-replication"></a>3. Criar todos os objetos necessários para dar suporte à replicação de DDL
Esta seção lista os scripts que você precisa usar para criar todos os objetos necessários para dar suporte à replicação de DDL. Você precisa executar os scripts especificados nesta seção no Site A e no Site B.

Abra um prompt de comando do Windows e navegue até a pasta Oracle GoldenGate, como C:\\OracleGG. Inicie o prompt de comando Mais \* do SQL com privilégios de administrador do banco de dados, como, por exemplo, usando **SYSDBA**, nos Sites A e B.

Em seguida, execute os seguintes scripts:

    SQL> @marker_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @ddl_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @role_setup.sql
    Enter GoldenGate schema name: ggate
    SQL> grant ggs_ggsuser_role to ggate;
     Grant succeeded.
     SQL> @ddl_enable
     Trigger altered.
     SQL> @ddl_pin ggate


A ferramenta Oracle GoldenGate requer um logon no nível de tabela para suporte a DDL (linguagem de definição de dados). Assim, habilite o registro em log no nível de tabela usando o comando ADD TRANDATA suplementar. Abra a janela do interpretador de comando do Oracle GoldenGate, faça logon no banco de dados e execute o comando ADD TRANDATA:

    GGSCI 5> DBLOGIN USERID ggate, PASSWORD ggate

    GGSCI(Hostname) 6> add trandata scott.inventory

## <a name="4-configure-goldengate-manager-on-site-a-and-site-b"></a>4. Configurar o GoldenGate Manager no Site A e no Site B
O Oracle GoldenGate Manager executa várias funções, como iniciar outros processos do GoldenGate, gerenciamento de arquivo de log de trilha e relatórios.

Você precisa configurar o processo do Oracle GoldenGate Manager no Site A e no Site B. Para fazer isso, execute as seguintes etapas nos Site A e B.

Abra a janela Comando do Windows e inicie o interpretador de comandos do Oracle GoldenGate:

    cd C:\OracleGG\
    c:\OracleGG>ggsci
    Oracle GoldenGate Command Interpreter for Oracle
    Version 11.2.1.0.3 14400833 OGGCORE_11.2.1.0.3_PLATFORMS_120823.1258
    Windows x64 (optimized), Oracle 11g on Aug 23 2012 16:50:36
    Copyright (C) 1995, 2012, Oracle and/or its affiliates. All rights reserved.


Registra a sessão GGSCI em um banco de dados para que você possa executar comandos que afetam o banco de dados:

    GGSCI (HostName) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

Exiba o status e a latência (quando pertinente) para todos os processos de Manager, Extract e Replicat em um sistema:

    GGSCI (HostName) 2> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

Abra o arquivo de parâmetro usando o comando EDIT PARAMS e, em seguida, acrescente as seguintes informações:

    GGSCI (HostName) 3> edit params mgr
    PORT 7809
    USERID ggate, PASSWORD ggate
    PURGEOLDEXTRACTS  C:\OracleGG\dirdat\ex, USECHECKPOINTS

Exiba o status e a latência (quando pertinente) para todos os processos de Manager, Extract e Replicat em um sistema:

    GGSCI (HostName) 46> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

Registra a sessão GGSCI em um banco de dados para que você possa executar comandos que afetam o banco de dados:

    GGSCI (HostName) 47> dblogin USERID ggate, PASSWORD ggate

    Successfully logged into database.

Inicie o processo de gerenciador:

    GGSCI (HostName) 48> start manager
    Manager started.

## <a name="5-create-extract-group-and-data-pump-processes-on-site-a-and-site-b"></a>5. Criar os processos Extrair Grupo e Bomba de Dados no Site A e no Site B
### <a name="create-extract-and-data-pump-processes-on-site-a"></a>Criar os processos Extrair e Bomba de dados no Site A
Você precisa criar processos Extrair e Bomba de dados no Site A e no Site B. Criar a área de trabalho remota ao Site A e ao Site B por meio do portal clássico do Azure. Abra a janela do interpretador de comando GGSCI. Execute os seguintes comandos no Site A:

    GGSCI (MachineGG1) 14> add extract ext1 tranlog begin now
    EXTRACT added.
    GGSCI (MachineGG1) 4> add exttrail C:\OracleGG\dirdat\lt, extract ext1
    EXTTRAIL added.
    GGSCI (MachineGG1) 16> add extract dpump1 exttrailsource C:\OracleGG\dirdat\aa
    EXTRACT added.
    GGSCI (MachineGG1) 17> add rmttrail C:\OracleGG\dirdat\ab extract dpump1
    RMTTRAIL added.

Abra o arquivo de parâmetro usando o comando EDIT PARAMS e, em seguida, acrescente as seguintes informações: GGSCI (MachineGG1) 18> edit params ext1 EXTRACT ext1 USERID ggate, PASSWORD ggate EXTTRAIL C:\OracleGG\dirdat\aa TRANLOGOPTIONS EXCLUDEUSER ggate TABLE scott.inventory, GETBEFORECOLS ( ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml), ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

Abra o arquivo de parâmetro usando o comando EDIT PARAMS e, em seguida, acrescente as seguintes informações:

    GGSCI (MachineGG1) 15> edit params dpump1
    EXTRACT dpump1
     USERID ggate, PASSWORD ggate
     RMTHOST ActiveGG2orcldb, MGRPORT 7809, TCPBUFSIZE 100000
     RMTTRAIL C:\OracleGG\dirdat\ab
     PASSTHRU
     TABLE scott.inventory;

### <a name="create-a-goldengate-checkpoint-table-on-site-b"></a>Criar uma tabela de ponto de verificação GoldenGate no Site B
Em seguida, você precisa adicionar uma tabela de ponto de verificação no Site B. Para fazer isso, abra uma janela do interpretador de comando GoldenGate e execute:

    C:\OracleGG\ggsci
    GGSCI (MachineGG2) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

Então adicione a tabela de ponto de verificação ao banco de dados, em que ggate é o proprietário:

    GGSCI (MachineGG2) 2> ADD CHECKPOINTTABLE ggate.checkpointtable
    Successfully created checkpoint table ggate.checkpointtable.

Adicione o nome da tabela de ponto de verificação ao arquivo GLOBALS no servidor de destino, que é o Site B nesta etapa. Edite o arquivo GLOBALS no Site B:

    GGSCI (MachineGG2) 1> EDIT PARAMS ./GLOBALS

Em seguida, anexe o parâmetro CHECKPOINTTABLE no arquivo GLOBALS existente:

    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

Como etapa final, salve e feche o arquivo de parâmetro GLOBALS.

### <a name="add-replicat-on-site-b"></a>Adicionar REPLICAT no Site B
Esta seção descreve como adicionar um processo REPLICAT "REP2" no Site B.

Use o comando ADD REPLICAT para criar um grupo Replicat no Site B:

    GGSCI (MachineGG2) 37> add replicat rep2 exttrail C:\OracleGG\dirdatab, checkpointtable ggate.checkpointtable

Abra o arquivo de parâmetro usando o comando EDIT PARAMS e, em seguida, acrescente as seguintes informações:

    GGSCI (MachineGG2) 10> edit params rep2
    REPLICAT rep2
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append,megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

### <a name="create-extract-and-data-pump-processes-on-site-b"></a>Crie os processos Extrair e Bomba de Dados no Site B
Esta seção descreve como criar um novo processo de extração "EXT2" e um novo processo de bomba de dados "DPUMP2" no Site B:

    GGSCI (MachineGG2) 3> add extract ext2 tranlog begin now
     EXTRACT added.
    GGSCI (MachineGG2) 4> add exttrail C:\OracleGG\dirdat\ac extract ext2
     EXTTRAIL added.
    GGSCI (MachineGG2) 5> add extract dpump2 exttrailsource C:\OracleGG\dirdat\ac
     EXTRACT added.
    GGSCI (MachineGG2) 6> add rmttrail C:\OracleGG\dirdat\ad extract dpump2
     RMTTRAIL added.

Abra o arquivo de parâmetro usando o comando EDIT PARAMS e, em seguida, acrescente as seguintes informações:

    GGSCI (MachineGG2) 31> edit params ext2
    EXTRACT ext2
    USERID ggate, PASSWORD ggate
    EXTTRAIL C:\OracleGG\dirdat\ac
    TRANLOGOPTIONS EXCLUDEUSER ggate
    TABLE scott.inventory,
    GETBEFORECOLS (
    ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml),
    ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

Abra o arquivo de parâmetro usando o comando EDIT PARAMS e, em seguida, acrescente as seguintes informações:

    GGSCI (MachineGG2) 32> edit params dpump2
    EXTRACT dpump2
    USERID ggate, PASSWORD ggate
    RMTHOST MachineGG1, MGRPORT 7809, TCPBUFSIZE 100000
    RMTTRAIL C:\OracleGG\dirdat\ad
    PASSTHRU
    TABLE scott.inventory;

### <a name="create-a-goldengate-checkpoint-table-on-site-a"></a>Criar uma tabela de ponto de verificação GoldenGate no Site A
Abra a janela do interpretador de comando Oracle GoldenGate e crie uma tabela de ponto de verificação:

    GGSCI (MachineGG1) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

    GGSCI (MachineGG1) 2> ADD CHECKPOINTTABLE ggate.checkpointtable

    Successfully created checkpoint table ggate.checkpointtable.

Você também precisa adicionar o nome da tabela de ponto de verificação ao arquivo GLOBALs no Site A.

Abra a janela do interpretador de comando Oracle GoldenGate e edite o arquivo GLOBALS no Site A:

    GGSCI (MachineGG1) 1> EDIT PARAMS ./GLOBALS
    Add the CHECKPOINTTABLE parameter to the existing GLOBALS file as follows:
    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

Salve e feche o arquivo de parâmetro GLOBALS.

### <a name="add-replicat-on-site-a"></a>Adicionar REPLICAT no Site A
Esta seção descreve como adicionar um processo REPLICAT "REP1" no Site A.

O comando a seguir cria um rep1 do grupo Replicat com o nome de uma trilha e a checkpointtable associada.

    GGSCI (MachineGG1) 21> add replicat rep1 exttrail C:\OracleGG\dirdat\ad,checkpointtable ggate.checkpointtable
     REPLICAT added.

Abra o arquivo de parâmetro usando o comando EDIT PARAMS e, em seguida, acrescente as seguintes informações:

    GGSCI (MachineGG1) 10> edit params rep1
    REPLICAT rep1
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append, megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

### <a name="add-trandata-on-site-a-and-site-b"></a>Adicionar trandata no Site A e no Site B
Habilite registro em log suplementar no nível de tabela usando o comando ADD TRANDATA. Abra janela do interpretador de comando do Oracle GoldenGate, faça logon no banco de dados e execute o comando ADD TRANDATA.

Crie a área de trabalho remota para MachineGG1, abra o interpretador de comando do Oracle GoldenGate e execute:

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Crie a área de trabalho remota para MachineGG2, abra o interpretador de comando do Oracle GoldenGate e execute:

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.

Exiba informações sobre o estado do registro em log suplementar no nível de tabela:

    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

### <a name="add-trandata-on-site-a-and-site-b"></a>Adicionar trandata no Site A e no Site B
Habilite registro em log suplementar no nível de tabela usando o comando ADD TRANDATA. Abra janela do interpretador de comando do Oracle GoldenGate, faça logon no banco de dados e execute o comando ADD TRANDATA.

Crie a área de trabalho remota para MachineGG1, abra o interpretador de comando do Oracle GoldenGate e execute:

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Crie a área de trabalho remota para MachineGG2, abra o interpretador de comando do Oracle GoldenGate e execute:

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.
    Display information about the state of table-level supplemental logging:
    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

### <a name="start-extract-and-data-pump-processes-on-site-a"></a>Inicie os processos Extrair e Bomba de dados no Site A
Inicie o processo Extrair ext1 no Site A:

    GGSCI (MachineGG1) 31> start extract ext1
    Sending START request to MANAGER …
    EXTRACT EXT1 starting

Inicie o processo de bomba de dados dpump1 no Site A:

    GGSCI (MachineGG1) 23> start extract dpump1
    Sending START request to MANAGER …
    EXTRACT DPUMP1 starting
Exiba informações sobre o grupo Extrair ext1: GGSCI (MachineGG1) 32> info extract ext1 EXTRACT    EXT1      Last Started 2013-11-25 08:03   Status RUNNING Checkpoint Lag       00:00:00 (updated 00:00:02 ago) Log Read Checkpoint  Oracle Redo Logs 2013-11-25 08:03:18  Seqno 6, RBA 3230720 SCN 0.1074371 (1074371)

Exiba o status e a latência (quando pertinente) para todos os processos de Manager, Extract e Replicat em um sistema:

    GGSCI (MachineGG1) 16> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP1      00:00:00      00:46:33
    EXTRACT     RUNNING     EXT1        00:00:00      00:00:04

### <a name="start-extract-and-data-pump-processes-on-site-b"></a>Inicie os processos Extrair e Bomba de Dados no Site B
Inicie o processo Extrair ext2 no Site B:

    GGSCI (MachineGG2) 22> start extract ext2
    Sending START request to MANAGER …
    EXTRACT EXT2 starting

Inicie o processo de bomba de dados dpump2 no Site B:

    GGSCI (MachineGG2) 23> start extract dpump2
    Sending START request to MANAGER …
    EXTRACT DPUMP2 starting

Exiba o status e a latência (quando pertinente) para todos os processos de Manager, Extract e Replicat em um sistema:

    GGSCI (ActiveGG2orcldb) 6> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP2      00:00:00      136:13:33
    EXTRACT     RUNNING     EXT2        00:00:00      00:00:04

### <a name="start-replicat-process-on-site-a"></a>Iniciar o processo de REPLICAT no Site A
Esta seção descreve como iniciar os processos REPLICAT "REP1" no Site A.

Inicie o processo Replicat no Site A:

    GGSCI (MachineGG1) 38> start replicat rep1
    Sending START request to MANAGER …
    REPLICAT REP1 starting

Exiba o status do grupo Replicat:

    GGSCI (MachineGG1) 39> status replicat rep1
     REPLICAT REP1: RUNNING

### <a name="start-replicat-process-on-site-b"></a>Iniciar o processo de REPLICAT no Site B
Esta seção descreve como iniciar os processos REPLICAT "REP2" no Site B.

Inicie o processo Replicat no Site B:

    GGSCI (MachineGG2) 26> start replicat rep2
    Sending START request to MANAGER …
    REPLICAT REP2 starting

Exiba o status do grupo Replicat:

    GGSCI (MachineGG2) 27> status replicat rep2
    REPLICAT REP2: RUNNING

## <a name="6-verify-the-bi-directional-replication-process"></a>6. Verifique o processo de replicação bidirecional
Para verificar a configuração do Oracle GoldenGate, insira uma linha no banco de dados no Site A. Crie a área de trabalho remota para o Site A. Abra a janela Comando do SQL*Plus e execute: SQL> selecione o nome do $vdatabase;

    NAME
    ———
    TESTGG

    SQL> insert into inventory values  (100,’TV’,100,sysdate);

    1 row created.

    SQL> commit;

    Commit complete.

Em seguida, verifique se essa linha será replicada no Site B. Para fazer isso, crie a área de trabalho remota para o Site B. Abra a janela SQL Plus e execute:

    SQL> select name from v$database;

    NAME
    ———
    TESTGG

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13

Inserir um novo registro no Site B:

    SQL> insert into inventory  values  (101,’DVD’,10,sysdate);
    1 row created.

    SQL> commit;

    Commit complete.

Crie a área de trabalho remota para Site A e verifique se a replicação ocorreu:

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13
    101 DVD 10 22-MAR-13

## <a name="additional-resources"></a>Recursos adicionais
[Imagens de Máquina Virtual Oracle para o Azure](virtual-machines-linux-classic-oracle-images.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)




<!--HONumber=Nov16_HO3-->


