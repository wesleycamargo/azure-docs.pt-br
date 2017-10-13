---
title: "Configurar a origem e destino de replicação do Hyper-V para um site secundário com o Azure Site Recovery | Microsoft Docs"
description: "Descreve como configurar a origem e o destino ao replicar VMs do Hyper-V para um site de VMM secundário com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: fa7809f1-7633-425f-b25d-d10d004e8d0b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 07135e9b5e619971a59cc22ec68a0a4e8bcaabe1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-set-up-the-replication-source-and-target"></a>Etapa 6: Configurar a origem e o destino de replicação


Depois de criar um cofre para os Serviços de Recuperação para replicação do Hyper-V em um site secundário do VMM com o [Azure Site Recovery](site-recovery-overview.md), use este artigo para configurar os locais de replicação de origem e de destino. 

Depois de ler este artigo, poste comentários na parte inferior ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).




## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Instale o Provedor do Azure Site Recovery nos servidores VMM e descubra e registre os servidores no cofre.

1. Clique em **Etapa 1: Preparar a Infraestrutura** > **Origem**.

    ![Configurar origem](./media/vmm-to-vmm-walkthrough-source-target/goals-source.png)
2. Em **Preparar origem**, clique em **+ VMM** para adicionar um servidor VMM.

    ![Configurar origem](./media/vmm-to-vmm-walkthrough-source-target/set-source1.png)
3. Em **Adicionar Servidor**, verifique se **Servidor System Center VMM** é exibido em **Tipo de servidor** e se o servidor VMM atende aos [pré-requisitos](#prerequisites).
4. Baixe o arquivo de instalação do Provedor do Azure Site Recovery.
5. Baixe a chave do registro. Você precisará dela quando executar a instalação. A chave é válida por cinco dias após ser gerada.

    ![Configurar origem](./media/vmm-to-vmm-walkthrough-source-target/set-source3.png)
6. Instale o Provedor do Azure Site Recovery no servidor do VMM de origem. Você não precisa instalar nada explicitamente nos servidores de host do Hyper-V.


## <a name="install-the-azure-site-recovery-provider"></a>Instalar o Provedor de Recuperação do Site do Azure

1. Execute o arquivo de configuração do Provedor em cada servidor VMM. Se o VMM é implantado em um cluster, faça o seguinte na primeira vez que você instala:
    -  Instale o provedor em um nó ativo e conclua a instalação para registrar o servidor VMM no cofre.
    - Em seguida, instale o Provedor nos outros nós. Nós de cluster devem todos executar a mesma versão do provedor.
2. A instalação executa algumas verificações de pré-requisito e solicita permissão para interromper o serviço do VMM. O serviço VMM será reiniciado automaticamente quando a instalação for finalizada. Se estiver instalando em um cluster do VMM, será solicitado que você pare a função de Cluster.
3. No **Microsoft Update**, você pode aceitar que as atualizações do Provedor sejam instaladas de acordo com a política do Microsoft Update.
4. Em **Instalação**, aceite ou modifique o local de instalação padrão e clique em **Instalar**.

    ![Local de instalação](./media/vmm-to-vmm-walkthrough-source-target/provider-location.png)
5. Quando a instalação for concluída, clique em **Registrar** para registrar o servidor no cofre.

    ![Local de instalação](./media/vmm-to-vmm-walkthrough-source-target/provider-register.png)
6. Em **Nome do cofre**, verifique o nome do cofre para o qual o servidor será registrado. Clique em *Próximo*.

    ![Registros do servidor](./media/vmm-to-vmm-walkthrough-source-target/vaultcred.png)
7. Em **Conexão com a Internet**, especifique como o provedor em execução no servidor VMM se conecta ao Azure.

    ![Configurações da Internet](./media/vmm-to-vmm-walkthrough-source-target/proxydetails.png)

   - Você pode especificar que o provedor deve se conectar diretamente à internet ou por meio de um proxy.
   - Especifique as configurações de proxy, se necessário.
   - Se você usar um proxy, uma conta RunAs do VMM (DRAProxyAccount) será criada automaticamente usando as credenciais de proxy especificadas. Configure o servidor proxy para que essa conta possa ser autenticada com êxito. As configurações de conta executar como podem ser modificadas no console do VMM > **configurações** > **segurança** > **contas executar como**. Reinicie o serviço VMM para atualizar as alterações.
8. Em **Chave de Registro**, selecione a chave que você baixou no Azure Site Recovery e copiou para o servidor VMM.
9. A configuração de criptografia é usada somente quando você estiver replicando VMs do Hyper-V em nuvens do VMM no Azure. Se estiver replicando para um site secundário, ela não é usada.
10. Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Em uma configuração de cluster, especifique o nome de função de cluster do VMM.
11. Em **Sincronizar metadados de nuvem**, selecione se você deseja sincronizar os metadados para todas as nuvens no servidor VMM com o cofre. Esta ação só precisa acontecer uma vez em cada servidor. Se você não desejar sincronizar todas as nuvens, deixe essa configuração desmarcada e sincronize cada nuvem individualmente nas propriedades da nuvem no console do VMM.
12. Clique em **Avançar** para concluir o processo. Após o registro, os metadados do servidor VMM é recuperado pela Recuperação de Site do Azure. O servidor é exibido na guia **Servidores VMM** da página **Servidores** no cofre.

    ![Servidor](./media/vmm-to-vmm-walkthrough-source-target/provider13.png)
13. Depois que o servidor estiver disponível no console de Recuperação de Site, em **Origem** > **Preparar origem** selecione o servidor VMM e selecione a nuvem na qual o host Hyper-V está localizado. Em seguida, clique em **OK**.

Você também pode instalar o provedor usando a linha de comando:

[!INCLUDE [site-recovery-rw-provider-command-line](../../includes/site-recovery-rw-provider-command-line.md)]


## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Selecione o servidor VMM e a nuvem de destino:

1. Clique em **Preparar infraestrutura** > **Destino** e selecione o servidor VMM de destino que você deseja usar.
2. Nuvens no servidor sincronizadas com a Recuperação de Site serão exibidas. Selecione a nuvem de destino.

   ![Destino](./media/vmm-to-vmm-walkthrough-source-target/target-vmm.png)



## <a name="next-steps"></a>Próximas etapas

Acesse a [Etapa 7: Configurar o mapeamento de rede](vmm-to-vmm-walkthrough-network-mapping.md).
