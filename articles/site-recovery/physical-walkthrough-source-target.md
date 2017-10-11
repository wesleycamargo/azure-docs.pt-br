---
title: "Configurar a origem e o destino para replicação de servidor físico para Azure com o Azure Site Recovery | Microsoft Docs"
description: "Resume as etapas para definir as configurações de origem e destino para replicação de servidores físicos para armazenamento do Azure com o serviço do Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 2e3d03bc-4e53-4590-8a01-f702d3cc9500
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: e89bbf5a2c1d71852e49da43d3106a05ebfc28a8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2017
---
# <a name="step-7-set-up-the-source-and-target-for-physical-server-replication-to-azure"></a>Etapa 7: Configurar a origem e o destino para replicação de servidor físico para Azure

Este artigo descreve como definir as configurações de origem e destino ao replicar servidores físicos locais para Azure utilizando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Configure o servidor de configuração, registre-o no cofre e descubra computadores.

1. Clique em **Site Recovery** > **Etapa 1: preparar a infraestrutura** > **Origem**.
2. Se não tiver um servidor de configuração, clique em **+Servidor de configuração**.
3. Em **Adicionar Servidor**, verifique se **Servidor de Configuração** aparece em **Tipo de servidor**.
4. Baixe o arquivo de instalação Configuração Unificada da Recuperação de Site.
5. Baixe a chave do registro do cofre. Você precisará dela quando executar a Configuração Unificada. A chave é válida por cinco dias após ser gerada.

   ![Configurar origem](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>Registrar o servidor de configuração no cofre

Faça o descrito a seguir antes de começar, então execute a Configuração Unificada para instalar o servidor de configuração, o servidor de processo e o servidor de destino mestre. O vídeo descreve a configuração dos componentes para VM do VMware para replicação do Azure. No entanto, o mesmo processo é válido ao servidor físico para replicação do Azure.

- Na VM do servidor de configuração, certifique-se de que o relógio do sistema esteja sincronizado com um [Servidor de Horário](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Ele deve ser correspondente. Se ele estiver 15 minutos adiantado ou atrasado, a instalação poderá falhar.
- Execute a instalação como um Administrador Local na máquina do servidor de configuração.
- Verifique se TLS 1.0 está habilitado na VM.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> O servidor de configuração também pode ser instalado [da linha de comando](http://aka.ms/installconfigsrv).




## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Antes de configurar o ambiente de destino, certifique-se de ter uma conta de armazenamento do Azure e uma rede virtual configurada.

1. Clique em **Preparar infraestrutura** > **Destino** e selecione a assinatura do Azure que você deseja usar.
2. Especifique se o seu modelo de implantação de destino é baseada no Gerenciador de Recursos ou clássico.
3. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

   ![Destino](./media/physical-walkthrough-source-target/gs-target.png)

4. Se não tiver criado uma conta de armazenamento ou de rede, clique em **+Conta de armazenamento** ou **+Rede** para criar uma conta do Gerenciador de Recursos ou embutida de rede.

## <a name="next-steps"></a>Próximas etapas

Vá para [Etapa 8: Configurar uma política de replicação](physical-walkthrough-replication.md)
