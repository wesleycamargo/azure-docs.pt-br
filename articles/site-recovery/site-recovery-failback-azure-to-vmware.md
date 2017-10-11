---
title: Failback de VMs VMware do Azure para o local | Microsoft Docs
description: "Saiba mais sobre como realizar o failback no site local após o failover de VMs do VMware e de servidores físicos no Azure."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 5a47337f-89a9-43e8-8fdc-7da373c0fb0f
ms.service: site-recovery
ms.devlang: na
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/27/2017
ms.author: ruturajd
ms.openlocfilehash: dde0bb6b4f6bc10afdd7d40adc6689d42b37de81
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2017
---
# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site"></a>Realizar o failback de máquinas virtuais VMware e servidores físicos para o site local


Este artigo descreve como realizar o failback de máquinas virtuais do Azure para o site local. Siga as instruções encontradas aqui quando estiver pronto para realizar o failback de suas máquinas virtuais VMware ou de servidores físicos Windows ou Linux após ter protegido novamente suas máquinas usando esta [referência](site-recovery-how-to-reprotect.md).

>[!NOTE]
>Se você estiver usando o Portal Clássico do Azure, consulte as instruções mencionadas [aqui](site-recovery-failback-azure-to-vmware-classic.md) para VMware aprimorado para arquitetura do Azure e [aqui](site-recovery-failback-azure-to-vmware-classic-legacy.md) para a arquitetura herdada

## <a name="overview"></a>Visão geral
Os diagramas desta seção mostram a arquitetura de failback para esse cenário.

Quando o Servidor de processo for local e você estiver usando uma conexão ExpressRoute do Azure, use esta arquitetura:

