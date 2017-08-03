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
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: c7c50c539149a929b15f50e4b52dc48d92534640
ms.contentlocale: pt-br
ms.lasthandoff: 07/06/2017


---
# <a name="how-to-install-a-linux-master-target-server"></a>Como instalar o servidor de destino mestre do Linux
Após o failover de suas máquinas virtuais, você poderá executar failback das máquinas virtuais para o site local. Para realizar failback, você precisa proteger novamente a máquina virtual do Azure para o site local. Para este processo, é necessário um servidor de destino mestre para receber o tráfego. Se sua máquina virtual protegida for uma máquina virtual do Windows, será necessário um destino mestre do Windows. Para uma máquina virtual Linux, é necessário um destino mestre do Linux. Leia as etapas a seguir para saber como criar e instalar um destino mestre do Linux.

> [!IMPORTANT]
> Começando com a versão do 9.10.0 do servidor de destino Mestre, o servidor de destino mestre mais recente pode ser instalado somente em um servidor do Ubuntu 16.04. Novas instalações não serão permitidas nos servidores CentOS6.6. No entanto, você pode continuar a atualizar os servidores de destino mestre antigos usando a versão 9.10.0.

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

**Instale o Ubuntu 16.04.2 Minimal**

Siga as etapas mencionadas a seguir para instalar o sistema operacional Ubuntu 16.04.2 de 64 bits.

**Etapa 1:** Nos links a seguir, selecione o espelho mais próximo para baixar uma ISO para o Ubuntu 16.04.2 Minimal de 64 bits

<https://www.ubuntu.com/download/server/thank-you?version=16.04.2&architecture=amd64>

Mantenha a ISO do Ubuntu 16.04.2 Minimal de 64 bits na unidade de DVD e inicialize o sistema.

**Etapa 2:** Selecione **Inglês** como o seu Idioma de preferência e pressione Enter.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image1.png)

**Etapa 3:** Selecione **Instalar Servidor do Ubuntu** e pressione Enter

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image2.png)

**Etapa 4:** Selecione **Inglês** como o seu Idioma de preferência e aperte Enter

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image3.png)

**Etapa 5:** Selecione a opção apropriada para o **Fuso Horário** na lista de opções e pressione Enter

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image4.png)

**Etapa 6:** Selecione a opção padrão **NÃO** e pressione Enter

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image5.png)

**Etapa 7:** Selecione **Inglês (EUA)** como um país de origem para o teclado e pressione Enter

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image6.png)

**Etapa 8:** Selecione **Inglês (EUA)** como um layout do teclado e pressione Enter

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image7.png)

**Etapa 9:** Digite o nome do host para o servidor na **Caixa de texto de nome do host**, Clique no botão **Continuar**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image8.png)

**Etapa 10:** Digite o **Nome de usuário** para criar uma conta de usuário na **Caixa de texto**, Clique no botão **Continuar**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image9.png)

**Etapa 11:** Digite a **Senha** para criar uma nova conta de usuário na **Caixa de texto**, Clique no botão **Continuar**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image10.png)

**Etapa 12:** Confirme a **Senha** para criar um novo usuário na **Caixa de texto**, Clique no botão **Continuar**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image11.png)

**Etapa 13:** Selecione a opção padrão **NÃO** e pressione **Enter**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image12.png)

**Etapa 14:** Selecione a opção padrão **SIM** se o fuso horário exibido estiver correto e pressione **Enter.**

Você pode selecionar a opção **Não** para configurar novamente o seu fuso horário.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image13.png)

**Etapa 15:** Selecione a opção "**Interativo -** **Usar disco inteiro**" nas opções de método de particionamento e pressione **Enter.**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image14.png)

**Etapa 16:** Selecione o disco adequado nas opções para **selecionar disco para partição** e pressione **Enter.**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image15.png)

**Etapa 17:** Selecione **SIM** para gravar alterações em discos e pressione **Enter.**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image16.png)

**Etapa 18:** Selecione a opção padrão, Selecione Continuar e pressione Enter.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image17.png)

**Etapa 19:** Selecione a opção apropriada para gerenciar atualizações manager no seu sistema e pressione **Enter**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image18.png)

> [!WARNING]
> Como o servidor de destino mestre de recuperação de site do Azure requer uma versão muito específica do Ubuntu, você precisa garantir que as atualizações de kernel estão desabilitadas para a máquina virtual. Se elas estiverem habilitadas, em seguida, todas as atualizações regulares farão com que o servidor de destino mestre não funcione corretamente. Verifique se você selecionou a opção "Sem atualizações automáticas".


**Etapa 20:** Você pode prosseguir com as opções padrão. Se você quiser uma conexão do openSSH para SSH, então selecione a opção "OpenSSH server" e depois Avançar.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image19.png)

