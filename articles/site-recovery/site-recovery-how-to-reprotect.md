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
ms.date: 12/15/2016
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: e650439b4bfd468c4bd7b236b80ce7361de1c6ac
ms.openlocfilehash: 972a3e88d15be656fd8cd3d51b7e507c7cbb49d5


---
# <a name="reprotect-from-azure-to-on-premises"></a>Proteja Novamente do Azure para o local

## <a name="overview"></a>Visão geral
Este artigo descreve como proteger novamente as máquinas virtuais do Azure para o site local. Siga as instruções neste artigo quando estiver pronto para realizar o failback de suas máquinas virtuais VMware ou de servidores físicos Windows/Linux após a realização do failover do site local para o Azure usando este [tutorial](site-recovery-vmware-to-azure-classic.md).

Depois que a nova proteção for concluída e da replicação de máquinas virtuais protegidas, você pode iniciar um failback na VM para trazê-las ao local.

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="pre-requisites"></a>Pré-requisitos
A seguir, temos algumas etapas de pré-requisito que você deve realizar ou considerar ao se preparar para o Proteja Novamente.

### <a name="reprotect-needs-a-s2s-vpn-or-an-express-route-to-replicate-data-back-to-on-premises"></a>A nova proteção precisa de uma VPN S2S ou uma rota expressa para replicar os dados para o local
Quando a replicação do local para o Azure puder ser feita via Internet ou uma rota expressa com o emparelhamento público, o Proteja Novamente e o Failback exigem uma VPN S2S configurada para replicar os dados. A rede deve ser fornecida de modo que as VMs que passaram por failover no Azure possam acessar o servidor de configuração local. Você também pode estar implantando um servidor de processo na rede do Azure da VM que passou por failover – este servidor de processo também deve ser capaz de se comunicar com o servidor de configuração local.

### <a name="process-server-is-needed-to-replicate-the-data-from-source-vms-to-on-premises"></a>O servidor de processo é necessário para replicar os dados das VMs de origem para o local
<!-- Read more about a process server here.!todo -->

As VMs do Azure que você deseja proteger novamente enviam os dados de replicação para um servidor de processo. A rede deve ser configurada de modo que o servidor de processo possa ser acessado pela VM do Azure.

Você pode implantar um servidor de processo no Azure ou usar o servidor de processo existente que você usou durante o failover. O ponto importante a considerar é a latência para enviar os dados de VMs do Azure para o servidor de processo. 