![Diagrama da arquitetura para ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Quando o Servidor de processo estiver no Azure e você tiver uma VPN ou uma conexão ExpressRoute, use esta arquitetura:

![Diagrama da arquitetura para VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

Para ver a lista completa de portas e o diagrama da arquitetura de failback, consulte a imagem abaixo:

![Lista de failover-failback para todas as portas](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Após a realização do failover para o Azure, execute o failback para seu site local em três estágios:

* **Estágio 1**: proteja novamente as VMs do Azure para que comecem a replicar de volta para as VMs VMware em execução em seu site local.
* **Estágio 2**: após a replicação das VMs do Azure para seu site local, execute um failover para realizar o failback do Azure.
* **Estágio 3**: após realizar o failback de seus dados, você protege novamente as VMs locais para as quais você realizou o failback, para que comecem a serem replicadas no Azure.

### <a name="fail-back-to-the-original-location-or-an-alternate-location"></a>Failback para o local original ou um local alternativo
Depois de realizar o failover de uma VM VMware, você pode fazer o failback para a mesma VM de origem, caso ela ainda exista no local. Neste cenário, apenas os deltas passarão por failback.

Se você realizou o failover de servidores físicos, o failback sempre ocorrerá para uma nova VM VMware. Antes de realizar failback em uma máquina física, observe que:
* Uma máquina física protegida voltará como uma máquina virtual após a realização do failover do Azure para o VMware. Se estiver protegido e passar por um failover no Azure, um computador físico com Windows Server 2008 R2 SP1 não pode passar por um failback. Um computador Windows Server 2008 R2 SP1 que tenha sido iniciado como uma máquina virtual local conseguirá realizar um failback.
* Certifique-se de descobrir pelo menos um servidor de destino mestre juntamente com os hosts ESX/ESXi cujo failback você precisa realizar.

Se realizar o failback para a VM original, você vai precisar do seguinte:

* Se a VM for gerenciada por um servidor vCenter, o host do ESX de destino mestre deve ter acesso ao armazenamento de dados das máquinas virtuais.
* Se a VM estiver em um host ESX, mas não for gerenciada pelo vCenter, o disco rígido da VM deverá estar em um armazenamento de dados que seja acessível ao host do MT.
* Se sua VM estiver em um host ESX e não usar o vCenter, complete a descoberta do host ESX do MT antes de protegê-la novamente. Isso se aplicará se você também estiver realizando o failback de servidores físicos.
* Outra opção (se a VM local existir) é excluí-la antes de fazer um failback. O failback vai criar uma nova VM no mesmo host que o host ESX de destino mestre.

Ao realizar o failback para um local alternativo, os dados são recuperados no mesmo armazenamento de dados e no mesmo host ESX usados pelo servidor de destino mestre local.

## <a name="prerequisites"></a>Pré-requisitos
* Para realizar failbacks de VMs VMware e servidores físicos, você vai precisar de um ambiente VMware. Não há suporte para a realização de failback para um servidor físico.
* Para realizar o failback, você precisa ter criado uma rede do Azure durante a configuração inicial da proteção. O failback precisa de uma conexão VPN ou de ExpressRoute da rede do Azure onde estão as VMs do Azure para o site local.
* Se as VMs para as quais você deseja realizar o failback forem gerenciadas por um servidor vCenter, verifique se você tem as permissões necessárias para a descoberta das VMs nos servidores vCenter. Para obter mais informações, consulte [Replicar máquinas virtuais VMware e servidores físicos no Azure com o Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).
* Se houver instantâneos em uma VM, a nova proteção vai falhar. Você pode excluir os instantâneos ou os discos.
* Antes de realizar o failback, crie estes componentes:
  * **Crie um Servidor de processos no Azure**. Esse componente é uma VM do Azure que você cria e mantém em execução durante o failback. Você pode excluir a VM após a conclusão do failback.
  * **Criar um servidor de destino mestre**: o servidor de destino mestre envia e recebe dados do failback. O servidor de gerenciamento que você criou no local tem um servidor de destino mestre que é instalado por padrão. No entanto, dependendo do volume do tráfego de failback, talvez seja necessário criar um servidor de destino mestre separado para o failback.
  * Para criar um servidor de destino mestre adicional que seja executado no Linux, configure a VM do Linux antes de instalar o servidor de destino mestre, conforme descrito adiante.
* O servidor de configuração é necessário localmente para fazer um failback. Durante o failback, a máquina virtual deve existir no banco de dados do servidor de configuração. Se o banco de dados do servidor de configuração não contiver VM, o failback não será bem-sucedido. Sendo assim, certifique-se de fazer backups agendados regulares de seu servidor. Em caso de desastre, você precisa restaurá-lo com o mesmo endereço IP para que o failback funcione.
* Defina a configuração disk.enableUUID=true nos **Parâmetros de Configuração** da VM de destino mestre no VMware. Se essa linha não existir, adicione-a. Essa configuração é necessária para fornecer um identificador universalmente exclusivo (UUID) consistente para que o arquivo de disco da máquina virtual (VMDK) seja montado corretamente.
* Fique atento à condição "Servidor de destino mestre não pode ser de armazenamento vMotion", que pode causar uma falha de failback. Não é possível criar a VM porque os discos não ficam disponíveis para ela.
* Adicione uma unidade, chamada de unidade de retenção, no servidor de destino mestre. Adicione um disco e formate a unidade.

## <a name="failback-policy"></a>Política de failback
Para replicar de volta para local, você precisa de uma política de failback. Essa política é criada automaticamente quando você cria uma política de direção de avanço, e é automaticamente associada ao servidor de configuração. Ela não é editável. A política tem as seguintes configurações de replicação:

* Limite RPO = 15 minutos
* Retenção de ponto de recuperação = 24 horas
* Frequência de instantâneos consistentes com aplicativos = 60 minutos

 ![Configurações de replicação da política de failback](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

## <a name="set-up-a-process-server-in-azure"></a>Crie um Servidor de processo no Azure
Instale um Servidor de processo no Azure para que as VMs do Azure possam enviar os dados de volta para o servidor de destino mestre local.

Se você protegeu suas máquinas virtuais como recursos clássicos (ou seja, a VM recuperada no Azure é uma VM que foi criada usando o modelo de implantação clássico), você precisará de um Servidor de processo no Azure. Se você tiver recuperado as VMs com o Azure Resource Manager como tipo de implantação, o Servidor de processo deve ter o Resource Manager como tipo de implantação. O tipo de implantação é selecionado pela rede virtual do Azure em que o Servidor de processo foi implantado.

1. Em **Cofre** > **Configurações** > **Infraestrutura do Site Recovery** (em **Gerenciar**) > **Servidores de Configuração** (em **Para VMware e Máquinas Físicas**), selecione o servidor de configuração.
2. Clique em **Servidor de processo**.

  ![Botão Servidor de processo](./media/site-recovery-failback-azure-to-vmware-classic/add-processserver.png)
3. Opte por implantar o Servidor de processo como **Implantar um Servidor de processo de failback no Azure**.
4. Selecione a assinatura para a qual você recuperou as VMs.
5. Selecione a rede do Azure para a qual você recuperou as VMs. O Servidor de processo precisa estar na mesma rede para que as VMs recuperadas e o Servidor de processo possam se comunicar.
6. Se você tiver selecionado um *modelo de implantação clássico* de rede, crie uma máquina virtual por meio do Azure Marketplace e, em seguida, instale o Servidor de processo nela.

 ![A janela “Adicionar Servidor de processo”](./media/site-recovery-failback-azure-to-vmware-classic/add-classic.png)

 Como você está criando o Servidor de processo, preste atenção ao seguinte:
 * O nome da imagem é *Servidor de processo V2 do Microsoft Azure Site Recovery*. Selecione **Clássico** como o modelo de implantação.

       ![Select "Classic" as the Process Server deployment model](./media/site-recovery-failback-azure-to-vmware-classic/templatename.png)
 * Instale o Servidor de processo de acordo com as instruções em [Replicar máquinas virtuais VMware e servidores físicos no Azure com o Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).
7. Se você selecionar a rede *Resource Manager* do Azure, implante o Servidor de processo fornecendo as seguintes informações:

  * O nome do grupo de recursos no qual você deseja implantar o servidor
  * O nome do servidor
  * O nome de usuário e a senha para entrar no servidor
  * A conta de armazenamento na qual você deseja implantar o servidor
  * A sub-rede e a interface de rede à qual você deseja conectá-la
   >[!NOTE]
   >Você precisa criar seu próprio [adaptador de rede](../virtual-network/virtual-networks-multiple-nics.md) (NIC) e selecioná-lo durante a implantação do Servidor de processo.

    ![Insira as informações na caixa de diálogo "Adicionar Servidor de processo"](./media/site-recovery-failback-azure-to-vmware-classic/psinputsadd.png)

8. Clique em **OK**. Esta ação dispara um trabalho que cria uma máquina virtual do tipo de implantação do Gerenciador de Recursos durante a instalação do Servidor de processo. Para registrar o servidor no servidor de configuração, execute a instalação dentro da VM, seguindo as instruções em [Replicar máquinas virtuais VMware e servidores físicos no Azure com o Azure Site Recovery](site-recovery-vmware-to-azure-classic.md). Um trabalho de implantação do Servidor de processo também será disparado.

  O Servidor de processo está listado na guia **Servidores de configuração** > **Servidores associados** > **Servidores de processo**.

    ![](./media/site-recovery-failback-azure-to-vmware-new/pslistingincs.png)

    > [!NOTE]
    > O Servidor de processo não está visível em **Propriedades da VM**. Ele só está visível na guia **Servidores** no servidor de gerenciamento em que está registrado. O Servidor de processo pode demorar de 10 a 15 minutos para aparecer.


## <a name="set-up-the-master-target-server-on-premises"></a>Configurar o servidor de destino mestre no local
O servidor de destino mestre recebe os dados do failback. O servidor é automaticamente instalado no servidor de gerenciamento local, mas se você estiver realizando o failback de um volume muito grande de dados, talvez seja necessário configurar um servidor de destino mestre adicional. Para configurar um servidor de destino mestre local, faça o seguinte:

> [!NOTE]
> Para configurar um servidor de destino mestre no Linux, vá para o próximo procedimento. Use apenas o sistema operacional mínimo CentOS 6.6 como o SO de destino mestre.

1. Se você estiver configurando o servidor de destino mestre no Windows, abra a página de início rápido da VM em que você está instalando o servidor de destino mestre.
2. Baixe o arquivo de instalação do assistente de Instalação Unificada do Azure Site Recovery.
3. Execute a instalação e, em **Antes de começar**, escolha **Adicionar Servidores de processo adicionais para escalar horizontalmente a implantação**.
4. Conclua o assistente da mesma forma que fez ao [configurar o servidor de gerenciamento](site-recovery-vmware-to-azure-classic.md). Na página **Detalhes do Servidor de Configuração** , especifique o endereço IP do servidor de destino mestre e insira uma senha para acessar a VM.

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>Configurar uma VM do Linux como o servidor de destino mestre
Para configurar o servidor de gerenciamento executando o servidor de destino mestre como uma VM do Linux, instale o sistema operacional mínimo CentOS 6.6. Em seguida, recupere as IDs de SCSI para cada disco rígido SCSI, instale alguns pacotes adicionais e aplique algumas alterações personalizadas.

#### <a name="install-centos-66"></a>Instalar o CentOS 6.6

1. Instale o sistema operacional mínimo CentOS 6.6 na VM do servidor de gerenciamento. Mantenha o ISO em uma unidade de DVD e inicialize o sistema. Ignore o teste de mídia. Escolha **Português (Brasil)** como idioma, escolha **Dispositivos de Armazenamento Básico**, verifique se o disco rígido não tem dados importantes, clique em **Sim** e descarte quaisquer dados. Insira o nome do host do servidor de gerenciamento e escolha o adaptador de rede do servidor.  Na caixa de diálogo **Sistema de Edição**, escolha **Conectar automaticamente** e, em seguida, adicione um endereço IP estático, a rede e as configurações de DNS. Especifique um fuso horário. Para acessar o servidor de gerenciamento, digite a senha raiz.
2. Quando receber uma solicitação do tipo de instalação desejado, escolha **Criar Layout Personalizado** como a partição. Clique em **Avançar**. Selecione **Gratuito** e, em seguida, clique em **Criar**. Crie partições **/**, **/var/crash** e **/home** com Tipo **FS:**  **ext4**. Crie a partição de troca como **Tipo FS: troca**.
3. Se algum dispositivo pré-existente for encontrado, uma mensagem de aviso é exibida. Clique em **Formatar** para formatar a unidade com as configurações de partição. Clique em **Gravar alteração no disco** para aplicar as alterações à partição.
4. Escolha **Instalar carregador de inicialização** > **Avançar** para instalar o carregador de inicialização na partição raiz.
5. Quando a instalação estiver concluída, clique em **Reiniciar**.

#### <a name="retrieve-the-scsi-ids"></a>Recuperar as IDs de SCSI

1. Após a instalação, recupere as IDs de SCSI de cada disco rígido SCSI na VM. Para fazer isso, desligue a VM do servidor de gerenciamento. Nas propriedades da VM no VMware, clique com o botão direito do mouse na entrada da VM > **Editar Configurações** > **Opções**.
2. Escolha **Avançado** > **Item geral** e, em seguida, clique em **Parâmetros de Configuração**. Essa opção ficará indisponível enquanto a máquina estiver em execução. Para a opção fique disponível, a máquina deve ser desligada.
3. Faça uma das opções a seguir:
 * Se a linha **disk.EnableUUID** existir, defina o valor como **True** (diferencia maiúsculas de minúsculas). Se o valor já estiver definido como True, você pode cancelar e testar o comando SCSI dentro do sistema operacional convidado após a inicialização.
 * Se a linha **disk.EnableUUID**não existir, clique em **Adicionar Linha** e, em seguida, adicione-a com o valor **True**. Não use aspas duplas.

#### <a name="install-additional-packages"></a>Instalar pacotes adicionais
Baixe e instale os pacotes adicionais.

1. Verifique se o servidor de destino mestre está conectado à internet.
2. Para baixar e instalar 15 pacotes do repositório CentOS, execute este comando: `# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools`.
3. Se as máquinas de origem que você está protegendo estiverem executando o Linux com um sistema de arquivos Reiser ou XFS no dispositivo de inicialização ou raiz, baixe e instale outros pacotes da seguinte maneira:

   * \# cd /usr/local
   * \# wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
   * \# wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
   * \# rpm –ivh kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm
   * \# wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
   * \# rpm –ivh xfsprogs-3.1.1-16.el6.x86_64.rpm
   * \# yum install device-mapper-multipath (necessário para habilitar pacotes de vários caminhos no servidor de destino mestre)

#### <a name="apply-custom-changes"></a>Aplicar alterações personalizadas
Após concluir das etapas pós-instalação e instalar os pacotes, faça o seguinte para aplicar as alterações personalizadas:

1. Copie o binário do Agente Unificado RHEL 6-64 para a VM. Para descompactar o binário, execute este comando: `tar –zxvf <file name>`.
2. Para conceder permissões, execute este comando: `# chmod 755 ./ApplyCustomChanges.sh`.
3. Execute o seguinte script: `# ./ApplyCustomChanges.sh`. Execute apenas uma vez. Após a execução com êxito, reinicie o servidor.

## <a name="run-the-failback"></a>Executar o failback
### <a name="reprotect-the-azure-vms"></a>Proteja novamente as VMs do Azure
1. Em **Cofre**, em **Itens replicados**, clique com o botão direito na VM em que foi executado o failover e, em seguida, selecione **Proteger Novamente**.
2. Na folha, você poderá ver que a direção da proteção **Azure para Local** já está marcada.
3. Em **Servidor de Destino Mestre** e **Servidor de Processo**, escolha o servidor de destino mestre local e o Servidor de processo da VM do Azure.
4. Selecione o armazenamento de dados no qual você deseja recuperar os discos localmente. Essa opção é usada quando a VM local é excluída e novos discos precisam ser criados. Ignorada a opção se os discos já existirem, mas você ainda precisará especificar um valor.
5. Use uma unidade de retenção para interromper os pontos no tempo quando a VM for replicada de volta para o local. Alguns critérios de uma unidade de retenção estão listados aqui. Sem esses critérios, a unidade não está listada para o servidor de destino mestre.

  * O volume não deve estar em uso para nenhuma outra finalidade (destino de replicação, entre outros).
  * O volume não deve estar no modo de bloqueio.
  * O volume não deve ser o volume de cache. (A instalação do destino mestre não deve existir nesse volume. O volume de instalação personalizada do Servidor de Processo mais o destino mestre não está qualificado para o volume de retenção. Aqui, o volume do servidor de processo instalado mais o destino mestre é o volume de cache de destino mestre.)
  * O tipo de sistema de arquivos do volume não deve ser FAT ou FAT32.
  * A capacidade de volume deve ser diferente de zero.
  * O volume de retenção padrão para o Windows é o volume R.
  * O volume de retenção padrão para o Linux é /mnt/retention.

6. A politica de failback é selecionada automaticamente.
7. Depois de clicar em **OK** para iniciar a nova proteção, um trabalho começará a replicar a VM do Azure para o site local. Você pode acompanhar o andamento na guia **Trabalhos** .

Se quiser recuperar para um local alternativo, escolha a unidade de retenção e o armazenamento de dados que estão configurados para o servidor de destino mestre. Quando você realiza o failback para o site local, as VMs do VMware no plano de proteção de failback usam o mesmo armazenamento de dados que o servidor de destino mestre. Se você quiser recuperar a VM réplica do Azure para a mesma VM local, esta já deve estar no mesmo armazenamento de dados que o servidor de destino mestre. Se não houver uma VM local, uma nova será criada durante a nova proteção.

![Selecione "Azure para local" no menu suspenso](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

Também é possível proteger novamente em um nível do plano de recuperação. Caso você tenha um grupo de replicação, ele só poderá ser protegido novamente com o uso de um plano de recuperação. Ao proteger novamente usando um plano de recuperação, use os valores anteriores para cada computador protegido.

> [!NOTE]
> Grupos de replicação devem ser protegidos novamente com o mesmo destino mestre. Se forem protegidos novamente com um servidor de destino mestre diferente, não será possível determinar um ponto comum no tempo para eles.

### <a name="run-a-failover-to-the-on-premises-site"></a>Executar um failover para o site local
Após a nova proteção da VM, é possível iniciar um failover do Azure para o local.

1. Na página Itens replicados, clique com o botão direito na máquina virtual e, em seguida, selecione **Failover não planejado**.
2. Em **Confirmar Failover** , verifique a direção do failover (para o Azure) e, em seguida, selecione o ponto de recuperação que você deseja usar para o failover (o mais recente, ou um que seja consistente com o aplicativo mais recente). Um ponto de recuperação consistente com o aplicativo ocorre antes do ponto mais recente no tempo, e vai causar perda de dados.
3. Durante o failover, o Site Recovery desligará as VMs do Azure. Depois de verificar se o failback foi concluído conforme o esperado, você pode verificar se as VMs do Azure foram desligadas conforme o esperado.

### <a name="reprotect-the-on-premises-site"></a>Proteger novamente o site local
Após a conclusão do failback, você precisará confirmar a máquina virtual para garantir que as VMs recuperadas no Azure sejam excluídas. Para fazer isso, clique com o botão direito no item protegido e, em seguida, clique em **Confirmar**. Essa ação dispara um trabalho que remove as máquinas virtuais recuperadas anteriormente no Azure.

Após a conclusão da confirmação, seus dados voltam ao site local, mas não estarão protegidos. Para iniciar a replicação no Azure novamente, faça o seguinte:

1. Em **Cofre**, em **Configuração** > **Itens replicados**, selecione as VMs na quais foi executado o failback e clique em **Proteger novamente**.
2. Forneça o valor do Servidor de processo que precisa ser usado para enviar dados de volta ao Azure.
3. Clique em **OK**.

Após a conclusão da nova proteção, a VM será replicada de volta no Azure e você poderá executar um failover.

### <a name="resolve-common-failback-issues"></a>Resolver problemas comuns de failback
* Se você executar a descoberta de vCenter de Usuário somente leitura e proteger as máquinas virtuais, ela terá êxito e o failover funcionará. Durante a nova proteção, o failover falha porque não é possível descobrir os armazenamentos de dados. Como sintoma, você não verá os armazenamentos de dados listados durante a nova proteção. Para resolver esse problema, você pode atualizar a credencial do vCenter com uma conta apropriada que tenha permissões e repetir o trabalho. Para obter mais informações, consulte [Replicar máquinas virtuais VMware e servidores físicos no Azure com o Azure Site Recovery](site-recovery-vmware-to-azure-classic.md)
* Ao executar failback de uma VM do Linux e executá-la localmente, você verá que o pacote do Gerenciador de Rede foi desinstalado do computador. A desinstalação ocorre porque o pacote do Gerenciador de Rede é removido quando a VM é recuperada no Azure.
* Quando uma máquina virtual é configurada com um endereço IP estático e o failover é feito para o Azure, o endereço IP é obtido por meio de DHCP. Quando você executa o failover de volta para o local, a VM continua a usar o DHCP para obter o endereço IP. Conecte-se manualmente à máquina e defina o endereço IP para um endereço estático, se necessário.
* Se você estiver usando a edição gratuita ESXi 5.5 ou a edição gratuita do 6 vSphere Hypervisor, o failover terá êxito, mas o failback, não. Para permitir o failback, atualize para a licença de avaliação dos programas.
* Se você não conseguir acessar o servidor de configuração do Servidor de processo, verifique a conectividade com o servidor de configuração por Telnet à máquina do servidor de configuração na porta 443. Você também pode tentar executar ping no servidor de configuração da máquina do Servidor de processo. O Servidor de processo também deve ter uma pulsação quando for conectado ao servidor de configuração.
* Se você estiver tentando realizar failback para um vCenter alternativo, verifique se seu novo vCenter está descoberto e se o servidor de destino mestre também está descoberto. Um sintoma típico é que os armazenamentos de dados não ficam acessíveis ou visíveis na caixa de diálogo **Proteger Novamente**.
* Um computador WS2008R2SP1 que esteja protegido como um computador local físico não pode passar por failback do Azure para local.

## <a name="fail-back-with-expressroute"></a>Failback com o ExpressRoute
Você pode realizar o failback em uma conexão VPN ou usando uma conexão ExpressRoute. Se você quiser usar uma conexão ExpressRoute, observe o seguinte:

* A conexão ExpressRoute deve ser configurada na rede virtual do Azure em que as máquinas de origem realizam failover e onde as VMs do Azure ficam após o failover.
* Os dados são replicados para uma conta de armazenamento do Azure em um ponto de extremidade público. Para usar uma conexão ExpressRoute, configure o emparelhamento público no ExpressRoute com o data center de destino para replicação do Site Recovery.
