---
title: Configurar a recuperação de desastre para VMs do Azure para uma região do Azure secundária com o Azure Site Recovery
description: Saiba como configurar a recuperação de desastre para VMs do Azure em uma região do Azure diferente usando o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/18/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: cff6d38867ef8ecaf1435fd4c4cc22fe63d70575
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52283226"
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region"></a>Configurar a recuperação de desastre para VMs do Azure para uma região do Azure secundária

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de recuperação de desastre ao gerenciar e orquestrar a replicação, o failover e o failback de computadores locais e de VMs (máquinas virtuais) do Azure.

Este tutorial mostra como configurar a recuperação de desastre em uma região secundária do Azure para VMs do Azure. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um cofre dos Serviços de Recuperação
> * Verificar as configurações de recursos de destino
> * Configurar o acesso de saída para VMs
> * Habilitar a replicação para uma VM

> [!NOTE]
> Este artigo fornece instruções para implantar a recuperação de desastre com as configurações mais simples. Caso queira saber mais sobre as configurações personalizadas, analise os artigos na [seção Como fazer](azure-to-azure-how-to-enable-replication.md). o

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Verifique se você entende os [componentes e a arquitetura do cenário](concepts-azure-to-azure-architecture.md).
- Examine os [requisitos de suporte](site-recovery-support-matrix-azure-to-azure.md) de todos os componentes.

## <a name="create-a-vault"></a>Criar um cofre

Crie o cofre em qualquer região, exceto a região de origem.

