---
title: Como instalar o servidor de destino mestre do Linux | Microsoft Docs
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
ms.date: 12/20/2016
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: d1a7ed7e182530f81a426a4383297a49505f65ea
ms.openlocfilehash: d76ea0fb27ecece4e8dcd06a2dde9a0794071884


---
# <a name="how-to-install-linux-master-target-server"></a>Como instalar o servidor de destino mestre do Linux
O Azure Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) administrando a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. As máquinas podem ser replicadas no Azure ou em um datacenter local secundário. Para ter uma breve visão geral, leia [O que é Azure Site Recovery?](site-recovery-overview.md)

## <a name="overview"></a>Visão geral
Este artigo fornece informações e instruções de recuperação (failover e failback) de máquinas virtuais e servidores físicos que são protegidos pela Recuperação de Site.

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="pre-requisites"></a>Pré-requisitos

1. Para selecionar corretamente o host em que você precisa implantar o MT, determine se o failback será para uma VM existente local ou uma nova VM (porque a VM local foi excluída). 
    * Para uma VM existente, o host do MT deve ter acesso aos repositórios de dados da VM.
    * Se a VM não existe localmente, a VM do failback é criada no mesmo host do MT.
2. O MT deve estar em uma rede que pode se comunicar com o servidor de processo e com o servidor de configuração.
3. A versão do MT deve ser inferior ou igual ao servidor de processo e o servidor de configuração. (exemplo, se o CS for 9.4, o MT poderá ser 9.4 e 9.3 – mas não 9.5)
4. O MT só pode ser uma VM do VMware, não uma VM física.


## <a name="steps-to-deploy-the-master-target-server"></a>Etapas para implantar o servidor de destino mestre

### <a name="install-centos-66-minimal"></a>Instalar o CentOS 6.6 Minimal

Siga as etapas mencionadas a seguir para instalar o sistema operacional CentOS 6.6 de 64 bits.

**Etapa 1:** dos links a seguir, selecione o espelho mais próximo para baixar um ISO para CentOS 6.6 Minimal de 64 bits.

<http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

Mantenha o ISO do CentOS 6.6 Minimal de 64 bits na unidade de DVD e inicialize o sistema.

