---
title: "Configure a origem e o destino da replicação do Hyper-V para o Azure (com o System Center VMM) com o Azure Site Recovery | Microsoft Docs"
description: "Resume as etapas para definir as configurações de origem e destino para replicação de VMs do Hyper-V em nuvens do VMM para armazenamento do Azure com o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5edb6d87-25a5-40fe-b6f1-ddf7b55a6b31
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/25/2017
ms.author: raynew
ms.openlocfilehash: c72f839d0a1288dccb7deb3e44fc2b20d64818f0
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2017
---
# <a name="step-8-set-up-the-source-and-target-for-hyper-v-with-vmm-replication-to-azure"></a>Etapa 8: Configurar a origem e o destino para replicação do Hyper-V (com o VMM) para o Azure

Depois de [criar um cofre](vmm-to-azure-walkthrough-create-vault.md) e especificar o que você deseja replicar, use este artigo para definir as configurações de origem e de destino ao replicar local máquinas virtuais de Hyper-V nas nuvens do System Center Virtual Machine Manager (VMM) do Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

S1. Clique em **Preparar a Infraestrutura** > **Origem**.

    ![Set up source](./media/vmm-to-azure-walkthrough-source-target/set-source1.png)

2. Em **Preparar origem**, clique em **+ VMM** para adicionar um servidor VMM.

    ![Configurar origem](./media/vmm-to-azure-walkthrough-source-target/set-source2.png)

