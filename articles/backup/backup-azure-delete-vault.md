---
title: Excluir um cofre de serviços de recuperação no Azure
description: Descreve como excluir um cofre dos serviços de recuperação.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: raynew
ms.openlocfilehash: 94d66e28f8edbda6c41dcceaf427d7d7d869c90f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620110"
---
# <a name="delete-a-recovery-services-vault"></a>Excluir um cofre dos Serviços de Recuperação

Este artigo descreve como excluir uma [Backup do Azure](backup-overview.md) o cofre dos serviços de recuperação. Ele contém instruções para remover dependências e, em seguida, excluir um cofre e excluir um cofre por força.


## <a name="before-you-start"></a>Antes de começar

Antes de começar, é importante entender que você não pode excluir um cofre dos serviços de recuperação com servidores registrados nele, ou que contém dados de backup.

- Para excluir um cofre, normalmente, cancelar o registro de servidores nele, remova os dados do cofre e, em seguida, exclua o cofre.
- Se você tentar excluir um cofre que ainda tem dependências, uma mensagem de erro é emitida. e você precisará remover manualmente as dependências de cofre, incluindo:
    - Itens submetidos a backup
    - Servidores protegidos
    - Fazer backup de servidores de gerenciamento (servidor de Backup do Azure, o DPM) ![selecione seu cofre para abrir seu painel](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)
- Se você não quiser reter dados no cofre de serviços de recuperação e deseja excluir o cofre, você pode excluir o cofre por força.
- Se você tentar excluir um cofre, mas não for possível, o cofre ainda estará configurado para receber dados de backup.


## <a name="delete-a-vault-from-the-azure-portal"></a>Excluir um cofre do portal do Azure

