---
title: Fazer failback de VMs da IaaS do Azure replicadas para uma região do Azure secundária para recuperação de desastre com o serviço do Azure Site Recovery.
description: Saiba como fazer failback de VMs do Azure com o serviço Azure Site Recovery.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: sideeksh
ms.custom: mvc
ms.openlocfilehash: d8721f313907f0e0519dca52f5565853f1c44110
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089686"
---
# <a name="fail-back-azure-vms-between-azure-regions"></a>Failback de VMs do Azure entre regiões do Azure

O serviço do [Azure Site Recovery](site-recovery-overview.md) contribui para a estratégia de recuperação de desastre, gerenciando e orquestrando replicação, failover e failback de computadores locais e VMs (máquinas virtuais) do Azure.

Este tutorial descreve como fazer failback de uma única VM do Azure. Depois de fazer failover, você fará failback para a região primária quando ela estiver disponível. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> 
> * Fazer failback da VM secundária
> * Proteger novamente a VM primária de volta à região secundária
> 
> [!NOTE]
> 
> Este tutorial destina-se a guiar o usuário pelas etapas de failover para uma região de destino e failback com personalização mínima e, se você quiser saber mais sobre os vários aspectos associados ao failover, incluindo considerações de rede, automação ou solução de problemas, consulte os documentos em 'Instruções' para VMs do Azure.

## <a name="prerequisites"></a>Pré-requisitos

> * Certifique-se de que a VM está com estado de Failover confirmado, verifique se a região primária está disponível e, em seguida, você poderá criar e acessar os novos recursos.
> * Certifique-se de que a nova proteção está habilitada.

## <a name="fail-back-to-the-primary-region"></a>Failback para a região primária

Depois que as VMs estiverem novamente protegidas, você poderá fazer failback para a região primária quando e como quiser.

1. Vá para o Cofre dos Serviços de Recuperação. Clique em Itens Replicados e selecione a VM que foi protegida novamente.

2. Você deverá ver o seguinte. Observe que é semelhante à folha para failover de teste e failover da região primária.
![Failback para primária](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Clique em Failover de Teste para executar um failover de teste de volta à região primária. Escolha o Ponto de Recuperação e a Rede Virtual para o failover de teste e selecione OK. É possível visualizar a VM de teste criada na região primária a qual você poderá acessar e inspecionar.

4. Depois que o Failover de teste estiver satisfatório, você poderá clicar em Limpar failover de teste para limpar os recursos criados na região de origem do failover de teste.

5. Em Itens Replicados, selecione a VM que você quer fazer failover > Failover.

6. Em Failover, selecione um Ponto de Recuperação para failover. Você pode usar uma das seguintes opções:
    1. Mais recente (padrão): Essa opção processa todos os dados no serviço do Site Recovery e fornece o menor RPO (Objetivo de Ponto de Recuperação)
    2. Mais recente processado: Essa opção reverte a máquina virtual para o ponto de recuperação mais recente que foi processado pelo serviço do Site Recovery
    3. Personalizado: Use essa opção para failover em um determinado ponto de recuperação. Essa opção é útil para executar um failover de teste

7. Selecione Desligar máquina antes de iniciar o failover, se você quiser que o Site Recovery tente fazer um desligamento das máquinas virtuais de origem antes de disparar o failover. O failover continuará mesmo o desligamento falhar. Observe que o Site Recovery não limpa a origem após o failover.

8. Acompanhar o andamento do failover na página Trabalhos

9. Após o failover, valide a máquina virtual, efetuando logon nela. Se você quiser ir para outro ponto de recuperação da máquina virtual, use a opção Alterar ponto de recuperação.

10. Quando você estiver satisfeito com o failover que a máquina virtual realizou, poderá Confirmar o failover. Confirmar exclui todos os pontos de recuperação disponíveis com o serviço. A opção Alterar ponto de recuperação não está mais disponível.

![VM nas regiões primária e secundária](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

Se a captura de tela anterior for exibida, isso indicará que foi feito o failover da VM "ContosoWin2016" da região EUA Central para o Leste dos EUA e o failback do Leste dos EUA para EUA Central.

Observe que as VMs de DR permanecerão no estado desalocado de desligamento. Esse comportamento ocorre por design, porque o Azure Site Recovery salva as informações da máquina virtual, que podem ser úteis no failover para o primário na região secundária posteriormente. Você não é cobrado pelas máquinas virtuais desalocadas e, portanto, elas devem ser mantidas no estado em que se encontram.

> [!NOTE]
> Confira a [seção "instruções"](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection) para obter mais detalhes sobre o fluxo de trabalho da nova proteção e o que acontece durante a nova proteção.
