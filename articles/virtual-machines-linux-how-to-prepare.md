<properties linkid="manage-linux-howto-linux-agent" urlDisplayName="Prepare a distribution" pageTitle="Prepare a distribution of Linux in Azure" metaKeywords="Azure Git CodePlex, Azure website CodePlex, Azure website Git" description="Learn how to use Git to publish to an Azure website, as well as enable continuous deployment from GitHub and CodePlex." metaCanonical="" services="virtual-machines" documentationCenter="" title="Prepare a Linux Virtual Machine for Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Preparar uma máquina virtual do Linux para o Azure

Uma máquina virtual no Azure executa o sistema operacional que você escolhe ao criar a máquina virtual. O Azure armazena o sistema operacional de uma máquina virtual em um disco rígido virtual no formato VHD (um arquivo .vhd). Um VHD de um sistema operacional que foi preparado para duplicação é chamado de uma imagem. Este artigo mostra como criar sua própria imagem, carregando um arquivo .vhd com um sistema operacional que você instalou e generalizou. Para obter mais informações sobre discos e imagens no Azure, consulte [Gerenciar discos e imagens (a página pode estar em inglês)][Gerenciar discos e imagens (a página pode estar em inglês)]

**Observação**: Ao criar uma máquina virtual, você pode personalizar as configurações do sistema operacional para facilitar a execução de seu aplicativo. A configuração que você definir é armazenada em disco nessa máquina virtual. Para obter instruções, consulte [Como criar uma máquina virtual personalizada][Como criar uma máquina virtual personalizada].

**Importante**: O SLA da plataforma do Azure se aplica a máquinas virtuais com o sistema operacional Linux somente quando uma das distribuições endossadas é usada com os detalhes da configuração especificados [neste artigo][neste artigo]. Todas as distribuições do Linux fornecidas na galeria de imagens do Azure são distribuições endossadas com a configuração necessária.

## Pré-requisitos

Este artigo pressupõe que você tenha os seguintes itens:

-   **Um certificado de gerenciamento** - Você criou um certificado de gerenciamento para a assinatura para a qual você deseja carregar um VHD e exportou o certificado para um arquivo .cer. Para obter mais informações sobre como criar certificados, consulte [Criar um certificado de gerenciamento para o Azure (a página pode estar em inglês)][Criar um certificado de gerenciamento para o Azure (a página pode estar em inglês)].

-   **Sistema operacional Linux instalado em um arquivo .vhd.** - Você instalou um sistema operacional que suporta Linux em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd. Você pode usar uma solução de virtualização, como o Hyper-V, para criar o arquivo .vhd e instalar o sistema operacional. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual][Instalar a função Hyper-V e configurar uma máquina Virtual].

    **Importante**: Não há suporte para o formato VHDX mais recente no Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd.

    Para obter uma lista de distribuições endossadas, consulte [Linux no Azure-Distribuições endossadas][Linux no Azure-Distribuições endossadas]. Como alternativa, consulte a seção no final deste artigo referente a [informações para distribuições não endossadas][informações para distribuições não endossadas].

-   **Ferramenta de linha de comando do Linux Azure.** Se você estiver usando um sistema operacional Linux para criar sua imagem, use essa ferramenta para carregar o arquivo VHD. Para baixar a ferramenta, consulte [Ferramentas de linha de comando do Azure para Linux e Mac][Ferramentas de linha de comando do Azure para Linux e Mac].

-   **Cmdlet Add-AzureVhd**, que faz parte do módulo do PowerShell do Azure. Para baixar o módulo, consulte [Downloads do Azure][Downloads do Azure] (a página pode estar em inglês). Para obter informações de referência, consulte [Add-AzureVhd (a página pode estar em inglês)][Add-AzureVhd (a página pode estar em inglês)].

Para todas as distribuições, observe o seguinte:

O Agente Linux do Azure (waagent) não é compatível com o NetworkManager. A configuração de rede deve usar o arquivo ifcfg-eth0 e deve ser controlável por meio de scripts de ifup/ifdown. O Waagent recusará a instalação se o pacote do NetworkManager for detectado.

