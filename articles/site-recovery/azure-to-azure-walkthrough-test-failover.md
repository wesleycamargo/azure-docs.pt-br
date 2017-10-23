---
title: "Executar um failover de teste para replicação de VM do Azure com o Azure Site Recovery | Microsoft Docs"
description: "Resume as etapas necessárias para executar um failover de teste para VMs do Azure replicando para outra região do Azure utilizando o serviço do Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: e15c1b0c-5d75-4fdf-acb0-e61def9e9339
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: 8babb0d016729f318442af93596d206c38d91206
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-run-a-test-failover-for-azure-vm-replication"></a>Etapa 6: executar um failover de teste para replicação de VM do Azure

Após habilitar a replicação para VMs (máquinas virtuais) do Azure, siga as etapas neste artigo para executar o failover de teste de uma região do Azure para outra, utilizando o serviço do [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

- Ao concluir o artigo, você deverá ter verificado com um failover de teste que pelo menos uma VM do Azure pode fazer failover para a região do Azure secundária. 
- Publique eventuais comentários no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

>[!NOTE]
>
> Atualmente, a replicação de VM do Azure está em versão prévia.


## <a name="before-you-start"></a>Antes de começar

- Antes de executar um failover de teste, é recomendável verificar as propriedades da VM e efetuar as alterações necessárias. É possível acessar as propriedades da VM em **Itens replicados**. A folha **Conceitos básicos** mostra as informações sobre as configurações e o status dos computadores.
- É recomendável utilizar uma rede de VM do Azure separada para o failover de teste e não a rede (padrão ou personalizada) que foi configurada para failover de produção.
- O failover de teste falha em VMs do Azure (e seu armazenamento) na região do Azure secundária. Não replica quaisquer aplicativos ou recursos dependentes. Se os aplicativos em execução com falhas nas VMs dependem de outros recursos, como o Active Directory ou o DNS, isso também será necessário replicar, se eles ainda não estiverem disponíveis em sua região secundária. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-active-directory-and-dns)
- Se você quiser acessar VMs replicadas após o failover de um site local será necessário [preparar para conectar](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) a essas VMs.

## <a name="run-a-test-failover"></a>Execute um teste de failover

1. Em **Configurações** > **Itens Replicados**, clique no ícone da VM **+ Failover de Teste**. 

2. Em **Failover de Teste**, selecione um ponto de recuperação para usar no failover:

    - **Processado mais recente**: falha na VM para o ponto de recuperação único que foi processado pelo serviço do Site Recovery. A carimbo de data/hora é mostrado. Com essa opção, nenhum tempo é gasto em processamento de dados, portanto, fornece um RTO (Objetivo do Tempo de Recuperação) baixo.
    - **Consistente com o aplicativo mais recente**: essa opção falha em todas as VMs para o ponto de recuperação consistente com o aplicativo mais recente. A carimbo de data/hora é mostrado. 
    - **Personalizar**: selecione qualquer ponto de recuperação.
 
3. Selecione a rede virtual de destino do Azure para qual as VMs do Azure na região secundária serão conectadas, após o failover ocorrer.
4. Para iniciar o failover, clique em **Ok**. Para acompanhar o progresso, clique na VM para abrir suas propriedades. Ou você pode clicar no trabalho **Failover de Teste** no nome do cofre > **Configurações** > **Trabalhos** > **Trabalhos do Site Recovery**.
5. Após a conclusão do failover, a réplica da VM do Azure aparece no portal do Azure > **Máquinas Virtuais**. Verifique se a VM é do tamanho apropriado, se está conectada à rede adequada e se está em execução.
6. Para excluir as VMs que foram criadas durante o failover de teste, clique em **Failover de teste de limpeza** no item replicado ou no plano de recuperação. Em **Observações**, registre e salve todas as observações associadas ao failover de teste. 

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre failovers de teste.

## <a name="next-steps"></a>Próximas etapas

Depois de testar o failover, esse passo a passo estará concluído. Agora, saiba mais sobre a execução de failovers em produção:

- [Saiba mais](site-recovery-failover.md) sobre diferentes tipos de failovers e como executá-los.
- Saiba mais sobre a falha em várias VMs[utilizando um plano de recuperação](site-recovery-create-recovery-plans.md).
- Saiba mais sobre [utilizar planos de recuperação](site-recovery-create-recovery-plans.md).
- Saiba mais sobre [proteger novamente as VMs do Azure](site-recovery-how-to-reprotect.md) após o failover.

