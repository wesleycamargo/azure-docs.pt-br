---
title: "Planejar a capacidade e o dimensionamento da replicação de VM do Hyper-V (com o VMM) para o Azure com o Azure Site Recovery | Microsoft Docs"
description: Use este artigo para planejar a capacidade e a escala ao replicar VMs do Hyper-V em nuvens do VMM para o Azure com o Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 41c3c83e-6b1a-496a-8179-498c552ef0c7
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: ab1dc21a829140f8cd2e57837d83a05b0d71bcdf
ms.contentlocale: pt-br
ms.lasthandoff: 07/26/2017

---

# <a name="step-3-plan-capacity-and-scaling-for-hyper-v-with-vmm-to-azure-replication"></a>Etapa 3: Planejar a capacidade e o dimensionamento para replicação do Hyper-V (com VMM) para o Azure

Depois de examinar os [pré-requisitos de implantação](vmm-to-azure-walkthrough-prerequisites.md) use este artigo para saber como planejar a capacidade e o dimensionamento ao replicar VMs locais do Hyper-V em nuvens do System Center Virtual Machine Manager (VMM) para o Azure com o [Azure Site Recovery](site-recovery-overview.md).

Depois de ler este artigo, poste comentários no final ou faça perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="how-do-i-start-capacity-planning"></a>Como iniciar o planejamento de capacidade?


Você coleta informações sobre o ambiente de replicação e, em seguida, planeja a capacidade usando estes dados, junto com as considerações destacadas neste artigo.


## <a name="gather-information"></a>Coletar informações

1. Reunir informações sobre seu ambiente de replicação, inclusive VMs, discos por VMs e armazenamento por disco.
2. Identifique sua taxa de alteração (variação) diária de dados replicados. Baixe a [ferramenta de planejamento de capacidade do Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para obter a taxa de alteração. Recomendamos que você execute essa ferramenta durante uma semana para capturar as médias.
 

## <a name="figure-out-capacity"></a>Descobrir a capacidade

Com base nas informações coletadas, você executa o [Planejador de Capacidade do Site Recovery](http://aka.ms/asr-capacity-planner-excel) para analisar o ambiente de origem e as cargas de trabalho, estimar as necessidades de largura de banda e os recursos do servidor para o local de origem, bem como os recursos (máquinas virtuais e armazenamento, etc.) necessários no local de destino. Você pode executar a ferramenta em vários modos:

- Planejamento rápido: execute a ferramenta nesse modo para obter as projeções de rede e de servidor baseadas no número médio de VMs, de discos, de armazenamento e da taxa de alteração.
- Planejamento detalhado: execute a ferramenta nesse modo e forneça detalhes de cada carga de trabalho no nível da VM. Analise a compatibilidade da VM e obtenha as projeções de rede e de servidor.

Agora execute a ferramenta:

1. Baixar a [ferramenta](http://aka.ms/asr-capacity-planner-excel)
2. Para executar o planejador rápido, siga [estas instruções](site-recovery-capacity-planner.md#run-the-quick-planner) e selecione o cenário **Hyper-V para o Azure**.
3. Para executar o planejador detalhado, siga [estas instruções](site-recovery-capacity-planner.md#run-the-detailed-planner) e selecione o cenário **Hyper-V para o Azure**.

## <a name="control-network-bandwidth"></a>Controlar largura de banda da rede

Depois de calcular a largura de banda necessária, você tem algumas opções para controlar a quantidade de largura de banda usada para replicação:

* **Restringir a largura de banda**: o tráfego do Hyper-V replicado para o Azure passa por um host específico do Hyper-V. Você pode limitar a largura de banda no servidor host.
* **Influenciar a largura de banda**: você pode influenciar a largura de banda usada para replicação usando algumas chaves do Registro.

### <a name="throttle-bandwidth"></a>Restringir a largura de banda
1. Abra o snap-in MMC do Backup do Microsoft Azure no servidor host do Hyper-V. Por padrão, um atalho para o Backup do Microsoft Azure está disponível na área de trabalho ou C:\Arquivos de Programas\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. No snap-in, clique em **Alterar Propriedades**.
3. Na guia **Limitação**, selecione **Habilitar limitação de uso de largura de banda da Internet para operações de backup** e defina os limites do horário comercial e não comercial. Os intervalos válidos são de 512 Kbps a 102 Mbps por segundo.

    ![Restringir a largura de banda](./media/vmm-to-azure-walkthrough-capacity/throttle2.png)

Você também pode usar o cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para definir a limitação. Veja um exemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica que nenhuma limitação é necessária.

### <a name="influence-network-bandwidth"></a>Influência da largura de banda de rede
1. No Registro, navegue até **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Para influenciar o tráfego de largura de banda em um disco de replicação, modifique o valor de **UploadThreadsPerVM**ou crie a chave caso ela não exista.
   * Para influenciar a largura de banda para o tráfego de failback do Azure, modifique o valor **DownloadThreadsPerVM**.
2. O valor padrão é 4. Em uma rede "sobreprovisionada", os valores padrão dessas chaves do registro precisam ser alterados. O máximo é 32. Monitore o tráfego para otimizar o valor.

## <a name="next-steps"></a>Próximas etapas

Ir para a [Etapa 4: Planejar a rede](vmm-to-azure-walkthrough-network.md).

