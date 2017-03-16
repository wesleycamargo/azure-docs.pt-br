---
title: Como instalar o servidor de Destino Mestre do Linux para o failover do Azure em locais | Microsoft Docs
description: "Antes de proteger novamente uma VM do Linux, você precisa de servidor de destino mestre do Linux. Saiba como instalar um."
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
ms.sourcegitcommit: c4fb25880584add0832464d9049dc6c78059c48b
ms.openlocfilehash: 8ac18526e6781f189444233a191aa1d6c5b863ff
ms.lasthandoff: 02/22/2017


---
# <a name="how-to-install-linux-master-target-server"></a>Como instalar o servidor de Destino Mestre do Linux
Depois de fazer failover suas máquinas virtuais, você pode failback da VM, de volta para o local. Failback, primeiro você precisa obter a máquina virtual no estado protegido por proteger novamente a máquina virtual do Azure no local. Para isso, é necessário um servidor de Destino Mestre para receber o tráfego local. Se sua máquina virtual protegida é uma VM do Windows, é necessário um Destino Mestre do Windows. Para uma VM do Linux, é necessário um Destino Mestre do Linux para proteger novamente. Leia as etapas abaixo sobre como criar e instalar um Destino Mestre do Linux.

## <a name="overview"></a>Visão geral
Este artigo fornece informações e instruções para instalar um Destino Mestre do Linux.

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="pre-requisites"></a>Pré-requisitos

* Para selecionar corretamente o host em que você precisa implantar o MT, determine se o failback será para uma VM existente local ou uma nova VM (porque a VM local foi excluída). 
    * Para uma VM existente, o host do MT deve ter acesso aos repositórios de dados da VM.
    * Se a VM não existe localmente, a VM do failback é criada no mesmo host do MT. Você pode escolher qualquer host ESXi para instalar o MT.
* O MT deve estar em uma rede que pode se comunicar com o servidor de processo e com o servidor de configuração.
* A versão do MT deve ser inferior ou igual ao servidor de processo e o servidor de configuração. (exemplo, se o CS for 9.4, o MT poderá ser 9.4 e 9.3 – mas não 9.5)
* O MT só pode ser uma VM do VMware, não uma VM física.


## <a name="steps-to-deploy-the-master-target-server"></a>Etapas para implantar o servidor de Destino Mestre

### <a name="install-centos-66-minimal"></a>Instalar o CentOS 6.6 Minimal

Siga as etapas mencionadas a seguir para instalar o sistema operacional CentOS 6.6 de 64 bits.

1. Dos links a seguir, selecione o espelho mais próximo para baixar um ISO de 64 bits do CentOS 6.6, no mínimo.

    <http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    Mantenha o ISO do CentOS 6.6 Minimal de 64 bits na unidade de DVD e inicialize o sistema.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

2. Selecione **Ignorar** para ignorar o processo de teste de mídia.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

3. Agora você pode ver a tela de boas-vindas da instalação. Clique no botão **Avançar**.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

4. Selecione **Inglês** como seu idioma de preferência e clique em **Avançar** para continuar.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

5. Selecione **Inglês (Estados Unidos)** como um Layout de teclado. Clique em **Avançar** para continuar a instalação.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

6. Selecione o tipo dos dispositivos em que você fará a instalação. Selecione **dispositivos de armazenamento básico**. Clique em **Avançar** para continuar a instalação.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

7. Uma mensagem de aviso é exibida, indicando que os dados existentes no disco rígido serão excluídos. Certifique-se de que o disco rígido não contém dados importantes e clique em **Sim, descartar qualquer dado**.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

8. Insira o nome do host para o servidor em **caixa de texto Nome de host**. Clique em **Configurar Rede** e, na janela **Conexão de rede**, selecione sua interface de rede. Clique no botão **Editar** para ajustar as configurações de IPV4.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

9. Em seguida, a janela **Sistema de Edição eth0** será exibida. Selecione a caixa de seleção **Conectar automaticamente**. Na guia "Configurações de IPv4", selecione o método como **Manual** e, em seguida, clique em **Adicionar**. Forneça os detalhes de IP estático, de Máscara de Rede, de Gateway e de Servidor DNS. Clique em **Aplicar** para salvar os detalhes.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

10. Selecione seu fuso horário na caixa de combinação e clique em **Avançar** para continuar.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

11. Insira a **Senha raiz** e confirme a senha, clique em **Avançar** para continuar.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

12. Selecione **Criar Layout Personalizado** como Modo de Partição e clique em **Avançar** para continuar.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

13. Selecione partição **Gratuita** e clique em **Criar** para a criação das partições **/**, **/var/crash** e **/home** com **ext4** como o Tipo de Sistema de Arquivos. Criar **Partição de troca** com **Troca** como tipo de sistema de arquivos. Para alocar o tamanho da partição, siga a fórmula de alocação de tamanho conforme mencionado na tabela abaixo.

    > [!NOTE]
    > O sistema Destino Mestre (MT) do Linux não deve usar LVM para a raiz ou espaços de armazenamento de retenção. O DM do Linux é configurado por padrão para evitar a detecção de partições/discos LVM.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

