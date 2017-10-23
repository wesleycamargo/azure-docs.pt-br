---
title: "Executar um failover de teste para a replicação de VMs do Hyper-V para um site secundário com o Azure Site Recovery | Microsoft Docs"
description: "Descreve como executar um failover de teste para replicação de uma VM do Hyper-V para um site secundário do System Center VMM com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a3fd11ce-65a1-4308-8435-45cf954353ef
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 23d235d326273e7ec59feee6588a39f685401e52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="step-10-run-a-test-failover-for-hyper-v-replication-to-a-secondary-site"></a>Etapa 10: Executar um failover de teste para replicação do Hyper-V para um site secundário


Após habilitar a replicação para máquinas virtuais do Hyper-V (VMs) com o [Azure Site Recovery](site-recovery-overview.md), use este artigo para executar um failover de teste. Um failover de teste verifica se a replicação está funcionando, sem afetar o ambiente de produção. 


Depois de ler este artigo, poste comentários na parte inferior ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Antes de começar

- Ao disparar um failover de teste, você pode especificar a rede à qual as VMs de réplica de teste se conectarão. [Saiba mais](site-recovery-test-failover-vmm-to-vmm.md#network-options-in-site-recovery) sobre as opções de rede.
- É recomendável não escolher uma rede que foi selecionada durante o mapeamento de rede.
- As instruções deste artigo descrevem como efetuar failover de uma única VM. Leia sobre como [Criar planos de recuperação](site-recovery-create-recovery-plans.md) se você quiser efetuar o failover de várias VMs juntas.
- Leia sobre [limitações para failovers de teste](site-recovery-test-failover-vmm-to-vmm.md#things-to-note).
- O endereço IP fornecido a uma VM durante o failover de teste é o mesmo endereço IP que a VM receberia em um failover planejado ou não planejado (supondo que o endereço IP esteja disponível na rede de failover de teste). Se o endereço IP não está disponível na rede de failover de teste, a VM recebe outro endereço IP que está disponível na rede de failover de teste.
- Se você quiser fazer um failover de teste na rede de produção, para a validação completa da máquina de conectividade de rede de ponta a ponta, observe o seguinte:
    - A VM primária deverá ser desligada quando você fizer o failover de teste. Caso contrário, duas VMs com a mesma identidade serão executadas na mesma rede ao mesmo tempo. 
    - Se você fizer alterações em VMs de teste, essas alterações serão perdidas quando você limpar o failover de teste. Essas alterações não são replicadas de volta na VM primária.
    - Testar uma rede de produção resulta em tempo de inatividade para as cargas de trabalho de produção. Peça aos usuários que não usem o aplicativo quando a análise de recuperação de desastre estiver em andamento.  


## <a name="run-a-test-failover-for-a-vm"></a>Executar um failover de teste para uma VM

1. Para fazer failover em uma única VM, em **Itens Replicados**, clique na VM > **Failover de Teste**.
2. Em **Failover de Teste**, especifique como as VMs de teste serão conectadas a redes após o failover de teste. 
3. Clique em **OK** para iniciar o failover. Acompanhar o progresso na guia **Trabalhos** .
5. Após o failover ser concluído, verifique se as VMs de teste são iniciadas com êxito.
6. Quando terminar, clique em **Failover de teste de limpeza** no plano de recuperação.
7. Em **Observações**, registre e salve todas as observações associadas ao failover de teste. Essa etapa exclui as máquinas virtuais e redes virtuais que foram criadas durante o failover de teste.


## <a name="next-steps"></a>Próximas etapas

Depois de testar a implantação, saiba mais sobre outros tipos de [failover](site-recovery-failover.md).
