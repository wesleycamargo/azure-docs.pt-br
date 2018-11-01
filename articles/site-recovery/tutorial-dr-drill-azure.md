---
title: Executar uma análise detalhada da recuperação de desastre em computadores locais para o Azure com o Azure Site Recovery | Microsoft Docs
description: Saiba mais sobre como executar a análise detalhada da recuperação de desastre localmente para o Azure, com o Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 10/29/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b0344095cd7c9aedd360d44f2649f27dfd78cd30
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50214402"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Realizar uma análise detalhada da recuperação de desastre para o Azure

Este artigo mostra como executar uma análise detalhada da recuperação de desastre para máquinas locais no Azure usando um failover de teste. Uma análise detalhada valida sua estratégia de replicação, sem perda de dados.

Este é o quarto tutorial em uma série que mostra como configurar a recuperação de desastres para o Azure para VMs VMware ou VMs do Hyper-V locais.

Este tutorial presume que você concluiu os três primeiros tutoriais:
    - No [primeiro tutorial](tutorial-prepare-azure.md), configuramos os componentes do Azure necessários para a recuperação de desastres do VMware.
    - No [segundo tutorial](vmware-azure-tutorial-prepare-on-premises.md) , preparamos os componentes locais para a recuperação de desastres e analisamos os pré-requisitos.
    - No [terceiro tutorial](vmware-azure-tutorial.md) configuramos e habilitamos a replicação para nossa VM VMware local.
    - Os tutoriais destinam-se a mostrar o **caminho de implantação mais simples para um cenário**. Eles usam opções padrão quando possível e não mostram todas as possíveis configurações e caminhos. Se você quiser saber mais sobre as etapas de failover de teste em mais detalhes, leia o [Guia](site-recovery-test-failover-to-azure.md).

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Configurar uma rede isolada para o failover de teste
> * Preparar-se para conectar-se à VM do Azure após o failover
> * Executar um failover de teste para um único computador



## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Antes de executar um failover de teste, confira as propriedades da VM e verifique se a [VM do Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) ou [VM do VMware](vmware-physical-azure-support-matrix.md#replicated-machines) está em conformidade com os requisitos do Azure.

1. Em **Itens Protegidos**, clique em **Itens Replicados** > e na VM.
2. No painel **Item Replicado**, há um resumo das informações da VM, o status de integridade e os últimos pontos de recuperação disponíveis. Clique em **Propriedades** para exibir mais detalhes.
3. Em **Computação e Rede**, você pode modificar o nome do Azure, o grupo de recursos, o tamanho do destino, o conjunto de disponibilidade e as configurações de disco gerenciado.
4. Você pode exibir ou modificar as configurações de rede, incluindo a rede/sub-rede na qual a VM do Azure estará localizada após o failover e o endereço IP que será atribuído a ela.
5. Em **Discos**, é possível visualizar as informações sobre o sistema operacional e os discos de dados na VM.

## <a name="create-a-network-for-test-failover"></a>Criar uma rede para failover de teste

Recomendamos que para o failover de teste você escolha uma rede isolada da rede do site de recuperação de produção especificada nas configurações de **Computação e Rede** para cada VM. Por padrão, quando você cria uma rede virtual do Azure, ela é isolada de outras redes. Essa rede de teste deve imitar a sua rede de produção:

- A rede de teste deve ter o mesmo número de sub-redes da rede de produção. As sub-redes devem ter os mesmos nomes.
- A rede de teste deve usar o mesmo intervalo de endereços IP.
- Atualize o DNS da rede de teste com o endereço IP especificado para a VM de DNS nas configurações de **Computação e Rede**. Leia as [considerações sobre o failover de teste para o Active Directory](site-recovery-active-directory.md#test-failover-considerations) para obter mais detalhes.

## <a name="run-a-test-failover-for-a-single-vm"></a>Executar um failover de teste para uma VM

Quando você executar um failover de teste, acontecerá o seguinte:

1. Uma verificação de pré-requisitos será executada para conferir se todas as condições exigidas para o failover foram cumpridas.
2. O failover processa os dados para que uma VM do Azure possa ser criada. Se você selecionar o último ponto de recuperação, um ponto de recuperação será criado a partir dos dados.
3. Uma VM do Azure é criada usando os dados processados na etapa anterior.

Execute o failover de teste da seguinte maneira:

1. Em **Configurações** > **Itens Replicados**, clique na VM > **+Failover de Teste**.
2. Selecione o ponto de recuperação **Último processado** para este tutorial. Isto fará failover da VM para o ponto no tempo mais recente disponível. A carimbo de data/hora é mostrado. Com esta opção, não há tempo gasto no processamento de dados, portanto, um RTO (Objetivo de Tempo de Recuperação) baixo é fornecido.
3. Em **Failover de Teste**, selecione a rede de destino do Azure à qual as VMs do Azure serão conectadas após o failover.
4. Clique em **OK** para iniciar o failover. Você pode acompanhar o andamento clicando na VM para abrir suas propriedades. Ou você pode clicar no trabalho **Failover de Teste** no nome do cofre > **Configurações** > **Trabalhos** >
   **Trabalhos do Site Recovery**.
5. Após a conclusão do failover, a réplica da VM do Azure aparece no portal do Azure > **Máquinas Virtuais**. Verifique se a VM é do tamanho apropriado, se está conectada à rede correta e se está em execução.
6. Agora você certamente poderá se conectar à VM replicada no Azure.
7. Para excluir as VMs do Azure criadas durante o failover de teste, clique em **Limpar failover de teste** na VM. Em **Observações**, registre e salve todas as observações associadas ao failover de teste.

Em alguns cenários, o failover requer um processamento adicional que leva cerca de oito a dez minutos para ser concluído. É possível observar um tempo de failover de teste mais longo em computadores VMware Linux, em VMs VMware que não têm o serviço DHCP habilitado e em VMs VMware que não têm os seguintes drivers de inicialização: storvsc, vmbus, storflt, intelide, atapi.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Se você quiser se conectar às VMs do Azure usando RDP/SSH após o failover, siga os requisitos resumidos na tabela mostrada [aqui](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

Siga as etapas descritas [aqui](site-recovery-failover-to-azure-troubleshoot.md) para solucionar quaisquer problemas de conectividade após o failover.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Executar failover e failback de VMs locais do VMware](vmware-azure-tutorial-failover-failback.md).
> [Executar failover e failback de VMs locais do Hyper-V](hyper-v-azure-failover-failback-tutorial.md).
