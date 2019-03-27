---
title: Mover as VMs da IaaS do Azure para outra região do Azure usando o serviço do Azure Site Recovery | Microsoft Docs
description: Use o Azure Site Recovery para mover as VMs IaaS do Azure de uma região do Azure para outra.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 7619b8831d75ce639c6f6c773c7c7d491abc93e7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122022"
---
# <a name="move-azure-vms-to-another-region"></a>Mover VMs do Azure para outra região

Há vários cenários em que é possível mover as VMs (máquinas virtuais) da IaaS do Azure existentes de uma região para outra. Por exemplo, você quer melhorar a confiabilidade e a disponibilidade da VMs existentes, melhorar a capacidade de gerenciamento ou mover por razões de governança. Para obter mais informações, consulte a [visão geral de movimentação da VM do Azure](azure-to-azure-move-overview.md). 

É possível usar o serviço do [Azure Site Recovery](site-recovery-overview.md) para gerenciar e orquestrar a recuperação de desastre de computadores locais e VMs do Azure para BCDR (continuidade dos negócios e recuperação de desastres). Você também pode usar o Site Recovery para gerenciar a movimentação de VMs do Azure para uma região secundária.

Neste tutorial, você irá:

> [!div class="checklist"]
> 
> * Verificar os pré-requisitos para a movimentação
> * Preparar as VMs de origem e a região de destino
> * Copiar os dados e habilitar a replicação
> * Testar a configuração e executar a movimentação
> * Excluir os recursos na região de origem
> 
> [!NOTE]
> Este tutorial mostra como mover as VMs do Azure de uma região para outra. Se você precisar melhorar a disponibilidade, movendo as VMs em um conjunto de disponibilidade para VMs fixas de zona em uma região diferente, consulte o tutorial [Mover as VMs do Azure para Zonas de Disponibilidade](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Pré-requisitos

- Verifique se as VMs do Azure estão na região do Azure a partir da qual você quer mover.
- Verifique se [há suporte para sua escolha de combinação de região de origem – região de destino](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) e tome uma decisão informada sobre a região de destino.
- Verifique se você entende os [componentes e a arquitetura do cenário](azure-to-azure-architecture.md).
- Examine os [requisitos e limitações com suporte](azure-to-azure-support-matrix.md).
- Verifique as permissões da conta. Se você criou a conta gratuita do Azure, você será o administrador da assinatura. Se você não for o administrador da assinatura, trabalhe com o administrador para atribuir as permissões necessárias. Para habilitar a replicação de uma VM e essencialmente copiar dados usando o Azure Site Recovery, você deverá ter:

    * Permissões para criar uma VM em recursos do Azure. A função interna Colaborador da Máquina Virtual tem essas permissões, incluindo:
        - Permissão para criar uma VM no grupo de recursos selecionado
        - Permissão para criar uma VM na rede virtual selecionada
        - Permissão para gravar na conta de armazenamento selecionada

    * Permissões para gerenciar as operações do Azure Site Recovery. A função Colaborador do Site Recovery tem todas as permissões necessárias para gerenciar operações do Site Recovery em um cofre dos Serviços de Recuperação.

## <a name="prepare-the-source-vms"></a>Preparar as VMs de origem

1. Certifique-se de que todos os certificados raiz mais recentes estejam nas VMs do Azure que você quer mover. Se os certificados raiz mais recentes não estiverem na VM, as restrições de segurança impedirão a cópia dos dados na região de destino.

    - Para VMs Windows, instale todas as atualizações do Windows mais recentes na VM para que todos os certificados raiz confiáveis estejam no computador. Em um ambiente desconectado, siga os processos padrão do Windows Update e de atualização de certificado para sua organização.
    - Para VMs do Linux, siga as diretrizes fornecidas pelo distribuidor Linux para obter os certificados raiz confiáveis mais recentes e a lista de certificados revogados na VM.
1. Certifique-se de que você não está usando um proxy de autenticação para controlar a conectividade de rede das VMs que você quer mover.
1. Se a VM que você está tentando mover não tiver acesso à Internet ou estiver usando um proxy de firewall para controlar o acesso de saída, [verifique os requisitos](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
1. Identifique o layout de rede de origem e todos os recursos que você está usando atualmente. Isso inclui, mas não se limita a, balanceadores de carga, NSGs (grupos de segurança de rede) e IPs públicos.

## <a name="prepare-the-target-region"></a>Preparar a região de destino

1. Verifique se a assinatura do Azure permite criar VMs na região de destino utilizada para recuperação de desastre. Contate o suporte para habilitar a cota necessária.

1. Certifique-se de que a assinatura tenha recursos suficientes para dar suporte a VMs com tamanhos que correspondam às VMs de origem. Se você estiver usando o Site Recovery para copiar dados para o destino, o Site Recovery escolherá o mesmo tamanho ou o tamanho mais próximo possível para a VM de destino.

1. Certifique-se de criar um recurso de destino para cada componente identificado no layout de rede de origem. Essa etapa é importante para garantir que as VMs tenham toda a funcionalidade e recursos na região de destino que você tinha na região de origem.

    > [!NOTE]
    > O Azure Site Recovery descobre e cria automaticamente uma rede virtual quando você habilita a replicação para a VM de origem. Você também pode pré-criar uma rede e atribuí-la à VM no fluxo do usuário para habilitar a replicação. Conforme mencionado posteriormente, será necessário criar manualmente outros recursos na região de destino.

     Para criar os recursos de rede mais utilizados e relevantes com base na configuração da VM de origem, consulte a documentação a seguir:

   - [Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Balanceadores de carga](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
   - [IP público](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
     Para quaisquer outros componentes de rede, consulte a [documentação da rede](https://docs.microsoft.com/azure/#pivot=products&panel=network).

1. [Crie manualmente uma rede de não produção](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) na região de destino, caso você queira testar a configuração antes de executar a movimentação final para a região de destino. Essa etapa é recomendável porque garante interferência mínima na rede de produção.

## <a name="copy-data-to-the-target-region"></a>Copiar os dados para a região de destino
As etapas a seguir mostram como usar o Azure Site Recovery para copiar dados para a região de destino.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Criar o cofre em qualquer região, exceto a região de origem

1. Entre no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
1. Selecione **Criar um recurso** > **Ferramentas de Gerenciamento** > **Backup e Site Recovery**.
1. Em **Nome**, especifique o nome amigável **ContosoVMVault**. Se você tiver mais de uma assinatura, selecione uma delas.
1. Crie o grupo de recursos **ContosoRG**.
1. Especifique uma região do Azure. Para verificar as regiões com suporte, consulte a disponibilidade geográfica em [detalhes de preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
1. Em **cofres dos Serviços de Recuperação**, selecione **Visão geral** > **ContosoVMVault** > **+Replicar**.
1. Em **Fonte**, selecione **Azure**.
1. Em **Local de origem**, selecione a fonte de região do Azure em que suas VMs estão sendo executados.
1. Selecione o modelo de implantação do Gerenciador de Recursos. Em seguida, selecione a **Assinatura de origem** e o **Grupo de recursos de origem**.
1. Selecione **OK** para salvar as configurações.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Habilitar replicação para VMs do Azure e iniciar cópia dos dados

O Site Recovery recupera uma lista das VMs associadas à assinatura e ao grupo de recursos.

1. Na próxima etapa, selecione a VM que você quer mover e selecione **OK**.
1. Em **Configurações**, selecione **Recuperação de desastre**.
1. Em **Configurar recuperação de desastre** > **Região de destino**, selecione a região de destino para a qual será replicada.
1. Para este tutorial, aceite as outras configurações padrão.
1. Selecione **Habilitar replicação**. Esta etapa inicia um trabalho para habilitar a replicação da VM.

    ![Habilitar a replicação](media/tutorial-migrate-azure-to-azure/settings.png)

## <a name="test-the-configuration"></a>Testar a configuração

1. Vá para o cofre. Em **Configurações** > **Itens replicados**, selecione a VM que você quer mover para a região de destino e, em seguida, selecione **+Failover de Teste**.
1. Em **Failover de Teste**, selecione um ponto de recuperação a ser utilizado para o failover:

   - **Mais recente processado**: Faz failover da VM para o ponto de recuperação único que foi processado pelo serviço do Site Recovery. A carimbo de data/hora é mostrado. Com essa opção, nenhum tempo é gasto no processamento de dados, portanto, fornece um RTO (Objetivo de Tempo de Recuperação) baixo.
   - **Consistente com o aplicativo mais recente**: Essa opção falha em todas as VMs para o ponto de recuperação consistente com o aplicativo mais recente. A carimbo de data/hora é mostrado.
   - **Personalizado**: Selecione qualquer ponto de recuperação.

1. Selecione a rede virtual de destino do Azure para a qual deseja mover as VMs do Azure para testar a configuração.
    > [!IMPORTANT]
    > É recomendável utilizar uma rede VM separada do Azure para realizar o failover de teste. Não utilize a rede de produção que foi configurada quando você habilitou a replicação e que eventualmente poderá querer mover as VMs.
1. Para começar a testar a movimentação, clique em **OK**. Para acompanhar o progresso, clique na VM para abrir suas propriedades. Ou você pode clicar no trabalho **Failover de Teste** no nome do cofre > **Configurações** > **Trabalhos** > **Trabalhos do Site Recovery**.
1. Após a conclusão do failover, a réplica da VM do Azure aparece no portal do Azure > **Máquinas Virtuais**. Certifique-se de que a VM esteja em execução, tenha o tamanho adequado e esteja conectada à rede apropriada.
1. Se você quiser excluir a VM que foi criada como parte do teste da movimentação, clique em **Limpar failover de teste** no item replicado. Em **Notas**, registre e salve quaisquer observações associadas ao teste.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Realizar a movimentação para a região de destino e confirmar

1. Vá para o cofre. Em **Configurações** > **Itens replicados**, selecione a VM e, em seguida, selecione **Failover**.
1. Em **Failover**, selecione **Mais recente**.
1. Selecione **Desligar o computador antes do início do failover**. O Site Recovery tenta desligar a VM de origem antes de acionar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .
1. Depois que o trabalho for concluído, verifique se a VM será exibida na região de destino do Azure conforme esperado.
1. Em **Itens replicados**, selecione a VM > **Confirmar**. Essa etapa conclui o processo de movimentação para a região de destino. Aguarde até que o trabalho de confirmação seja concluído.

## <a name="delete-the-resource-in-the-source-region"></a>Excluir o recurso na região de origem

Vá até a VM. Selecione **Desabilitar Replicação**. Essa etapa impede que o processo copie os dados para a VM.

> [!IMPORTANT]
> É importante executar essa etapa para evitar ser cobrado pela replicação do Azure Site Recovery.

Se você não tiver planos de reutilizar os recursos de origem, siga estas etapas:

1. Exclua todos os recursos de rede relevantes na região de origem listada como parte da etapa 4 em [Preparar as VMs de origem](#prepare-the-source-vms).
1. Exclua a conta de armazenamento correspondente da região de origem.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu uma VM do Azure para uma região do Azure diferente. Agora, você pode configurar a recuperação de desastre para a VM que você moveu.

> [!div class="nextstepaction"]
> [Configurar a recuperação de desastres após a migração](azure-to-azure-quickstart.md)