3. Em **Adicionar Servidor**, verifique se **Servidor System Center VMM** é exibido em **Tipo de servidor** e se o servidor VMM atende aos [pré-requisitos e aos requisitos de URL](#prerequisites).
4. Baixe o arquivo de instalação do Provedor do Azure Site Recovery.
5. Baixe a chave do registro. Você precisará dela quando executar a instalação. A chave é válida por cinco dias após ser gerada.

    ![Configurar origem](./media/vmm-to-azure-walkthrough-source-target/set-source3.png)

## <a name="install-the-provider-on-the-vmm-server"></a>Instalar o Provedor no servidor VMM

1. Execute o arquivo de configuração do provedor no servidor VMM.
2. No **Microsoft Update**, você pode aceitar as atualizações para que as atualizações do Provedor sejam instaladas de acordo com a política do Microsoft Update.
3. Em **Instalação**, aceite ou modifique o local de instalação padrão do Provedor e clique em **Instalar**.

    ![Local de instalação](./media/vmm-to-azure-walkthrough-source-target/provider2.png)
4. Quando a instalação terminar, clique em **Registrar** para registrar o servidor do VMM no cofre.
5. Na página **Configurações do Cofre**, clique em **Procurar** para selecionar o arquivo da chave do cofre. Especifique a assinatura do Azure Site Recovery e o nome do cofre.

    ![Registros do servidor](./media/vmm-to-azure-walkthrough-source-target/provider10.png)
6. Em **Conexão da Internet**, especifique como o Provedor em execução no servidor VMM conectará a Recuperação de Site pela Internet.

   * Se você quiser que o Provedor se conecte diretamente, selecione **Conectar diretamente o Azure Site Recovery sem um proxy**.
   * Se o proxy existente exigir autenticação ou se você quiser usar um proxy personalizado, selecione **Conectar o Azure Site Recovery com um servidor proxy**.
   * Se você usar um proxy personalizado, especifique o endereço, a porta e as credenciais.
   * Se estiver usando um proxy, você já deverá ter concedido as URLs descritas em [pré-requisitos](#on-premises-prerequisites).
   * Se você usar um proxy personalizado, uma conta RunAs VMM (DRAProxyAccount) será criada automaticamente usando as credenciais de proxy especificadas. Configure o servidor proxy para que essa conta possa ser autenticada com êxito. As configurações da conta RunAs VMM podem ser modificadas no console do VMM. Em **Configurações**, expanda **Segurança** > **Contas Executar como** e modifique a senha de DRAProxyAccount. Você precisará reiniciar o serviço VMM para que essa configuração entre em vigor.

     ![internet](./media/vmm-to-azure-walkthrough-source-target/provider13.png)
7. Aceite ou modifique o local de um certificado SSL automaticamente gerado para criptografia de dados. Esse certificado é usado se você habilitar a criptografia de dados para uma nuvem protegida pelo Azure no portal de Recuperação de Site do Azure. Mantenha esse certificado protegido. Quando você executar um failover para o Azure, precisará dele para descriptografar se a criptografia de dados estiver habilitada.
8. Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Em uma configuração de cluster, especifique o nome de função de cluster do VMM.
9. Habilite **Sincronizar metadados de nuvem**, se quiser sincronizar os metadados para todas as nuvens no servidor VMM com o cofre. Esta ação só precisa acontecer uma vez em cada servidor. Se você não quiser sincronizar todas as nuvens, você pode deixar essa configuração desmarcada e sincronizar cada nuvem individualmente nas propriedades da nuvem no console VMM. Clique em **Registrar** para concluir o processo.

    ![Registros do servidor](./media/vmm-to-azure-walkthrough-source-target/provider16.png)
10. O registro é iniciado. Após a conclusão do registro, o servidor é exibido em **Infraestrutura do Site Recovery** > **Servidores VMM**.


## <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Instalar o Agente de Serviços de Recuperação do Azure em hosts do Hyper-V

1. Depois de configurar o Provedor, será necessário baixar o arquivo de instalação do agente de Serviços de Recuperação do Azure. Execute a instalação em cada servidor do Hyper-V na nuvem do VMM.

    ![Sites do Hyper-V](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent1.png)
2. Em **Verificação de Pré-requisitos**, clique em **Avançar**. Quaisquer pré-requisitos faltantes serão instalados automaticamente.

    ![Agente de Serviços de Recuperação de Pré-requisitos](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent2.png)
3. Em **Configurações da Instalação**, aceite ou modifique o local de instalação e o local do cache. Você pode configurar o cache em uma unidade que tenha pelo menos 5 GB de armazenamento disponível, mas é recomendável uma unidade de cache com 600 GB ou mais de espaço livre. Em seguida, clique em **Instalar**.
4. Quando a instalação terminar, clique no botão **Fechar** para concluir.

    ![Registrar o Agente MARS](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent3.png)

### <a name="command-line-installation"></a>Instalação de linha de comando
Você pode instalar o Agente de Serviços de Recuperação do Microsoft Azure por meio da linha de comando usando o comando a seguir:

     marsagentinstaller.exe /q /nu

### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Configurar o acesso de proxy de Internet para a Recuperação de Site de hosts do Hyper-V

O agente dos Serviços de Recuperação em execução em hosts do Hyper-V precisam de acesso à Internet para o Azure para a replicação de VMs. Se você estiver acessando a Internet por meio de um proxy, configure-o da seguinte maneira:

1. Abra o snap-in MMC do Backup do Microsoft Azure no host do Hyper-V. Por padrão, um atalho para o Backup do Microsoft Azure está disponível na área de trabalho ou C:\Arquivos de Programas\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. No snap-in, clique em **Alterar Propriedades**.
3. Na guia **Configuração do Proxy** , especifique as informações do servidor proxy.

    ![Registrar o Agente MARS](./media/vmm-to-azure-walkthrough-source-target/mars-proxy.png)
4. Verifique se o agente pode acessar as URLs descritas nos [pré-requisitos](#on-premises-prerequisites).

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem
Especifique a conta de armazenamento do Azure a ser usada para a replicação e a rede do Azure à qual as VMs do Azure se conectarão após o failover.

1. Clique em **Preparar infraestrutura** > **Destino**, selecione a assinatura e o grupo de recursos no qual deseja criar as máquinas virtuais do failover. Escolha o modelo de implantação que você deseja usar no Azure (clássico ou gerenciamento de recursos) para as máquinas virtuais do failover.

    ![Armazenamento](./media/vmm-to-azure-walkthrough-source-target/enablerep3.png)

2. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

    ![Armazenamento](./media/vmm-to-azure-walkthrough-source-target/compatible-storage.png)

3. Se você não tiver criado uma conta de armazenamento e se desejar criar uma usando o Resource Manager, clique em **+Conta de armazenamento** para fazer isso de forma embutida.  Na folha **Criar conta de armazenamento** , especifique um nome de conta, um tipo, uma assinatura e uma localização. A conta deve estar no mesmo local do que o cofre dos Serviços de Recuperação.

   ![Armazenamento](./media/vmm-to-azure-walkthrough-source-target/gs-createstorage.png)


   * Se você quiser criar uma conta de armazenamento usando o modelo clássico, faça isso no portal do Azure. [Saiba mais](../storage/common/storage-create-storage-account.md)
   * Se você estiver usando uma conta de armazenamento premium para os dados replicados, configure uma conta de armazenamento standard adicional para armazenar os logs de replicação que capturam as alterações contínuas nos dados locais.
5. Se você não tiver criado uma rede do Azure e se quiser criar uma usando o Resource Manager, clique em **+Rede** para fazer isso de forma embutida. Na folha **Criar rede virtual** , especifique um nome de rede, um intervalo de endereços, detalhes de sub-rede, uma assinatura e uma localização. A rede deve estar no mesmo local do que o cofre dos Serviços de Recuperação.

   ![Rede](./media/vmm-to-azure-walkthrough-source-target/gs-createnetwork.png)

   Se você quiser criar uma rede usando o modelo clássico, faça isso no portal do Azure. [Saiba mais](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).





## <a name="next-steps"></a>Próximas etapas

Vá para a [Etapa 9: Configurar o mapeamento de rede](vmm-to-azure-walkthrough-network-mapping.md)
