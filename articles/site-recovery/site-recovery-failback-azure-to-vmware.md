<properties 
   pageTitle="Realizar o failback de máquinas virtuais VMware e servidores físicos para o site local | Microsoft Azure"
   description="Saiba mais sobre como realizar o failback no site local após o failover de VMs do VMware e de servidores físicos no Azure." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="10/05/2016"
   ms.author="ruturajd"/>


# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site"></a>Realizar o failback de máquinas virtuais VMware e servidores físicos para o site local

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-failback-azure-to-vmware.md)
- [Portal Clássico do Azure](site-recovery-failback-azure-to-vmware-classic.md)
- [Portal Clássico do Azure (Herdado)](site-recovery-failback-azure-to-vmware-classic-legacy.md)


Este artigo descreve como realizar o failback de máquinas virtuais do Azure para o site local. Siga as instruções neste artigo quando estiver pronto para realizar o failback de suas máquinas virtuais VMware ou de servidores físicos Windows/Linux após a realização do failover do site local para o Azure usando este [tutorial](site-recovery-vmware-to-azure-classic.md).



## <a name="overview"></a>Visão geral

Este diagrama mostra a arquitetura de failback para esse cenário.

Use essa arquitetura quando o servidor de processo é local e você está usando uma Rota Expressa.