NUMA não tem suporte devido a um bug nas versões de kernel do Linux inferiores à 2.6.37. A instalação manual do waagent desabilitará automaticamente o NUMA na configuração do GRUB para a linha de comando do kernel do Linux.

O agente Linux do Azure exige que o pacote python-pyasn1 esteja instalado.

É recomendável que você não crie uma partição SWAP no momento da instalação. Você pode configurar o espaço SWAP usando o Agente Linux do Azure. Também não é recomendável usar o kernel base do Linux com uma máquina virtual do Azure sem o patch disponível no [Site da Microsoft][Site da Microsoft] (muitas distribuições/kernels atuais já podem incluir essa correção).

Todos os VHDs devem ter tamanhos que são múltiplos de 1 MB.

Esta tarefa inclui as seguintes etapas:

-   [Etapa 1: Preparar a imagem a ser carregada][Etapa 1: Preparar a imagem a ser carregada]
-   [Etapa 2: Criar uma conta de armazenamento no Azure][Etapa 2: Criar uma conta de armazenamento no Azure]
-   [Etapa 3: Preparar a conexão com o Azure][Etapa 3: Preparar a conexão com o Azure]
-   [Etapa 4: Carregar a imagem no Azure][Etapa 4: Carregar a imagem no Azure]

## <span id="prepimage"></span> </a>Etapa 1: Preparar a imagem a ser carregada

### Preparar o sistema operacional CentOS 6.2 +

Você deve concluir as etapas de configuração específicas do sistema operacional para que a máquina virtual seja executada no Azure.

1.  No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.

2.  Clique em **Conectar** para abrir a janela da máquina virtual.

3.  Desinstale o NetworkManager executando o seguinte comando:

        rpm -e --nodeps NetworkManager

    **Observação:**Se o pacote ainda não foi instalado, esse comando falhará com uma mensagem de erro. Isso é esperado.

4.  Crie um arquivo chamado **rede** no diretório `/etc/sysconfig/` que contém o seguinte texto:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  Crie um arquivo chamado **ifcfg-eth0** no diretório `/etc/sysconfig/network-scripts/` que contém o seguinte texto:

        DEVICE=eth0
        ONBOOT=yes
        DHCP=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Ative o serviço de rede executando o seguinte comando:

        chkconfig network on

7.  CentOS 6.2 ou 6.3: Instale os drivers dos Serviços de Integração Linux

    **Observação:** A etapa só é válida para CentOS 6.2 e 6.3. Os serviços de integração do Linux em CentOS 6.4 + já estão disponíveis no kernel.

    a) Obtenha o arquivo .iso que contém os drivers dos Serviços de Integração Linux do [Centro de Download][Centro de Download].

    b) No Gerenciador do Hyper-V, no painel **Ações**, clique em **Configurações**.

    ![Abrir configurações do Hyper-V][Abrir configurações do Hyper-V]

    c) No painel **Hardware**, clique em **Controlador IDE 1**.

    ![Adicionar unidade de DVD com mídia de instalação][Adicionar unidade de DVD com mídia de instalação]

    d) Na caixa **Controlador IDE**, clique em **Unidade de DVD** e em **Adicionar**.

    e) Selecione o **Arquivo de imagem**, navegue até **Linux IC v3.2.iso** e clique em **Abrir**.

    f) Na página **Configurações**, clique em **OK**.

    g) Clique em **Conectar** para abrir a janela para a máquina virtual.

    h) Na janela Prompt de comando, digite os seguintes comandos:

        mount /dev/cdrom /media
        /media/install.sh
        reboot

8.  Instale o python-pyasn1 executando o seguinte comando:

        sudo yum install python-pyasn1

9.  Substitua o arquivo /etc/yum.repos.d/CentOS-Base.repo pelo seguinte texto

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

10. Adicione as seguintes linhas /etc/yum.conf

        http_caching=packages
        exclude=kernel*

11. Desabilite o módulo yum "fastestmirror", editando o arquivo "/etc/yum/pluginconf.d/fastestmirror.conf" e, em [main], digite o seguinte

        set enabled=0

12. Execute o seguinte comando para limpar os metadados yum atuais:

        yum clean all