14. Após a criação da partição, clique em **Avançar** para continuar.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

15. Se dispositivos já existentes forem encontrados, a mensagem de aviso aparecerá para formatação. Clique em **Formatar** para formatar o disco rígido de acordo com a tabela de partição mais recente.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)

16. Clique em **Gravar alterações no disco** para aplicar as alterações de partição no disco.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

17. Marque a opção **Instalar carregador de inicialização** e clique em **Avançar** para instalar o carregador de inicialização na partição raiz.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)


18. O processo de Instalação é iniciado. Você pode monitorar o progresso da instalação.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

19. A tela a seguir é exibida após a conclusão bem-sucedida da instalação. Clique em **Reinicializar**

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>Etapas pós-instalação
Em seguida, podemos irá preparar o computador a ser configurado como um servidor de Destino Mestre.

Para obter a ID de SCSI de cada disco SCSI em uma máquina virtual Linux, você deve habilitar o parâmetro "disk.EnableUUID = TRUE".

Para habilitar esse parâmetro, execute as etapas conforme indicado abaixo:

a. Desligue sua máquina virtual.

b. Clique com o botão direito do mouse na entrada da VM no painel esquerdo e selecione **Editar Configurações.**

c. Clique na guia **Opções** .

d. Selecione **Avançado&gt;Item geral** à esquerda e clique em **Parâmetros de Configuração** à direita.

