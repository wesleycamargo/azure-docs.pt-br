---
title: Como utilizar o Proteja Novamente do Azure para um site local | Microsoft Docs
description: "Após o failover de máquinas virtuais no Azure, você pode iniciar um failback para trazer as máquinas virtuais de volta para o local. Aprenda as etapas para criar uma nova proteção antes de um failback."
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
ms.openlocfilehash: a655c7bf1ea5ca1439d4353df5067c0e07f2d49f
ms.lasthandoff: 04/25/2017


---
# <a name="reprotect-from-azure-to-an-on-premises-site"></a>Proteja Novamente do Azure para um site local

## <a name="overview"></a>Visão geral
Este artigo descreve como proteger novamente as máquinas virtuais do Azure para o site local. Quando achar que está pronto, siga as instruções neste artigo, para fazer o failback de suas máquinas virtuais VMware ou de servidores físicos Windows/Linux após a realização do failover do site local para o Azure usando [Replicar máquinas virtuais VMware e servidores físicos para o Azure com o Azure Site Recovery](site-recovery-failover.md).

Depois que o Proteja Novamente for concluído e da replicação de máquinas virtuais protegidas, você pode iniciar um failback nas máquinas virtuais para trazê-las ao site local.

Publique comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Para obter uma visão geral, assista a este vídeo sobre como fazer failover do Azure para um site local.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="prerequisites"></a>Pré-requisitos
A seguir estão as etapas de pré-requisito que você deve levar em conta ou realizar quando se preparar para proteger novamente.