13. Para CentOS 6.2 e 6.3, atualize o kernel de uma VM em funcionamento executando os seguintes comandos

    Para CentOS 6.2, execute os seguintes comandos:

        sudo yum remove kernel-firmware
        sudo yum --disableexcludes=all install kernel

    Para CentOS 6.3+, digite o seguinte:

        sudo yum --disableexcludes=all install kernel

14. Modifique a linha de inicialização do kernel no grub para incluir os parâmetros a seguir. Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

15. Certifique-se de que todos os dispositivos SCSI montados em seu núcleo incluam um tempo limite de e/s de 300 segundos ou mais.

16. Em /etc/sudoers, comente a linha a seguir, se existente:

        Defaults targetpw

17. Certifique-se de que o servidor SSH esteja instalado e configurado para iniciar no momento da inicialização

18. Instale o agente Linux do Azure executando o seguinte comando

        yum install WALinuxAgent

19. Não crie espaço swap no disco do sistema operacional

    O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

20. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

21. Clique em **Desligar** no Gerenciador do Hyper-V.

### Preparar o sistema operacional Ubuntu 12.04+

1.  No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.

2.  Clique em **Conectar** para abrir a janela da máquina virtual.

3.  Substitua os repositórios atuais na sua imagem para usar os repositórios do Azure que contêm o kernel e o pacote do agente que você precisará para atualizar a máquina virtual. As etapas variam um pouco dependendo da versão do Ubuntu.

    Antes de editar o /etc/apt/sources.list, é recomendável fazer um backup
     sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12,04:

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
        sudo apt-get update

    Ubuntu 12.10:

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-add-repository 'http://archive.canonical.com/ubuntu quantal-backports main'
        sudo apt-get update

    Ubuntu 13.04+:

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-get update

4.  Atualize o sistema operacional para o kernel mais recente, executando os seguintes comandos:

    Ubuntu 12,04:

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-precise-virtual
        (recommended) sudo apt-get dist-upgrade
        sudo reboot

    Ubuntu 12.10:

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-quantal-virtual
        (recommended) sudo apt-get dist-upgrade
        sudo reboot

    Ubuntu 13.04 e 13.10:

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
        sudo reboot

5.  Ubuntu aguardará o prompt Grub para entrada do usuário após uma falha do sistema. Para evitar isso, conclua as seguintes etapas:

    a) Abra o arquivo /etc/grub.d/00\_header.

    b) Na função **make\_timeout()**, pesquise por **se ${recordfail}" = 1 ]; e, em seguida**

    c) Altere a instrução abaixo desta linha para **set timeout=5**.

    d) Execute update-grub.

6.  Modifique a linha de inicialização do kernel no Grub ou Grub2 para incluir os parâmetros a seguir. Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

7.  Em /etc/sudoers, comente a linha a seguir, se existente:

        Defaults targetpw

8.  Certifique-se de que o servidor SSH esteja instalado e configurado para iniciar no momento da inicialização

9.  Instale o agente executando os seguintes comandos com sudo:

        apt-get update
        apt-get install walinuxagent 

10. Não crie espaço swap no disco do sistema operacional

    O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12. Clique em **Desligar** no Gerenciador do Hyper-V.

### Preparar o sistema operacional SUSE Linux Enterprise Server 11 SP2 & SP3

**OBSERVAÇÃO:** [SUSE Studio][SUSE Studio] pode criar e gerenciar facilmente suas imagens SLES/opeSUSE para Azure e Hyper-V. Além disso, as seguintes imagens oficiais no SUSE Studio Gallery podem ser baixadas ou clonadas em sua própria conta SUSE Studio para personalização fácil:

> -   [SLES 11 SP2 para Azure no SUSE Studio Gallery][SLES 11 SP2 para Azure no SUSE Studio Gallery]
> -   [SLES 11 SP3 para Azure no SUSE Studio Gallery][SLES 11 SP3 para Azure no SUSE Studio Gallery]

1.  No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.

2.  Clique em **Conectar** para abrir a janela da máquina virtual.