**Etapa 21:** Selecione a opção **SIM** e pressione **Enter**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image20.png)

**Etapa 22:** Selecione o dispositivo apropriado (Preferencialmente /dev/sda) para instalação do carregador de inicialização e pressione **Enter.**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image21.png)

**Etapa 23:** Selecione o botão **Continuar** e pressione **Enter** para **concluir a instalação.**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image22.png)

Após a conclusão da instalação. Faça logon na VM usando as novas credenciais de usuário (Consulte a **Etapa 10**).

Siga as etapas mencionadas na captura de tela a seguir para definir a senha do usuário raiz e para fazer logon como usuário raiz para outras operações.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image23.png)


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

#### <a name="disable-kernel-upgrades"></a>**Desabilitar atualizações de kernel**

Como o servidor de destino mestre de recuperação de site do Azure requer uma versão muito específica do Ubuntu, você precisa garantir que as atualizações de kernel estão desabilitadas para a máquina virtual. Se elas estiverem habilitadas, em seguida, todas as atualizações regulares farão com que o servidor de destino mestre não funcione corretamente. Siga as etapas a seguir para desabilitar atualizações de kernel.
> [!IMPORTANT]
> As etapas de script devem ser colocadas aqui

#### <a name="download-and-install-additional-packages"></a>Baixar e instalar pacotes adicionais

> [!NOTE]
> Verifique se você tem conectividade com a Internet para baixar e instalar pacotes adicionais. Sem conectividade com a Internet, você precisará encontrar esses pacotes RPM e instalá-los manualmente.

```
apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx
```

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
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Exemplo: /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt

    Aguarde até que o script seja concluído. Se o destino mestre for registrado com êxito, ele será listado na página Infraestrutura do Site Recovery do portal.


#### <a name="install-the-master-target-by-using-interactive-install"></a>Instalar o destino mestre usando a instalação interativa

1. Execute o comando a seguir para instalar o destino mestre. Escolha a função de agente **Destino mestre**.

    ```
    ./install
    ```

2. Escolha o local padrão para instalação e pressione **Enter** para continuar.

    ![Escolher um local padrão para a instalação de destino mestre](./media/site-recovery-how-to-install-linux-master-target/image17.png)

Quando a instalação for concluída, você precisa registrar o servidor de configuração usando a linha de comando.

1. Anote o endereço IP do servidor de configuração. Você precisará disso na próxima etapa.

2. Execute o comando a seguir para instalar o servidor de destino mestre e registrar o servidor no servidor de configuração.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Exemplo: /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt

    Aguarde até que o script seja concluído. Se o destino mestre for registrado com êxito, ele será listado na página Infraestrutura do Site Recovery do portal.


### <a name="upgrade-the-master-target"></a>Atualizar o destino mestre

Execute o instalador. Ele detecta automaticamente que o agente está instalado no destino mestre. Selecione 'Y' para atualizar. Quando a instalação for concluída, você pode verificar a versão de destino mestre instalada usando o comando a seguir.

    ```
        cat /usr/local/.vx_version
    ```

Você pode ver que o campo de versão fornece o número de versão de destino mestre.

### <a name="install-vmware-tools-on-the-master-target-server"></a>Instalar ferramentas do VMware no servidor de destino mestre

Você precisa instalar ferramentas do VMware no destino mestre para que ele possa descobrir os armazenamentos de dados. Se as ferramentas não forem instaladas, a tela Proteja Novamente não listará os repositórios de dados. Você deverá reiniciar após a instalação das ferramentas do VMware.

## <a name="next-steps"></a>Próximas etapas
Depois que o destino mestre tiver concluído a instalação e o registro, você poderá ver o destino mestre na seção **Destino Mestre** na **Infraestrutura do Site Recovery**, na visão geral do servidor de configuração.

Você pode prosseguir com a [nova proteção](site-recovery-how-to-reprotect.md), seguida pelo failback.

## <a name="common-issues"></a>Problemas comuns

* Não ative Storage vMotion em quaisquer componentes de gerenciamento como um destino mestre. Se o destino mestre se mover após um proteger novamente com êxito, os VMDKs (discos de máquina virtual) não poderão ser desanexados e o failback falhará.
* O destino mestre não deve ter todos os instantâneos na máquina virtual. Se houver instantâneos, o failback falhará.
* Devido a algumas configurações de NIC personalizadas em alguns clientes, a interface de rede é desabilitada durante a inicialização, e não é possível inicializar o destino mestre. Verifique se as propriedades a seguir foram definidas corretamente. Verifique essas propriedades em /etc/sysconfig/network-scripts/ifcfg-eth* do arquivo da placa Ethernet.
    * BOOTPROTO=dhcp
    * ONBOOT=yes