![Diagrama da arquitetura da Rota Expressa](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Use essa arquitetura quando o servidor de processo está no Azure e você tem uma VPN ou uma conexão de Rota Expressa.

![Diagrama da arquitetura da VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

Para ver a lista completa de portas e o diagrama da arquitetura de failback, consulte a imagem abaixo

![Failover-Failback de todas as portas](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Veja a seguir como o failback funciona:

- Após a realização do failover para o Azure, execute o failback para seu site local por meio de alguns estágios:
    - **Estágio 1**: você protege novamente as VMs do Azure para que elas comecem a serem replicadas de volta nas VMs do VMware em execução no site local. 
    - **Estágio 2**: assim que as VMs do Azure estiverem sendo replicadas no site local, você executa um failover para realizar o failback no Azure.
    - **Estágio 3**: após realizar o failback de seus dados, você protege novamente as VMs locais para as quais você realizou o failback, para que comecem a serem replicadas no Azure.


### <a name="failback-to-the-original-or-alternate-location"></a>Failback para o local original ou para um local alternativo

Se você realizou o failover de uma VM do VMware, poderá realizar o failback para a mesma VM de origem, caso ela ainda exista no local. Nessa situação, apenas as alterações delta passarão por failback. Observe que:

- Se você realizou o failover de servidores físicos, o failback sempre ocorrerá para uma nova VM do VMware.
    - Antes de realizar failback em um computador físico, observe que:
        - A máquina física protegida voltará como uma máquina virtual após o failover do Azure para o VMware
        - Certifique-se de descobrir, pelo menos, um servidor de Destino Mestre juntamente com os hosts ESX/ESXi necessários para os quais você precisa fazer failback.
- Se você realizar o failback para a VM original, precisará do seguinte:
    - Se a VM for gerenciada por um servidor vCenter, o host do ESX de Destino Mestre deve ter acesso ao repositório de dados das máquinas virtuais.
    - Se a VM estiver em um host ESX, mas não for gerenciada pelo vCenter, o disco rígido da VM deverá estar em um repositório de dados acessível ao host do MT.
    - Se sua VM estiver em um host ESX e não usar o vCenter, complete a descoberta do host ESX do MT antes de protegê-la novamente. Isso se aplicará se você também estiver realizando o failback de servidores físicos.
    - Outra opção (se a VM local existir) é excluí-la antes de fazer um failback. Assim, o failback criará uma nova VM no mesmo host que o host ESX de destino mestre.
    
- Ao realizar o failback para um local alternativo, os dados serão recuperados no mesmo armazenamento de dados e no mesmo host ESX usados pelo servidor de destino mestre local. 


## <a name="prerequisites"></a>Pré-requisitos

- Você precisará de um ambiente VMware para realizar failbacks de VMs do VMware e servidores físicos. Não há suporte para a realização de failback para um servidor físico.
- Para realizar o failback você deve ter criado uma rede do Azure durante a configuração inicial da proteção. O failback precisa de uma conexão VPN ou de Rota Expressa da rede do Azure onde estão as VMs do Azure para o site local.
- Se as VMs para as quais você deseja realizar o failback forem gerenciadas por um servidor vCenter, você precisará das permissões necessárias para a descoberta das VMs nos servidores vCenter. [Leia mais](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
- Se houver instantâneos em uma VM, a nova proteção falhará. Você pode excluir os instantâneos ou os discos. 
- Antes de realizar failback, será necessário criar diversos componentes:
    - **Crie um servidor de processos no Azure**. Esse servidor de processos é uma VM do Azure que você precisará criar e manter em execução durante o failback. Você pode excluir a máquina após a conclusão do failback.
    - **Criar um servidor de destino mestre**: o servidor de destino mestre envia e recebe dados do failback. O servidor de gerenciamento criado no local tem um servidor de destino mestre instalado por padrão. No entanto, dependendo do volume do tráfego de failback, talvez seja necessário criar um servidor de destino mestre separado para o failback.
    - Se você quiser criar um servidor de destino mestre adicional em execução no Linux, será necessário configurar a VM do Linux antes de instalar o servidor de destino mestre, conforme descrito abaixo.
- O servidor de configuração é necessário localmente para fazer um failback. Durante o failback, a máquina virtual deve existir no banco de dados de servidor de configuração. Caso contrário, o failback não será bem-sucedido. Sendo assim, certifique-se de fazer o backup agendado regular de seu servidor. Em caso de desastre, você precisará restaurá-lo com o mesmo endereço IP para que o failback funcione.
- Verifique se você definiu a configuração disk.enableUUID=true nos Parâmetros de Configuração da VM de destino Mestre no VMware. Se essa linha não existir, adicione-a. Isso é necessário para fornecer um UUID consistente para o VMDK para que ele seja montado corretamente.
- **O servidor de destino mestre não pode ser armazenado com vMotioned**. Isso pode causar a falha do failback. A VM não será mostrada, pois os discos não serão disponibilizados para ela.

## <a name="failback-policy"></a>Política de failback
Para replicar de volta para local, você precisará de uma política de failback. Essa política é criada automaticamente quando você cria uma política de direção de encaminhamento. Observe que 

1. essa política é associada automaticamente ao servidor de configuração durante a criação.
2. essa política não é editável.
3. os valores definidos da política são (Limite de RPO = 15 min, Retenção de Ponto de Recuperação = 24 horas, Frequência de Instantâneo de Consistência do Aplicativo = 60 min) ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

## <a name="set-up-the-process-server-in-azure"></a>Configurar o servidor de processos no Azure

É necessário instalar um servidor de processo no Azure para que as VMs do Azure possam enviar os dados de volta para um servidor de destino mestre local. 

Se você protegeu os computadores como recursos clássicos (ou seja, a VM recuperada no Azure é uma VM clássica), você precisará de um servidor em processo clássico no Azure. Se você recuperou os computadores como um tipo de implantação do gerenciador de recursos, você precisará de um servidor em processo de um tipo de implantação do gerenciador de recursos. O tipo é selecionado pela rede virtual do Azure na qual o servidor em processo foi implantado.

1.  Em Cofre > Configurações > Infraestrutura do Site Recovery (no cabeçalho “Gerenciar”) > **Servidores de Configuração** (no cabeçalho “Para VMware e Máquinas Físicas”), selecione o servidor de configuração. Clique em “+ Servidor de processo”, realçado em amarelo na imagem de exemplo abaixo.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/add-processserver.png)

2. Opte por implantar o servidor em processo como “Implantar um servidor em processo de failback no Azure”.

3. Selecione a assinatura na qual você recuperou os computadores. 

4. Em seguida, selecione a rede do Azure que contém os computadores recuperados. O servidor em processo precisa estar na mesma rede para que as VMs recuperadas e o servidor em processo possam se comunicar.

5. Se você selecionou uma rede de *implantação clássica* , será necessário criar uma nova VM por meio da galeria do Azure e instalar o servidor em processo nela.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/add-classic.png)
    
    1. O nome da imagem é *Servidor em processo V2 do Microsoft Azure Site Recovery*. Verifique se você selecionou *Clássico* como o modelo de implantação.
    
        ![](./media/site-recovery-failback-azure-to-vmware-classic/templatename.png)
    
    2. Instalar o Servidor em processo de acordo com as etapas [fornecidas aqui](./site-recovery-vmware-to-azure-classicz.md#step-5-install-the-management-server)
    
6. Se você selecionar a rede *Azure Resource Manager* , será necessário fornecer as entradas a seguir para implantar o servidor.

    1. Grupo de Recursos no qual você deseja implantar o servidor
    
    2. Dê um nome ao servidor
    
    3. Dê uma senha ao nome de usuário para que você possa fazer logon
    
    4. Escolha a conta de armazenamento na qual você deseja implantar o servidor
    
    5. Escolha a Sub-rede específica e a Interface de Rede que será usada para conectá-la. Observação: você precisa criar sua própria [NIC](../virtual-network/virtual-networks-multiple-nics.md) (Adaptador de rede) e selecioná-la durante a implantação.
    
        ![](./media/site-recovery-failback-azure-to-vmware-classic/psinputsadd.png)
    
    6. Clique em OK. Isso disparará um trabalho que vai criar uma máquina virtual do tipo de implantação do Gerenciador de Recursos com a instalação do servidor em processo. É necessário executar a instalação na VM para registrar o servidor no servidor de configuração. É possível fazer isso seguindo [essas etapas](./site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server).

    7. Ocorrerá o disparo de um trabalho de implantação do servidor de processo.

7. No final, o servidor em processo deverá estar listado na página de servidores de configuração, na seção de servidores associados, sob a guia Servidores em Processo.
    ![](./media/site-recovery-failback-azure-to-vmware-new/pslistingincs.png)

        
    >[AZURE.NOTE] O servidor não ficará visível nas **Propriedades da VM**. Ele ficará visível apenas na guia **Servidores** , no servidor de gerenciamento no qual foi registrado. Pode demorar de 10 a 15 minutos para que o servidor de processo apareça.


## <a name="set-up-the-master-target-server-on-premises"></a>Configurar o servidor de destino mestre no local

O servidor de destino mestre recebe os dados do failback. Um servidor de destino mestre é instalado automaticamente no servidor de gerenciamento local, mas se você estiver realizando o failback de muitos dados, talvez seja necessário configurar um servidor de destino mestre adicional. Faça isso da seguinte forma:

>[AZURE.NOTE] Se você quiser instalar um servidor de destino mestre no Linux, siga as instruções do próximo procedimento.

1. Se você estiver instalando o servidor de destino mestre no Windows, abra a página Início Rápido da VM na qual você está instalando o servidor de destino mestre, e baixe o arquivo de instalação do Assistente de Instalação Unificada do Azure Site Recovery.
2. Execute a instalação e, em **Antes de começar**, escolha **Adicionar servidores de processo adicionais para escalar horizontalmente a implantação**.
3. Conclua o assistente da mesma forma que fez ao [configurar o servidor de gerenciamento](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server). Na página **Detalhes do Servidor de Configuração** , especifique o endereço IP deste servidor de destino mestre e uma senha para acessar a VM.

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>Configurar uma VM do Linux como o servidor de destino mestre
Para configurar o servidor de gerenciamento que executa o servidor de destino mestre como uma VM do Linux, será necessário instalar o sistema operacional mínimo CentOS 6.6, recuperar as IDs de SCSI de cada disco SCSI, instalar alguns pacotes adicionais e aplicar algumas alterações personalizadas.

#### <a name="install-centos-6.6"></a>Instalar o CentOS 6.6

1.  Instale o sistema operacional mínimo CentOS 6.6 na VM do servidor de gerenciamento. Mantenha o ISO em uma unidade de DVD e inicialize o sistema. Ignore o teste de mídia, escolha Português (Brasil) como idioma, escolha **Dispositivos de Armazenamento Básico**, verifique se o disco rígido não tem dados importantes e clique em **Sim**, descartando quaisquer dados. Insira o nome do host do servidor de gerenciamento e escolha o adaptador de rede do servidor.  Na caixa de diálogo **Sistema de Edição**, escolha **Conectar automaticamente** e adicione um endereço IP estático, a rede e as configurações de DNS. Especifique um fuso horário e uma senha raiz para acessar o servidor de gerenciamento. 
2.  Quando receber uma solicitação do tipo de instalação desejado, escolha **Criar Layout Personalizado** como a partição. Depois de clicar em **Avançar** select **Gratuito** e clique em Criar. Crie partições **/**, **/var/crash** e **/home** com Tipo **FS: ** **ext4**. Crie a partição de troca como **Tipo FS: troca**.
3.  Se algum dispositivo pré-existente for encontrado, uma mensagem de aviso será exibida. Clique em **Formatar** para formatar a unidade com as configurações de partição. Clique em **Gravar alteração no disco** para aplicar as alterações à partição.
4.  Escolha **Instalar carregador de inicialização** > **Avançar** para instalar o carregador de inicialização na partição raiz.
5.  Após a conclusão da instalação, clique em **Reiniciar**.


#### <a name="retrieve-the-scsi-ids"></a>Recuperar as IDs de SCSI

1. Após a instalação, recupere as IDs de SCSI de cada disco SCSI na VM. Para fazer isso, desligue a VM do servidor de gerenciamento; nas propriedades da VM no VMware, clique com o botão direito do mouse na entrada da VM > **Editar Configurações** > **Opções**.
2. Escolha **Avançado** > **Item geral** e clique em **Parâmetros de Configuração**. Essa opção será desativada quando a máquina estiver em execução. Para torná-la ativa, a máquina deve ser desligada.
3. Se a linha **disk.EnableUUID** existir, defina seu valor como **True** (diferencia maiúsculas de minúsculas). Se já estiver definida como True, cancele e teste o comando SCSI dentro do sistema operacional convidado após a inicialização. 
4. Se a linha não existir, clique em **Adicionar Linha** e adicione-a com o valor **True**. Não use aspas duplas.

#### <a name="install-additional-packages"></a>Instalar pacotes adicionais

Será necessário baixar e instalar alguns pacotes adicionais. 

1.  Verifique se o servidor de destino mestre está conectado à internet.
2.  Execute este comando para baixar e instalar 15 pacotes do repositório CentOS: **# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools**.
3.  Se as máquinas de origem que você está protegendo estiverem executando o sistema de arquivos Linux wit Reiser ou XFS no dispositivo de inicialização ou raiz, baixe e instale outros pacotes da seguinte maneira:

    - # <a name="cd-/usr/local"></a>cd /usr/local
    - # <a name="wget-[http://elrepo.org/linux/elrepo/el6/x86_64/rpms/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/rpms/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
    - # <a name="wget-[http://elrepo.org/linux/elrepo/el6/x86_64/rpms/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/rpms/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
    - # <a name="rpm-–ivh-kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm-reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm"></a>rpm –ivh kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm
    - # <a name="wget-[http://mirror.centos.org/centos/6.6/os/x86_64/packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)"></a>wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
    - # <a name="rpm-–ivh-xfsprogs-3.1.1-16.el6.x86_64.rpm"></a>rpm –ivh xfsprogs-3.1.1-16.el6.x86_64.rpm

#### <a name="apply-custom-changes"></a>Aplicar alterações personalizadas

Faça o seguinte para aplicar as alterações personalizadas após a conclusão das etapas pós-instalação e após a instalação dos pacotes:

1.  Copie o binário do Agente Unificado RHEL 6-64 para a VM. Execute este comando para descompactar o binário: **tar –zxvf <file name>**
2.  Execute esse comando para conceder permissões: **# chmod 755 ./ApplyCustomChanges.sh**
3.  Execute o script: **# ./ApplyCustomChanges.sh**. Você só deve executar o script uma vez. Reinicie o servidor após a execução bem-sucedida do script.


## <a name="run-the-failback"></a>Executar o failback

### <a name="reprotect-the-azure-vms"></a>Proteja novamente as VMs do Azure

1.  No Cofre > Itens replicados, selecione a VM na qual foi executado o failover e clique com o botão direito do mouse em **Proteger Novamente**. Também é possível clicar no computador e selecionar Proteger Novamente nos botões de comando.
2.  Na folha, você poderá ver que a direção da proteção “Azure para Local” já está marcada.
3.  Em **Servidor de Destino Mestre** e **Servidor de Processo**, escolha o servidor de destino mestre local e o servidor de processo da VM do Azure.
4.  Selecione o **Armazenamento de Dados** no qual você deseja recuperar os discos localmente. Essa opção é usada quando a VM local é excluída e novos discos precisam ser criados. Essa opção será ignorada se os discos já existirem, mas você ainda precisará especificar um valor. 
5.  A Unidade de Retenção é usada para interromper os pontos no tempo quando a VM foi replicada de volta para o local. Alguns dos critérios de uma unidade de retenção são mostrados abaixo, sem os quais a unidade não será listada para o servidor de destino mestre.
    a. O volume não deverá ser usado para qualquer outra finalidade (destino de replicação, etc.). b. O volume não deve estar no modo de bloqueio.
    c. O volume não deve ser o volume de cache. (A instalação MT não deve existir nesse volume. O volume de instalação personalizada PS+MT não está qualificado para o volume de retenção. O volume PS+MT instalado aqui é o volume de cache de MT). d. O tipo de sistema de arquivos do volume não deve ser FAT ou FAT32.
    e. A capacidade de volume deve ser diferente de zero.
    e. O volume de retenção padrão para o Windows é o volume R. 
    f. O volume de retenção padrão para o Linux é /mnt/retention.

6.  A política de failback será selecionada automaticamente.

7.  Depois de clicar em **OK** para iniciar a nova proteção, um trabalho começará a replicar a VM do Azure para o site local. Você pode acompanhar o andamento na guia **Trabalhos** .

Se você quiser recuperar para um local alternativo, escolha a unidade de retenção e o armazenamento de dados configurados para o servidor de destino mestre. Quando você realiza o failback para o site local, as VMs do VMware no plano de proteção de failback usarão o mesmo armazenamento de dados que o servidor de destino mestre. Se você quiser recuperar a VM réplica do Azure para a mesma VM local, a VM local já deve estar no mesmo armazenamento de dados que o servidor de destino mestre. Se não houver uma VM local, uma nova será criada durante a nova proteção.
    ![](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)



Também é possível proteger novamente em um nível do plano de recuperação. Caso você tenha um grupo de replicação, ele só poderá ser protegido novamente com um plano de recuperação. Com a nova proteção por meio de um plano de recuperação, será necessário fornecer os valores acima para cada computador protegido. 

>[AZURE.NOTE] Um grupo de replicação deve ser protegido novamente usando o mesmo destino Mestre. Se eles forem protegidos novamente usando um servidor de destino mestre diferente, um ponto no tempo comum não poderá ser usado para ele.

### <a name="run-a-failover-to-the-on-premises-site"></a>Executar um failover para o site local

Após a nova proteção da VM, é possível iniciar um failover do Azure para o local. 

1.  Na página Itens replicados, selecione a máquina virtual e clique com o botão direito do mouse para selecionar **Failover Não Planejado**.
2.  Em **Confirmar Failover** , verifique a direção do failover (para o Azure) e selecione o ponto de recuperação que você deseja usar para o failover (o mais recente, ou um que seja consistente com o aplicativo mais recente). O ponto consistente com o aplicativo seria atrás do último ponto no tempo e causará a perda de alguns dados.
3.  Durante o failover, a Recuperação de Site desligará as VMs do Azure. Depois de verificar se o failback foi concluído conforme o esperado, você pode verificar se as VMs do Azure foram desligadas conforme o esperado.

### <a name="reprotect-the-on-premises-site"></a>Proteger novamente o site local

Após a conclusão do failback, você precisará confirmar a máquina virtual para garantir que as VMs recuperadas no Azure sejam excluídas.

1. Clique com o botão direito do mouse no item protegido e clique em Confirmar. Será disparado um trabalho que removerá as máquinas virtuais anteriormente recuperadas no Azure.

Após a conclusão da confirmação, seus dados voltarão ao site local, mas não estarão protegidos. Para iniciar a replicação no Azure novamente, faça o seguinte:

1.  No Cofre > Configuração > Itens, replicados, selecione as VMs na quais foi executado o failback e clique em **Proteger Novamente**. 
2.  Forneça o valor do Servidor em processo que precisa ser usado para enviar dados de volta ao Azure.
3.  Clique em OK para iniciar o trabalho de nova proteção.

Quando a nova proteção for concluída, a VM será replicada de volta no Azure e você poderá executar um failover.


### <a name="common-issues-in-failback"></a>Problemas Comuns de failback

1. Se você executar a descoberta de vCenter de Usuário Somente Leitura e proteger as máquinas virtuais, ela terá êxito e o failover funcionará. No momento de Proteger Novamente, ele falhará pois os repositórios de dados não podem ser descobertos. Como sintoma, você não verá os armazenamentos de dados listados ao proteger novamente. Para resolver esse problema, você pode atualizar a credencial do vCenter com a conta apropriada que tenha permissões e repetir o trabalho. [Leia mais](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
2. Ao executar failback de uma VM do Linux e executá-la localmente, você verá que o pacote do Gerenciador de Rede é desinstalado do computador. Isso ocorre porque quando a VM é recuperada no Azure, o pacote do Gerenciador de Rede é removido.
3. Quando uma máquina virtual é configurada com um endereço IP Estático e o failover é feito para o Azure, o endereço IP é obtido por meio de DHCP. Quando você executa o failover de volta para o Local, a VM continua a usar o DHCP para obter o endereço IP. Você precisará fazer logon manualmente na máquina e, se necessário, definir o endereço IP de volta para o endereço Estático.
4. Se você estivesse usando a edição gratuita ESXi 5.5 ou a edição gratuita do 6 vSphere Hypervisor, o failover teria êxito, mas isso não acontecerá. Será necessário atualizar para a Licença de Avaliação para habilitar o failback.

## <a name="failing-back-with-expressroute"></a>Realizar o failback com a Rota Expressa

Você pode realizar o failback em uma conexão VPN ou pela Rota Expressa do Azure. Se você quiser usar a Rota Expressa, observe o seguinte:

- A Rota Expressa deve ser configurada na rede virtual do Azure para a qual as máquinas de origem passam por failover, e nas quais as VMs do Azure ficam após o failover.
- Os dados são replicados para uma conta de armazenamento do Azure em um ponto de extremidade público. Você deve configurar o emparelhamento público na Rota Expressa com o data center de destino para que a replicação da Recuperação de Site use a Rota Expressa.






<!--HONumber=Oct16_HO2-->