3.  Adicione o repositório que contém o kernel mais recente e o Agente Linux do Azure. Execute o comando `zypper lr`. Por exemplo, com o SLES 11 SP3, a saída deve ser semelhante ao seguinte:

        # | Alias                        | Name               | Enabled | Refresh
        --+------------------------------+--------------------+---------+--------
        1 | susecloud:SLES11-SP1-Pool    | SLES11-SP1-Pool    | No      | Yes
        2 | susecloud:SLES11-SP1-Updates | SLES11-SP1-Updates | No      | Yes
        3 | susecloud:SLES11-SP2-Core    | SLES11-SP2-Core    | No      | Yes
        4 | susecloud:SLES11-SP2-Updates | SLES11-SP2-Updates | No      | Yes
        5 | susecloud:SLES11-SP3-Pool    | SLES11-SP3-Pool    | Yes     | Yes
        6 | susecloud:SLES11-SP3-Updates | SLES11-SP3-Updates | Yes     | Yes

    Caso o comando retorne uma mensagem de erro como a seguinte:

        "No repositories defined. Use the 'zypper addrepo' command to add one or more repositories."

    então os repositórios talvez precisem ser reativados ou registrados no sistema. Isso pode ser feito por meio do utilitário suse\_register. Para obter mais informações, consulte a [documentação SLES][documentação SLES].

    Caso um dos repositórios de atualização relevantes não estiver ativado, ative-o com o comando a seguir:

        zypper mr -e [REPOSITORY NUMBER]

    No caso acima, o comando apropriado seria

        zypper mr -e 1 2 3 4

1.  Atualize o kernel para a versão mais recente disponível:

        zypper up kernel-default

2.  Instale o Agente Linux do Azure:

        zypper up WALinuxAgent

    Você deve ver uma mensagem semelhante à seguinte:

        "There is an update candidate for 'WALinuxAgent', but it is from different vendor.
        Use 'zypper install WALinuxAgent-1.2-1.1.noarch' to install this candidate."

    Como o fornecedor do pacote foi alterado de "Microsoft Corporation" para "SUSE LINUX Products GmbH, Nuremberg, Alemanha", é preciso instalar o pacote como mencionado na mensagem.

    **Observação**: A versão do pacote do WALinuxAgent pode ser um pouco diferente.

3.  Modifique a linha de inicialização do kernel no Grub para incluir os parâmetros a seguir. Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

4.  É recomendável que você defina o /etc/sysconfig/network/dhcp ou o equivalente de DHCLIENT\_SET\_HOSTNAME="yes" como DHCLIENT\_SET\_HOSTNAME="no"

5.  Em /etc/sudoers, comente a linha a seguir, se existente:

        Defaults targetpw

6.  Certifique-se de que o servidor SSH esteja instalado e configurado para iniciar no momento da inicialização

7.  Não crie espaço swap no disco do sistema operacional

    O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

8.  Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

9.  Clique em **Desligar** no Gerenciador do Hyper-V.

### Preparar o sistema operacional openSUSE 12.3

**OBSERVAÇÃO:** [SUSE Studio][SUSE Studio] pode criar e gerenciar facilmente suas imagens SLES/opeSUSE para Azure e Hyper-V. Além disso, as seguintes imagens oficiais no SUSE Studio Gallery podem ser baixadas ou clonadas em sua própria conta SUSE Studio para personalização fácil:

> -   [openSUSE 12,3 para Azure no SUSE Studio Gallery][openSUSE 12,3 para Azure no SUSE Studio Gallery]

1.  No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.

2.  Clique em **Conectar** para abrir a janela da máquina virtual.

3.  Atualize o sistema operacional para os kernel e patches mais recentes disponíveis

4.  No shell, execute o comando '`zypper lr`'. Se esse comando retornar

        # | Alias                     | Name                      | Enabled | Refresh
        --+---------------------------+---------------------------+---------+--------
        1 | Cloud:Tools_openSUSE_12.3 | Cloud:Tools_openSUSE_12.3 | Yes     | Yes
        2 | openSUSE_12.3_OSS         | openSUSE_12.3_OSS         | Yes     | Yes
        3 | openSUSE_12.3_Updates     | openSUSE_12.3_Updates     | Yes     | Yes

    os repositórios estão configurados conforme o esperado e nenhum ajuste é necessário.

    Se o comando retornar "Nenhum repositório definido. Use o comando 'zypper addrepo' para adicionar um
