---
title: "Criar um banco de dados Oracle Database 12c em uma máquina virtual do Azure | Microsoft Docs"
description: Coloque rapidamente em funcionamento um banco de dados Oracle Database 12c no ambiente do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tonyguid
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/26/2017
ms.author: rclaus
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 38cb5c4c1beb0e50c8a6395afb118c63b2750210
ms.contentlocale: pt-br
ms.lasthandoff: 05/05/2017

---

# <a name="create-an-oracle-database-12c-database-in-an-azure-virtual-machine"></a>Criar um banco de dados Oracle Database 12c em uma máquina virtual do Azure

Você pode usar a CLI do Azure para criar e gerenciar recursos do Azure em um prompt de comando ou em scripts. Neste artigo, usamos scripts na CLI do Azure para implantar um banco de dados Oracle Database 12c de uma imagem da galeria do Azure Marketplace.

Antes de começar, verifique se a CLI do Azure está instalada. Para saber mais, confira o [Guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="sign-in-to-azure"></a>Entrar no Azure 

Na CLI do Azure, para entrar na assinatura do Azure, use o comando [az login](/cli/azure/#login). Em seguida, execute as instruções na tela.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um grupo de recursos, use o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-vm"></a>Criar uma máquina virtual

Para criar uma VM (máquina virtual), use o comando [az vm create](/cli/azure/vm#create). 

O exemplo a seguir cria uma VM chamada `myVM`. Ele também criará chaves SSH, se elas ainda não existirem em um local de chave padrão. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.  

```azurecli
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys
```

Depois de criar a VM, a CLI do Azure exibe informações semelhantes ao exemplo a seguir. Observe o valor de `publicIpAddress`. Você pode usar esse endereço para acessar a VM.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>Conectar-se à VM

Para criar uma sessão SSH com a VM, use o comando a seguir. Substitua o endereço IP pelo valor `publicIpAddress` para a sua VM.

```bash 
ssh <publicIpAddress>
```

## <a name="create-the-database"></a>Criar o banco de dados

O software Oracle já está instalado na imagem do Marketplace. Em seguida, instale o banco de dados. 

1.  Execute o superusuário *oracle* e inicialize o ouvinte para registro em log:

    ```bash
    sudo su - oracle
    [oracle@myVM /]$ lsnrctl start
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  Crie o banco de dados:

    ```bash
    [oracle@myVM /]$ dbca -silent \
    -createDatabase \
    -templateName General_Purpose.dbc \
    -gdbname cdb1 \
    -sid cdb1 \
    -responseFile NO_VALUE \
    -characterSet AL32UTF8 \
    -sysPassword OraPasswd1 \
    -systemPassword OraPasswd1 \
    -createAsContainerDatabase true \
    -numberOfPDBs 1 \
    -pdbName pdb1 \
    -pdbAdminPassword OraPasswd1 \
    -databaseType MULTIPURPOSE \
    -automaticMemoryManagement false \
    -storageType FS \
    -ignorePreReqs

    Copying database files
    1% complete
    2% complete
    8% complete
    13% complete
    19% complete
    27% complete
    Creating and starting Oracle instance
    29% complete
    32% complete
    33% complete
    34% complete
    38% complete
    42% complete
    43% complete
    45% complete
    Completing Database Creation
    48% complete
    51% complete
    53% complete
    62% complete
    70% complete
    72% complete
    Creating Pluggable Databases
    78% complete
    100% complete
    Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for further details.
    ```

## <a name="prepare-for-connectivity"></a>Preparar para conectividade 
Para verificar se o banco de dados foi inicializado corretamente, teste a conectividade local. A maneira mais fácil de fazer isso é se conectando a `sqlplus`.  

Antes de se conectar, você precisa definir duas variáveis de ambiente: ORACLE_HOME e ORACLE_SID.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME

ORACLE_SID=cdb1; export ORACLE_SID
```

Você também pode adicionar ORACLE_HOME e ORACLE_SID ao arquivo .bashrc. Isso poupa as variáveis de ambiente para futuras entradas.

```
# Add ORACLE_HOME.
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1

# Add ORACLE_SID.
export ORACLE_SID=cdb1

```

## <a name="oracle-em-express-connectivity"></a>Conectividade com o Oracle EM Express

Para ter uma ferramenta de gerenciamento de GUI que você pode usar para explorar o banco de dados, configure o Oracle EM Express. Para se conectar ao Oracle EM Express, a porta deve ser configurada primeiramente no Oracle:

```bash
$ sudo su - oracle

sqlplus / as sysdba

SQL*Plus: Release 12.1.0.2.0 Production on Fri Apr 7 13:16:30 2017

Copyright (c) 1982, 2014, Oracle.  All rights reserved.


Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL> select con_id, name, open_mode from v$pdbs;

    CON_ID NAME                           OPEN_MODE
---------- ------------------------------ ----------
         2 PDB$SEED                       READ ONLY
         3 PDB1                           MOUNT

SQL> alter session set container=pdb1;

Session altered.

SQL> alter database open;

database opened.

SQL> alter session set container=pdb1;

Session altered.

SQL> exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);

PL/SQL procedure successfully completed.
```

## <a name="automate-database-startup-and-shutdown"></a>Automatizar a inicialização e o desligamento do banco de dados

O banco de dados Oracle, por padrão, não é iniciado automaticamente quando você inicia a VM. Para configurar o banco de dados Oracle para iniciar quando você iniciar a VM, primeiro entre como raiz. Em seguida, crie e atualize alguns arquivos do sistema.

1.  Entre como raiz:

    ```bash
    # sudo su -
    ```

2.  Altere o arquivo /etc/oratab do padrão `N` para `Y`:

    ```
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Crie o arquivo /etc/init.d/dbora:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  Altere as permissões:

    ```bash
    # chgrp dba /etc/init.d/dbora
    # chmod 750 /etc/init.d/dbora
    ```

5.  Crie links simbólicos para inicialização e desligamento:

    ```bash
    # ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    # ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    # ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Para testar as alterações, reinicie a VM:

    ```bash
    # reboot
    ```

## <a name="open-ports-for-connectivity"></a>Abrir as portas para conectividade

A última tarefa é configurar alguns pontos de extremidade externos. Para configurar o Grupo de Segurança de Rede do Azure que protege a VM, primeiro saia da sessão SSH na VM. 

1.  Para abrir o ponto de extremidade que você usa para acessar remotamente o banco de dados Oracle, execute o seguinte comando: 

    ```azurecli
    az network nsg rule create --resource-group myResourceGroup\
        --nsg-name myVmNSG --name allow-oracle\
        --protocol tcp --direction inbound --priority 999 \
        --source-address-prefix '*' --source-port-range '*' \
        --destination-address-prefix '*' --destination-port-range 1521 --access allow
    ```

    O resultado deve ser semelhante a este exemplo:

    ```
    {
    "access": "Allow",
    "description": null,
    "destinationAddressPrefix": "*",
    "destinationPortRange": "1521",
    "direction": "Inbound",
    "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
    "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
    "name": "allow-oracle",
    "priority": 999,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "sourceAddressPrefix": "*",
    "sourcePortRange": "*"
    }
    ```

2.  Para abrir o ponto de extremidade que você usa para acessar a Oracle EM Express remotamente, execute o seguinte comando:

    ```azurecli
    az network nsg rule create --resource-group myResourceGroup\
        --nsg-name myVmNSG --name allow-oracle-EM\
        --protocol tcp --direction inbound --priority 1001 \
        --source-address-prefix '*' --source-port-range '*' \
        --destination-address-prefix '*' --destination-port-range 5502 --access allow
    ```

    O resultado deve ser semelhante a este exemplo:

    ```azurecli
    {
    "access": "Allow",
    "description": null,
    "destinationAddressPrefix": "*",
    "destinationPortRange": "5502",
    "direction": "Inbound",
    "etag": "W/\"06c68b5e-1b3f-4ae0-bcf6-59b3b981d685\"",
    "id": "/subscriptions/2dad32d6-b188-49e6-9437-ca1d51cec4dd/resourceGroups/kennyRG/providers/Microsoft.Network/networkSecurityGroups/kennyVM1NSG/securityRules/allow-oracle-EM",
    "name": "allow-oracle-EM",
    "priority": 1001,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "sourceAddressPrefix": "*",
    "sourcePortRange": "*"
    }
    ```

3.  Conecte-se ao EM Express do navegador: 

    ```
    https://<VM hostname>:5502/em
    ```

Você pode fazer logon usando a conta SYS, com a senha que você definiu durante a instalação.


## <a name="delete-the-vm"></a>Excluir a VM

Quando a VM não for mais necessária, o comando abaixo poderá ser usado para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

[Tutorial: criar VMs altamente disponíveis](../../linux/create-cli-complete.md)

[Explorar exemplos da CLI do Azure de implantação de VM](../../linux/cli-samples.md)