* Se as máquinas virtuais para as quais você deseja realizar o failback forem gerenciadas por um servidor vCenter, você precisará das permissões necessárias para a descoberta das máquinas virtuais nos servidores vCenter. [Leia mais](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* Se houver instantâneos em uma máquina virtual local, o Proteja Novamente falhará. Você pode excluir os instantâneos antes de prosseguir para o Proteja Novamente.
* Antes de realizar o failback, será necessário criar dois componentes adicionais:
  * **Criar um servidor de processo**. O servidor de processo recebe dados da máquina virtual protegida no Azure e envia dados para o site local. Uma rede de baixa latência é necessária entre o servidor de processo e a máquina virtual protegida. Assim, você poderá ter um servidor de processo local se estiver usando uma conexão Azure ExpressRoute, ou um servidor de processo do Azure se estiver usando VPN.
  * **Criar um servidor de destino mestre**: o servidor de destino mestre envia e recebe dados do failback. O servidor de gerenciamento local criado tem um servidor de destino mestre instalado por padrão. No entanto, dependendo do volume do tráfego de failback, talvez seja necessário criar um servidor de destino mestre separado para o failback.
        * [Uma máquina virtual Linux precisa de um servidor de destino mestre do Linux](site-recovery-how-to-install-linux-master-target.md).
        * Uma máquina virtual Windows precisa de um servidor de destino mestre do Windows. Você pode usar o servidor de processo local e os computadores de servidor de destino mestre novamente.
* O servidor de configuração é necessário localmente para fazer um failback. Durante o failback, a máquina virtual deve existir no banco de dados do servidor de configuração. Caso contrário, o failback não será bem-sucedido. Faça o backup agendado regular de seu servidor. Em caso de desastre, você precisará restaurar o servidor com o mesmo endereço IP para que o failback funcione.
* Verifique se você definiu a configuração disk.EnableUUID=true nos parâmetros de configuração da máquina virtual de destino mestre no VMware. Se essa linha não existir, adicione-a. Essa configuração é necessária para fornecer um UUID consistente para o VMDK (disco de máquina virtual) para que ele monte corretamente.
* *Você não pode usar Storage vMotion no servidor de destino mestre*. Isso pode causar a falha do failback. A máquina virtual não será iniciada porque os discos não estarão disponíveis para ela. Para evitar isso, exclua os servidores de destino mestre de sua lista de vMotion.
* Você precisa adicionar uma nova unidade ao servidor de destino mestre. Essa unidade é chamada de uma unidade de retenção. Adicione um novo disco e formate a unidade.
* O destino mestre tem outros pré-requisitos listados em [Tarefas comuns para verificar em um destino mestre antes de proteger novamente](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server).


### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-connection-to-replicate-data-back-to-the-on-premises-site"></a>Por que preciso de uma VPN S2S ou uma conexão ExpressRoute para replicar dados de volta para o site local?
Quando a replicação do local para o Azure pode ser feita via Internet ou uma conexão ExpressRoute que tenha emparelhamento público, proteger novamente e failback exigem uma VPN S2S (Site a Site) para replicar os dados. A rede deve ser fornecida de modo que as máquinas virtuais que passaram por failover no Azure possam acessar (executar ping) o servidor de configuração local. Você também pode implantar um servidor de processo na rede do Azure da máquina virtual com failover. Esse servidor de processo também deve ser capaz de se comunicar com o servidor de configuração local.

### <a name="when-should-i-install-a-process-server-in-azure"></a>Quando instalar um servidor de processo no Azure?


As máquinas virtuais no Azure que você deseja proteger novamente enviam dados de replicação a um servidor de processo. A rede deve ser configurada para que as máquinas virtuais no Azure possam acessar o servidor de processo.

Você pode implantar um servidor de processo no Azure ou usar o servidor de processo existente que você usou durante o failover. O ponto importante a considerar é a latência para enviar os dados de máquinas virtuais no Azure para o servidor de processo.

Se você tiver uma conexão ExpressRoute configurada, um servidor de processo local poderá ser usado para enviar dados porque a latência entre a máquina virtual e o servidor de processo é baixa.

 ![Diagrama da arquitetura para ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



No entanto, se você tem apenas uma VPN S2S, é recomendável implantar o servidor de processo no Azure.

 ![Diagrama da arquitetura da VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


Lembre-se de que a replicação ocorrerá somente pela VPN S2S ou pelo emparelhamento privado da sua rede ExpressRoute. Certifique-se de que há largura de banda suficiente disponível através desse canal de rede.

Leia mais sobre como instalar um [servidor de processo do Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md).

> [!TIP]
> É sempre recomendável usar o Azure com base em servidor de processo durante o failback. O desempenho de replicação será maior se o servidor de processo estiver mais próximo da máquina virtual de replicação (a máquina no Azure com failover). No entanto, durante a prova de conceitos ou demonstrações, você pode usar o servidor de processo local junto com o ExpressRoute com emparelhamento privado para concluir a prova de conceito mais rapidamente.



### <a name="what-are-the-ports-to-be-open-on-different-components-so-that-reprotect-can-work"></a>Quais são as portas a serem abertas em diferentes componentes para que a nova proteção possa funcionar?

![Failover-failback de todas as portas](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

### <a name="which-master-target-server-should-be-used-for-reprotect"></a>O servidor de destino mestre deve ser usado para proteger novamente?
Um servidor de destino mestre local é necessário para receber dados do servidor de processo e, em seguida, gravar o VMDK da máquina virtual local. Se você estiver protegendo máquinas virtuais do Windows, precisará de um servidor de destino mestre do Windows. Você pode reutilizar o destino mestre e o servidor de processo local <!-- !todo component -->. Para máquinas virtuais Linux, você precisa configurar outro destino mestre Linux local.


Clique nos links a seguir para ler sobre como instalar um servidor de destino mestre:

* [Como instalar o servidor de destino mestre do Windows](site-recovery-vmware-to-azure.md#run-site-recovery-unified-setup)
* [Como instalar o servidor de destino mestre do Linux](site-recovery-how-to-install-linux-master-target.md)


#### <a name="common-things-to-check-after-completing-installation-of-the-master-target-server"></a>Coisas comuns a verificar após a conclusão da instalação do destino mestre

* Se a máquina virtual está presente no local no servidor vCenter, o servidor de destino mestre precisa acessar o VMDK da máquina virtual local. O acesso é necessário para gravar os dados replicados nos discos da máquina virtual. Verifique se o armazenamento de dados da máquina virtual local está montado no host de destino mestre com acesso de leitura/gravação.

* Se a máquina virtual não está presente no local no servidor vCenter, o serviço de recuperação do site precisa criar uma nova máquina virtual durante o Proteja Novamente. Essa máquina virtual será criada no host ESX em que você criou o destino mestre. Escolha o host ESX com cuidado para que a máquina virtual de failback seja criada no host desejado.

* *Você não pode usar Storage vMotion para o servidor de destino mestre*. Isso pode causar a falha do failback. A máquina virtual não será iniciada porque os discos não estarão disponíveis para ela.

> [!WARNING]
> No caso de um destino mestre sofrer uma nova proteção de postagem de vMotion de armazenamento, os discos das máquinas virtuais protegidas que estiverem anexados ao destino mestre serão migrados para o destino do vMotion. Se você tentar fazer failback após isso, desanexar do disco resultará em falha e a mensagem de erro informará que os discos não foram encontrados. Depois disso, ficará muito difícil localizar os discos em suas contas de armazenamento. Você precisará localizá-los manualmente e anexá-los à máquina virtual. Depois disso, a máquina virtual local poderá ser inicializada.

* Você precisa adicionar uma nova unidade ao servidor de destino mestre do Windows. Essa unidade é chamada de uma unidade de retenção. Adicione um novo disco e formate a unidade. A unidade de retenção é usada para interromper os pontos no tempo em que a máquina virtual replica para o site local. Abaixo vemos alguns dos critérios de uma unidade de retenção sem os quais a unidade não será listada para o servidor de destino mestre.

   * O volume não deve ser usado para qualquer outra finalidade, como um destino de replicação, etc.

   * O volume não deve estar no modo de bloqueio.

   * O volume não deve ser o volume de cache. A instalação do destino mestre não deve existir nesse volume. O volume de instalação personalizada do servidor de processo e do destino mestre não está qualificado para um volume de retenção. Quando o servidor de processo e o destino mestre são instalados em um volume, o volume é um cache de destino mestre.

   * O tipo de sistema de arquivos do volume não deve ser FAT ou FAT32.

   * A capacidade de volume deve ser diferente de zero.

   * O volume de retenção padrão para o Windows é o volume R.

   * O volume de retenção padrão para o Linux é /mnt/retention.
   
   > [!IMPORTANT]
   > Você precisa adicionar uma nova unidade caso esteja usando uma máquina CS+PS existente ou uma escala ou máquina PS+MT. A nova unidade deve atender aos requisitos acima. Se a unidade de retenção não estiver presente, nenhuma será listada no menu suspenso no portal. Depois de adicionar uma unidade ao destino mestre local, levará no máximo 15 minutos para a unidade se refletir na seleção no portal. Você também poderá atualizar o servidor de configuração se a unidade não aparecer depois de quinze minutos.



* Uma máquina virtual do Linux após failover precisa de um servidor de destino mestre do Linux. Uma máquina virtual do Windows após failover precisa de um servidor de destino mestre do Windows.

* Instalar ferramentas do VMware no servidor de destino mestre. Sem as ferramentas do VMware, os armazenamentos de dados no host de ESXi do destino mestre não podem ser detectados.

* Habilite o parâmetro disk. EnableUUID = true na máquina virtual de destino mestre usando as propriedades do vCenter. <!-- !todo Needs link. -->

* O destino mestre deve ter pelo menos um repositório VMFS (sistema de arquivos de máquina virtual) anexado. Se não houver nenhum, a entrada do **Repositório de Dados** na página de nova proteção estará vazia, e você não poderá continuar.

* O servidor de destino mestre não pode ter instantâneos nos discos. Se houver instantâneos, o processo Proteger Novamente/Failback falhará.

* O destino mestre não pode ter um controlador Paravirtual SCSI. O controlador só pode ser um controlador LSI Logic. Sem um controlador LSI Logic, a nova proteção falhará.

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>Etapas para proteger novamente

Antes de executar a proteção novamente, verifique se você instalou o [Servidor de processo](site-recovery-vmware-setup-azure-ps-resource-manager.md) no Azure e no Windows local ou no [destino mestre do Linux](site-recovery-how-to-install-linux-master-target.md).

> [!NOTE]
> Depois que uma máquina virtual é inicializada no Azure, levará algum tempo para que o agente seja registrado no servidor de configuração (no máximo 15 minutos). Durante esse tempo, o Proteja Novamente falhará e uma mensagem de erro indicará que o agente não está instalado. Aguarde alguns minutos e tente proteger novamente.



1. Em **Vault** > **Itens replicados**, clique com o botão direito do mouse na máquina virtual em que foi executado o failover e, em seguida, selecione **Proteger Novamente**. Também é possível clicar no computador e selecionar **Proteger Novamente** nos botões de comando.
2. Na folha, observe que a direção da proteção **Azure para Local** já está marcada.
3. Em **Servidor de Destino Mestre** e **Servidor de Processo**, selecione o servidor de destino mestre local e o servidor de processo.
4. Selecione o **Armazenamento de Dados** no qual você deseja recuperar os discos localmente. Essa opção é usada quando a máquina virtual no local é excluída e você precisa criar novos discos. Essa opção será ignorada se os discos já existirem, mas você ainda precisará especificar um valor.
5. Selecione a unidade de retenção.
6. A política de failback será selecionada automaticamente.
7. Depois de clicar em **OK** para iniciar a nova proteção, um trabalho começará a replicar a máquina virtual do Azure para o site local. Você pode acompanhar o andamento na guia **Trabalhos** .

Se você quiser recuperar para um local alternativo (quando a máquina virtual local for excluída), selecione a unidade de retenção e o repositório de dados configurados para o servidor de destino mestre. Quando você realiza o failback para o site local, as máquinas virtuais do VMware no plano de proteção de failback usarão o mesmo armazenamento de dados que o servidor de destino mestre. Uma nova máquina virtual será criada no vCenter.
Se você quiser recuperar a máquina virtual do Azure para uma máquina virtual local existente, os repositórios de dados da VM local deverão ser montados com acesso de leitura/gravação no host de ESXi do servidor de destino mestre.
    ![Proteger novamente a caixa de diálogo](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

Também é possível proteger novamente no nível de um plano de recuperação. Um grupo de replicação só pode ser protegido novamente com um plano de recuperação. Ao proteger novamente usando um plano de recuperação, você precisará fornecer os valores anteriores para cada computador protegido.

> [!NOTE]
> Um grupo de replicação deve ser protegido novamente usando o mesmo destino mestre. Se ele é protegido usando um servidor de destino mestre diferente, o servidor não pode fornecer um ponto comum no tempo.


Depois que o Proteja Novamente for bem-sucedido, a máquina virtual entrará no estado protegido.

## <a name="next-steps"></a>Próximas etapas

Depois que a máquina virtual entrou em um estado protegido, você poderá iniciar um failback. O failback desligará a máquina virtual no Azure e inicializará a máquina virtual local. Portanto, há um pequeno tempo de inatividade do aplicativo. Sendo assim, faça o failback quando seu aplicativo puder passar por um tempo de inatividade.

[Etapas para iniciar o failback da máquina virtual](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-fail-back)

## <a name="common-issues"></a>Problemas comuns

* Se você usou um modelo para criar as máquinas virtuais, verifique se cada máquina virtual tem um UUID exclusivo para os discos. Se o UUID da máquina virtual local estiver em conflito com o do destino mestre (porque ambos foram criados com base no mesmo modelo), a nova proteção falhará. Você precisa implantar outro destino mestre que não tenha sido criado com base no mesmo modelo.
* Se você executar a descoberta de vCenter de Usuário somente leitura e proteger as máquinas virtuais, a proteção terá êxito e o failover funcionará. Durante a nova proteção, o failover falha porque não é possível descobrir os armazenamentos de dados. Como sintoma, você não verá os armazenamentos de dados listados durante a nova proteção. Para resolver esse problema, você pode atualizar a credencial do vCenter com uma conta apropriada que tenha permissões e repetir o trabalho. Para obter mais informações, consulte [Replicar máquinas virtuais VMware e servidores físicos no Azure com o Azure Site Recovery](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* Ao executar failback de uma máquina virtual Linux e executá-la localmente, você verá que o pacote do Gerenciador de Rede foi desinstalado do computador. A desinstalação ocorre porque o pacote do Gerenciador de Rede é removido quando a máquina virtual é recuperada no Azure.
* Quando uma máquina virtual Linux é configurada com um endereço IP estático e o failover é feito para o Azure, o endereço IP é obtido do DHCP. Quando você executa o failover para o local, a máquina virtual continua a usar o DHCP para obter o endereço IP. Conecte-se manualmente à máquina e defina o endereço IP para um endereço estático, se necessário. Uma máquina virtual Windows pode adquirir novamente seu endereço IP estático.
* Se você usa a edição gratuita ESXi 5.5 ou a edição gratuita do 6 vSphere Hypervisor, o failover tem êxito, mas não acontece. Para permitir o failback, atualize para a licença de avaliação dos programas.
* Se você não conseguir acessar o servidor de configuração do servidor de processo, use Telnet para verificar a conectividade com o servidor de configuração na porta 443. Você também pode tentar executar ping no servidor de configuração do servidor de processo. O servidor de processo também deve ter uma pulsação quando for conectado ao servidor de configuração.
* Se você estiver tentando realizar failback para um vCenter alternativo, verifique se seu novo vCenter está descoberto e se o servidor de destino mestre também está descoberto. Um sintoma típico é que os armazenamentos de dados não ficam acessíveis ou visíveis na caixa de diálogo **Proteger Novamente**.
* Um servidor Windows Server 2008 R2 SP1 que é protegido, já que um servidor local físico não pode fazer failback do Azure para um site local.

