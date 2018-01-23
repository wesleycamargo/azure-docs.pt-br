---
title: "Configurar a recuperação de desastre em VMs locais do Hyper-V (sem o VMM) para o Azure com o Azure Site Recovery | Microsoft Docs"
description: "Saiba como configurar a recuperação de desastre de VMs locais do Hyper-V (sem o VMM) para o Azure com o serviço Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/31`/2017
ms.author: raynew
ms.openlocfilehash: 633c14bd25bc8a1419196b2c76ca94c26db68991
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Configurar a recuperação de desastre de VMs locais do Hyper-V para o Azure

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de recuperação de desastre ao gerenciar e orquestrar a replicação, o failover e o failback de computadores locais e de VMs (máquinas virtuais) do Azure.

Este tutorial mostra como configurar a recuperação de desastre de VMs locais do Hyper-V para o Azure. O tutorial é relevante para VMs do Hyper-V que não são gerenciadas pelo System Center Virtual Machine Manager (VMM). Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Selecionar a origem e o destino de replicação.
> * Configurar o ambiente de replicação de origem, incluindo componentes de Site Recovery locais e o ambiente de replicação de destino.
> * Criar uma política de replicação.
> * Habilitar a replicação para uma VM.

Este é o terceiro tutorial de uma série. Este tutorial presume que você já tenha concluído as tarefas nos tutoriais anteriores:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar Hyper-V local](tutorial-prepare-on-premises-hyper-v.md)

Antes de começar, é aconselhável [examinar a arquitetura](concepts-hyper-v-to-azure-architecture.md) do cenário de recuperação de desastre.

## <a name="select-a-replication-goal"></a>Selecione uma meta de replicação


1. Em **Todos os Serviços** > **Cofres dos Serviços de Recuperação**, clique no nome do cofre que preparamos no tutorial anterior, **ContosoVMVault**.
2. Em **Introdução**, clique em **Site Recovery**. A seguir, clique em **Preparar Infraestrutura**
3. Em **Objetivo de proteção** > **Onde os seus computadores estão localizados**, selecione **local**.
4. Em **Para qual deseja replicar os seus computadores**, selecione **Para o Azure**.
5. Em **Os seus computadores estão virtualizados?**, selecione **Sim, com o Hyper-V**.
6. Em **Você está usando o System Center VMM?**, selecione **Não**. Em seguida, clique em **OK**.

    ![Meta de replicação](./media/tutorial-hyper-v-to-azure/replication-goal.png)

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Para configurar o ambiente de origem, adicione hosts Hyper-V a um site do Hyper-V, baixe e instale o Provedor do Azure Site Recovery e o agente dos Serviços de Recuperação do Azure e registre o site do Hyper-V no cofre. 

1. Em **Preparar a infraestrutura**, clique em **Origem**.
2. Clique em **+Site Hyper-V** e especifique o nome do site que criamos no tutorial anterior, **ContosoHyperVSite**.
3. Clique em **+Servidor Hyper-V**.
4. Baixe o de arquivo de instalação do Provedor.
5. Baixe a chave do registro do cofre. Você precisará dela quando executar a instalação do Provedor. A chave é válida por cinco dias após ser gerada.

    ![Baixar Provedor](./media/tutorial-hyper-v-to-azure/download.png)
    

### <a name="install-the-provider"></a>Instale o Provedor

Execute o arquivo de instalação do Provedor (AzureSiteRecoveryProvider.exe) em cada host Hyper-V adicionado ao site **ContosoHyperVSite**. A configuração instala o Provedor do Azure Site Recovery e o agente dos Serviços de Recuperação em cada host do Hyper-V.

1. No assistente de Instalação do Provedor do Azure Site Recovery > **Microsoft Update**, opte por usar o Microsoft Update para verificar se há atualizações do provedor.
2. Em **Instalação**, aceite o local de instalação padrão do Provedor e do agente e clique em **Instalar**.
3. Após a instalação, no Assistente de Registro do Microsoft Azure Site Recovery > **Configurações do Cofre**, clique em **Procurar** e, em **Arquivo de Chave**, selecione a chave do cofre do arquivo baixado. 
4. Especifique a assinatura do Azure Site Recovery, o nome do cofre (**ContosoVMVault**) e o site de Hyper-V (**ContosoHyperVSite**) ao qual pertence o servidor Hyper-V.
5. Em **Configurações de Proxy**, selecione **Conectar diretamente ao Azure Site Recovery sem um proxy**.
6. Em **Registro**, depois que o servidor foi registrado no cofre, clique em **Concluir**.

Os metadados do servidor Hyper-V são recuperados pelo Azure Site Recovery e o servidor é exibido em **Infraestrutura do Site Recovery** > **Hosts Hyper-V**. Isso pode demorar até 30 minutos.


## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Selecione e verifique os recursos de destino. 

1. Clique em **Preparar a Infraestrutura** > **Destino**.
2. Selecione a assinatura e o grupo de recursos **ContosoRG**, em que as VMs do Azure serão criadas após o failover.
3. Selecione o modelo de implantação do **Resource Manager**.

A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.


## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. Clique em **Preparar a Infraestrutura** > **Configurações de Replicação** > **+Criar e associar**.
2. Em **Criar e associar política**, especifique um nome de política, **ContosoReplicationPolicy**.
3. Deixe as configurações padrão e clique em **OK**.
    - **Frequência de cópia** indica que os dados delta (após a replicação inicial) serão replicadas a cada cinco minutos.
    - **Retenção do ponto de recuperação** indica que as janelas de retenção para cada ponto de recuperação serão de duas horas.
    - **Frequência do instantâneo consistente com o aplicativo** indica que os pontos de recuperação contendo instantâneos consistentes com aplicativos serão criados a cada hora.
    - **Hora de início para replicação inicial** indica que a replicação inicial deve começar imediatamente.
4. Depois que a política for criada, clique em **OK**. Quando você cria uma nova política, ela é automaticamente associada ao site do Hyper-V especificado (**ContosoHyperVSite**)

    ![Política de replicação](./media/tutorial-hyper-v-to-azure/replication-policy.png)


## <a name="enable-replication"></a>Habilitar a replicação


1. Em **Replicar aplicativo**, clique em **Origem**. 
2. Em **Origem**, selecione o site **ContosoHyperVSite**. Em seguida, clique em **OK**.
3. Em **Destino**, verifique o Azure como o destino, a assinatura do cofre e o modelo de implantação do **Resource Manager**.
4. Selecione a conta de armazenamento **contosovmsacct1910171607** que criamos no tutorial anterior para os dados replicados e a rede **ContosoASRnet**, na qual as VMs do Azure serão localizadas após o failover.
5. Em **Máquinas Virtuais** > **Selecionar**, selecione a VM que deseja replicar. Em seguida, clique em **OK**.

 É possível acompanhar o progresso da ação **Habilitar Proteção** em **Trabalhos** > **Trabalhos do Site Recovery**. Após a conclusão do trabalho **Finalizar Proteção**, a replicação inicial é concluída, e a máquina virtual estará pronta para failover.

## <a name="next-steps"></a>Próximas etapas
[Realizar uma simulação de recuperação de desastre](tutorial-dr-drill-azure.md)
