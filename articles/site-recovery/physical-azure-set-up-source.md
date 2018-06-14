---
title: Configurar o ambiente de origem (servidores físicos para Azure) | Microsoft Docs
description: Este artigo descreve como configurar seu ambiente local para iniciar a replicação de servidores físicos executando Windows ou Linux no Azure.
services: site-recovery
documentationcenter: ''
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 96004a70547c4bfb3a1a3bfadecb1304e4910b52
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
ms.locfileid: "29811133"
---
# <a name="set-up-the-source-environment-physical-server-to-azure"></a>Configurar o ambiente de origem (servidor físico para Azure)

Este artigo descreve como configurar seu ambiente local para iniciar a replicação de servidores físicos executando Windows ou Linux no Azure.

## <a name="prerequisites"></a>pré-requisitos

O artigo supõe que você já tenha:
1. Um cofre de Serviços de Recuperação no [Portal do Azure](http://portal.azure.com "Portal do Azure").
3. Um computador físico no qual instalar o servidor de configuração.

### <a name="configuration-server-minimum-requirements"></a>Requisitos mínimos do servidor de configuração
A tabela a seguir lista os requisitos mínimos de hardware, software e rede para um servidor de configuração.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Os servidores proxy baseados em HTTPS não são compatíveis com o servidor de configuração.

## <a name="choose-your-protection-goals"></a>Escolher as metas de proteção

1. No Portal do Azure, vá até a folha de cofres dos **Serviços de Recuperação** e selecione seu cofre.
2. No menu **Recurso** do cofre, clique em **Introdução** > **Site Recovery** > **Etapa 1: Preparar Infraestrutura** > **Meta de proteção**.

    ![Escolher metas](./media/physical-azure-set-up-source/choose-goals.png)
3. Em **Meta de proteção**, escolha **Para o Azure** e **Não virtualizado/Outro** e clique em **OK**.

    ![Escolher metas](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

1. Em **Preparar origem**, se você não tiver um servidor de configuração, clique em **+Servidor de configuração** para adicionar um.

  ![Configurar origem](./media/physical-azure-set-up-source/plus-config-srv.png)
2. Na folha **Adicionar Servidor**, verifique se o **Servidor de Configuração** é exibido no **Tipo de servidor**.
4. Baixe o arquivo de instalação Configuração Unificada da Recuperação de Site.
5. Baixe a chave do registro do cofre. Você precisa da chave de registro ao executar a Instalação Unificada. A chave é válida por cinco dias após ser gerada.

    ![Configurar origem](./media/physical-azure-set-up-source/set-source2.png)
6. No computador que você está usando como o servidor de configuração, execute a **Instalação Unificada do Azure Site Recovery** para instalar o servidor de configuração, o servidor de processo e o servidor de destino mestre.

#### <a name="run-azure-site-recovery-unified-setup"></a>Executar a Configuração Unificada do Azure Site Recovery

> [!TIP]
> O registro do servidor de configuração falhará se o relógio do sistema do computador estiver com uma diferença de mais de cinco minutos da hora local. Sincronize o relógio do sistema com um [servidor de horário](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) antes de iniciar a instalação.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> O servidor de configuração pode ser instalado por meio de uma linha de comando. Para obter mais informações, confira [Instalando o servidor de configuração usando ferramentas de linhas de comando](http://aka.ms/installconfigsrv).


## <a name="common-issues"></a>Problemas comuns

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Próximas etapas

A próxima etapa envolve a [configuração do ambiente de destino](physical-azure-set-up-target.md) no Azure.