ou mais repositórios". Em seguida, os repositórios precisam ser reabilitados:

        zypper ar -f http://download.opensuse.org/distribution/12.3/repo/oss openSUSE_12.3_OSS
        zypper ar -f http://download.opensuse.org/update/12.3 openSUSE_12.3_Updates

    Verifique se seus repositórios foram adicionados chamando 'zypper Ir' novamente.

    Caso um dos repositórios de atualização relevantes não estiver ativado, ative-o com o comando a seguir:

        zypper mr -e [NUMBER OF REPOSITORY]

1.  Desative a sondagem automática do DVD ROM.

2.  Instale o Agente Linux do Azure:

    Primeiro, adicione o repositório que contém o novo WALinuxAgent:

        zypper ar -f -r http://download.opensuse.org/repositories/Cloud:/Tools/openSUSE_12.3/Cloud:Tools.repo

    Em seguida, execute o seguinte comando:

        zypper up WALinuxAgent

    Depois de executar este comando, você pode receber uma mensagem semelhante a seguinte:

        "There is an update candidate for 'WALinuxAgent', but it is from different vendor. 
        Use 'zypper install WALinuxAgent' to install this candidate."

    Essa mensagem é esperada. Como o fornecedor do pacote foi alterado de "Microsoft Corporation" para "obs://build.opensuse.org/Cloud", é necessário instalar o pacote explicitamente conforme mencionado na mensagem.

3.  Modifique a linha de inicialização do kernel no Grub para incluir os parâmetros a seguir. Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    E em /boot/grub/menu.lst, remova os parâmetros a seguir da linha de comando do kernel, se eles existirem:

        libata.atapi_enabled=0 reserve=0x1f0,0x8

4.  É recomendável que você defina o /etc/sysconfig/network/dhcp ou o equivalente de DHCLIENT\_SET\_HOSTNAME="yes" como DHCLIENT\_SET\_HOSTNAME="no"

5.  Em /etc/sudoers, comente a linha a seguir, se existente:

        Defaults targetpw

6.  Certifique-se de que o servidor SSH esteja instalado e configurado para iniciar no momento da inicialização

7.  Não crie espaço swap no disco do sistema operacional

    O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

8.  Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

9.  Verifique se o Agente Linux do Azure é executado durante a inicialização:

        systemctl enable waagent.service

10. Clique em **Desligar** no Gerenciador do Hyper-V.

## <span id="createstorage"></span> </a>Etapa 2: Criar uma conta de armazenamento no Azure

A conta de armazenamento representa o mais alto nível do namespace para acessar os serviços de armazenamento e está associada à assinatura do Azure. Você precisa de uma conta de armazenamento no Azure para carregar um arquivo .vhd no Azure que pode ser usado para a criação de uma máquina virtual. Você pode criar uma conta de armazenamento usando o Portal de Gerenciamento do Azure.

1.  Entre no Portal de Gerenciamento do Azure.

2.  Na barra de comandos, clique em **Nova**.

    ![Criar conta de armazenamento][Criar conta de armazenamento]

3.  Clique em **Conta de Armazenamento** e clique em **Criação Rápida**.

    ![Criação rápida de uma conta de armazenamento][Criação rápida de uma conta de armazenamento]

4.  Preencha os campos da seguinte maneira:

    ![Insira os detalhes da conta de armazenamento][Insira os detalhes da conta de armazenamento]

-   Em **URL**, digite um nome de subdomínio para usar no URL para a conta de armazenamento. A entrada pode conter de 3 a 24 letras minúsculas e números. Esse nome se torna o nome do host na URL que é usada para lidar com os recursos Blob, Fila ou Tabela da assinatura.

-   Escolha o local ou o grupo de afinidade da conta de armazenamento. Especificando um grupo de afinidade, você pode localizar seus serviços de nuvem no mesmo data center com seu armazenamento.