![](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

A opção "Parâmetros de Configuração" estará em um estado desativado quando a máquina estiver em execução. Para ativar essa guia, desligue a máquina.

e. Verifique se já existe uma linha com **disk.EnableUUID**.

f. Se o valor existir e se estiver definido como Falso, substitua o valor por Verdadeiro (os valores Verdadeiro e Falso diferenciam letras maiúsculas de minúsculas).

g. Se o valor existe e se estiver definido como true, clique em Cancelar.
    
h. Se o valor não existir, clique em **Adicionar Linha.**

i. Adicione o disco. EnableUUID à coluna Nome e defina seu valor como TRUE

   * Se já existir e se o valor estiver definido como Falso, substitua o valor por Verdadeiro (os valores Verdadeiro e Falso diferenciam letras maiúsculas de minúsculas).

   * Se já existir e estiver definida como Verdadeiro, clique em Cancelar e teste o comando SCSI dentro do sistema operacional convidado após a inicialização.

f. Se não existir, clique em **Adicionar Linha.**

  Adicione disk.EnableUUID à coluna Nome.

  Defina seu valor como VERDADEIRO.



![](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-the-additional-packages"></a>Baixar e instalar os pacotes adicionais

> [!NOTE]
> Verifique se o sistema tem conectividade com a Internet antes de baixar e instalar pacotes adicionais caso contrário, você precisará descobrir esses pacotes RPM e instalá-los manualmente.

```
yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

O comando acima baixará os 15 pacotes mencionados abaixo do repositório CentOS 6.6 e os instalará. Se você não tiver acesso à internet, você precisará baixar o RPM a seguir.


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
> Se as máquinas protegidas de origem usarem o sistema de arquivos Reiser ou XFS para o dispositivo raiz ou de inicialização, será necessário baixar e instalar os pacotes adicionais a seguir no Destino Mestre do Linux antes da proteção.
 

***ReiserFS (se usado em Suse11SP3. Entretanto, o ReiserFS não é o sistema de arquivos padrão em Suse11SP3)***

```
cd /usr/local

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
```

***XFS (RHEL, CentOS 7 e seguintes)***

```
cd /usr/local

wget
<http://archive.kernel.org/centos-vault/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

yum install device-mapper-multipath 
```
Isso é necessário para habilitar pacotes de vários caminhos no servidor do MT.

### <a name="get-the-installer-for-setup"></a>Obter o Instalador para instalação

Se você tiver acesso à internet do seu servidor de Destino Mestre, você pode baixar o instalador por meio de etapas a seguir - ou você pode copiar o instalador do servidor de processo e instalá-lo.

#### <a name="download-the-mt-installation-packages"></a>Baixe os pacotes de instalação do MT

Baixe os bits de instalação mais recentes do destino mestre do Linux aqui – [https://aka.ms/latestlinuxmobsvc](https://aka.ms/latestlinuxmobsvc).

Para baixá-lo por meio do Linux, digite 

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

Certifique-se de baixar e descompactar o instalador apenas em seu diretório base. Se você descompactá-lo em /usr/Local, a instalação falhará.


#### <a name="access-the-installer-from-the-process-server"></a>Acessar o instalador do servidor de processos

Vá para o servidor de processo e navegue até o diretório - C:\Arquivos de Programas (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository

Copie o arquivo do instalador obrigatório do servidor de processo e salvá-lo como latestlinuxmobsvc.tar.gz no seu diretório base


### <a name="apply-custom-configuration-changes"></a>Aplicar alterações de configuração personalizadas

Para aplicar alterações de configuração personalizadas, execute as etapas mencionadas abaixo:


1. Execute o comando abaixo para descompactar o binário.
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/image16.png)
    
2. Execute o comando abaixo para conceder a permissão.
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. Execute o comando abaixo para executar o script.
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> Execute o script apenas uma vez no servidor. Desligue o servidor. Reinicialize o servidor depois de adicionar um disco conforme indicado nas etapas a seguir.

### <a name="add-retention-disk-to-linux-mt-vm"></a>Adicionar disco de retenção à VM do MT do Linux 

Siga as etapas abaixo para criar um disco de retenção.

1. Anee um novo disco de **1 TB** à VM de MT do Linux e **inicialize** a máquina.

2. Invocar o comando **multipath -ll** para saber a identificação de vários caminhos do disco de retenção.

    ```
    multipath -ll
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. Formate a unidade e crie um sistema de arquivos na nova unidade. 
    
    ```
    mkfs.ext4 /dev/mapper/<Retention disk's multipath id>
    ```
    ![](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. Depois de terminar a criação do sistema de arquivos, monte o disco de retenção.
    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. Finalmente, crie a entrada /etc/fstab para montar a unidade de retenção durante cada inicialização
    ```
    vi /etc/fstab 
    ```
    Pressione [Inserir] para começar a editar o arquivo. Crie uma nova linha e insira o seguinte texto nela. Edite a ID de vários caminhos de disco com base no ID do multipath realçado no comando anterior.

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    Pressione [Esc] e digite :wq (gravar e sair) para fechar a janela do editor.

### <a name="install-master-target"></a>Instalar Destino Mestre

> [!NOTE]
> A versão do servidor de destino mestre deve ser inferior ou igual ao servidor de processo e o servidor de configuração. Se a versão do MT for superior, o Proteja Novamente terá êxito, mas a replicação falhará.
 

> [!NOTE]
> Antes de instalar o servidor de destino mestre, verifique se o arquivo /etc/hosts na VM contém entradas que mapeiam o nome do host local para endereços IP associados a todos os adaptadores de rede.
 
1. Para fazer isso, copie a frase secreta de **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** no servidor de configuração e salve-a em passphrase.txt no mesmo diretório local ao executar o comando a seguir.

    ```
    echo <passphrase> >passphrase.txt
    ```
    Exemplo: echo itUx70I47uxDuUVY >passphrase.txt

2. Anote o endereço IP do servidor de Configuração. Precisaremos disso na próxima etapa

3. Execute o seguinte comando para instalar o servidor de Destino Mestre e registrar o servidor com o servidor de configuração.
    
    ```
    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i <Configuration Server IP Address> -p 443 -s y -c https -P passphrase.txt
    ```

    Exemplo: ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

    Aguarde a conclusão da execução do script. Se o Destino Mestre é registrado com êxito, você pode ver o DM listado na página de infra-estrutura de recuperação de Site no portal.

#### <a name="installing-master-target-using-interactive-install"></a>Instalação do Destino Mestre usando a instalação interativa

1. Execute o comando a seguir para instalar o Destino Mestre. Escolha a função de agente "Destino mestre".

    ```
    ./install
    ```

2. Escolha o local padrão para instalação - pressione [Enter] para continuar
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image17.png)
    

3. Escolha as configurações globais a configurar

    ![](./media/site-recovery-how-to-install-linux-master-target/image18.png)
    
4. Especifique os endereços IP do servidor CS

5. Especifique a porta do servidor CS como 443.
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image19.png)
    
6. Copie a frase secreta CS de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase no servidor de configuração e especifique-a na caixa de senha. Ele aparecerá vazio mesmo após a colagem de cópia.

7. Vá para [Sair] no menu

8. Deixe que a instalação e o registro sejam concluídos.

### <a name="install-vmware-tools-on-the-master-target-server"></a>Instalar ferramentas do VMware no servidor de Destino Mestre

As ferramentas do VMware precisam ser instaladas no MT para que ele possa descobrir os repositórios de dados. Se as ferramentas não forem instaladas, a tela Proteja Novamente não listará os repositórios de dados. Você deverá reiniciar após a instalação das ferramentas do VMware.

## <a name="next-steps"></a>Próximas etapas
Uma vez o mestre de destino foi concluída a instalação e registro, você pode ver o DM aparecem na seção de Destino Mestre na infra-estrutura de recuperação de Site, em Visão geral do servidor de configuração.

Você pode prosseguir com a [Nova Proteção](site-recovery-how-to-reprotect.md), seguida pelo Failback.

## <a name="common-issues"></a>Problemas comuns

* Não ative Storage vMotion em quaisquer componentes de gerenciamento como DM. Se o DM move post Proteja um successfult, não é possível desanexar o VMDK e o failback falhará.
* A máquina MT não deve ter todos os instantâneos na máquina virtual. Se houver instantâneos, o failback falhará.
* Devido a algumas configurações de NIC personalizadas em alguns clientes, a interface de rede é desabilitada durante a inicialização, e não é possível inicializar o agente MT. Verifique se as seguintes propriedades estão definidas corretamente.
    * Verifique as duas propriedades na ethernet cartão arquivos /etc/sysconfig/network-scripts/ifcfg-eth *
        * BOOTPROTO=dhcp 
        * ONBOOT=yes