![](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

**Etapa 2:** selecione **Ignorar** para ignorar o processo de teste de mídia.

![](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

**Etapa 3:** agora você pode ver a tela de boas-vindas da instalação. Clique no botão **Avançar**.

![](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

**Etapa 4:** selecione **Inglês** como seu idioma de preferência e clique em **Avançar** para continuar.

![](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

**Etapa 5:** selecione **inglês americano** como seu layout de teclado. Clique em **Avançar** para continuar a instalação.

![](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

**Etapa 6:** selecione o tipo dos dispositivos no qual você fará a instalação. Selecione **dispositivos de armazenamento básico**.

Clique em **Avançar** para continuar a instalação.

![](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

**Etapa 7:** uma mensagem de aviso é exibida, indicando que os dados existentes no disco rígido serão excluídos. Certifique-se de que o disco rígido não contém dados importantes e clique em **Sim, descartar qualquer dado**.

![](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

**Etapa 8:** digite o nome do host para o servidor em **caixa de texto de nome de host**.
Clique em **Configurar Rede**,

Na janela **Conexão de rede**, selecione seu adaptador de rede. Clique no botão **Editar** para ajustar as configurações de IPV4.

![](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

**Etapa 9:** A janela **Sistema de edição** será exibida. Selecione a caixa de seleção **Conectar automaticamente**. Na guia "Configurações de IPv4", selecione o método como **Manual** e, em seguida, clique em **Adicionar**. Forneça os detalhes do endereço IP estático, máscara de rede, Gateway e servidor DNS. Clique em **Aplicar** para salvar os detalhes.

![](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

**Etapa 10:** selecione seu fuso horário na caixa de combinação e clique em **Avançar** para continuar.

![](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

**Etapa 11:** insira a **senha raiz** e confirme a senha, clique em **Avançar** para continuar.

![](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

**Etapa 12:** selecione **Criar Layout Personalizado** como modo de partição e clique em **Avançar** para continuar.

![](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

**Etapa 13:** selecione partição **Gratuita** e clique em **Criar** para a criação das partições **/**, **/var/crash** e **/home** com **ext4** como tipo de sistema de arquivos. Criar **Partição de troca** com **Troca** como tipo de sistema de arquivos. Para alocar o tamanho da partição, siga a fórmula de alocação de tamanho conforme mencionado na tabela abaixo.

OBSERVAÇÃO: o sistema DM (destino de mestre) do Linux não deve usar LVM para espaços de armazenamento raiz ou de retenção. O DM do Linux é configurado por padrão para evitar a detecção de partições/discos LVM.

![](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

**Etapa 14:** após a criação da partição, clique em **Avançar** para continuar.

![](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

**Etapa 15:** se dispositivos já existentes forem encontrados, a mensagem de aviso aparecerá para formatação.

Clique em **Formatar** para formatar o disco rígido de acordo com a tabela de partição mais recente.

![](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)



**Etapa 16:** clique em **Gravar alterações no disco** para aplicar as alterações de partição no disco.

![](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

**Etapa 17:** marque a opção **Instalar carregador de inicialização** e clique em **Avançar** para instalar o carregador de inicialização na partição raiz.

![](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)



**Etapa 18:** inicia-se o processo de instalação. Você pode monitorar o progresso da instalação.

![](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

**Etapa 19:** exibe a tela a seguir após a conclusão bem-sucedida da instalação. Clique em **Reinicializar**

![](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>Etapas pós-instalação

Para obter a ID de SCSI de cada disco SCSI em uma máquina virtual Linux, você deve habilitar o parâmetro "disk.EnableUUID = TRUE".

Para habilitar esse parâmetro, execute as etapas conforme indicado abaixo:

a. Desligue sua máquina virtual.

b. Clique com o botão direito do mouse na entrada da VM no painel esquerdo e selecione **Editar Configurações.**

c. Clique na guia **Opções** .

d. Selecione **Avançado&gt;Item geral** à esquerda e clique em **Parâmetros de Configuração** à direita.

![](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

A opção "Parâmetros de Configuração" estará em um estado desativado quando a máquina estiver em execução. Para ativar essa guia, desligue a máquina.

e. Verifique se já existe uma linha com **disk.EnableUUID**.

  Se já existir e se o valor estiver definido como Falso, substitua o valor por Verdadeiro (os valores Verdadeiro e Falso diferenciam letras maiúsculas de minúsculas).

  Se já existir e estiver definida como Verdadeiro, clique em Cancelar e teste o comando SCSI dentro do sistema operacional convidado após a inicialização.

f. Se não existir, clique em **Adicionar Linha.**

  Adicione disk.EnableUUID à coluna Nome.

  Defina seu valor como VERDADEIRO.

OBSERVAÇÃO: não adicione os valores acima com aspas duplas.

![](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-the-additional-packages"></a>Baixar e instalar os pacotes adicionais

OBSERVAÇÃO: verifique se o sistema tem conexão com a Internet antes de baixar e instalar os pacotes adicionais.

```
# yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

O comando acima baixará os 15 pacotes mencionados abaixo do repositório CentOS 6.6 e os instalará.


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

OBSERVAÇÃO: se as máquinas protegidas de origem usarem o sistema de arquivos Reiser ou XFS para o dispositivo raiz ou de inicialização, será necessário baixar e instalar os pacotes adicionais a seguir no destino mestre do Linux antes da proteção.

***ReiserFS (Suse11SP3. O ReiserFS não é o sistema de arquivos padrão de Suse11SP3)***

```
# cd /usr/local

# wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

# wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
```

***XFS (RHEL, CentOS 7 e seguintes)***

```
# cd /usr/local

# wget
<http://archive.kernel.org/centos-vault/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

# yum install device-mapper-multipath 
```
Isso é necessário para habilitar pacotes de vários caminhos no servidor do MT.

### <a name="download-the-mt-installation-packages"></a>Baixe os pacotes de instalação do MT

Baixe os bits de instalação mais recentes do destino mestre do Linux aqui – [https://aka.ms/latestlinuxmobsvc](https://aka.ms/latestlinuxmobsvc).

Para baixá-lo por meio do Linux, digite 

```
    # wget https://aka.ms/latestlinuxmobsvc
```

Certifique-se de baixar e descompactar o instalador apenas em seu diretório base. Se você descompactá-lo em /usr/Local, a instalação falhará.

### <a name="apply-custom-configuration-changes"></a>Aplicar alterações de configuração personalizadas

Antes de aplicar alterações de configuração personalizadas, execute as Etapas Pós-instalação

Para aplicar alterações de configuração personalizadas, execute as etapas mencionadas abaixo:

1. Copie o binário Agente Unificado RHEL6-64 para o OS recém-criado.

2. Execute o comando abaixo para descompactar o binário.
    ```
    tar -zxvf <File name>
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/image16.png)
    
3. Execute o comando abaixo para conceder a permissão.
    ```
    # chmod 755 ./ApplyCustomChanges.sh
    ```

4. Execute o comando abaixo para executar o script.
    ```
    # ./ApplyCustomChanges.sh
    ```
OBSERVAÇÃO: execute o script apenas uma vez no servidor. **REINICIALIZE** o servidor após a execução bem-sucedida do script acima.

### <a name="add-retention-disk-to-linux-mt-vm"></a>Adicione o disco de retenção à VM do MT do Linux 

Siga as etapas abaixo para criar um disco de retenção.

**Etapa 1:** anexar um novo disco de **1 TB** à VM do MT do Linux e descobrir sua identificação de vários caminhos

Invocar o comando **multipath -ll** para saber a identificação de vários caminhos do disco de retenção.

![](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

**Etapa 2:** invocar o comando **mkfs.ext4 /dev/mapper/<ID de multicaminho do disco de retenção>** para criar um sistema de arquivos no disco de retenção.

![](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

**Etapa 3:** após a criação do sistema de arquivos, invocar os comandos abaixo para montar o disco de retenção.

![](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

**Etapa 4:** criar a entrada fstab:
```
vi /etc/fstab 
```
e acrescentar a linha

** /dev/mapper/36000c2989daa2fe6dddcde67f2079afe /mnt/retention ext4 rw 0 0 **

### <a name="install-master-target"></a>Instalar Destino Mestre


> [!NOTE]
> A versão do servidor de destino mestre deve ser inferior ou igual ao servidor de processo e o servidor de configuração. Se a versão do MT for superior, o Proteja Novamente terá êxito, mas a replicação falhará.
> 

> [!NOTE]
> Antes de instalar o servidor de destino mestre, verifique se o arquivo /etc/hosts na VM contém entradas que mapeiam o nome do host local para endereços IP associados a todos os adaptadores de rede.
> 


1. Execute o comando abaixo para instalar o destino mestre. Escolha a função de agente "Destino mestre".
```
# ./install
```

2. Escolha o local padrão para instalação
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image17.png)
    

3. Escolha as configurações globais a configurar

    ![](./media/site-recovery-how-to-install-linux-master-target/image18.png)
    
4. Especifique os endereços IP do servidor CS

5. Especifique a porta do servidor CX – geralmente, é a porta 9443.
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image19.png)
    
6. Copie a frase secreta CS de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase no servidor de configuração.

7. Deixe que a instalação e o registro sejam concluídos.
### <a name="install-vmware-tools-on-the-master-target-server"></a>Instalar ferramentas do VMware no servidor de destino mestre

As ferramentas do VMware precisam ser instaladas no MT para que ele possa descobrir os repositórios de dados. Se as ferramentas não forem instaladas, a tela Proteja Novamente não listará os repositórios de dados.

## <a name="next-steps"></a>Próximas etapas
 

## <a name="common-issues"></a>Problemas comuns



<!--HONumber=Feb17_HO3-->


