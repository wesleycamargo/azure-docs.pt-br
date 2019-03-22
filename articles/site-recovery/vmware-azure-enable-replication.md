---
title: Habilite a replicação de VMs do VMware para recuperação de desastre do VMware para o Azure com o Azure Site Recovery | Microsoft Docs'
description: Este artigo descreve como habilitar a replicação de VMs do VMware para recuperação de desastre no Azure, usando o Azure Site Recovery.
author: mayurigupta13
ms.service: site-recovery
ms.date: 3/6/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 41ff32f840b7a0e9e5fa5d8f7bf25a93fa679955
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098688"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Habilitar a replicação no Azure de VMs VMware


Este artigo descreve como habilitar replicação de VMs VMware locais no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo supõe que você:

1.  [Configure o ambiente de origem local](vmware-azure-set-up-source.md).
2.  [Configure o ambiente de destino no Azure](vmware-azure-set-up-target.md).


## <a name="before-you-start"></a>Antes de começar
Ao replicar máquinas virtuais VMware:

* Sua conta de usuário do Azure precisa ter certas [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para habilitar a replicação de uma nova máquina virtual para o Azure.
* VMs VMware são descobertas a cada 15 minutos. Pode levar 15 minutos ou mais para que elas sejam exibidas no portal do Azure após a descoberta. Da mesma forma, a descoberta pode levar 15 minutos ou mais quando você adiciona um novo servidor vCenter ou host vSphere.
* As alterações de ambiente na máquina virtual (como instalação de ferramentas VMware) podem levar 15 minutos ou mais para serem atualizadas no portal.
* Você pode verificar a hora da última descoberta das VMs VMware no campo **Último Contato Às** do servidor vCenter/host vSphere, na página **Servidores de Configuração**.
* Para adicionar computadores para replicação sem precisar esperar pela descoberta agendada, realce o servidor de configuração (não clique nele) e clique no botão **Atualizar**.
* Quando você habilitar a replicação, se o computador estiver preparado, o servidor em processo instalará automaticamente o Serviço de Mobilidade nele.


## <a name="enable-replication"></a>Habilitar a replicação

>[!NOTE]
>* Agora, o Azure Site Recovery replica diretamente para o Managed Disks para todas as replicações novo. Servidor de processo grava os logs de replicação para uma conta de armazenamento de cache na região de destino. Esses logs são usados para criar pontos de recuperação em discos gerenciados de réplica. 
>* No momento do failover, o ponto de recuperação selecionado pelo cliente é usado para criar o disco gerenciado de destino.
>* As VMs que são configuradas anteriormente para replicar a contas de armazenamento de destino não serão afetadas. 
>* A replicação para contas de armazenamento para uma nova máquina só está disponível por meio da API REST e Powershell. Use a API versão 2016-08-10 ou 2018-01-10 para a replicação para contas de armazenamento.

1. Clique em **Etapa 2: Replicar aplicativo** > **Origem**. Depois de habilitar a replicação pela primeira vez, clique em **+Replicar** no cofre para habilitar a replicação para outros computadores.
2. Na página **Origem** > **Origem**, selecione o servidor de configuração.
3. Em **Tipo de computador**, selecione **Máquinas Virtuais** ou **Computadores Físicos**.
4. Em **Hipervisor do vCenter/vSphere**, selecione o servidor vCenter que gerencia o host vSphere ou selecione o host. Essa configuração não será relevante se você estiver replicando computadores físicos.
5. Selecione o servidor de processo, que terá o nome do servidor de configuração caso você não tenha criado um servidor de processo adicional. Em seguida, clique em **OK**.

    ![Habilitar a origem de replicação](./media/vmware-azure-enable-replication/enable-replication2.png)

6. Em **Destino**, selecione a assinatura e o grupo de recursos no qual você deseja criar as máquinas virtuais do failover. Escolha o modelo de implantação que você deseja usar no Azure para as máquinas virtuais do failover.

7. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se conectarão quando forem rotacionadas após o failover. A rede deve estar na mesma região do que o cofre de Recuperação de Site. Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todos os computadores selecionados para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por computador. Se você não tiver uma rede, precisará criar uma. Para criar uma rede usando o Resource Manager, clique em **Criar nova**. Selecione uma sub-rede, se aplicável e, em seguida, clique em **OK**.
   
   ![Habilitar a configuração de destino de replicação](./media/vmware-azure-enable-replication/enable-rep3.png)

8. Em **Máquinas Virtuais** > **Selecionar máquinas virtuais**, selecione cada máquina que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Em seguida, clique em **OK**. Se você não conseguir exibir/selecionar nenhuma máquina virtual específica, clique [aqui](https://aka.ms/doc-plugin-VM-not-showing) para resolver o problema.

    ![Habilitar máquinas virtuais de replicação selecionadas](./media/vmware-azure-enable-replication/enable-replication5.png)

9. Em **Propriedades** > **Configurar propriedades**, selecione a conta usada pelo servidor de processo para instalar automaticamente o Serviço de Mobilidade no computador. Além disso, escolha o tipo de disco gerenciado de destino que você desejaria para replicar com base em seus dados de variação de padrões.
10. Por padrão, todos os discos de um computador de origem são replicados. Para excluir discos da replicação, desmarque a opção **Include** caixa de seleção para quaisquer discos que você não deseja replicar.  Em seguida, clique em **OK**. Você pode definir propriedades adicionais posteriormente. [Saiba mais](vmware-azure-exclude-disk.md) sobre a exclusão de discos.

    ![Habilitar propriedades de configuração de replicação](./media/vmware-azure-enable-replication/enable-replication6.png)

11. Em **Configurações de replicação** > **Definir configurações de replicação**, verifique se a política de replicação correta está selecionada. Você pode modificar as configurações da política de replicação em **Configurações** > **Políticas de replicação** > (nome da política) > **Editar Configurações**. Alterações aplicadas a uma política também serão aplicadas a computadores novos e de replicação.
12. Habilite **Consistência de várias VMs** se você quiser reunir computadores em um grupo de replicação. Especifique um nome para o grupo e então clique em **OK**. 

    > [!NOTE]
    > 
    >    * Os computadores em um grupo de replicação são replicados em conjunto e têm pontos de recuperação consistentes compartilhados com o aplicativo e com falhas quando executam failover.
    >    * Colete VMs e servidores físicos para que espelhem suas cargas de trabalho. Habilitar a consistência de várias VMs pode afetar o desempenho da carga de trabalho. Use somente se os computadores estiverem executando a mesma carga de trabalho e você precisar de consistência.

    ![Habilitar a replicação](./media/vmware-azure-enable-replication/enable-replication7.png)
    
13. Clique em **Habilitar a Replicação**. Você pode acompanhar o progresso do trabalho **Habilitar Proteção** em **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.

## <a name="view-and-manage-vm-properties"></a>Exibir e gerenciar as propriedades da VM

Em seguida, verifique as propriedades do computador de origem. Lembre-se de que o nome da VM do Azure precisa estar em conformidade com os [Requisitos de máquina virtual do Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Clique em **Configurações** > **Itens replicados** > e então selecione o computador. A página **Essentials** mostra as informações sobre as configurações e o status dos computadores.
2. Em **Propriedades**, você pode exibir informações de replicação e de failover para a VM.
3. Em **Computação e Rede** > **propriedades de Computação**, você pode alterar várias propriedades da VM:
   * Nome da VM do Azure – modifique o nome para que ele fique em conformidade com os requisitos do Azure se necessário
   * Tamanho da VM de destino ou um tipo VM - o tamanho da VM padrão é escolhido com base no tamanho da VM de origem. Selecione outro tamanho de VM, conforme a necessidade, a qualquer momento antes do failover. Observe que o tamanho do disco de VM também se baseia no tamanho do disco de origem e só pode ser alterado pós-failover. Saiba mais sobre tamanhos de disco e IOPS no artigo [Metas de escalabilidade para discos](../virtual-machines/windows/disk-scalability-targets.md).

     ![Propriedades de Computação e Rede](./media/vmware-azure-enable-replication/vmproperties.png)

   * Grupo de Recursos – selecione um [grupo de recursos](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines) no qual um computador se tornará parte de um pós-failover. Você pode alterar essa configuração a qualquer momento antes do failover. Após o failover, se você migrar o computador para outro grupo de recursos, as configurações de proteção desse computador serão interrompidas.
   * Conjunto de Disponibilidade – selecione um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) se o computador precisar fazer parte de um pós-failover. Durante a seleção de um conjunto de disponibilidade, tenha em mente que:

       * Apenas os conjuntos de disponibilidade que pertencem ao grupo de recursos especificado serão listados.  
       * Os computadores com redes virtuais diferentes não podem fazer parte do mesmo conjunto de disponibilidade.
       * Somente máquinas virtuais do mesmo tamanho podem fazer parte de um mesmo conjunto de disponibilidade.
4. Você também pode exibir e adicionar as informações sobre a rede de destino, a sub-rede e o endereço IP à VM do Azure.
5. Em **Discos**, é possível ver o sistema operacional e os discos de dados na VM que serão replicados.

### <a name="configure-networks-and-ip-addresses"></a>Configurar redes e endereços IP

- Você pode definir o endereço IP de destino. Se você não fornecer um endereço, o computador que fez failover usará o DHCP. Se você definir um endereço que não esteja disponível no failover, o failover não funcionará. Se o endereço estiver disponível na rede de failover de teste, o mesmo endereço IP de destino poderá ser usado para failover de teste.
- O número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino, como a seguir:
    - Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que a origem.
    - Se o número de adaptadores para máquina virtual de origem exceder o número permitido para o tamanho de destino e o tamanho máximo de destino usado.
    Por exemplo, se um computador de origem tiver dois adaptadores de rede e o tamanho do computador de destino der suporte a quatro, o computador de destino terá dois adaptadores. Se o computador de origem tiver dois adaptadores, mas o tamanho de destino com suporte só aceitar um, o computador de destino terá apenas um adaptador.
    - Se a máquina virtual tiver vários adaptadores de rede, todos eles se conectarão à mesma rede. Além disso, o primeiro deles mostrados na lista se torna o adaptador de rede *Padrão* na máquina virtual do Azure.

### <a name="azure-hybrid-benefit"></a>Benefício Híbrido do Azure

Clientes do Microsoft Software Assurance podem usar o Benefício Híbrido do Azure para economizar nos custos de licenciamento para máquinas do Windows Server que são migradas para o Azure, ou que usam o Azure para recuperação de desastres. Se você está qualificado para usar o Benefício Híbrido do Azure, pode especificar que a máquina virtual atribuída a esse benefício seja a criada pelo Azure Site Recovery se houver um failover. Para fazer isso:
- Vá para a seção de propriedades de Computação e Rede da máquina virtual replicada.
- Responda à pergunta que pergunta se você tem uma licença do Windows Server que faz com que você tenha direito ao Benefício Híbrido do Azure.
- Marque a caixa de seleção para confirmar que você tem uma licença do Windows Server com o Software Assurance que você pode usar para aplicar o Benefício Híbrido do Azure no computador que será criado durante o failover.
- Salve as configurações para o computador replicado.

Saiba mais sobre o [Benefício Híbrido do Azure](https://aka.ms/azure-hybrid-benefit-pricing).

## <a name="common-issues"></a>Problemas comuns

* Cada disco deve ter menos de 4 TB.
* O disco do sistema operacional deve ser um disco básico e não um disco dinâmico.
* Para máquinas virtuais habilitadas pela geração 2/UEFI, a família do sistema operacional deve ser o Windows e o disco de inicialização deve ser menor que 300 GB.

## <a name="next-steps"></a>Próximas etapas

Depois que a proteção for concluída e a máquina estiver protegida, você pode tentar um [failover](site-recovery-failover.md) para verificar se o seu aplicativo é mostrado no Azure ou não.

* Saiba como [limpar as configurações de registro e proteção](site-recovery-manage-registration-and-protection.md) para desabilitar a replicação.
* Saiba como [automatizar a replicação para as máquinas virtuais usando o PowerShell](vmware-azure-disaster-recovery-powershell.md)
