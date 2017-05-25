---
title: "Configurar o Oracle ASM em uma máquina virtual Linux do Azure | Microsoft Docs"
description: Coloque o Oracle ASM em funcionamento rapidamente no ambiente do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/2/2017
ms.author: v-shiuma
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: e3c98a3d4d7012f6cb6c7e1a1b8263e6ecdee57b
ms.contentlocale: pt-br
ms.lasthandoff: 05/17/2017

---

# <a name="set-up-oracle-asm-on-an-azue-linux-virtual-machine"></a>Configurar o Oracle ASM em uma máquina virtual Linux do Azure 

Neste artigo, saiba como instalar e configurar o Oracle ASM (Automatic Storage Management) em uma VM (máquina virtual) Linux do Oracle no Azure.

Antes de começar, verifique se a CLI do Azure está instalada. Para saber mais, confira o [Guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="sign-in-to-azure"></a>Entrar no Azure 

Na CLI do Azure, para entrar na assinatura do Azure, use o comando [az login](/cli/azure/#login). Em seguida, execute as instruções na tela.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um grupo de recursos, use o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos chamado myResourceGroup no local westus.

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-vm"></a>Criar uma máquina virtual

Para criar uma VM para usar com o Oracle ASM, conclua as seguintes etapas:

1.  Para criar uma máquina virtual, use o comando [az vm create](/cli/azure/vm#create). 

  O exemplo a seguir cria uma VM chamada myVM. Ele também criará chaves SSH, se elas ainda não existirem em um local de chave padrão. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.  

  ```azurecli
  az vm create --resource-group myResourceGroup --name myVM --image Oracle:Oracle-Database-Ee:12.1.0.2:latest --size Standard_DS2_v2 --generate-ssh-keys
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

2.  Adicione discos para uso na configuração do Oracle ASM:

  ```azurecli
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk2 --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk3 --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk4 --new --size-gb 50
  ```

## <a name="connect-to-the-vm"></a>Conectar-se à VM

Para criar uma sessão SSH com a máquina virtual, use o comando a seguir. Substitua o endereço IP pelo valor `publicIpAddress` para a sua VM.

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Instalar o Oracle ASM

Para instalar o Oracle ASM, conclua as etapas a seguir. 

Para saber mais sobre como instalar o ASM do Oracle, confira [Downloads do Oracle ASMLib para Oracle Linux 6](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Execute `yum list`:

  ```bash
  $ sudo su -
  # yum list
  ```
  Pode levar vários minutos para o `yum list` carregar na primeira vez em que for executado.

2.  Execute estes comandos adicionais:

  ```bash
  # yum list | grep oracleasm
  # yum -y install kmod-oracleasm.x86_64
  # yum -y install oracleasm-support.x86_64
  # wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm
  # yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm
  # rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
  ```

3.  Verifique se o Oracle ASM está instalado:

  ```bash
  # rpm -qa |grep oracleasm
  oracleasm-support-2.1.10-4.el6.x86_64
  kmod-oracleasm-2.0.8-15.el6_9.x86_64
  oracleasmlib-2.0.12-1.el6.x86_64
  ```

4.  Adicione usuários e grupos:

  ```bash
  # groupadd -g 54345 asmadmin
  # groupadd -g 54346 asmdba
  # groupadd -g 54347 asmoper
  # useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid
  # usermod -g oinstall -G dba,asmdba,asmadmin oracle
  ```

5.  Verifique usuários e grupos:

  ```bash
  # id grid
  uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
  ```

6.  Crie uma pasta e altere o proprietário:

  ```bash
  # mkdir /u01/app/grid
  # chown grid:oinstall /u01/app/grid
  ```

## <a name="set-up-oracle-asm"></a>Configurar o Oracle ASM

Para este tutorial, o usuário padrão é *grade* e o grupo padrão é *asmadmin*. Verifique se o usuário *oracle* faz parte do grupo asmadmin. Para configurar a instalação do Oracle ASM, conclua as seguintes etapas:

1.  Defina o driver da biblioteca Oracle ASM:

  ```bash
  # /usr/sbin/oracleasm configure -i

  Configuring the Oracle ASM library driver.

  This will configure the on-boot properties of the Oracle ASM library
  driver. The following questions will determine whether the driver is
  loaded on boot and what permissions it will have. The current values
  will be shown in brackets ('[]'). Hitting <ENTER> without typing an
  answer will keep that current value. Ctrl-C will abort.

  Default user to own the driver interface []: grid
  Default group to own the driver interface []: asmadmin
  Start Oracle ASM library driver on boot (y/n) [n]: y
  Scan for Oracle ASM disks on boot (y/n) [y]: y
  Writing Oracle ASM library driver configuration: done
  ```

2.  Veja a configuração de disco:
  ```bash
  # cat /proc/partitions
  ```

3.  Formate o disco:

  ```bash
  # fdisk /dev/sdc
  Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
  Building a new DOS disklabel with disk identifier 0xf865c6ca.
  Changes will remain in memory only, until you decide to write them.
  After that, of course, the previous content won't be recoverable.

  Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

  The device presents a logical sector size that is smaller than
  the physical sector size. Aligning to a physical sector (or optimal
  I/O) size boundary is recommended, or performance may be impacted.

  WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
          switch off the mode (command 'c') and change display units to
          sectors (command 'u').

  Command (m for help): n
  Command action
    e   extended
    p   primary partition (1-4)
  p
  Partition number (1-4): 1
  First cylinder (1-6527, default 1):
  Using default value 1
  Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
  Using default value 6527

  Command (m for help): w
  The partition table has been altered!

  Calling ioctl() to re-read partition table.
  Syncing disks.
  ```

4.  Repita a etapa anterior para /dev/sdd, /dev/sde e /dev/sdf.

5.  Verifique a configuração de disco:

  ```bash
  # cat /proc/partitions
  major minor  #blocks  name

    8       16   14680064 sdb
    8       17   14678976 sdb1
    8       32   52428800 sdc
    8       33   52428096 sdc1
    8       48   52428800 sdd
    8       49   52428096 sdd1
    8       64   52428800 sde
    8       65   52428096 sde1
    8       80   52428800 sdf
    8       81   52428096 sdf1
    8        0   52428800 sda
    8        1     512000 sda1
    8        2   51915776 sda2
    11        0    1048575 sr0
  ```

6.  Verifique o status do serviço Oracle ASM:

  ```bash
  # service oracleasm status
  Checking if ASM is loaded: no
  Checking if /dev/oracleasm is mounted: no
  ```

7.  Inicie o serviço Oracle ASM:

  ```bash
  # service oracleasm start
  Initializing the Oracle ASMLib driver:                     [  OK  ]
  Scanning the system for Oracle ASMLib disks:               [  OK  ]
  ```

8.  Crie discos do Oracle ASM:

  ```bash
  # service oracleasm createdisk ASMSP /dev/sdc1
  Marking disk "ASMSP" as an ASM disk:                       [  OK  ]

  # service oracleasm createdisk DATA /dev/sdd1
  Marking disk "DATA" as an ASM disk:                        [  OK  ]

  # service oracleasm createdisk DATA1 /dev/sde1
  Marking disk "DATA1" as an ASM disk:                       [  OK  ]

  [root@myVM ~]# service oracleasm createdisk FRA /dev/sdf1
  Marking disk "FRA" as an ASM disk:                         [  OK  ]
  ```

9.  Liste os discos do Oracle ASM:

  ```bash
  # service oracleasm listdisks
  ASMSP
  DATA
  DATA1
  FRA
  ```

10. Altere as senhas dos usuários raiz, oracle e grade (você usará as senhas posteriormente, durante a instalação):

  ```bash
  # passwd oracle
  # passwd grid
  # passwd root
  ```

11. Altere a permissão de pasta:

  ```bash
  # chmod -R 775 /opt
  # chown grid:oinstall /opt
  # chown oracle:oinstall /dev/sdc1
  # chown oracle:oinstall /dev/sdd1
  # chown oracle:oinstall /dev/sde1
  # chown oracle:oinstall /dev/sdf1
  # chmod 600 /dev/sdc1
  # chmod 600 /dev/sdd1
  # chmod 600 /dev/sde1
  # chmod 600 /dev/sdf1
  ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Baixe e prepare a infraestrutura em grade do Oracle

Para baixar e preparar o software de infraestrutura em grade do Oracle, conclua as seguintes etapas:

1.  Baixe a infraestrutura em grade do Oracle da [página de download do Oracle ASM](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

  No título do download **Banco de Dados Oracle 12c versão 1 Infraestrutura em Grade (12.1.0.2.0) para Linux x86-64**, deve haver dois arquivos .zip para download.

2.  Depois de baixar os arquivos .zip para o computador cliente, você poderá usar o protocolo SCP para copiar os arquivos na VM.

    ```bash
    scp *.zip <publicIpAddress>:<folder>
    ```

3.  Mova os arquivos .zip para a pasta /opt. Em seguida, altere o proprietário dos arquivos:

    ```bash
    # mv <folder>/*.zip /opt
    # cd /opt
    # chown grid:oinstall linuxamd64_12102_grid_1of2.zip
    # chown grid:oinstall linuxamd64_12102_grid_2of2.zip
    ```

4.  Descompacte os arquivos (instale o utilitário unzip do Linux se ele ainda não estiver instalado):

    ```bash
    # yum install unzip
    # unzip linuxamd64_12102_grid_1of2.zip
    # unzip linuxamd64_12102_grid_2of2.zip
    ```

5.  Alterar permissão:

    ```bash
    # chown -R grid:oinstall /opt/grid
    ```

6.  Desligue o firewall:

    ```bash
    # service iptables status
    # service iptables stop
    ```

7.  Verifique o espaço de permuta disponível. Você precisa de pelo menos 6 GB de espaço de permuta para instalar a infraestrutura em grade do Oracle:

    ```bash
    # swapon -s
    ```

    Se você tiver menos de 6 GB de espaço de permuta, poderá adicionar mais espaço executando os seguintes comandos:

    ```bash
    # dd if=/dev/zero of=/extraswap bs=1M count=6144
    6144+0 records in
    6144+0 records out
    6442450944 bytes (6.4 GB) copied, 141.245 s, 45.6 MB/s

    # mkswap /mnt/resource/extraswap
    mkswap: /mnt/resource/extraswap: warning: don't erase bootbits sectors
            on whole disk. Use -f to force.
    Setting up swapspace version 1, size = 6291452 KiB
    no label, UUID=80bd7816-b3a2-4eec-a824-733209644fc5
    # swapon /mnt/resource/extraswap

    ```

## <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>Preparar o cliente e a VM para executar X11 (somente para clientes Windows)

Para preparar o cliente e a VM para executar X11, conclua as seguintes etapas: 

1.  Enter como raiz e edite o arquivo /etc/ssh/ssh_config. Altere a configuração de Forwardx11 para `yes`:

  ```
  #   ForwardX11 no
  ForwardX11 yes

  ```

2.  Baixe o PuTTY e o Xming em seu computador Windows:

  * [Baixar PuTTY](http://www.putty.org/)
  * [Baixar Xming](https://xming.en.softonic.com/)

3.  Depois de instalar o PuTTY, na pasta PuTTY (por exemplo, C:\Arquivos de Programa\PuTTY), execute puttygen.exe (Gerador de Chave PuTTY).

4.  No Gerador de Chave PuTTY:

  1.  Para gerar uma chave, selecione o botão **Gerar**. 
  2.  Copie o conteúdo da chave (Ctrl+C).
  3.  Selecione o botão **Salvar chave privada**. 
  4.  Ignore o aviso que aparece e selecione **OK**.

  ![Captura de tela da página do gerador de chave PuTTY](./media/asm-configuration/puttykeygen.png)

5.  Em sua VM, execute estes comandos:

  ```bash
  # sudo su - grid
  $ mkdir .ssh (if not already created)
  $ cd .ssh
  ```

6.  Crie um arquivo chamado authorized_keys. Cole o conteúdo da chave nesse arquivo e salve-o.

  > [!NOTE]
  > A chave deve conter a cadeia de caracteres `ssh-rsa`. Além disso, o conteúdo da chave deve ser uma única linha de texto.
  >  

7.  Inicie o PuTTY. No painel **Categoria**, vá para **Conexão** > **SSH** > **Autenticação**. Na caixa **Arquivo de chave privada para autenticação**, navegue até a chave que você gerou anteriormente.

  ![Captura de tela da página Instalar chave privada](./media/asm-configuration/setprivatekey.png)

9.  No painel **Categoria**, vá para **Conexão** > **SSH** > **X11**. Selecione a caixa **Habilitar encaminhamento X11**.

  ![Captura de tela da página Habilitar X11](./media/asm-configuration/enablex11.png)

10. No painel **Categoria**, vá para **Sessão**. Insira as informações do host e selecione **Abrir**.

  ![Captura de tela da página Sessão](./media/asm-configuration/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Instalar a infraestrutura em grade do Oracle

Para instalar a infraestrutura em grade do Oracle, conclua as seguintes etapas:

1. Entre como grade. (Você deve ser capaz de entrar sem inserir uma senha.) 

  > [!NOTE]
  > Verifique se Xming está em execução antes de iniciar a instalação.

    ```bash
    $ cd /opt/grid
    $ ./runInstaller
    ```

  O instalador da Infraestrutura em grade do Oracle 12c versão 1 é aberto. (Pode levar alguns minutos para iniciar o instalador.)

2. Na página **Selecionar Opção de Instalação** , selecione **Instalar e Configurar a Infraestrutura em grade do Oracle para um Servidor Autônomo**.

  ![Captura de tela da página Selecionar opção de instalação do instalador](./media/asm-configuration/install01.png)

3. Na página **Selecionar Idiomas do Produto** , selecione **Inglês** ou o idioma desejado.

  ![Captura de tela da página Selecionar idiomas do produto do instalador](./media/asm-configuration/install02.png)

4. Na página **Criar grupo de discos ASM**:
  1.  Insira um nome para o grupo de discos.
  2.  Em **Redundância**, selecione **Externa**.
  3.  Em **Tamanho da Unidade de Alocação**, selecione **4**.
  4.  Em **Adicionar Discos**, selecione **ORCLASMSP**.

  ![Captura de tela da página Criar grupo de discos ASM do instalador](./media/asm-configuration/install03.png)

5. Na página **Especificar Senha ASM**, selecione a opção **Usar as mesmas senhas para essas contas** e digite uma senha.

  ![Captura de tela da página Especificar senha de ASM do instalador](./media/asm-configuration/install04.png)

6. (Opcional) Na página **Especificar Opções de Gerenciamento**, marque a caixa **Registrar com o controle de nuvem do EM (Enterprise Manager)**.

  ![Captura de tela da página Especificar opções de gerenciamento do instalador](./media/asm-configuration/install05.png)

7. Na página **Grupos Privilegiados do Sistema Operacional**, use as configurações padrão.

  ![Captura de tela da página Grupos privilegiados do sistema operacional do instalador](./media/asm-configuration/install06.png)

8. Na página **Especificar Local de Instalação**, use as configurações padrão.

  ![Captura de tela da página Especificar local de instalação do instalador](./media/asm-configuration/install07.png)

9. Na página **Criar Inventário**, insira ou navegue até o local da pasta.

  ![Captura de tela da página Criar inventário do instalador](./media/asm-configuration/install08.png)

10. Na página **Configuração de execução de script de raiz**, marque a caixa **Executar scripts de configuração automaticamente**. Em seguida, selecione a opção **Usar credenciais de usuário "raiz"** e digite a senha do usuário raiz.

  ![Captura de tela da página de configuração de execução de script de raiz do instalador](./media/asm-configuration/install09.png)

11. Na página **Executar Verificações de Pré-requisito**, selecione **Corrigir e Verificar Novamente**.

  ![Captura de tela da página Executar verificações de pré-requisito do instalador](./media/asm-configuration/install10.png)

12. Na página **Script de Correção**, selecione **OK**.

  ![Captura de tela da página Script de correção do instalador](./media/asm-configuration/install11.png)

13. Na página **Resumo**, examine suas escolhas de configurações e selecione **Instalar**.

  ![Captura de tela da página Resumo do instalador](./media/asm-configuration/install12.png)

14. Uma caixa de diálogo de aviso é exibida. Escolha **Sim** para continuar.

  ![Captura de tela da caixa de diálogo de aviso](./media/asm-configuration/install14.png)

15. Na página **Concluir**, selecione **Fechar** para concluir a instalação.

  ![Captura de tela da página Concluir do instalador](./media/asm-configuration/install16.png)

## <a name="set-up-your-oracle-asm-installation"></a>Configurar a sua instalação do Oracle ASM

Para configurar a instalação do Oracle ASM, conclua as seguintes etapas:

1.  Entre como grade na sua sessão X11:

  ```bash
  $ cd /u01/app/grid/product/12.1.0/grid/bin
  $ ./asmca
  ```

  O assistente de configuração do Oracle ASM é aberto.

2.  Na página **Configurar ASM: Grupos de Discos**, selecione o botão **Criar** botão e selecione **Exibir Opções Avançadas**.

  ![Captura de tela da página Configurar ASM: Grupos de Discos](./media/asm-configuration/asm01.png)

3.  Na página **Criar Grupo de Discos**:

  1.  Insira o nome do grupo.
  2.  Em **Selecionar discos membros**, selecione **ORCL_DATA** e **ORCL_DATA1**.
  3.  Em **Tamanho da Unidade de Alocação**, selecione **4**. 

  ![Captura de tela da página Criar grupo de discos](./media/asm-configuration/asm02.png)

4.  Na página **Configurar ASM: Grupos de Discos**, selecione o botão **Criar** botão e selecione **Exibir Opções Avançadas**.

  ![Captura de tela da página Configurar ASM: Grupos de Discos](./media/asm-configuration/asm03.png)

5.  Na página **Criar Grupo de Discos**:

  1.  Insira o nome do grupo.
  2.  Em **Redundância**, selecione **Externa**.
  3.  Em **Selecionar discos membros**, selecione **ORCL_FRA**.
  4.  Em **Tamanho da Unidade de Alocação**, selecione **4**.

  ![Captura de tela da página Criar grupo de discos](./media/asm-configuration/asm04.png)

6.  Selecione **Sair** para fechar o assistente de configuração do ASM.

  ![Captura de tela da página Configurar ASM: grupos de discos com o botão Sair](./media/asm-configuration/asm05.png)

## <a name="create-the-database"></a>Criar o banco de dados

O software Oracle já está instalado na imagem do Azure Marketplace. Para instalar o banco de dados, conclua as seguintes etapas:

1.  Alterne usuários para o superusuário oracle e inicialize o ouvinte para registro:

  ```bash
  $ su - oracle
  Password:
  $ cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
  $ ./dbca
   ```
   O assistente de configuração do banco de dados é aberto.

2.  Na página **Operação do Banco de Dados**, selecione **Criar Banco de Dados**.

  ![Captura de tela da página Operação do banco de dados](./media/asm-configuration/createdb01.png)
  
3. Na página **Modo de Criação**:

    1.  Insira um nome para o banco de dados. 
    2.  Em **Tipo de Armazenamento**, selecione **ASM (Gerenciamento de Armazenamento Automático)**.
    3.  Em **Local dos Arquivos de Banco de Dados**, navegue até a pasta que você deseja usar.
    4.  Em **Área de Recuperação Rápida**, navegue até a pasta que você deseja usar.

  ![Captura de tela da página Modo de criação](./media/asm-configuration/createdb02.png)

4.  Na página **Resumo**, examine suas escolhas de configurações e selecione **Concluir** para criar o banco de dados.

  ![Captura de tela da página Resumo](./media/asm-configuration/createdb03.png)

5.   (Opcional) Na página **Concluir**, para alterar as senhas, selecione **Gerenciamento de Senhas**.

  ![Captura de tela da página Concluir](./media/asm-configuration/createdb04.png)


## <a name="delete-the-vm"></a>Excluir a VM

Quando a VM não for mais necessária, o comando abaixo poderá ser usado para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

[Tutorial: criar máquinas virtuais altamente disponíveis](../../linux/create-cli-complete.md)

[Explorar exemplos da CLI do Azure de implantação de VM](../../linux/cli-samples.md)