-   Escolha entre usar a replicação geográfica ou não para a conta de armazenamento. A replicação geográfica é ativada por padrão. Essa opção replica os dados para um local secundário, sem nenhum custo para você, para que seja feito o failover para um local secundário em caso de uma falha grave que não possa ser tratada no local primário. O local secundário é atribuído automaticamente e não pode ser alterado. Se os requisitos legais ou a política organizacional exigir um controle mais restrito do local de seu armazenamento baseado em nuvem, você poderá desativar a replicação geográfica. No entanto, saiba que ao ativar a replicação geográfica posteriormente, você deverá pagar uma taxa de transferência de uma única vez para replicar seus dados existentes para o local secundário. Serviços de armazenamento sem replicação geográfica são oferecidos com desconto.

1.  Clique em **Criar Conta de Armazenamento**.

    A conta é listada em **Contas de Armazenamento**.

    ![Conta de armazenamento criada com êxito][Conta de armazenamento criada com êxito]

## <span id="#connect"></span> </a>Etapa 3: Preparar a conexão com o Azure

Para poder carregar um arquivo .vhd, você precisa estabelecer uma conexão segura entre seu computador e sua assinatura do Azure.

1.  Abra uma janela do PowerShell do Azure.

2.  Tipo:

    `Get-AzurePublishSettingsFile`

    Esse comando abre uma janela de navegador e baixa automaticamente um arquivo .publishsettings que contém informações e um certificado de sua assinatura do Azure.

3.  Salve o arquivo .publishsettings.

4.  Tipo:

    `Import-AzurePublishSettingsFile <PathToFile>`

    Onde `<PathToFile>` é o caminho completo para o arquivo .publishsettings.

    Para obter mais informações, consulte [Introdução aos cmdlets do Azure][Introdução aos cmdlets do Azure]

## <span id="upload"></span> </a>Etapa 4: Carregar a imagem no Azure

Quando carrega o arquivo .vhd, você pode colocá-lo em qualquer lugar no seu armazenamento de blob. Nos exemplos de comando a seguir, **BlobStorageURL** é a URL da conta de armazenamento que você criou na Etapa 2, **YourImagesFolder** é o contêiner no armazenamento de blob onde você deseja armazenar as imagens. **VHDName** é o rótulo que aparece no Portal de Gerenciamento para identificar o disco rígido virtual. **PathToVHDFile** é o caminho completo e o nome do arquivo .vhd.

Faça uma das opções a seguir:

