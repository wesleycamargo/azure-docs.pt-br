---
title: "Antes de iniciar a replicação de VMs Azure para outra região | Microsoft Docs'"
description: "Resume as etapas necessárias antes de replicar VMs do Azure entre regiões do Azure, utilizando o serviço do Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: d38fc766d5226be7161433555da9622e006c80e9
ms.contentlocale: pt-br
ms.lasthandoff: 08/31/2017

---

# <a name="step-2-before-you-start"></a>Etapa 2: antes de iniciar

Após ter revisado a [arquitetura](azure-to-azure-walkthrough-architecture.md) para replicar VMs (máquinas virtuais) do Azure entre regiões do Azure com o [Azure Site Recovery](site-recovery-overview.md), utilize este artigo para verificar os pré-requisitos.

- Ao concluir artigo, você deverá ter uma compreensão clara do que é necessário para que a implantação funcione e ter concluído as etapas de pré-requisitos.
- Publique eventuais comentários no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

>[!NOTE]
>
> Atualmente, a replicação de VM do Azure está em versão prévia.



## <a name="support-recommendations"></a>Recomendações de suporte

Revise a tabela abaixo. Obter uma lista completa de requisitos de suporte na [matriz de suporte](site-recovery-support-matrix-azure-to-azure.md).

**Componente** | **Requisito**
--- | ---
**Cofre dos Serviços de Recuperação** | É recomendável que você crie um cofre de serviços de recuperação na região do Azure de destino que você deseja utilizar para recuperação de desastres. Por exemplo, se você quiser replicar VMs de origem no leste dos EUA para o EUA central, crie o cofre no EUA Central.
**Assinatura do Azure** | Sua assinatura do Azure deve ser habilitada para criar VMs, na localização de destino que deseja utilizar como região de recuperação de desastres. Contate o suporte para habilitar a cota necessária.
**Capacidade da região de destino** | Na região do Azure de destino, a assinatura deve ter capacidade suficiente para VMs, contas de armazenamento e componentes de rede.
**Armazenamento** | Utilize as [diretrizes de armazenamento](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) para VMs virtuais de origem, de modo a evitar problemas de desempenho.<br/><br/> As contas de armazenamento devem estar na mesma região que o cofre.<br/><br/> Você não pode replicar contas premium no Sul e no Centro da Índia.<br/><br/> Se você implantar a replicação com as configurações padrão, o Site Recovery cria as contas de armazenamento necessárias com base na configuração de origem. Se você personalizar as configurações, siga os [destinos de escalabilidade para discos de VM](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks).
**Rede** | É necessário permitir a conectividade de saída das VMs do Azure para intervalos de IP/URLs específicos.<br/><br/> As contas de rede devem estar na mesma região que o cofre.
**VM do Azure** | Certifique-se de que todos os certificados raiz mais recentes estão na VM do Azure do Windows/Linux. Se não estiverem, não será possível registrar a VM no Site Recovery, devido a restrições de segurança.
**Conta de usuário do Azure** | Sua conta de usuário do Azure precisa ter certas [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para habilitar a replicação de uma nova máquina virtual para o Azure.


## <a name="set-permissions-on-the-account"></a>Definir permissões na conta

1. Leia sobre as [permissões](site-recovery-role-based-linked-access-control.md) necessárias para replicação.
2. Sega essas [instruções](../active-directory/role-based-access-control-configure.md#add-access) para adicionar permissões.


## <a name="verify-target-resources"></a>Verifique os recursos de destino

1. Habilite sua assinatura do Azure para permitir criar VMs na região de destino que deseja utilizar para recuperação de desastres e que deseja usar como região de recuperação de desastres. Contate o suporte para habilitar a cota necessária.
2. Certifique-se de que a sua assinatura possui recursos suficientes para habilitar VMs com tamanhos que correspondem às VMs de origem. Por padrão, ao configurar a replicação, o Site Recovery escolhe o mesmo tamanho para a VM de destino ou o tamanho mais próximo possível. Saiba mais sobre a [solução de problemas](site-recovery-azure-to-azure-troubleshoot-errors.md#azure-resource-quota-issues-error-code-150097) de recursos de destino.

## <a name="verify-azure-vm-certificates"></a>Verifique os certificados de VM do Azure

1. Verifique se todos os certificados raiz mais recentes estão presentes nas VMs do Windows ou Linux que deseja replicar. Se os certificados raiz mais recentes não estiverem presentes, a VM não poderá ser registrada para a recuperação de Site devido a restrições de segurança.
2. Para VMs do Windows, faça o seguinte:

    - Instale todas as atualizações do Windows mais recentes na VM para que todos os certificados raiz confiáveis estejam na máquina.
    - Em um ambiente desconectado, siga o processo de atualização do certificado/processo do Windows Update padrão em sua organização para obter os certificados de raiz mais recentes e, a CRL atualizada, nas VMs.
3. Para VMs do Linux, siga as diretrizes fornecidas pelo seu distribuidor Linux para obter os certificados raiz confiáveis mais recentes e a última lista de certificados revogados na VM. Saiba mais sobre a [solução de problemas](site-recovery-azure-to-azure-troubleshoot-errors.md#trusted-root-certificates-error-code-151066) para problemas de raiz confiável.


## <a name="next-steps"></a>Próximas etapas

Acesse a[Etapa 3: Planejar rede](azure-to-azure-walkthrough-network.md) para estabelecer conectividade de saída.

