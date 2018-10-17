---
title: Executar failover e failback de VMs VMware e de servidores físicos replicados para Azure com o Site Recovery | Microsoft Docs
description: Saiba como executar failover de VMs VMware e de servidores físicos para o Azure e o failback para o site local, com o Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/11/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: e9ed0ba8d24f30f67dbb315848dc4c260cae4f50
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391361"
---
# <a name="fail-over-and-fail-back-vmware-vms-and-physical-servers-replicated-to-azure"></a>Executar failover e failback de VMs VMware e de servidores físicos replicados para Azure

Este tutorial descreve como efetuar failover de uma VM VMware para o Azure. Depois de executar failover, execute failback para o site local quando ele estiver disponível. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verificar as propriedades da VM VMware para saber se elas estão em conformidade com os requisitos do Azure
> * Executar um failover para o Azure
> * Criar um servidor de processo e o servidor de destino mestre para failback
> * Proteger novamente as VMs do Azure para o site local
> * Executar failover do Azure para o local
> * Proteger novamente as VMs locais, para iniciar novamente a replicação para o Azure

>[!NOTE]
>Os tutoriais destinam-se a mostrar o caminho de implantação mais simples para um cenário. Eles usam opções padrão quando possível e não mostram todas as possíveis configurações e caminhos. Se você quiser saber mais informações sobre as etapas de failover de teste, leia o [Guia de instruções](site-recovery-failover.md).

Este é o quinto tutorial de uma série. Este tutorial presume que você já tenha concluído as tarefas nos tutoriais anteriores.

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar o VMware local](vmware-azure-tutorial-prepare-on-premises.md)
3. [Configurar a recuperação de desastre](vmware-azure-tutorial.md)
4. [Realizar uma simulação de recuperação de desastre](tutorial-dr-drill-azure.md)
5. Além das etapas acima, é aconselhável [examinar a arquitetura](vmware-azure-architecture.md) do cenário de recuperação de desastre.

## <a name="failover-and-failback"></a>Failover e failback

O failover e o failback têm quatro fases:

1. **Failover para o Azure**: faz failover de computadores do site local para o Azure.
2. **Proteger novamente as VMs do Azure**: proteja as VMs do Azure novamente, para que elas comecem a replicação para as VMs VMware locais. A VM local é desativada durante o processo de nova proteção. Isso ajuda a garantir a consistência de dados durante a replicação.
3. **Executar failover para o local**: execute um failover, para executar failback do Azure.
4. **Proteger novamente as VMs locais**: após o failback do dados, proteja as VMs locais para as quais o failback foi executado, para que elas iniciem a replicação para o Azure.

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Confira as propriedades da VM e verifique se a VM está em conformidade com os [Requisitos do Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Em **Itens Protegidos**, clique em **Itens Replicados** > VM.

2. No painel **Item Replicado**, há um resumo das informações da VM, o status de integridade e os últimos pontos de recuperação disponíveis. Clique em **Propriedades** para exibir mais detalhes.

3. Em **Computação e Rede**, você pode modificar o nome do Azure, o grupo de recursos, o tamanho do destino, o [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) e as [configurações de disco gerenciado](#managed-disk-considerations)

4. Você pode exibir ou modificar as configurações de rede, incluindo a rede/sub-rede na qual a VM do Azure estará localizada após o failover e o endereço IP que será atribuído a ela.

5. Em **Discos**, é possível visualizar as informações sobre o sistema operacional e os discos de dados na VM.

## <a name="run-a-failover-to-azure"></a>Executar um failover para o Azure

1. Em **Configurações** > **Itens replicados** clique em VM > **Failover**.

2. Em **Failover**, selecione um **Ponto de Recuperação** para o qual fazer o failover. Você pode usar uma das seguintes opções:
   - **Mais recente**: essa opção primeiro processa todos os dados enviados ao Site Recovery. Ela fornece o RPO (objetivo de ponto de recuperação) mais baixo porque a VM do Azure criada após o failover tem todos os dados que foram replicados para o Site Recovery quando o failover foi disparado.
   - **Últimos processados**: essa opção executa failover da VM para o último ponto de recuperação processado pelo Site Recovery. Essa opção fornece um RTO (Objetivo do Tempo de Recuperação) baixo porque não há tempo gasto para processar dados não processados.
   - **Consistente com o aplicativo mais recente**: essa opção executa failover de todas as VMs para o ponto de recuperação consistente com o aplicativo mais recente pelo Site Recovery.
   - **Personalizado**: especifica um ponto de recuperação.

3. Selecione **Desligar o computador antes de iniciar o failover** para tentar fazer um desligamento das máquinas virtuais de origem antes de disparar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .

Em alguns cenários, o failover requer um processamento adicional que leva cerca de oito a dez minutos para ser concluído. Você pode notar **tempos de failover de teste maiores** para máquinas virtuais VMware usando o serviço de mobilidade com uma versão mais antiga que 9.8, servidores físicos, máquinas virtuais VMware Linux, máquinas Hyper-V protegidas como servidores físicos, VMs VMware que não têm o serviço DHCP habilitado e VMs do VMware que não têm os seguintes drivers de inicialização: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> **Não cancelar um failover em andamento**: antes de iniciar o failover, a replicação da VM é interrompida.
> Se você cancelar um failover em andamento, o failover será interrompido, mas a VM não será replicada novamente.

## <a name="connect-to-failed-over-virtual-machine-in-azure"></a>Conectar-se a uma máquina virtual após o failover no Azure

1. Se você quiser se conectar às VMs do Azure usando RDP/SSH após o failover, siga os requisitos resumidos na tabela mostrada [aqui](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. Após o failover, vá para a máquina virtual e valide-a [conectando-se](../virtual-machines/windows/connect-logon.md) a ela.
3. Após a validação, clique em **Confirmar** para finalizar o ponto de recuperação da máquina virtual após o failover. Após a confirmação, todos os outros pontos de recuperação disponíveis são excluídos. Isso conclui a atividade de failover.

>[!TIP]
> **Alterar o ponto de recuperação** ajuda você a escolher um ponto de recuperação diferente após o failover, se você não estiver satisfeito com a máquina virtual após o failover. Após a **confirmação**, essa opção não estará mais disponível.

Siga as etapas descritas [aqui](site-recovery-failover-to-azure-troubleshoot.md) para solucionar quaisquer problemas de conectividade após o failover.

## <a name="preparing-for-reprotection-of-azure-vm"></a>Preparar para a nova proteção da VM do Azure

### <a name="create-a-process-server-in-azure"></a>Criar um servidor de processo no Azure

O servidor de processo recebe dados da VM do Azure e envia-os para o site local. Uma rede de baixa latência é necessária entre o servidor de processo e a VM protegida.

- Para fins de teste, se você tiver uma conexão Azure ExpressRoute, será possível usar o servidor de processo local (servidor de processo incorporado) que é instalado automaticamente no servidor de configuração.
- Se você tiver uma conexão VPN ou estiver executando o failback em um ambiente de produção, deverá configurar uma VM do Azure como um servidor de processo baseado no Azure para failback.
- Para configurar um servidor de processo no Azure, siga as instruções [neste artigo](vmware-azure-set-up-process-server-azure.md).

### <a name="configure-the-master-target-server"></a>Configurar o servidor de destino mestre

Um servidor de destino mestre que recebe e manipula os dados de replicação durante o failback do Azure. Disponível por padrão no servidor de configuração local. Neste tutorial, vamos usar o servidor de destino mestre padrão.

>[!NOTE]
>Proteger uma máquina virtual Linux exige a criação de um Servidor de Destino Mestre separado. [Clique aqui](vmware-azure-install-linux-master-target.md) para saber mais.

Se a VM estiver em um **host ESXi gerenciado por um vCenter Server**, o servidor de destino mestre deve ter acesso ao armazenamento de dados da VM (VMDK), para gravar os dados replicados nos discos da VM. Verifique se o armazenamento de dados da VM está montado no host de destino mestre com acesso de leitura/gravação.

Se a VM estiver em um **ESXi que não seja gerenciado por um vCenter Server**, o serviço Site Recovery criará uma nova VM durante o processo de nova proteção. A VM é criada no host ESX na qual você cria o destino mestre.
O disco rígido da VM precisa estar em um armazenamento de dados acessível para o host no qual o servidor de destino mestre está em execução.

Se a VM **não usa o vCenter**, você deverá concluir a descoberta do host no qual o servidor de destino mestre está em execução para que seja possível proteger o computador novamente. Isso também vale para failback de servidores físicos. Outra opção, se a VM local existir, ela deverá ser excluída antes da execução de um failback. O failback vai criar uma nova VM no mesmo host que o host ESX de destino mestre. Ao realizar o failback para um local alternativo, os dados são recuperados no mesmo armazenamento de dados e no mesmo host ESX usados pelo servidor de destino mestre local.

Você não pode usar o Storage vMotion no servidor de destino mestre. Se você fizer isso, o failback não funcionará, porque os discos não estarão disponíveis para ele. Exclua os servidores de destino mestres de sua lista do vMotion.

>[!Warning]
>Se você usar um servidor de destino mestre diferente para proteger novamente um grupo de replicação, o servidor não poderá fornecer um ponto comum no tempo.

## <a name="reprotect-azure-vms"></a>Proteger as VMs do Azure

Proteger novamente a VM do Azure leva à replicação de dados para a VM local. Isso é uma etapa obrigatória antes de executar o failover do Azure para a VM local. Siga as instruções fornecidas abaixo para executar a nova proteção.

1. Em **Configurações** > **Itens replicados**, clique com o botão direito do mouse na VM com failover > **Proteger novamente**.
2. Em **Proteger novamente**, verifique se **Do Azure para local** está selecionado.
3. Especifique o servidor de destino mestre local e o servidor de processo.
4. Em **Armazenamento de dado**, selecione o armazenamento de dados de destino mestre para o qual deseja recuperar os discos locais. Se a VM tiver sido excluída, os novos discos serão criados nesse armazenamento de dados. Essa configuração será ignorada se os discos já existirem, mas você precisa especificar um valor.
5. Selecione a unidade de retenção de destino mestre. A politica de failback é selecionada automaticamente.
6. Clique em **OK** para iniciar a nova proteção. Um trabalho começará a replicar a máquina virtual do Azure para o site local. Você pode acompanhar o andamento na guia **Trabalhos**.
7. Depois que o status da VM nos **Itens replicados** for alterado para **Protegido**, o computador está pronto para failover local.

> [!NOTE]
> A VM do Azure pode ser recuperada para uma VM local existente ou um local alternativo. Leia [este artigo](concepts-types-of-failback.md) para saber mais.

## <a name="run-a-failover-from-azure-to-on-premises"></a>Executar um failover do Azure para o local

Para replicar novamente para o local, uma política de failback é usada. Essa política foi criada automaticamente quando você criou uma política de replicação para a replicação para o Azure:

- A política é associada automaticamente ao servidor de configuração.
- A política não pode ser modificada.
- Os valores da política são:
    - Limite RPO = 15 minutos
    - Retenção de ponto de recuperação = 24 horas
    - Frequência de instantâneos consistentes com o aplicativo = 60 minutos

Execute o failover da seguinte maneira:

1. Na página **Itens replicados**, clique com o botão direito do mouse no computador > **Failover**.
2. Em **Confirmar Failover**, verifique se a direção do failover é do Azure.
    ![failover-direction](media/vmware-azure-tutorial-failover-failback/failover-direction.PNG)
3. Selecione o ponto de recuperação que você deseja usar para o failover. Um ponto de recuperação consistente com o aplicativo ocorre antes do ponto mais recente no tempo, e vai causar perda de dados.

    >[!WARNING]
    >Quando o failover é executado, o Site Recovery encerra as VMs do Azure e inicializa a VM local. Haverá em certo tempo de inatividade, portanto escolha um momento apropriado.

4. O andamento do trabalho pode ser controlado em **Cofre de Serviços de Recuperação** > **Monitoramento e Relatórios** > **Trabalhos do Site Recovery**.
5. Após a conclusão do failover, clique com o botão direito do mouse na máquina virtual e clique em **Confirmar**. Isso dispara um trabalho que remove as VMs do Azure.
6. Verifique se as VMs do Azure foram desligadas conforme o esperado.

## <a name="reprotect-on-premises-machines-to-azure"></a>Proteger novamente as máquinas locais para o Azure

Os dados agora devem estar de volta no site local, mas eles não estão sendo replicados para o Azure. Você pode iniciar a replicação para o Azure novamente da seguinte maneira:

1. No cofre > **Itens protegidos** >**Itens replicados**, selecione a VM na qual foi executado o failback e clique em **Proteger novamente**.
2. Selecione o servidor de processo que será usado para enviar os dados replicados para o Azure e, em seguida, clique em **OK**.

Depois que a nova proteção for concluída, a VM será replicada novamente para o Azure e você poderá executar failover conforme o necessário.
