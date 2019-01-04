---
title: Mover as VMs IaaS do Azure para outra região do Azure usando o serviço Azure Site Recovery | Microsoft Docs
description: Use o Azure Site Recovery para mover as VMs IaaS do Azure de uma região do Azure para outra.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: dc27e49cc67a902bb45b1d889bb61b1f4b3aab83
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788762"
---
# <a name="move-azure-vms-to-another-region"></a>Mover VMs do Azure para outra região

Além de usar o serviço [Azure Site Recovery](site-recovery-overview.md) para gerenciar e coordenar a recuperação de desastres de máquinas locais e VMs do Azure para fins de continuidade de negócios e recuperação de desastres (BCDR), você também pode usar o Site Recovery para gerenciar a transferência das VMs do Azure para uma região secundária. Para mover as VMs do Azure, habilite a replicação para elas e faça o failover da região primária para uma região secundária de sua escolha.

Este tutorial mostra como mover VMs do Azure para outra região. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um cofre de serviços de recuperação
> * Habilitar a replicação para uma VM
> * Executar um failover para mover a VM

Este tutorial pressupõe que você já tem uma assinatura do Azure. Caso não tenha, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.





## <a name="prerequisites"></a>Pré-requisitos

- Verifique se que você tem VMs do Azure na região do Azure de onde deseja mover.
- Verifique se você entende os [componentes e a arquitetura do cenário](azure-to-azure-architecture.md).
- Examine os [requisitos e limitações com suporte](azure-to-azure-support-matrix.md).



## <a name="before-you-start"></a>Antes de começar

Antes de configurar a replicação, conclua estas etapas.


### <a name="verify-target-resources"></a>Verifique os recursos de destino

1. Verifique se a sua assinatura do Azure permite criar VMs na região de destino usada para recuperação de desastre. Contate o suporte para habilitar a cota necessária.

2. Verifique se a sua assinatura tem recursos suficientes para dar suporte a VMs com tamanhos que correspondem às VMs de origem. O Site Recovery escolhe o mesmo tamanho ou o tamanho mais próximo possível para a VM de destino.


### <a name="verify-account-permissions"></a>Verificar permissões de conta

Se você acabou de criar sua conta gratuita do Azure, você é o administrador da assinatura. Se você não for o administrador da assinatura, peça para o administrador atribuir as permissões necessárias. Para habilitar a replicação para uma nova VM, você deve ter:

1. Permissões para criar uma VM em recursos do Azure. A função interna 'Colaborador da Máquina Virtual' tem essas permissões, que incluem:
    - Permissão para criar uma VM no grupo de recursos selecionado
    - Permissão para criar uma VM na rede virtual selecionada
    - Permissão para gravar na conta de armazenamento selecionada

2. Você também precisa de permissão para gerenciar operações de recuperação do Azure Site Recovery. A função 'Colaborador do Site Recovery' tem todas as permissões necessárias para gerenciar operações do Site Recovery em um cofre de Serviços de Recuperação.


### <a name="verify-vm-outbound-access"></a>Verificar o acesso de saída da VM

1. Verifique se você não está usando um proxy de autenticação para controlar a conectividade de rede das VMs que deseja mover. 
2. Para os fins deste tutorial, pressupomos que as VMs que você deseja mover podem acessar a internet e não estão usando um proxy de firewall para controlar o acesso de saída. Caso tenha, verifique os requisitos [aqui](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).

### <a name="verify-vm-certificates"></a>Verificar os certificados de VM

Verifique se todos os certificados raiz mais recentes estão presentes nas VMs do Azure que deseja mover. Caso não estejam, a VM não poderá ser registrada no Site Recovery devido a restrições de segurança.

- Para VMs Windows, instale todas as atualizações do Windows mais recentes na VM para que todos os certificados raiz confiáveis estejam no computador. Em um ambiente desconectado, siga os processos padrão do Windows Update e de atualização de certificado para sua organização.
- Para VMs Linux, siga as diretrizes fornecidas pelo seu distribuidor Linux para obter os certificados raiz confiáveis mais recentes e a lista de certificados revogados na VM.



## <a name="create-a-vault"></a>Criar um cofre

Crie o cofre em qualquer região, exceto a região de origem.

1. Entre no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
2. Clique em **Criar um recurso** > **Monitoramento e Gerenciamento** > **Backup e Site Recovery**.
3. Em **Nome**, especifique o nome amigável **ContosoVMVault**. Se você tiver mais de uma assinatura, selecione uma delas.
4. Crie um grupo de recursos **ContosoRG**.
5. Especifique uma região do Azure. Para verificar as regiões com suporte, confira a disponibilidade geográfica nos [Detalhes dos Preços de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Para acessar rapidamente o cofre no painel, clique em **Fixar no painel** e em **Criar**.

   ![Novo cofre](./media/tutorial-migrate-azure-to-azure/azure-to-azure-vault.png)

O novo cofre é adicionado ao **Painel** em **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.






## <a name="select-the-source"></a>Selecione a origem

1. Em cofres dos Serviços de Recuperação, clique em **ConsotoVMVault** > **+Replicate**.
2. Em **Fonte**, selecione **Azure**.
3. Em **Local de origem**, selecione a fonte de região do Azure em que suas VMs estão sendo executados.
4. Selecione o modelo de implantação do Gerenciador de Recursos. Depois selecione o **Grupo de recursos de origem**.
5. Clique em **OK** para salvar as configurações.


## <a name="enable-replication-for-azure-vms"></a>Habilitar a replicação para VMs do Azure

O Site Recovery recupera uma lista das VMs associadas à assinatura e ao grupo de recursos.


1. No portal do Azure, clique em **Máquinas virtuais**.
2. Selecione a VM que deseja mover. Em seguida, clique em **OK**.
3. Em **Configurações**, clique em **Recuperação de desastre**.
4. Em **Configurar a recuperação de desastre** > **Região de destino**, selecione a região de destino para a qual você replicará.
5. Para este tutorial, aceite as outras configurações padrão.
6. Clique em **Habilitar a replicação**. Isso inicia um trabalho para habilitar a replicação para a VM.

    ![habilitar a replicação](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="run-a-failover"></a>Executar um failover

1. Em **Configurações** > **Itens replicados** clique no computador e depois em **Failover**.
2. Em **Failover**, selecione **Mais recente**. A configuração de chave de criptografia não é relevante para esse cenário.
3. Selecione **Desligar o computador antes do início do failover**. O Site Recovery tenta desligar a VM de origem antes de acionar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .
4. Verifique se a VM do Azure aparece no Azure, conforme o esperado.
5. Em **Itens replicados**, clique com o botão direito do mouse em VM > **Confirmar**. Isso conclui o processo de migração.
6. Após a confirmação, clique em **Desabilitar Replicação**.  Isso interrompe a replicação da VM.



## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu uma VM do Azure para uma região diferente dele. Agora é possível configurar a recuperação de desastres para VMs do Azure movida.

> [!div class="nextstepaction"]
> [Configurar a recuperação de desastres após a migração](azure-to-azure-quickstart.md)

