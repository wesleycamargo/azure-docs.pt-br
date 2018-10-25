---
title: Configurar replicação para VMs habilitadas para ADE (Azure Disk Encryption) no Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como configurar a replicação para VMs de ADE, de uma região do Azure para outra, usando o Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/28/2018
ms.author: sutalasi
ms.openlocfilehash: 5d1beb124bbb857d13aecad7bf0cef493d42dac5
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043270"
---
# <a name="replicate-azure-disk-encryption-ade-enabled-virtual-machines-to-another-azure-region"></a>Replicar máquinas virtuais habilitadas para ADE (Azure Disk Encryption) para outra região do Azure

Este artigo descreve como habilitar a replicação de VMs habilitadas para ADE (Azure Disk Encryption) de uma região do Azure para outra.

>[!NOTE]
>Somente VMs do Azure executando sistema operacional Windows e [habilitadas para criptografia com o aplicativo do Azure AD](https://aka.ms/ade-aad-app) são compatíveis com o Azure Site Recovery atualmente.
>

## <a name="required-user-permissions"></a>Permissões de usuário exigidas

O usuário deve ter as seguintes permissões para habilitar a replicação de VMs de ADE no portal.
- Permissões de cofre de chave
    - list
    - Criar
    - Obter

-   Permissões secretas de cofre de chave
    - Listar
    - Criar
    - Obter

- Permissões de chave do cofre de chaves (necessárias somente se as VMs usarem a chave de Criptografia de chave para criptografar chaves da Criptografia de disco)
    - Listar
    - Obter
    - Criar
    - Criptografar
    - Descriptografar

Para gerenciar as permissões, navegue para o recurso de cofre de chaves no portal e adicione as permissões necessárias ao usuário.

![keyvaultpermissions](./media/azure-to-azure-how-to-enable-replication-ade-vms/keyvaultpermissions.png)

Se o usuário que estiver habilitando a recuperação de desastre (DR) não tiver as permissões necessárias para copiar as chaves, o script abaixo poderá ser fornecido ao administrador de segurança com as permissões apropriadas para copiar os segredos e as chaves de criptografia para a região de destino.

>[!NOTE]
>Para habilitar a replicação de VMs de ADE do portal, você precisa, pelo menos, de permissões de "Lista" nas chaves, segredos e cofres de chaves
>

## <a name="copy-ade-keys-to-dr-region-using-powershell-script"></a>Copiar chaves de ADE para a região de DR usando o Script do PowerShell

1. Abra o código de script "CopyKeys" bruto em uma janela do navegador clicando [neste link](https://aka.ms/ade-asr-copy-keys-code).
2. Copie o script para um arquivo e nomeie-o como 'Copy-keys.ps1'.
2. Abra o aplicativo Windows PowerShell e vá para o local da pasta onde o arquivo está localizado.
3. Iniciar a 'Copy-keys.ps1'
4. Forneça as credenciais de logon do Azure.
5. Escolha a **assinatura do Azure** de suas VMs.
6. Aguarde o carregamento dos grupos de recursos e, em seguida, escolha o **grupo de recursos** de suas VMs.
7. Escolha as VMs da lista de máquinas virtuais exibidas. Apenas as VMs habilitadas com a criptografia de disco do Azure são mostradas na lista.
8. Escolha o **local de destino**.
9. **Cofres de chaves de criptografia de disco**: por padrão, o Azure Site Recovery cria um novo cofre de chaves na região de destino com nome com sufixo "asr", com base nas chaves de criptografia de disco da VM de origem. Caso o cofre de chaves criado pelo Azure Site Recovery já exista, ele é reutilizado. Se necessário, escolha um cofre de chaves diferente na lista.
10. **Cofres de chaves de criptografia de chave**: por padrão, o Azure Site Recovery cria um novo cofre de chaves na região de destino com nome com sufixo "asr", com base nas chaves de criptografia de chave da VM de origem. Caso o cofre de chaves criado pelo Azure Site Recovery já exista, ele é reutilizado. Se necessário, escolha um cofre de chaves diferente na lista.

## <a name="enable-replication"></a>Habilitar a replicação

Este procedimento pressupõe que a região do Azure primária seja Leste da Ásia e a região secundária seja Sudeste Asiático.

1. No cofre, clique em **+Replicar**.
2. Observe os seguintes campos:
    - **Fonte**: o ponto de origem das VMs que, neste caso, é o **Azure**.
    - **Local de origem**: a região do Azure onde você deseja proteger as máquinas virtuais. Para esta ilustração, a localização da fonte é 'Ásia Oriental'
    - **Modelo de implantação**: o modelo de implantação do Azure de computadores de origem.
    - **Assinatura de origem**: a assinatura à qual suas máquinas virtuais de origem pertencem. Pode ser qualquer assinatura dentro do mesmo locatário do Azure Active Directory na qual existe o cofre dos serviços de recuperação.
    - **Grupo de recursos**: o grupo de recursos ao qual pertencem as máquinas virtuais de origem. Todas as máquinas virtuais do grupo de recursos selecionado são listadas para a proteção na próxima etapa.

3. Em **Máquinas Virtuais > Escolher máquinas virtuais**, clique e escolha cada VM que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Em seguida, clique em **OK**.

4. Em **Configurações**, você também pode definir configurações do site de destino:

    - **Local de destino**: o local onde seus dados de máquina virtual de origem serão replicados. Dependendo de seu local de máquinas selecionadas, a recuperação de Site fornece a lista de regiões de destino adequado. É recomendável que você mantenha o local de destino o mesmo do local do cofre dos Serviços de Recuperação.
    - **Assinatura de destino**: a assinatura de destino usada para recuperação de desastres. Por padrão, a assinatura de destino será o mesma que a assinatura de origem.
    - **Grupo de recursos de destino**: o grupo de recursos ao qual pertencem todas as máquinas virtuais replicadas. Por padrão, o Azure Site Recovery cria um novo grupo de recursos na região de destino com um sufixo "asr". Caso o grupo de recursos criado pelo Azure Site Recovery já exista, ele é reutilizado. Você também poderá escolher personalizá-lo conforme mostrado na seção abaixo. O local do grupo de recursos pode ser qualquer região do Azure exceto a região em que as máquinas virtuais de origem estão hospedadas.
    - **Rede Virtual de Destino**: por padrão, o Site Recovery cria uma nova rede virtual na região de destino com o nome com um sufixo "asr". Isso é mapeado para sua rede de origem e usado para qualquer proteção futura. [Saiba mais](site-recovery-network-mapping-azure-to-azure.md) sobre o mapeamento de rede.
    - **Contas de armazenamento de destino (se a VM de origem não usar discos gerenciados)**: por padrão, o Site Recovery cria uma nova conta de armazenamento de destino imitando sua configuração de armazenamento da VM de origem. Caso a conta de armazenamento já exista, ela é reutilizada.
    - **Discos gerenciados de réplica (se a VM de origem usar discos gerenciados)**: o Site Recovery cria novos discos gerenciados de réplica na região de destino para espelhar os discos gerenciados da VM de origem com o mesmo tipo de armazenamento (Standard ou premium) do disco gerenciado da VM de origem.
    - **Contas de armazenamento em cache**: o Site Recovery precisa de uma conta de armazenamento extra, chamada armazenamento em cache na região de origem. Todas as alterações ocorrendo nas máquinas virtuais de origem são controladas e enviadas para a conta de armazenamento do cache antes de replicar para o local de destino.
    - **Conjunto de disponibilidade**: por padrão, o Azure Site Recovery cria uma nova configuração de disponibilidade na região de destino com o sufixo "asr". Caso o conjunto de disponibilidade criado pelo Azure Site Recovery já exista, ele é reutilizado.
    - **Cofres de chaves de criptografia de disco**: por padrão, o Azure Site Recovery cria um novo cofre de chaves na região de destino com nome com sufixo "asr", com base nas chaves de criptografia de disco da VM de origem. Caso o cofre de chaves criado pelo Azure Site Recovery já exista, ele é reutilizado.
    - **Cofres de chaves de criptografia de chave**: por padrão, o Azure Site Recovery cria um novo cofre de chaves na região de destino com nome com sufixo "asr", com base nas chaves de criptografia de chave da VM de origem. Caso o cofre de chaves criado pelo Azure Site Recovery já exista, ele é reutilizado.
    - **Política de replicação**: define as configurações para histórico de retenção de ponto de recuperação e frequência de instantâneo consistente do aplicativo. Por padrão, o Azure Site Recovery cria uma nova política de replicação com configurações padrão de “24 horas” para retenção de pontos de recuperação e “60 minutos” para a frequência de instantâneo consistente do aplicativo.



## <a name="customize-target-resources"></a>Personalizar os recursos de destino

Você pode modificar as configurações de destino padrão usadas pelo Site Recovery.


1. Clique em **Personalizar:** ao lado de "Assinatura de destino" para modificar a assinatura de destino padrão. Selecione a assinatura na lista de todas as assinaturas disponíveis no mesmo locatário do AAD (Azure Active Directory).

2. Clique em **Personalizar** ao lado de 'Grupo de recursos, armazenamento, rede e conjuntos de disponibilidade' para modificar as seguintes configurações padrão:
    - Em **Grupo de recursos de destino**, selecione o grupo de recursos da lista de todos os grupos de recursos no local de destino da assinatura.
    - Em **Rede virtual de destino**, selecione a rede em uma lista de todas as redes virtuais no local de destino.
    - Em **Conjunto de disponibilidade**, você pode adicionar configurações do conjunto de disponibilidade à VM, caso elas façam parte de um conjunto de disponibilidade na região de origem.
    - Em **Contas de armazenamento de destino**, selecione a conta que você deseja usar.


2. Clique em **Personalizar** ao lado de 'Configurações de criptografia' para modificar as seguintes configurações padrão:
    - Em **Cofre de chaves de criptografia de disco de destino**, escolha o cofre de chaves de criptografia de disco de destino na lista de todos os cofres de chaves no local de destino da assinatura.
  - Em **Cofre de chaves de criptografia de chave de destino**, escolha o cofre de chaves de criptografia de chave de destino na lista de todos os cofres de chaves no local de destino da assinatura.

3. Clique em **Criar o recurso de destino** > **Habilitar Replicação**.
4. Depois que as VMs forem habilitadas para replicação, você poderá verificar o status da integridade da VM em **Itens replicados**

>[!NOTE]
>Durante a replicação inicial, o status pode levar algum tempo para atualizar, sem andamento. Clique no botão **Atualizar** para obter o status mais recente.
>

## <a name="update-target-vm-encryption-settings"></a>Atualizar configurações de criptografia da VM de destino
Nos cenários abaixo, você será solicitado a atualizar as configurações de criptografia da VM de destino.
  - Você habilitou a replicação da recuperação de site na VM e ativou a ADE (Azure Disk Encryption) na VM de origem em uma data posterior
  - Você habilitou a replicação da recuperação de site na VM e alterou a chave de criptografia de disco e/ou a chave de criptografia de chaves na VM de origem em uma data posterior

Você pode usar [o script](#copy-ade-keys-to-dr-region-using-powershell-script) para copiar as chaves de criptografia para a região de destino e atualizar as configurações de criptografia de destino em **Cofre dos serviços de recuperação -> item replicado -> Propriedades -> Computação e Rede.**

![update-ade-settings](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="next-steps"></a>Próximas etapas

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre a execução de failovers de teste.
