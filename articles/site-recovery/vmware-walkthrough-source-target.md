---
title: "Configurar a origem e o destino de replicação de VMware para o Azure com o Azure Site Recovery | Microsoft Docs"
description: "Resume as etapas para definir as configurações de origem e destino para replicação de VMs VMware para armazenamento do Azure com o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d99e422e-daf7-4fa8-af3c-af2340340136
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 94b629a62c3a54eee69ee397b2f27e3f20b753d5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2017
---
# <a name="step-8-set-up-the-source-and-target-for-vmware-replication-to-azure"></a>Etapa 8: Configurar a origem e o destino para replicação de VMware para o Azure

Este artigo descreve como definir as configurações de origem e destino ao replicar máquinas virtuais VMware locais para Azure utilizando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Defina o servidor de configuração, registre-o no cofre e descubra VMs.

1. Clique em **Site Recovery** > **Etapa 1: preparar a infraestrutura** > **Origem**.
2. Se não tiver um servidor de configuração, clique em **+Servidor de configuração**.
3. Em **Adicionar Servidor**, verifique se **Servidor de Configuração** aparece em **Tipo de servidor**.
4. Baixe o arquivo de instalação Configuração Unificada da Recuperação de Site.
5. Baixe a chave do registro do cofre. Você precisará dela quando executar a Configuração Unificada. A chave é válida por cinco dias após ser gerada.

   ![Configurar origem](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>Registrar o servidor de configuração no cofre

Faça o descrito a seguir antes de começar, então execute a Configuração Unificada para instalar o servidor de configuração, o servidor de processo e o servidor de destino mestre.
    - Obter uma rápida visão geral em vídeo

        > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video1-Source-Infrastructure-Setup/player]

    - Na VM do servidor de configuração, certifique-se de que o relógio do sistema esteja sincronizado com um [Servidor de Horário](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Ele deve ser correspondente. Se ele estiver 15 minutos adiantado ou atrasado, a instalação poderá falhar.
    - Execute a instalação como um Administrador Local na VM do servidor de configuração.
    - Verifique se TLS 1.0 está habilitado na VM.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> O servidor de configuração também pode ser instalado [da linha de comando](http://aka.ms/installconfigsrv).



## <a name="connect-to-vmware-servers"></a>Conectar a servidores VMware

Para permitir que o Azure Site Recovery descubra máquinas virtuais em execução no ambiente local, você precisa conectar seu VMware vCenter Server ou hosts vSphere ESXi ao Site Recovery. Observe o seguinte antes de começar:

- Se você adicionar o servidor vCenter ou hosts vSphere ao Site Recovery com uma conta sem privilégios de administrador no servidor, a conta precisará ter esses privilégios habilitados:
    - Datacenter, Repositório de Dados, Pasta, Host, Rede, Recurso, Máquina Virtual, vSphere Distributed Switch.
    - O servidor vCenter precisa das permissões de exibição do Armazenamento.
- Quando você adiciona servidores VMware ao Site Recovery, pode levar 15 minutos ou mais para que eles apareçam no portal.

### <a name="add-the-account-for-automatic-discovery"></a>Adicione a conta para descoberta automática

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="set-up-a-connection"></a>Configurar uma conexão

Conecte-se aos servidores da seguinte maneira:

1. Selecione **+vCenter** para iniciar a conexão de um VMware vCenter Server ou um host VMware vSphere ESXi.
2. Em **Adicionar vCenter**, especifique um nome amigável para o host vSphere ou servidor vCenter e então especifique o endereço IP ou o FQDN do servidor.
3. Deixe a porta como 443, a menos que os servidores do VMware estejam configurados para escutar solicitações em uma porta diferente. Selecione a conta que deve se conectar ao VMware vCenter ou ao servidor vSphere ESXi. Clique em **OK**.
4. A Site Recovery conecta-se a servidores VMware usando as configurações especificadas e descobre VMs.

> [!NOTE]
> Se você estiver adicionando um servidor ou host vCenter com uma conta que não tem privilégios de administrador no vCenter ou no servidor host, verifique se a conta tem estes privilégios habilitados: Data center, Armazenamento de Dados, Pasta, Host, Rede, Recursos, Máquina virtual e Comutador Distribuído do vSphere. Além disso, o servidor VMware vCenter precisa do privilégio de Exibição do Armazenamento habilitado.


## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Antes de configurar o ambiente de destino, certifique-se de ter uma conta de armazenamento do Azure e uma rede virtual configurada.

1. Clique em **Preparar infraestrutura** > **Destino** e selecione a assinatura do Azure que você deseja usar.
2. Especifique se o seu modelo de implantação de destino é baseada no Gerenciador de Recursos ou clássico.
3. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

   ![Destino](./media/vmware-walkthrough-source-target/gs-target.png)
4. Se não tiver criado uma conta de armazenamento ou de rede, clique em **+Conta de armazenamento** ou **+Rede** para criar uma conta do Gerenciador de Recursos ou embutida de rede.

## <a name="next-steps"></a>Próximas etapas

Vá para a [Etapa 9: Configurar uma política de replicação](vmware-walkthrough-replication.md)
