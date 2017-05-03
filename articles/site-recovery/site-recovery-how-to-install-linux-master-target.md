---
title: Como instalar o servidor de destino mestre do Linux para o failover do Azure no local | Microsoft Docs
description: "Antes de proteger novamente uma máquina virtual do Linux, você precisa de servidor de destino mestre do Linux. Saiba como instalar um."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 02/13/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 22a86001fe93dcb11e180dbdd75045b49b85b58f
ms.lasthandoff: 04/25/2017


---
# <a name="how-to-install-a-linux-master-target-server"></a>Como instalar o servidor de destino mestre do Linux
Após o failover de suas máquinas virtuais, você poderá executar failback das máquinas virtuais para o site local. Para realizar failback, você precisa proteger novamente a máquina virtual do Azure para o site local. Para este processo, é necessário um servidor de destino mestre para receber o tráfego. Se sua máquina virtual protegida for uma máquina virtual do Windows, será necessário um destino mestre do Windows. Para uma máquina virtual Linux, é necessário um destino mestre do Linux. Leia as etapas a seguir para saber como criar e instalar um destino mestre do Linux.

## <a name="overview"></a>Visão geral
Este artigo fornece informações e instruções para instalar um destino mestre do Linux.

Publique comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Pré-requisitos

* Para escolher corretamente o host no qual você precisa implantar o destino mestre, determine se o failback vai ser para uma máquina virtual local existente ou para uma nova máquina virtual porque a máquina virtual local foi excluída.
    * Para uma máquina virtual existente, o host do destino mestre deve ter acesso a armazenamentos de dados da máquina virtual.
    * Se a máquina virtual no local não existir, a máquina virtual de failback será criada no mesmo host como o destino mestre. Você pode escolher qualquer host ESXi para instalar o destino mestre.
* O destino mestre deve estar em uma rede que pode se comunicar com o servidor de processo e com o servidor de configuração.
* A versão de destino mestre deve ser igual ou anterior às versões de servidor de processo e o servidor de configuração. Por exemplo, se a versão do servidor de configuração for a 9.4, a versão de destino mestre poderá ser 9.4 ou 9.3 mas não 9.5.
* O destino mestre só pode ser uma máquina virtual VMware e não um servidor físico.

## <a name="master-target-sizing-guideline"></a>Orientação de dimensionamento de destino mestre

O destino mestre precisa ser criado com as diretrizes de dimensionamento a seguir
    * RAM – 6 GB ou mais
    * Tamanho do disco do sistema operacional – 100 GB ou mais (para instalar o CentOS6.6)
    * Tamanho de disco adicional para a unidade de retenção – 1 TB
    * Núcleos de CPU – 4 núcleos ou mais


## <a name="steps-to-deploy-the-master-target-server"></a>Etapas para implantar o servidor de destino mestre

### <a name="install-centos-66-minimal"></a>Instalar o CentOS 6.6 mínimo

Use as etapas a seguir para instalar o sistema de operacional CentOS 6.6 de 64 bits:

1. Dos links a seguir, selecione o espelho mais próximo para baixar um ISO de 64 bits do CentOS 6.6, no mínimo.

    <http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    Coloque o ISO do CentOS 6.6 Minimal de 64 bits em uma unidade de DVD e inicialize o sistema.

    ![Caixa de diálogo Bem-vindo ao CentoOS 6.6](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

2. Clique em **Ignorar** para ignorar o processo de teste de mídia.

    ![Selecione Ignorar para ignorar o processo de teste de mídia](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

3. Na tela de boas-vindas de instalação, clique no botão **Avançar**.

    ![O botão Avançar na tela de boas-vindas de instalação](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

4. Selecione **Inglês** como seu idioma de preferência e clique em **Avançar**.

    ![Selecionar um idioma](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

5. Selecione **Inglês (Estados Unidos)** como um layout de teclado e clique em **Avançar**.

    ![Selecionar o layout de teclado em inglês](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

6. Selecione **Dispositivos de armazenamento básico** e clique em **Avançar**.

    ![Selecionar um dispositivo de armazenamento](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

7. A mensagem de aviso exibida indica que os dados existentes no disco rígido serão excluídos. Certifique-se de que o disco rígido não contém dados importantes e então clique em **Sim, descartar qualquer dado**.

    ![Aviso sobre a exclusão de dados se você continuar](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

8. Insira o nome do host para o servidor na caixa **Nome do host** e clique em **Configurar Rede**. Na caixa de diálogo **Conexão de Rede**, selecione a interface de rede e clique no botão **Editar** para configurar IPV4Settings.

    ![Selecionar um nome de host e configurar o IPV4](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

9. Na caixa de diálogo **Editando Sistema eth0**, marque a caixa de seleção **Conectar automaticamente**. Na guia **Configurações de IPv4**, selecione **Manual** como o **Método** e clique em **Adicionar**. Forneça os detalhes de **IP Estático**, de **Máscara de Rede**, de **Gateway** e de **Servidor DNS**. Clique em **Aplicar** para salvar os detalhes.

    ![Configurações para a configuração de rede](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

10. Selecione seu fuso horário e clique em **Avançar**.

    ![Selecionar um fuso horário](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

11. Insira a **Senha Raiz**, confirme a senha e clique em **Avançar**.

    ![Adicionar uma senha](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

12. Selecione **Criar Layout Personalizado** e clique em **Avançar**.

    ![Selecionar um tipo de instalação](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

13. Selecione partição **Livre** e clique em **Criar** para criar as partições **/**, **/var/crash** e **/home** com **ext4** como o tipo de sistema de arquivos. Crie uma **Partição de troca** com **swap** como o tipo de sistema de arquivos. Para alocar o tamanho da partição, siga a fórmula de alocação de tamanho na tabela a seguir.

    > [!NOTE]
    > O servidor de destino mestre do Linux não deve usar o LVM (Gerenciador de Volume Lógico) para espaços de armazenamento raiz ou de retenção. O destino mestre Linux é configurado por padrão para evitar a detecção de partições/discos LVM.

    ![Tabela de nomes de partição, tamanhos de partição e tipos de sistema de arquivos](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

14. Depois de criar a partição, clique em **Avançar**.

    ![Caixa de diálogo que mostra os valores selecionados para partições](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

15. Se dispositivos já existentes forem encontrados, uma mensagem de aviso aparecerá para formatação. Clique em **Formatar** para formatar o disco rígido de acordo com a tabela de partição mais recente.

    ![Clique no botão Formatar para formatar o disco](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)

16. Clique em **Gravar alterações no disco** para aplicar as alterações de partição ao disco.

    ![Clique em "Alterações de gravação em disco"](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

17. Marque a opção **Instalar carregador de inicialização** e clique em **Avançar** para instalar o carregador de inicialização na partição raiz.

    ![Instalar o carregador de inicialização na partição raiz](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)


18. O processo de Instalação é iniciado. Você pode monitorar o andamento.

    ![Caixa de diálogo que mostra o andamento da instalação](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

19. A tela a seguir é exibida após a conclusão bem-sucedida da instalação. Clique em **Reinicializar**.

    ![Tela de instalação bem-sucedida](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>Etapas pós-instalação
Em seguida, prepare o computador a ser configurado como um servidor de destino mestre.

Para obter a ID de cada disco SCSI em uma máquina virtual Linux, você deve habilitar o parâmetro **disk.EnableUUID = TRUE**.

Para habilitar esse parâmetro, use as seguintes etapas:

1. Desligue sua máquina virtual.

2. Clique a entrada da máquina virtual no painel esquerdo e selecione **Editar Configurações.**

3. Clique na guia **Opções** .

4. Selecione **Avançado &gt; Geral** no painel esquerdo e clique no botão **Parâmetros de Configuração** à direita.

    ![Guia Opções](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

    A opção **Parâmetros de Configuração** não fica disponível enquanto a máquina está em execução. Para ativar essa guia, desligue a máquina virtual.

5. Verifique se já existe uma linha com **disk.EnableUUID**.

    - Se o valor existir e estiver definido como **False**, altere o valor para **True** (os valores não diferenciam maiúsculas de minúsculas).

    - Se o valor existir e se estiver definido como true**True**, clique em **Cancelar**.

    - Se o valor não existir, clique em **Adicionar Linha.**

    - Adicione **disk.EnableUUID** à coluna **Nome** e defina seu valor como **TRUE**.

    ![Verificação se disk.EnableUUID já existe](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-additional-packages"></a>Baixar e instalar pacotes adicionais

> [!NOTE]
> Verifique se você tem conectividade com a Internet para baixar e instalar pacotes adicionais. Sem conectividade com a Internet, você precisará encontrar esses pacotes RPM e instalá-los manualmente.

```
yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

O comando anterior baixará os 15 pacotes a seguir do repositório do CentOS 6.6 e os instalará. Se você não tiver acesso à internet, você precisará baixar os seguintes pacotes RPM:


bc-1.06.95-1.el6.x86\_64.rpm

busybox-1.15.1-20.el6.x86\_64.rpm

elfutils-libs-0.158-3.2.el6.x86\_64.rpm

kexec-tools-2.0.0-280.el6.x86\_64.rpm

lsscsi-0.23-2.el6.x86\_64.rpm

lzo-2.03-3.1.el6\_5.1.x86\_64.rpm

perl-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-Module-Pluggable-3.90-136.el6\_6.1.x86\_64.rpm

perl-Pod-Escapes-1.04-136.el6\_6.1.x86\_64.rpm

perl-Pod-Simple-3.13-136.el6\_6.1.x86\_64.rpm

perl-libs-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-version-0.77-136.el6\_6.1.x86\_64.rpm

rsync-3.0.6-12.el6.x86\_64.rpm

snappy-1.1.0-1.el6.x86\_64.rpm

wget-1.12-5.el6\_6.1.x86\_64.rpm


#### <a name="install-additional-packages-for-specific-operating-systems"></a>Instalar pacotes adicionais em sistemas operacionais específicos

> [!NOTE]
> Se as máquinas protegidas de origem usarem os sistemas de arquivos ReiserFS ou XFS para o dispositivo raiz ou de inicialização, baixe e instale os pacotes adicionais a seguir no destino mestre Linux antes da proteção.


***ReiserFS (se usado em Suse11SP3. O ReiserFS não é o sistema de arquivos padrão no Suse11SP3)***

```
cd /usr/local

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
```

***XFS (RHEL, CentOS 7 e posteriores)***

```
cd /usr/local

wget
<http://archive.kernel.org/centos-vault/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

yum install device-mapper-multipath
```
Isso é necessário para habilitar pacotes de vários caminhos no destino mestre.

### <a name="get-the-installer-for-setup"></a>Obter o instalador para instalação

Se o destino mestre tiver conectividade com a Internet, você poderá usar as etapas a seguir para baixar o instalador. Caso contrário, você pode copiar o instalador do servidor de processo e instalá-lo.

#### <a name="download-the-master-target-installation-packages"></a>Baixar os pacotes de instalação do destino mestre

[Baixe os bits de instalação de destino mestre Linux mais recentes](https://aka.ms/latestlinuxmobsvc).

Para baixá-los usando o Linux, digite:

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

Baixe e descompacte o instalador em seu diretório base. Se você o descompactar em /usr/Local, a instalação falhará.


#### <a name="access-the-installer-from-the-process-server"></a>Acessar o instalador do servidor de processos

1. Vá para C:\Arquivos de Programas (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository no servidor de processos.

2. Copie o arquivo do instalador obrigatório do servidor de processo e salvá-lo como latestlinuxmobsvc.tar.gz no seu diretório base.


### <a name="apply-custom-configuration-changes"></a>Aplicar alterações de configuração personalizadas

Para aplicar alterações de configuração personalizada, use as seguintes etapas:


1. Execute o comando a seguir para descompactar o binário.
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```
    ![Captura de tela do comando executado](./media/site-recovery-how-to-install-linux-master-target/image16.png)

2. Execute o comando a seguir para conceder permissão.
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. Execute o comando a seguir para executar o script.
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> Execute o script apenas uma vez no servidor. Desligue o servidor. Reinicialize o servidor depois de adicionar um disco, conforme descrito nas próximas etapas.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Adicionar um disco de retenção para a máquina virtual de destino mestre do Linux

Use as etapas a seguir para criar um disco de retenção:

1. Anexe um novo disco de **1 TB** à máquina virtual de destino mestre Linux e **inicialize** a máquina.

2. Use o comando **multipath -ll** para conhecer a ID de vários caminhos do disco de retenção.

    ```
    multipath -ll
    ```

    ![A ID de vários caminhos do disco de retenção](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. Formate a unidade e crie um sistema de arquivos na nova unidade.

    ```
    mkfs.ext4 /dev/mapper/<Retention disk's multipath id>
    ```
    ![Criação de um sistema de arquivos na unidade](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. Depois de criar o sistema de arquivos, monte o disco de retenção.
    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

    ![Montagem do disco de retenção](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. Crie a entrada **fstab** para montar a unidade de retenção durante cada inicialização.
    ```
    vi /etc/fstab
    ```
    Pressione **Inserir** para começar a editar o arquivo. Crie uma nova linha e insira o texto a seguir. Edite a ID de vários caminhos de disco com base na ID de vários caminhos realçada no comando anterior.

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    Pressione **Esc** e digite **:wq** (gravar e sair) para fechar a janela do editor.

### <a name="install-the-master-target"></a>Instalar o destino mestre

> [!IMPORTANT]
> A versão do servidor de destino mestre deve ser igual ou anterior às versões de servidor de processo e o servidor de configuração. Se essa condição não for atendida, o proteger novamente terá êxito, mas a replicação falhará.


> [!NOTE]
> Antes de instalar o servidor de destino mestre, verifique se o arquivo /etc/hosts na máquina virtual contém entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede.

1. Para fazer isso, copie a frase secreta de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase no servidor de configuração e salve-a em passphrase.txt no mesmo diretório local ao executar o comando a seguir.

    ```
    echo <passphrase> >passphrase.txt
    ```
    Exemplo: echo itUx70I47uxDuUVY >passphrase.txt

2. Anote o endereço IP do servidor de configuração. Você precisará disso na próxima etapa.

3. Execute o comando a seguir para instalar o servidor de destino mestre e registrar o servidor no servidor de configuração.

    ```
    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i <Configuration Server IP Address> -p 443 -s y -c https -P passphrase.txt
    ```

    Exemplo: ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

    Aguarde até que o script seja concluído. Se o destino mestre for registrado com êxito, ele será listado na página Infraestrutura do Site Recovery do portal.

#### <a name="install-the-master-target-by-using-interactive-install"></a>Instalar o destino mestre usando a instalação interativa

1. Execute o comando a seguir para instalar o destino mestre. Escolha a função de agente **Destino mestre**.

    ```
    ./install
    ```

2. Escolha o local padrão para instalação e pressione **Enter** para continuar.

    ![Escolher um local padrão para a instalação de destino mestre](./media/site-recovery-how-to-install-linux-master-target/image17.png)


3. Escolha as configurações **Globais** a definir.

    ![Definição de configurações globais](./media/site-recovery-how-to-install-linux-master-target/image18.png)

4. Especifique os endereços IP do servidor de configuração.

5. Especifique a porta do servidor de configuração como 443.

    ![Especificação do endereço IP e da porta para o servidor de configuração](./media/site-recovery-how-to-install-linux-master-target/image19.png)

6. Copie a senha do servidor de configuração de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase no servidor de configuração e cole-a na caixa **Senha**. A caixa estará vazia, mesmo depois que você colar o texto.

7. Vá para **Sair** no menu.

8. Deixe que a instalação e o registro terminem.

### <a name="install-vmware-tools-on-the-master-target-server"></a>Instalar ferramentas do VMware no servidor de destino mestre

Você precisa instalar ferramentas do VMware no destino mestre para que ele possa descobrir os armazenamentos de dados. Se as ferramentas não forem instaladas, a tela Proteja Novamente não listará os repositórios de dados. Você deverá reiniciar após a instalação das ferramentas do VMware.

## <a name="next-steps"></a>Próximas etapas
Depois que o destino mestre tiver concluído a instalação e o registro, você poderá ver o destino mestre na seção **Destino Mestre** na **Infraestrutura do Site Recovery**, na visão geral do servidor de configuração.

Você pode prosseguir com a [nova proteção](site-recovery-how-to-reprotect.md), seguida pelo failback.

## <a name="common-issues"></a>Problemas comuns

* Não ative Storage vMotion em quaisquer componentes de gerenciamento como um destino mestre. Se o destino mestre se mover após um proteger novamente com êxito, os VMDKs (discos de máquina virtual) não poderão ser desanexados e o failback falhará.
* O destino mestre não deve ter todos os instantâneos na máquina virtual. Se houver instantâneos, o failback falhará.
* Devido a algumas configurações de NIC personalizadas em alguns clientes, a interface de rede é desabilitada durante a inicialização, e não é possível inicializar o destino mestre. Verifique se as propriedades a seguir foram definidas corretamente. Verifique essas propriedades em /etc/sysconfig/network-scripts/ifcfg-eth* do arquivo da placa Ethernet.
        * BOOTPROTO=dhcp * ONBOOT=yes