1. Abra o painel do cofre.  
2. No painel, clique em **excluir**. Verifique se que você deseja excluir.

    ![selecione seu cofre para abrir o painel dele](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

Se você receber um erro, remova [itens de backup](#remove-backup-items), [servidores de infraestrutura](#remove-backup-infrastructure-servers), e [pontos de recuperação](#remove-azure-backup-agent-recovery-points)e, em seguida, exclua o cofre.

![Excluir erro de cofre](./media/backup-azure-delete-vault/error.png)


## <a name="delete-the-recovery-services-vault-by-force"></a>Excluir o cofre dos Serviços de Recuperação à força

Você pode excluir um cofre por força com o PowerShell. Exclusão forçada significa que o cofre e todos os respectivos dados de backup é excluído permanentemente.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


Para excluir um cofre por força:

1. Entrar em sua assinatura do Azure com o `Connect-AzAccount` de comando e siga na tela direções.

   ```powershell
    Connect-AzAccount
   ```
2. Na primeira vez que você usar o Backup do Azure, deverá registrar o provedor de serviços de recuperação do Azure em sua assinatura com [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider).

   ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

3. Abra uma janela do PowerShell com privilégios de Administrador.
4. Use `Set-ExecutionPolicy Unrestricted` para remover quaisquer restrições.
5. Execute o seguinte comando para baixar o pacote do Cliente do Azure Resource Manager de chocolately.org.

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

6. Use o seguinte comando para instalar o Cliente da API do Azure Resource Manager.

   `choco.exe install armclient`

7. No portal do Azure, colete o ID e o recurso grupo nome da assinatura para o cofre que você deseja excluir.
8. No PowerShell, execute o seguinte comando usando sua ID da assinatura, o nome do grupo de recursos e o nome do cofre. Quando você executa o comando, ele exclui o cofre e todas as dependências.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
9. Se o cofre não vazio, você receber o erro "Cofre não pode ser excluído pois há recursos existentes dentro do cofre". Para remover contido dentro de um cofre, faça o seguinte:

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

10. No portal do Azure, verifique se que o cofre é excluído.


## <a name="remove-vault-items-and-delete-the-vault"></a>Remova itens do cofre e excluir o cofre

Esses procedimento fornece alguns exemplos de remoção de dados de backup e servidores de infraestrutura. Depois que tudo seja removido de um cofre, você pode excluí-lo.

### <a name="remove-backup-items"></a>Remover itens de backup

Esse procedimento fornece um exemplo que mostra como remover dados de backup de arquivos do Azure.

1. Clique em **itens de Backup** > **Azure Storage (arquivos do Azure)**

    ![selecione o tipo de backup](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

2. Cada item de arquivos do Azure para remover e, em seguida, clique com o botão direito **parar backup**.

    ![selecione o tipo de backup](./media/backup-azure-delete-vault/stop-backup-item.png)


3. Na **parar Backup** > **escolha uma opção**, selecione **excluir dados de Backup**.
4. Digite o nome do item e, em seguida, clique em **parar backup**. 
   - Isso confirma que você deseja excluir o item.
   - O **parar Backup** botão é ativado depois de verificar.
   - Se você mantém e não exclua os dados, você não poderá excluir o cofre.

     ![Excluir dados de backup](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Opcionalmente, forneça um motivo por que você está excluindo os dados e adicionar comentários.
6. Para verificar se o trabalho de exclusão é concluída, verifique as mensagens do Azure ![Excluir dados de backup](./media/backup-azure-delete-vault/messages.png).
7. Depois que o trabalho for concluído, o serviço envia uma mensagem: **o processo de backup foi interrompido e os dados de backup foram excluídos**.
8. Depois de excluir um item na lista, no menu **Itens de Backup**, clique em **Atualizar** para ver os itens no cofre.

      ![Excluir dados de backup](./media/backup-azure-delete-vault/empty-items-list.png)


### <a name="remove-backup-infrastructure-servers"></a>Remova os servidores de infraestrutura de backup

1. No menu do painel do cofre, clique em **infraestrutura de Backup**.
2. Clique em **servidores de gerenciamento de Backup** para exibir os servidores. 

    ![selecione seu cofre para abrir o painel dele](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

2. Clique com o botão direito > **excluir**.

    ![selecione o tipo de backup](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

3. . Na **parar Backup** > **escolha uma opção**, selecione **excluir dados de Backup**.
4. Digite o nome do item e, em seguida, clique em **parar backup**. 
   - Isso confirma que você deseja excluir o item.
   - O **parar Backup** botão é ativado depois de verificar.
   - Se você mantém e não exclua os dados, você não poderá excluir o cofre.

     ![Excluir dados de backup](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Opcionalmente, forneça um motivo por que você está excluindo os dados e adicionar comentários.
6. Para verificar se o trabalho de exclusão é concluída, verifique as mensagens do Azure ![Excluir dados de backup](./media/backup-azure-delete-vault/messages.png).
7. Depois que o trabalho for concluído, o serviço envia uma mensagem: **o processo de backup foi interrompido e os dados de backup foram excluídos**.
8. Depois de excluir um item na lista, no menu **Itens de Backup**, clique em **Atualizar** para ver os itens no cofre.


### <a name="remove-azure-backup-agent-recovery-points"></a>Remover pontos de recuperação de agente de Backup do Azure

1. No menu do painel do cofre, clique em **infraestrutura de Backup**.
2. Clique em **servidores de gerenciamento de Backup** para exibir os servidores de infraestrutura.

    ![selecione seu cofre para abrir o painel dele](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. Na lista **Servidores Protegidos**, clique em Agente de Backup do Azure.

    ![selecione o tipo de backup](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Clique no servidor na lista de servidores protegidos usando o agente de Backup do Azure.

    ![selecione o servidor protegido específico](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. No painel do servidor selecionado, clique em **excluir**.

    ![exclua o servidor selecionado](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. No menu **Excluir**, digite o nome do item e clique em **Excluir**.
   - Isso confirma que você deseja excluir o item.
   - O **parar Backup** botão é ativado depois de verificar.
   - Se você mantém e não exclua os dados, você não poderá excluir o cofre.

     ![Excluir dados de backup](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. Opcionalmente, forneça um motivo por que você está excluindo os dados e adicionar comentários.
8. Para verificar se o trabalho de exclusão é concluída, verifique as mensagens do Azure ![Excluir dados de backup](./media/backup-azure-delete-vault/messages.png).
7. Depois que o trabalho for concluído, o serviço envia uma mensagem: **o processo de backup foi interrompido e os dados de backup foram excluídos**.
8. Depois de excluir um item na lista, no menu **Itens de Backup**, clique em **Atualizar** para ver os itens no cofre.






### <a name="delete-the-vault-after-removing-dependencies"></a>Excluir o cofre após a remoção de dependências

1. Quando todas as dependências foram removidas, role até a **Essentials** painel no menu do cofre.
2. Verifique se que não haja nenhum **itens de Backup**, **fazer Backup de servidores de gerenciamento**, ou **itens replicados** listados. Se itens ainda aparecerem no cofre, removê-los.

2. Quando não houver nenhum outro item no cofre, clique em **Excluir** no painel do cofre.

    ![Excluir dados de backup](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Para verificar se você deseja excluir o cofre, clique em **Sim**. O cofre é excluído e o portal retorna para o menu de serviço **Novo** .

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>E se eu parar o processo de backup, mas mantiver os dados?

Se você parar o processo de backup, mas manter acidentalmente os dados, você deve excluir os dados de backup conforme descrito nas seções anteriores.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre](backup-azure-recovery-services-vault-overview.md) cofres dos serviços de recuperação.
