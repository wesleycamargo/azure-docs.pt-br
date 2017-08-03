---
title: "Instalar o Serviço de mobilidade para a replicação de servidor físico para o Azure | Microsoft Docs"
description: "Este artigo descreve como instalar o agente do Serviço de mobilidade em servidores físicos que são replicados para o Azure com o serviço Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 3189fbcd-6b5b-4ffb-b5a9-e2080c37f9d9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: d73267d7a64221a3138af19e9a2d5dd15809b927
ms.contentlocale: pt-br
ms.lasthandoff: 06/29/2017

---

# <a name="step-9-install-the-mobility-service"></a>Etapa 9: instalar o Serviço de mobilidade manualmente


Este artigo descreve como instalar o componente do Serviço de mobilidade durante a replicação de servidores físicos Windows/Linux locais para o Azure, usando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

O Serviço de mobilidade captura gravações de dados em um computador e as encaminha para o servidor em processo. Ele deve ser instalado em cada servidor que você deseja replicar para o Azure.

Instale o Serviço de mobilidade manualmente, usando uma instalação por push por meio do servidor em processo do Site Recovery quando a replicação for habilitada ou usando uma ferramenta como o System Center Configuration Manager. Se você usar a instalação por push, o serviço será instalado no servidor quando você habilitar a replicação.

Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="install-manually"></a>Instalar manualmente

1. Verifique os [pré-requisitos](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites) para instalação manual.
2. Siga [estas instruções](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) para a instalação manual usando o portal.
3. Se preferir instalar por meio da linha de comando, siga [estas instruções](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt).

## <a name="install-from-the-process-server"></a>Instalar por meio do servidor em processo

Se você desejar enviar a instalação do Serviço de mobilidade por push por meio do servidor em processo quando habilitar a replicação em um computador, precisará de uma conta que pode ser usada pelo servidor em processo para acessar o computador. A conta é usada apenas para a instalação por push.

1. Se você não tiver criado uma conta, faça isso usando estas diretrizes:

    - Você pode usar uma conta local ou de domínio
    - Para Windows, se não estiver usando uma conta de domínio, precisará desabilitar o controle de Acesso de Usuário Remoto no computador local. Para fazer isso, no registro, em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy** com um valor de 1.
    - Se desejar adicionar a entrada do Registro do Windows por meio de uma CLI, digite:

        ```
        REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.
        ```

    - Para Linux, a conta deve ser a raiz no servidor Linux de origem.

2. Em seguida, siga [estas instruções](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) se desejar enviar o Serviço de mobilidade por push para VMs que executam o Windows ou o Linux.

## <a name="other-installation-methods"></a>Outros métodos de instalação

- [Saiba mais sobre](site-recovery-install-mobility-service-using-sccm.md) como instalar o Serviço de mobilidade usando o Configuration Manager
- [Saiba mais sobre](site-recovery-automate-mobility-service-install.md) como instalar com o DSC de Automação do Azure.


## <a name="next-steps"></a>Próximas etapas

Ir para a [Etapa 10: Habilitar a replicação](physical-walkthrough-enable-replication.md)