1. Entre no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
2. Clique em **Criar um recurso** > **Monitoramento e Gerenciamento** > **Backup e Site Recovery**.
3. Em **Nome**, especifique um nome amigável para identificar o cofre. Se você tiver mais de uma assinatura, selecione uma delas.
4. Crie um grupo de recursos ou selecione um existente. Especifique uma região do Azure. Para verificar as regiões com suporte, confira a disponibilidade geográfica nos [Detalhes dos Preços de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Para acessar rapidamente o cofre no painel, clique em **Fixar no painel** e em **Criar**.

   ![Novo cofre](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   O novo cofre é adicionado ao **Painel** em **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

## <a name="verify-target-resources"></a>Verifique os recursos de destino

1. Verifique se a sua assinatura do Azure permite criar VMs na região de destino. Contate o suporte para habilitar a cota necessária.
2. Verifique se a sua assinatura tem recursos suficientes para dar suporte a tamanhos de VMs que correspondem às VMs de origem. O Site Recovery escolhe o mesmo tamanho, ou o tamanho mais próximo possível, para a VM de destino.

## <a name="configure-outbound-network-connectivity"></a>Configurar a conectividade de rede de saída

Para o Site Recovery funcionar conforme o esperado, você precisa modificar a conectividade de rede de saída das VMs que deseja replicar.

> [!NOTE]
> O Site Recovery não dá suporte ao uso de um proxy de autenticação para controlar a conectividade de rede.



### <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Caso esteja usando um proxy de firewall baseado em URL para controlar a conectividade de saída, permita acesso a essas URLs.

| **URL** | **Detalhes** |
| ------- | ----------- |
| *.blob.core.windows.net | Permite que os dados sejam gravados da VM para a conta de armazenamento de cache da região de origem. |
| login.microsoftonline.com | Fornece autorização e autenticação para as URLs do serviço Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Permite que a VM se comunique com o serviço Site Recovery. |
| * .servicebus.windows.net | Permite que a VM grave o monitoramento do Site Recovery e os dados de diagnóstico. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Caso queira controlar a conectividade de saída usando endereços IP em vez de URLs, dê permissão a esses endereços para regras de proxy, NSG ou firewalls baseados em IP.

  - [Intervalos de IP do datacenter do Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653)
  - [Intervalos de IP do datacenter do Microsoft Azure na Alemanha](https://www.microsoft.com/en-us/download/details.aspx?id=54770)
  - [Intervalos de IP do datacenter do Microsoft Azure na China](https://www.microsoft.com/en-us/download/details.aspx?id=42064)
  - [Intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Endereços IP de ponto de extremidade de serviço do Site Recovery](https://aka.ms/site-recovery-public-ips)

É possível usar esse [script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702) para criar as regras de NSG necessárias.

## <a name="verify-azure-vm-certificates"></a>Verifique os certificados de VM do Azure

Verifique se as VMs que você deseja replicar contam com os certificados raiz mais recentes. Em caso negativo, a VM não poderá ser registrada no Site Recovery devido a restrições de segurança.

- Para VMs Windows, instale todas as atualizações do Windows mais recentes na VM para que todos os certificados raiz confiáveis estejam no computador. Em um ambiente desconectado, siga os processos padrão do Windows Update e de atualização de certificado para sua organização.
- Para VMs Linux, siga as diretrizes fornecidas pelo seu distribuidor Linux para obter os certificados raiz confiáveis mais recentes e a lista de certificados revogados na VM.

## <a name="set-permissions-on-the-account"></a>Definir permissões na conta

O Azure Site Recovery fornece três funções internas para controlar as operações de gerenciamento do Site Recovery.

- **Colaborador do Site Recovery** -essa função tem todas as permissões necessárias para gerenciar operações do Azure Site Recovery em um cofre dos Serviços de Recuperação. No entanto, um usuário com essa função não pode criar ou excluir um cofre dos Serviços de Recuperação ou atribuir direitos de acesso a outros usuários. Essa função é ideal para os administradores de recuperação de desastre que podem habilitar e gerenciar a recuperação de desastre para aplicativos ou organizações inteiras.

- **Operador do Site Recovery** - Essa função tem permissões para executar e gerenciar operações de Failover e Failback. Um usuário com essa função não pode habilitar ou desabilitar a replicação, criar ou excluir cofres, registrar nova infraestrutura ou atribuir direitos de acesso a outros usuários. Essa função é ideal para um operador de recuperação de desastre que pode executar failover em máquinas virtuais ou aplicativos quando instruído por administradores de TI ou proprietários do aplicativo em uma situação de desastre real ou simulada. Após a resolução do desastre, o operador de recuperação de desastre pode proteger e fazer failback das máquinas virtuais novamente.

- **Leitor do Site Recovery**: essa função tem permissões para exibir todas as operações de gerenciamento do Site Recovery. Essa função é ideal para um executivo de monitoramento de TI que possa monitorar o estado atual de proteção e gerar tíquetes de suporte.

Saiba mais sobre as [funções internas do RBAC do Azure](../role-based-access-control/built-in-roles.md)

## <a name="enable-replication"></a>Habilitar a replicação

### <a name="select-the-source"></a>Selecione a origem

1. Em cofres dos Serviços de Recuperação, clique no nome do cofre > **+Replicar**.
2. Em **Fonte**, selecione **Azure**.
3. Em **Local de origem**, selecione a fonte de região do Azure em que suas VMs estão sendo executados.
4. Selecione a **Assinatura de origem** na qual as máquinas virtuais estão em execução. Pode ser qualquer assinatura dentro do mesmo locatário do Azure Active Directory na qual existe o cofre dos serviços de recuperação.
5. Selecione o **Grupo de recursos de origem** para VMs do Gerenciador de Recursos ou **serviço de nuvem** para VMs clássicas.
6. Clique em **OK** para salvar as configurações.

### <a name="select-the-vms"></a>Selecione as VMs

O Site Recovery recupera uma lista das VMs associadas ao serviço de nuvem/grupo de recursos e assinatura.

1. Em **Máquinas Virtuais**, selecione as VMs que deseja replicar.
2. Clique em **OK**.

### <a name="configure-replication-settings"></a>Definir configurações de replicação

O Site Recovery cria as configurações padrão e a política de replicação para a região de destino. É possível alterar as configurações conforme o necessário.

1. Clique em **Configurações** para exibir o destino e as configurações de replicação.
2. Para substituir as configurações de destino padrão, clique em **Personalizar** ao lado de **Grupo de recursos, Rede, Armazenamento e Conjuntos de Disponibilidade**.

  ![Configurar definições](./media/azure-to-azure-tutorial-enable-replication/settings.png)


3. Personalize as configurações de destino da seguinte maneira:

    - **Assinatura de destino**: a assinatura de destino usada para recuperação de desastres. Por padrão, a assinatura de destino será o mesma que a assinatura de origem. Clique em "Personalizar" para selecionar uma assinatura de destino diferente no mesmo locatário do Active Directory do Azure.
    - **Localização de destino**: a região de destino usada para recuperação de desastre. É recomendável que a localização de destino corresponda à localização do cofre do Site Recovery.
    - **Grupo de recursos de destino**: o grupo de recursos na região de destino que contém as VMs do Azure após o failover. Por padrão, o Site Recovery cria um novo grupo de recursos na região de destino com um sufixo "asr". local do grupo de recurso do grupo de recurso de destino pode ser de qualquer região exceto a região onde as suas máquinas virtuais de origem estão hospedadas.
    - **Rede virtual de destino**: a rede na região de destino na qual as VMs estarão localizadas após o failover.
      Por padrão, o Site Recovery cria uma nova rede virtual (e sub-redes) na região de destino com um sufixo "asr".
    - **Contas de armazenamento de cache**: o Site Recovery utiliza uma conta de armazenamento na região de origem. As alterações às VMs de origem são enviadas para essa conta, antes da replicação para a localização de destino.
      >[!NOTE]
      >Caso esteja usando a conta de armazenamento de cache com firewall habilitado, assegure-se de “Permitir serviços confiáveis da Microsoft”. [Saiba mais.](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
      >

    - **Contas de armazenamento de destino (se a VM de fonte não usar discos gerenciados)**: por padrão, o Site Recovery cria uma nova conta de armazenamento na região de destino, para espelhar a conta de armazenamento da VM de origem.
      >[!NOTE]
      >Caso esteja usando a conta de armazenamento de origem ou destino com firewall habilitado, assegure-se de “Permitir serviços confiáveis da Microsoft”. [Saiba mais.](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
      >

    - **Discos gerenciados de réplica (se a VM de origem usar discos gerenciados)**: por padrão, o Site Recovery cria novos discos gerenciados de réplica na região de destino para espelhar os discos gerenciados da VM de origem com o mesmo tipo de armazenamento (Standard ou premium) do disco gerenciado da VM de origem.
    - **Conjuntos de disponibilidade de destino**: por padrão, o Site Recovery cria um novo conjunto de disponibilidade na região de destino, com o sufixo "asr". Somente será possível adicionar conjuntos de disponibilidade se as VMs forem parte de um conjunto na região de origem.

4. Para personalizar as configurações de política de replicação, clique em **Personalizar** ao lado da **Política de replicação** e modifique as configurações a seguir conforme o necessário:

    - **Nome da política de replicação**: nome da política.
    - **Retenção de ponto de recuperação**: por padrão, o Site Recovery mantém os pontos de recuperação por 24 horas. É possível configurar um valor entre 1 e 72 horas.
    - **Frequência de instantâneo consistente com o aplicativo**: por padrão, o Site Recovery obtém um instantâneo consistente com o aplicativo a cada 4 horas. É possível configurar qualquer valor entre 1 e 12 horas. Um instantâneo consistente com o aplicativo é um instantâneo em um ponto no tempo dos dados do aplicativo dentro da VM. O VSS (Serviço de Cópias de Sombra de Volume) garante que o aplicativo na VM esteja em um estado consistente quando o instantâneo for criado.
    - **Grupo de replicação**: se seu aplicativo precisa de consistência de VMs múltiplas entre VMs, você pode criar um grupo de replicação para essas VMs. Por padrão, as VMs selecionadas não fazem parte de nenhum grupo de replicação.

5. Em **Personalizar**, selecione **Sim** para a consistência de várias VMs caso queira adicionar VMs a um grupo de replicação novo ou existente. para tornar as VMs parte de um grupo de replicação. Em seguida, clique em **OK**.

    - Todos os computadores de um grupo de replicação terão pontos de recuperação consistentes com o aplicativo e consistentes com falhas quando passarem por failover. Habilitar a consistência de VMs múltiplas pode afetar o desempenho da carga de trabalho e só deve ser usada se os computadores estão executando a mesma carga de trabalho e se você precisa de consistência entre vários computadores.
    - Se você habilitar a consistência de várias VMs, as máquinas virtuais no grupo de replicação se comunicarão entre si pela porta 20004. Veja se não há nenhum dispositivo de firewall bloqueando a comunicação interna entre as VMs através da porta 20004. Se você quiser que VMs do Linux façam parte de um grupo de replicação, abra manualmente o tráfego de saída na porta 20004, de acordo com as diretrizes da versão específica do Linux.

### <a name="configure-encryption-settings"></a>Definir configurações de criptografia

Se a máquina virtual de origem tiver o ADE (Azure Disk Encryption) habilitado, as configurações de criptografia serão exibidas:

1. Analise as configurações de criptografia.
    - **Cofres de chaves de criptografia de disco**: por padrão, o Azure Site Recovery cria um novo cofre de chaves na região de destino com nome com sufixo "asr", com base nas chaves de criptografia de disco da VM de origem. Caso o cofre de chaves criado pelo Azure Site Recovery já exista, ele é reutilizado.
    - **Cofres de chaves de criptografia de chave**: por padrão, o Azure Site Recovery cria um novo cofre de chaves na região de destino com nome com sufixo "asr", com base nas chaves de criptografia de chave da VM de origem. Caso o cofre de chaves criado pelo Azure Site Recovery já exista, ele é reutilizado.

2. Clique em **Personalizar** para selecionar cofres de chaves personalizados.

>[!NOTE]
>Somente VMs do Azure executando sistemas operacionais Windows e [habilitadas para criptografia com o aplicativo do Azure AD](https://aka.ms/ade-aad-app) são compatíveis com o Azure Site Recovery atualmente.
>

### <a name="track-replication-status"></a>Acompanhar o status de replicação

1. Em **Configurações**, clique em **Atualizar** para obter o status mais recente.
2. Acompanhe o progresso e o status da seguinte maneira:
    - Acompanhe o progresso do trabalho **Habilitar proteção** em **Configurações** > **Trabalhos** > **Trabalhos do Site Recovery**.
    - Em **Configurações** > **Itens Replicados**, você pode exibir o status das máquinas virtuais e o andamento da replicação inicial. Clique em VM para fazer uma busca detalhada em suas configurações.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou a recuperação de desastre para uma VM do Azure. Agora é possível iniciar uma análise de recuperação de desastres para verificar que se o failover está funcionando conforme o esperado.

> [!div class="nextstepaction"]
> [Realizar uma simulação de recuperação de desastre](azure-to-azure-tutorial-dr-drill.md)
