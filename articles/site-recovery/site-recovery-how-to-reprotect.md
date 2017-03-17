---
title: Como utilizar o Proteja Novamente do Azure para o local | Microsoft Docs
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
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: f2da9c4b02f0bb8a93347c05be358516f39e2df0
ms.lasthandoff: 03/09/2017


---
# <a name="reprotect-from-azure-to-on-premises"></a>Proteja Novamente do Azure para o local

## <a name="overview"></a>Visão geral
Este artigo descreve como proteger novamente as máquinas virtuais do Azure para o site local. Siga as instruções neste artigo quando estiver pronto para realizar o failback de suas máquinas virtuais VMware ou de servidores físicos Windows/Linux após a realização do failover do site local para o Azure usando este [tutorial](site-recovery-failover.md).

Depois que a nova proteção for concluída e da replicação de máquinas virtuais protegidas, você pode iniciar um failback na VM para trazê-las ao local.

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Para obter uma visão geral em vídeo, você também pode usar o vídeo aqui.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

leia sobre o processo completo de failback aqui.

## <a name="pre-requisites"></a>Pré-requisitos
A seguir, temos algumas etapas de pré-requisito que você deve realizar ou considerar ao se preparar para o Proteja Novamente.

* Se as VMs para as quais você deseja realizar o failback forem gerenciadas por um servidor vCenter, você precisará das permissões necessárias para a descoberta das VMs nos servidores vCenter. [Leia mais](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* Se houver instantâneos em uma VM local, o Proteja Novamente falhará. Você pode excluir os instantâneos antes de prosseguir para o Proteja Novamente.
* Antes de realizar o failback, será necessário criar dois componentes adicionais:
  * **Criar um servidor de processo**. O servidor de processo é usado para receber os dados da VM protegida no Azure e enviar os dados ao local. Isso exige que ele esteja em uma rede de baixa latência entre o servidor de processo e a VM protegida. Portanto, o servidor de processo poderá ser local (se você estiver usando uma conexão de rota expressa) ou no Azure (se você estiver usando uma VPN).
  * **Criar um servidor de destino mestre**: o servidor de destino mestre envia e recebe dados do failback. O servidor de gerenciamento criado no local tem um servidor de destino mestre instalado por padrão. No entanto, dependendo do volume do tráfego de failback, talvez seja necessário criar um servidor de destino mestre separado para o failback.
        * [Uma VM do Linux precisa de um servidor de destino mestre do Linux](site-recovery-how-to-install-linux-master-target.md).
        * Uma VM do Windows precisa de um destino mestre do Windows. Você pode reutilizar o computador PS+MT local.
* O servidor de configuração é necessário localmente para fazer um failback. Durante o failback, a máquina virtual deve existir no banco de dados de servidor de configuração. Caso contrário, o failback não será bem-sucedido. Sendo assim, certifique-se de fazer o backup agendado regular de seu servidor. Em caso de desastre, você precisará restaurá-lo com o mesmo endereço IP para que o failback funcione.
* Verifique se você definiu a configuração disk.enableUUID=true nos Parâmetros de Configuração da VM de destino Mestre no VMware. Se essa linha não existir, adicione-a. Isso é necessário para fornecer um UUID consistente para o VMDK para que ele seja montado corretamente.
* **O servidor de destino mestre não pode ser armazenado com vMotioned**. Isso pode causar a falha do failback. A VM não será mostrada, pois os discos não serão disponibilizados para ela.
* Você precisa de uma nova unidade adicionada ao servidor de destino Mestre. Essa unidade é chamada de uma unidade de retenção. Adicione um novo disco e formate a unidade.
* O Destino Mestre tem outros pré-requisitos listados em [Tarefas comuns para verificar em um Destino Mestre antes de proteger novamente](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-master-target).


### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-to-replicate-data-back-to-on-premises"></a>Por que preciso de uma VPN S2S ou um ExpressRoute para replicar dados de volta para o local?
Quando a replicação do local para o Azure pode ser feita via Internet ou ExpressRoute com emparelhamento público, proteger novamente e failback exigem uma VPN S2S configurada para replicar os dados. **A rede deve ser fornecida de modo que as VMs que passaram por failover no Azure possam acessar (executar ping) o servidor de configuração local**. Você também pode estar implantando um servidor de processo na rede do Azure da VM que passou por failover – este servidor de processo também deve ser capaz de se comunicar com o servidor de configuração local.

### <a name="when-should-i-install-a-process-server-in-azure"></a>Quando instalar um servidor de Processo no Azure?


As VMs do Azure que você deseja proteger novamente enviam os dados de replicação para um servidor de processo. A rede deve ser configurada de modo que o servidor de processo possa ser acessado pela VM do Azure.

Você pode implantar um servidor de processo no Azure ou usar o servidor de processo existente que você usou durante o failover. O ponto importante a considerar é a latência para enviar os dados de VMs do Azure para o servidor de processo.

* Se você tiver uma rota expressa configurada, um PS local poderá ser usado para enviar os dados. Isso ocorre porque a latência entre a VM e o PS seria baixa.

    ![Diagrama da arquitetura do ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



* No entanto, se você tem apenas uma VPN S2S, é recomendável implantar o servidor de processo no Azure.

    ![Diagrama da arquitetura da VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


Lembre-se de que a replicação ocorrerá somente pela VPN S2S ou pelo emparelhamento privado da sua rede de rota expressa. Certifique-se de que há largura de banda suficiente disponível através desse canal de rede.

Leia mais sobre como instalar um [Servidor de Processo do Azure aqui](site-recovery-vmware-setup-azure-ps-resource-manager.md).

### <a name="what-are-the-different-ports-to-be-open-on-different-components-so-that-reprotect-can-work"></a>Quais são as diferentes portas a serem abertas em diferentes componentes para que a nova proteção possa funcionar?

![Failover-Failback de todas as portas](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

### <a name="which-master-target-server-to-use-for-reprotect"></a>Qual servidor de Destino Mestre deve ser usado para proteger novamente?
Um servidor de destino mestre é necessário localmente para receber os dados do servidor de processo e, em seguida, gravar o VMDK da VM local. Se você estiver protegendo máquinas virtuais do Windows, será necessário um servidor de destino mestre Windows, e aqui você poderá reutilizar o PS + MT <!-- !todo component --> local. Para VMs do Linux, você precisará configurar um destino mestre adicional local do Linux.


Clique nos links a seguir para ler as etapas sobre como instalar um servidor de Destino Mestre.

* [Como instalar o servidor de destino mestre do Windows](site-recovery-vmware-to-azure.md#run-site-recovery-unified-setup)
* [Como instalar o servidor de destino mestre do Linux](site-recovery-how-to-install-linux-master-target.md)


#### <a name="common-things-to-check-after-completing-installation-of-master-target"></a>Coisas comuns a verificar após a conclusão da instalação do destino mestre

* Se a VM está presente localmente no servidor vCenter, o servidor de destino mestre precisa acessar o VMDK da VM local. Isso serve para gravar os dados replicados nos discos da VM. Para isso, você precisa garantir que o **repositório de dados da VM local está montado no host do MT com acesso de leitura/gravação**.

* Se a VM não está presente localmente no servidor vCenter, uma nova VM deve ser criada durante o Proteja Novamente. Essa VM será criada no host ESX no qual você cria a MT. Portanto, escolha o host ESX com cuidado para que a VM de failback seja criada no host desejado.

* **O servidor de destino mestre não pode ser armazenado com vMotioned**. Isso pode causar a falha do failback. A VM não será mostrada, pois os discos não serão disponibilizados para ela.

* Você precisa de uma nova unidade adicionada ao servidor de destino mestre do Windows. Essa unidade é chamada de uma unidade de retenção. Adicione um novo disco e formate a unidade. A Unidade de Retenção é usada para interromper os pontos no tempo quando a VM foi replicada de volta para o local. Alguns dos critérios de uma unidade de retenção são mostrados abaixo, sem os quais a unidade não será listada para o servidor de destino mestre.

   * O volume não deve estar em uso para nenhuma outra finalidade (destino de replicação, etc.)

   * O volume não deve estar no modo de bloqueio.

   * O volume não deve ser o volume de cache. (A instalação MT não deve existir nesse volume. O volume de instalação personalizada PS+MT não está qualificado para o volume de retenção. Aqui o volume PS+MT instalado é o volume de cache do MT.)

   * O tipo de sistema de arquivos do volume não deve ser FAT ou FAT32.

   * A capacidade de volume deve ser diferente de zero.

   * O volume de retenção padrão para o Windows é o volume R.

   * O volume de retenção padrão para o Linux é /mnt/retention.

* Uma VM do Linux após failover precisa de um servidor de destino mestre do Linux. Uma VM do Windows após failover precisa de um servidor de destino mestre do Windows.

* Instalar ferramentas do VMware no servidor de destino mestre. Sem as ferramentas do VMware, os armazenamentos de dados no host de ESXi do MT não podem ser detectados.

* Habilite o disk.EnableUUID = parâmetro True na VM do MT nas propriedades do vCenter. <!-- !todo Needs link. -->

* O Destino Mestre deve ter pelo menos um repositório de dados VMFS anexado. Se não houver nenhum, a entrada do Repositório de Dados na página de nova proteção estará vazia, e você não poderá continuar.

* O servidor de Destino Mestre não pode ter instantâneos nos discos. Se houver instantâneos, Proteger Novamente/Failback falhará.

* MT não pode ter um controlador Paravirtual SCSI. Ele só pode ser um controlador LSI Logic. Sem um controlador LSI Logic, a nova proteção falhará.

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>Etapas para proteger novamente

Antes de executar a proteção novamente, verifique se você instalou o [Servidor de processo](site-recovery-vmware-setup-azure-ps-resource-manager.md) no Azure e no Windows local ou no [Destino Mestre do Linux](site-recovery-how-to-install-linux-master-target.md).

> [!NOTE]
> Depois que uma VM for inicializada no Azure, levará algum tempo para que o agente seja registrado no servidor de configuração (no máximo 15 minutos). Durante esse tempo, você verá que a nova proteção falhará, e uma mensagem de erro informará que o agente não está instalado. Aguarde alguns minutos e tente Proteger novamente.



1. No Cofre > Itens replicados, selecione a VM na qual foi executado o failover e clique com o botão direito do mouse em **Proteger Novamente**. Também é possível clicar no computador e selecionar Proteger Novamente nos botões de comando.
2. Na folha, você poderá ver que a direção da proteção “Azure para Local” já está marcada.
3. Em **Servidor de Destino Mestre** e **Servidor de Processo**, selecione o servidor de destino mestre local e o servidor de processo.
4. Selecione o **Armazenamento de Dados** no qual você deseja recuperar os discos localmente. Essa opção é usada quando a VM local é excluída e novos discos precisam ser criados. Essa opção será ignorada se os discos já existirem, mas você ainda precisará especificar um valor.
5. Selecione a unidade de retenção.
6. A política de failback será selecionada automaticamente.
7. Depois de clicar em **OK** para iniciar a nova proteção, um trabalho começará a replicar a VM do Azure para o site local. Você pode acompanhar o andamento na guia **Trabalhos** .

Se você quiser recuperar para um local alternativo (quando a VM local for excluída), selecione a unidade de retenção e o repositório de dados configurados para o servidor de destino mestre. Quando você realiza o failback para o site local, as VMs do VMware no plano de proteção de failback usarão o mesmo repositório de dados que o servidor de destino mestre e uma nova VM será criada no vCenter.
Se você quiser recuperar a VM do Azure para uma VM local existente, os repositórios de dados da VM local deverão ser montados com acesso de leitura/gravação no host de ESXi do servidor de destino mestre.
    ![](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

Também é possível proteger novamente em um nível do plano de recuperação. Caso você tenha um grupo de replicação, ele só poderá ser protegido novamente com um plano de recuperação. Com a nova proteção por meio de um plano de recuperação, será necessário fornecer os valores acima para cada computador protegido.

> [!NOTE]
> Um grupo de replicação deve ser protegido novamente usando o mesmo destino Mestre. Se eles forem protegidos novamente usando um servidor de destino mestre diferente, um ponto no tempo comum não poderá ser usado para ele.


Depois que a nova proteção tiver sucesso, a VM entrará em um estado protegido.

## <a name="next-steps"></a>Próximas etapas

Depois que a VM estiver em estado protegido, você pode iniciar um failback. O failback vai desligar a VM no Azure e inicializar a VM local. Portanto, há um pequeno tempo de inatividade do aplicativo. Sendo assim, faça o failback quando seu aplicativo puder passar por um tempo de inatividade.

[Etapas para iniciar o failback da VM](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-failback)

## <a name="common-issues"></a>Problemas comuns

* Se as máquinas virtuais foram criadas usando um modelo, antes da proteção, você deve garantir que cada VM tenha um UUID exclusivo para os discos. Se o UUID da VM local estiver em conflito com o do destino Mestre (porque ambos foram criados com base no mesmo modelo), a nova proteção falhará. Você precisará implantar outro destino Mestre que não tenha sido criado com base no mesmo modelo.
* Se você executar a descoberta de vCenter de Usuário somente leitura e proteger as máquinas virtuais, ela terá êxito e o failover funcionará. Durante a nova proteção, o failover falha porque não é possível descobrir os armazenamentos de dados. Como sintoma, você não verá os armazenamentos de dados listados durante a nova proteção. Para resolver esse problema, você pode atualizar a credencial do vCenter com uma conta apropriada que tenha permissões e repetir o trabalho. Para obter mais informações, consulte [Replicar máquinas virtuais VMware e servidores físicos no Azure com o Azure Site Recovery](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
* Ao executar failback de uma VM do Linux e executá-la localmente, você verá que o pacote do Gerenciador de Rede foi desinstalado do computador. A desinstalação ocorre porque o pacote do Gerenciador de Rede é removido quando a VM é recuperada no Azure.
* Quando uma VM Linux é configurada com um endereço IP estático e o failover é feito para o Azure, o endereço IP é obtido por meio de DHCP. Quando você executa o failover de volta para o local, a VM continua a usar o DHCP para obter o endereço IP. Conecte-se manualmente à máquina e defina o endereço IP para um endereço estático, se necessário. Uma VM do Windows pode adquirir novamente seu endereço IP estático.
* Se você estiver usando a edição gratuita ESXi 5.5 ou a edição gratuita do 6 vSphere Hypervisor, o failover terá êxito, mas o failback, não. Para permitir o failback, atualize para a licença de avaliação dos programas.
* Se você não conseguir acessar o servidor de configuração do Servidor de processo, verifique a conectividade com o servidor de configuração por Telnet à máquina do servidor de configuração na porta 443. Você também pode tentar executar ping no servidor de configuração da máquina do Servidor de processo. O Servidor de processo também deve ter uma pulsação quando for conectado ao servidor de configuração.
* Se você estiver tentando realizar failback para um vCenter alternativo, verifique se seu novo vCenter está descoberto e se o servidor de destino mestre também está descoberto. Um sintoma típico é que os armazenamentos de dados não ficam acessíveis ou visíveis na caixa de diálogo **Proteger Novamente**.
* Um computador WS2008R2SP1 que esteja protegido como um computador local físico não pode passar por failback do Azure para local.

