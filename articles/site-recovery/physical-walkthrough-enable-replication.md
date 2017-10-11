---
title: "Habilitar a replicação de servidores físicos que são replicados para o Azure com o Azure Site Recovery | Microsoft Docs"
description: "Resume as etapas necessárias para habilitar a replicação de servidores físicos para o Azure, usando o serviço Azure Site Recovery"
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 519c5559-7032-4954-b8b5-f24f5242a954
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 42f35c53eec06a346281fd90c97aecfd2269307d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2017
---
# <a name="step-10-enable-replication-for-physical-servers-to-azure"></a>Etapa 10: Habilitar a replicação de servidores físicos para o Azure


Este artigo descreve como habilitar a replicação de servidores físicos Windows/Linux locais para o Azure, usando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Antes de começar

- Os servidores devem ter o [componente Serviço de mobilidade instalado](physical-walkthrough-install-mobility.md).
- Se uma VM for preparada para a instalação por push, o servidor de processo instalará automaticamente o serviço de Mobilidade quando você habilitar a replicação.
- Quando você habilita um computador para replicação, sua conta de usuário do Azure precisa de [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) específicas para garantir que você está apto a usar o armazenamento do Azure e criar VMs do Azure.
- Quando você adiciona ou modifica endereços IP de servidores, pode levar 15 minutos ou mais para que as alterações entrem em vigor e sejam exibidas no portal.


## <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

Por padrão, todos os discos em um computador são replicados. Você pode excluir discos da replicação. Por exemplo, talvez você não queira replicar discos com dados temporários ou dados atualizados cada vez que um computador ou um aplicativo é reiniciado (por exemplo, pagefile.sys ou SQL Server tempdb). [Saiba mais](site-recovery-exclude-disk.md)

## <a name="replicate-servers"></a>Replicar servidores

1. Clique em **Etapa 2: replicar aplicativo** > **Origem**.
2. Em **Origem**, selecione o servidor de configuração.
3. Em **Tipo de máquina**, selecione **Máquinas Físicas**.
4. Selecione o servidor de processo. Se você não criou nenhum servidor de processo adicional, esse será o servidor de configuração. Em seguida, clique em **OK**.
5. Em **Destino**, selecione a assinatura e o grupo de recursos em que você deseja criar as VMs com failover. Escolha o modelo de implantação que você deseja usar no Azure (clássico ou gerenciamento de recursos) para as VMs do failover.
6. Selecione a conta de armazenamento do Azure que você deseja usar para replicar os dados. Se não quiser usar uma conta que já configurou, você poderá criar uma nova.
7. Selecione a **Rede** e a **Sub-rede do Azure** às quais as VMs do Azure serão conectadas após o failover. Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todos os computadores selecionados para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por computador. Se não quiser usar uma rede existente, você poderá criar uma.

    ![Habilitar a replicação](./media/physical-walkthrough-enable-replication/targetsettings.png)

8. Em **Computadores físicos**, clique em **+Computador físico** e insira o **Nome** e **Endereço IP**. Escolha o sistema operacional do computador que você deseja replicar. Demora alguns minutos até que os computadores sejam descobertos e mostrados na lista.
9. Em **Propriedades** > **Configurar propriedades**, selecione a conta que será usada pelo servidor de processo para instalar automaticamente o serviço de Mobilidade no computador.
10. Por padrão, todos os discos são replicados. Clique em **Todos os Discos** e desmarque os discos que você não deseja replicar. Em seguida, clique em **OK**. Você pode definir propriedades de VM adicionais posteriormente.

    ![Habilitar a replicação](./media/physical-walkthrough-enable-replication/enable-replication6.png)
11. Em **Configurações de replicação** > **Definir configurações de replicação**, verifique se a política de replicação correta está selecionada. Se você modificar uma política, as alterações serão aplicadas à máquina de replicação e às novas máquinas.
12. Habilite **Consistência de várias VMs** se você quiser reunir computadores em um grupo de replicação e especifique um nome para o grupo. Em seguida, clique em **OK**. Observe que:

    * Os computadores nos grupos de replicação são replicados em conjunto e têm pontos de recuperação consistentes compartilhados com o aplicativo e com falhas quando executam failover.
    * Recomendamos que você reúna os servidores físicos, de forma que eles espelhem as cargas de trabalho. Habilitar a consistência de várias VMs pode afetar o desempenho da carga de trabalho e só deve ser usada se os computadores estão executando a mesma carga de trabalho e precisam de consistência.

13. Clique em **Habilitar a Replicação**. Você pode acompanhar o progresso do trabalho **Habilitar Proteção** em **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.

## <a name="next-steps"></a>Próximas etapas

Acesse a [Etapa 11: Executar um failover de teste](physical-walkthrough-test-failover.md)