-   Na janela PowerShell do Azure que você usou na etapa anterior, digite:

    `Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

    Para obter mais informações, consulte [Add-AzureVhd (a página pode estar em inglês)][Add-AzureVhd (a página pode estar em inglês)].

-   Use a ferramenta de linha de comando do Linux para carregar a imagem. Você pode carregar uma imagem usando o seguinte comando:

        Azure vm image create <image name> --location <Location of the data center> --OS Linux <Sourcepath to the vhd>

## <span id="nonendorsed"></span> </a>Informações para as distribuições não endossadas

Essencialmente, todas as distribuições em execução no Azure precisam atender aos seguintes pré-requisitos para ter a chance de serem executadas corretamente na plataforma.

Esta lista não é conclusiva, já que cada distribuição é diferente e, por isso, é bem possível que mesmo que você atenda a todos os critérios abaixo, ainda será preciso ajustar significativamente sua imagem para garantir que ela seja corretamente executada no topo da plataforma.

É por isso que recomendamos que você inicie com uma das nossas [imagens endossadas de parceiros][imagens endossadas de parceiros].

A lista a seguir substitui a etapa 1 do processo para criar seu próprio VHD:

1.  Você deverá garantir que está executando um kernel que incorpora os drivers LIS mais recentes para o Hyper-V ou que você conseguiu compilá-los (os códigos deles foram abertos). Os drivers podem ser encontrados [neste local][neste local]

2.  O kernel também deve incluir a versão mais recente do driver ATA PiiX que é usado para fazer a provisão de imagens e possui as correções para o kernel com confirmação cd006086fa5d91414d8ff9ff2b78fbb593878e3c Date: Fri May 4 22:15:11 2012 +0100 ata\_piix: adie os discos para the Hyper-V drivers por padrão

3.  O seu intird compactado deve ser menor que 40 MB (\*estamos trabalhando para aumentar esse número, que pode já estar desatualizado neste momento)

4.  Modifique a linha de inicialização do kernel no Grub ou Grub2 para incluir os parâmetros a seguir. Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

5.  É recomendável que você defina o /etc/sysconfig/network/dhcp ou o equivalente de DHCLIENT\_SET\_HOSTNAME="yes" como DHCLIENT\_SET\_HOSTNAME="no"

6.  Você deve se certificar de que todos os dispositivos SCSI montados em seu núcleo incluam um tempo limite de e/s de 300 segundos ou mais.

7.  Você precisará instalar o Agente Linux do Azure seguindo as etapas no [Guia do Agente Linux][Guia do Agente Linux]. O agente foi lançado sob a licença Apache 2 e você pode obter os bits mais recentes no [local do agente GitHub][local do agente GitHub]

8.  Em /etc/sudoers, comente a linha a seguir, se existente:

        Defaults targetpw

9.  Certifique-se de que o servidor SSH esteja instalado e configurado para iniciar no momento da inicialização

10. Não crie espaço swap no disco do sistema operacional

    O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. Você precisará executar os seguintes comandos para cancelar o provisionamento da máquina virtual:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12. Em seguida, será necessário desligar a máquina virtual e continuar com o carregamento.

  [Como criar uma máquina virtual personalizada]: /pt-br/manage/windows/how-to-guides/custom-create-a-vm/
  [neste artigo]: http://support.microsoft.com/kb/2805216
  [Instalar a função Hyper-V e configurar uma máquina Virtual]: http://technet.microsoft.com/pt-br/library/hh846766.aspx
  [Linux no Azure-Distribuições endossadas]: ../linux-endorsed-distributions
  [informações para distribuições não endossadas]: #nonendorsed
  [Ferramentas de linha de comando do Azure para Linux e Mac]: http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409
  [Downloads do Azure]: /pt-br/develop/downloads/
  [Site da Microsoft]: http://go.microsoft.com/fwlink/?LinkID=253692&clcid=0x409
  [Etapa 1: Preparar a imagem a ser carregada]: #prepimage
  [Etapa 2: Criar uma conta de armazenamento no Azure]: #createstorage
  [Etapa 3: Preparar a conexão com o Azure]: #connect
  [Etapa 4: Carregar a imagem no Azure]: #upload
  [Centro de Download]: http://www.microsoft.com/pt-br/download/details.aspx?id=34603
  [Abrir configurações do Hyper-V]: ./media/virtual-machines-linux-how-to-prepare/settings.png
  [Adicionar unidade de DVD com mídia de instalação]: ./media/virtual-machines-linux-how-to-prepare/installiso.png
  [SUSE Studio]: http://www.susestudio.com
  [SLES 11 SP2 para Azure no SUSE Studio Gallery]: http://susestudio.com/a/02kbT4/sles-11-sp2-for-windows-azure
  [SLES 11 SP3 para Azure no SUSE Studio Gallery]: http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure
  [documentação SLES]: https://www.suse.com/documentation/sles11/
  [openSUSE 12,3 para Azure no SUSE Studio Gallery]: http://susestudio.com/a/02kbT4/opensuse-12-3-for-windows-azure
  [Criar conta de armazenamento]: ./media/virtual-machines-linux-how-to-prepare/create.png
  [Criação rápida de uma conta de armazenamento]: ./media/virtual-machines-linux-how-to-prepare/storage-quick-create.png
  [Insira os detalhes da conta de armazenamento]: ./media/virtual-machines-linux-how-to-prepare/storage-create-account.png
  [Conta de armazenamento criada com êxito]: ./media/virtual-machines-linux-how-to-prepare/Storagenewaccount.png
  [Introdução aos cmdlets do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj554332.aspx
  [imagens endossadas de parceiros]: https://www.windowsazure.com/pt-br/manage/linux/other-resources/endorsed-distributions/
  [neste local]: http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409
  [Guia do Agente Linux]: https://www.windowsazure.com/pt-br/manage/linux/how-to-guides/linux-agent-guide/
  [local do agente GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=250998&clcid=0x409
