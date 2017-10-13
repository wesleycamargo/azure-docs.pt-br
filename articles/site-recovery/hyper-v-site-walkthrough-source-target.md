---
title: "Configure a origem e o destino da replicação do Hyper-V para o Azure (sem o System Center VMM) com o Azure Site Recovery | Microsoft Docs"
description: "Resume as etapas para definir as configurações de origem e destino para replicação de VMs Hyper-V para armazenamento do Azure com o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d2010d85-77fd-4dea-84f3-1c960ed4c63f
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: b38eb3a011d46f2239891ea1d1bcac2a4059a866
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="step-8-set-up-the-source-and-target-for-hyper-v-replication-to-azure"></a>Etapa 8: configurar a origem e o destino para replicação do Hyper-V para o Azure

Este artigo descreve como definir as configurações de origem e destino ao replicar máquinas virtuais Hyper-V locais (sem o System Center VMM) para o Azure utilizando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Configure o site do Hyper-V, instale o Provedor do Azure Site Recovery e o agente dos Serviços de Recuperação do Azure em hosts do Hyper-V e registre o site no cofre.

1. Em **Preparar a infraestrutura**, clique em **Origem**. Para adicionar um novo site do Hyper-V como um contêiner para seus hosts ou clusters do Hyper-V, clique em **+ Site Hyper-V**.

    ![Configurar origem](./media/hyper-v-site-walkthrough-source-target/set-source1.png)
2. Em **Criar site do Hyper-V**, especifique um nome para o site. Em seguida, clique em **OK**. Agora, selecione o site que você criou e clique em **+Servidor Hyper-V** para adicionar um servidor ao site.

    ![Configurar origem](./media/hyper-v-site-walkthrough-source-target/set-source2.png)

3. Em **Adicionar Servidor** > **Tipo de servidor**, verifique se **Servidor Hyper-V** é exibido.

    - Certifique-se de que o servidor Hyper-V que você deseja adicionar seja compatível com os [pré-requisitos](#on-premises-prerequisites) e seja capaz de acessar as URLs especificadas.
    - Baixe o arquivo de instalação do Provedor do Azure Site Recovery. Você executa esse arquivo para instalar o Provedor e o agente dos Serviços de Recuperação em cada host do Hyper-V.

    ![Configurar origem](./media/hyper-v-site-walkthrough-source-target/set-source3.png)


## <a name="install-the-provider-and-agent"></a>Instalar o Provedor e o agente

1. Execute o arquivo de configuração do Provedor em cada host que você adicionou ao site do Hyper-V. Se você estiver instalando em um cluster do Hyper-V, execute a instalação em cada nó de cluster. Instalar e registrar cada nó de cluster do Hyper-V garante que as VMs estarão protegidas, mesmo se migrarem entre nós.
2. No **Microsoft Update**, você pode aceitar as atualizações para que as atualizações do Provedor sejam instaladas de acordo com a política do Microsoft Update.
3. Em **Instalação**, aceite ou modifique o local de instalação padrão do Provedor e clique em **Instalar**.
4. Em **Configurações do Cofre**, clique em **Procurar** para selecionar o arquivo da chave do cofre baixado. Especifique a assinatura do Azure Site Recovery, o nome do cofre e o site de Hyper-V ao qual pertence o servidor Hyper-V.

    ![Registros do servidor](./media/hyper-v-site-walkthrough-source-target/provider3.png)

5. Em **Configurações de Proxy**, especifique como o Provedor em execução nos hosts Hyper-V se conecta ao Azure Site Recovery pela Internet.

    * Se você quiser que o Provedor conecte diretamente, selecione **Conectar diretamente o Azure Site Recovery sem um proxy**.
    * Se o proxy existente exigir autenticação ou se você quiser usar um proxy personalizado para a conexão ao Provedor, selecione **Conectar o Azure Site Recovery com um servidor proxy**.
    * Se você usar um proxy:
        - Especifique o endereço, a porta e as credenciais
        - Certifique-se de que as URLs descritas nos [pré-requisitos](#prerequisites) sejam permitidas através do proxy.

    ![internet](./media/hyper-v-site-walkthrough-source-target/provider7.png)

6. Quando a instalação terminar, clique em **Registrar** para registrar o servidor no cofre.

    ![Local de instalação](./media/hyper-v-site-walkthrough-source-target/provider2.png)

7. Após a conclusão do registro, os metadados do servidor Hyper-V são recuperados pelo Azure Site Recovery e o servidor é exibido em **Infraestrutura do Site Recovery** > **Hosts Hyper-V**.


## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Especifique a conta de armazenamento do Azure para a replicação e a rede do Azure à qual as VMs do Azure se conectarão após o failover.

1. Clique em **Preparar a Infraestrutura** > **Destino**.
2. Selecione a assinatura e o grupo de recursos em que você deseja criar as VMs do Azure após o failover. Escolha o modelo de implantação que você deseja usar no Azure (clássico ou gerenciamento de recursos) para as VMs.

3. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

    - Se você não tiver uma conta de armazenamento, clique em **+Armazenamento** para criar um conta embutida baseada no Resource Manager. 
    - Se você não tiver uma rede do Azure, clique em **+Rede** para criar um rede embutida baseada no Resource Manager.






## <a name="next-steps"></a>Próximas etapas

Vá para a [Etapa 9: Configurar uma política de replicação](hyper-v-site-walkthrough-replication.md)