* Se você tiver uma rota expressa configurada, um PS local poderá ser usado para enviar os dados. Isso ocorre porque a latência entre a VM e o PS seria baixa.
    
    ![Diagrama da arquitetura da Rota Expressa](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



* No entanto, se você tem apenas uma VPN S2S, é recomendável implantar o servidor de processo no Azure.

    ![Diagrama da arquitetura da VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


Lembre-se de que a replicação ocorrerá somente pela VPN S2S ou pelo emparelhamento privado da sua rede de rota expressa. Certifique-se de que há largura de banda suficiente disponível através desse canal de rede.

### <a name="ports-to-be-opened-on-different-machines-so-that-all-the-components-can-communicate"></a>As portas devem ser abertas em máquinas diferentes para que todos os componentes possam se comunicar

![Failover-Failback de todas as portas](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

### <a name="master-target-needs-to-be-available-on-premises-to-receive-data-from-process-server"></a>O destino mestre precisa estar disponível localmente para receber os dados do servidor de processo
Um servidor de destino mestre é necessário localmente para receber os dados do servidor de processo e, em seguida, gravar o VMDK da VM local. Se você estiver protegendo máquinas virtuais do Windows, será necessário um servidor de destino mestre Windows, e aqui você poderá reutilizar o PS + MT <!-- !todo component --> local. Para VMs do Linux, você precisará configurar um destino mestre adicional local do Linux.

Clique nos links a seguir para ler as etapas sobre como instalar um servidor de destino mestre.

* [Como instalar o servidor de destino mestre do Windows](site-recovery-vmware-to-azure.md#run-site-recovery-unified-setup)
* [Como instalar o servidor de destino mestre do Linux](site-recovery-how-to-install-linux-master-target.md)


#### <a name="common-things-to-check-after-completing-installation-of-master-target"></a>Coisas comuns a verificar após a conclusão da instalação do destino mestre

* Se a VM está presente localmente no servidor vCenter, o servidor de destino mestre precisa acessar o VMDK da VM local. Isso serve para gravar os dados replicados nos discos da VM. Para isso, você precisa garantir que o **repositório de dados da VM local está montado no host do MT com acesso de leitura/gravação**.

* Se a VM não está presente localmente no servidor vCenter, uma nova VM deve ser criada durante o Proteja Novamente. Essa VM será criada no host ESX no qual você cria a MT. Portanto, escolha o host ESX com cuidado para que a VM de failback seja criada no host desejado.
    
* **O servidor de destino mestre não pode ser armazenado com vMotioned**. Isso pode causar a falha do failback. A VM não será mostrada, pois os discos não serão disponibilizados para ela.

* Você precisa de uma nova unidade adicionada ao servidor de destino mestre. Essa unidade é chamada de uma unidade de retenção. Adicione um novo disco e formate a unidade. A Unidade de Retenção é usada para interromper os pontos no tempo quando a VM foi replicada de volta para o local. Alguns dos critérios de uma unidade de retenção são mostrados abaixo, sem os quais a unidade não será listada para o servidor de destino mestre.
   
   a. O volume não deve estar em uso para nenhuma outra finalidade (destino de replicação, etc.)

   b. O volume não deve estar no modo de bloqueio.

   c. O volume não deve ser o volume de cache. (A instalação MT não deve existir nesse volume. O volume de instalação personalizada PS+MT não está qualificado para o volume de retenção. Aqui o volume PS+MT instalado é o volume de cache do MT.)

   d. O tipo de sistema de arquivos do volume não deve ser FAT ou FAT32.

   e. A capacidade de volume deve ser diferente de zero.

   e. O volume de retenção padrão para o Windows é o volume R.

   f. O volume de retenção padrão para o Linux é /mnt/retention.

* Uma VM do Linux após failover precisa de um servidor de destino mestre do Linux. Uma VM do Windows após failover precisa de um servidor de destino mestre do Windows.

* Instalar ferramentas do VMware no servidor de destino mestre. Sem as ferramentas do VMware, os armazenamentos de dados no host de ESXi do MT não podem ser detectados.

* Habilite o disk.EnableUUID = parâmetro True na VM do MT nas propriedades do vCenter. <!-- !todo Needs link. -->



### <a name="failback-policy"></a>Política de failback
Para replicar de volta para local, você precisará de uma política de failback. Essa política é criada automaticamente quando você cria uma política de direção de encaminhamento. Observe que

1. Essa política é associada automaticamente ao servidor de configuração durante a criação.
2. Essa política não é editável.
3. Os valores definidos da política são (Limite de RPO = 15 min, Retenção de Ponto de Recuperação = 24 horas, Frequência de Instantâneo de Consistência do Aplicativo = 60 min) ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)



## <a name="steps-to-reprotect"></a>Etapas para proteger novamente

1. No Cofre > Itens replicados, selecione a VM na qual foi executado o failover e clique com o botão direito do mouse em **Proteger Novamente**. Também é possível clicar no computador e selecionar Proteger Novamente nos botões de comando.
2. Na folha, você poderá ver que a direção da proteção “Azure para Local” já está marcada.
3. Em **Servidor de Destino Mestre** e **Servidor de Processo**, selecione o servidor de destino mestre local e o servidor de processo.
4. Selecione o **Armazenamento de Dados** no qual você deseja recuperar os discos localmente. Essa opção é usada quando a VM local é excluída e novos discos precisam ser criados. Essa opção será ignorada se os discos já existirem, mas você ainda precisará especificar um valor.
5. Selecione a unidade de retenção. 
6. A política de failback será selecionada automaticamente.
7. Depois de clicar em **OK** para iniciar a nova proteção, um trabalho começará a replicar a VM do Azure para o site local. Você pode acompanhar o andamento na guia **Trabalhos** .

Se você quiser recuperar para um local alternativo (quando a VM local for excluída), selecione a unidade de retenção e o repositório de dados configurados para o servidor de destino mestre. Quando você realiza o failback para o site local, as VMs do VMware no plano de proteção de failback usarão o mesmo repositório de dados que o servidor de destino mestre e uma nova VM será criada no vCenter. Se você quiser recuperar a VM do Azure para uma VM local existente, os repositórios de dados da VM local deverão ser montados com acesso de leitura/gravação no host de ESXi do servidor de destino mestre.
    ![](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

Também é possível proteger novamente em um nível do plano de recuperação. Caso você tenha um grupo de replicação, ele só poderá ser protegido novamente com um plano de recuperação. Com a nova proteção por meio de um plano de recuperação, será necessário fornecer os valores acima para cada computador protegido.

> [!NOTE]
> Um grupo de replicação deve ser protegido novamente usando o mesmo destino Mestre. Se eles forem protegidos novamente usando um servidor de destino mestre diferente, um ponto no tempo comum não poderá ser usado para ele.
> 
> 

Depois que a nova proteção tiver sucesso, a VM entrará em um estado protegido.

## <a name="next-steps"></a>Próximas etapas

Depois que a VM estiver em estado protegido, você pode iniciar um failback. O failback vai desligar a VM no Azure e inicializar a VM local. Portanto, há um pequeno tempo de inatividade do aplicativo. Sendo assim, faça o failback quando seu aplicativo puder passar por um tempo de inatividade.

[Etapas para iniciar o failback da VM](site-recovery-how-to-failback-v2a.md#steps-to-failback)

## <a name="common-issues"></a>Problemas comuns 



<!--HONumber=Feb17_HO2-->


