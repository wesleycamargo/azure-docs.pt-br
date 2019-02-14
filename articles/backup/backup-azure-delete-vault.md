---
title: Excluir um cofre dos Serviços de Recuperação no Microsoft Azure
description: Esse artigo explica como excluir um cofre de Serviços de Recuperação. O artigo inclui etapas de solução de problemas quando você tenta excluir um cofre, mas não é possível.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: raynew
ms.openlocfilehash: d514074e56ff37cc7af6a97ea86aa9e02e3763e0
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55492276"
---
# <a name="delete-a-recovery-services-vault"></a>Excluir um cofre dos Serviços de Recuperação

Este artigo descreve como remover todos os itens de um cofre dos Serviços de Recuperação e, em seguida, excluí-lo. Você não poderá excluir um cofre dos Serviços de Recuperação se ele estiver registrado em um servidor e armazenar dados de backup. Se você tentar excluir um cofre, mas não for possível, o cofre ainda estará configurado para receber dados de backup.

Para saber como excluir um cofre, confira a seção [Excluir um cofre do portal do Azure](backup-azure-delete-vault.md#delete-a-vault-from-azure-portal). Se você não quiser reter os dados no cofre dos Serviços de Recuperação e quiser excluir o cofre, confira a seção [Excluir o cofre à força](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force). Se você não tiver certeza do que está no cofre e você precisar se certificar de que pode excluir o cofre, confira a seção [Remover dependências do cofre e excluir o cofre](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault).

## <a name="delete-a-vault-from-azure-portal"></a>Excluir um cofre do portal do Azure

Se você já tiver o cofre dos Serviços de Recuperação aberto, vá para a segunda etapa.

1. Abra o portal do Azure e, no Painel, abra o cofre que você deseja excluir.

   Se você não tiver o cofre dos Serviços de Recuperação fixado ao Painel, no menu Hub, clique em **Todos os Serviços** e, na lista de recursos, digite **Serviços de Recuperação**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Para exibir a lista dos cofres na sua assinatura, clique em **Cofres dos Serviços de Recuperação**.

   ![Criar Cofre de Serviços de Recuperação - etapa 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   A lista de cofres dos Serviços de Recuperação é exibida. 

   ![escolher o cofre na lista](./media/backup-azure-delete-vault/choose-vault-to-delete-.png)

1. Na lista, selecione o cofre que você deseja excluir. Ao selecionar o cofre, o painel do cofre é aberto.

    ![selecione seu cofre para abrir o painel dele](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

1. Para excluir um cofre, no painel do cofre, clique em **Excluir**. Será solicitado que você verifique se deseja excluir o cofre.

    ![selecione seu cofre para abrir o painel dele](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

    Se o **Erro de exclusão de cofre** aparecer, você poderá remover as dependências do cofre ou poderá usar o PowerShell para excluir o cofre à força. As seções a seguir explicam como realizar essas tarefas.

    ![Erro de exclusão de cofre](./media/backup-azure-delete-vault/vault-delete-error.png)


## <a name="delete-the-recovery-services-vault-by-force"></a>Excluir o cofre dos Serviços de Recuperação à força

Você pode usar o PowerShell para excluir um cofre dos Serviços de Recuperação à força. À força significa que o cofre dos Serviços de Recuperação e todos os dados de backup associados são excluídos permanentemente. 

> [!Warning]
> Ao usar o PowerShell para excluir um cofre dos Serviços de Recuperação, certifique-se de que deseja excluir permanentemente todos os dados de backup no cofre.
>

Para excluir um cofre dos Serviços de Recuperação:

1. Entre na sua conta do Azure.

   Entre na sua assinatura do Azure com o comando `Connect-AzureRmAccount` e siga as instruções na tela.

   ```powershell
    Connect-AzureRmAccount
   ```
   Na primeira vez que você usar o Backup do Azure, deverá registrar o provedor de serviços de recuperação do Azure em sua assinatura com [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider).

   ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

1. Abra uma janela do PowerShell com privilégios de Administrador.

1. Use `Set-ExecutionPolicy Unrestricted` para remover quaisquer restrições.

1. Execute o seguinte comando para baixar o pacote do Cliente do Azure Resource Manager de chocolately.org.

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

1. Use o seguinte comando para instalar o Cliente da API do Azure Resource Manager.

   `choco.exe install armclient`

1. No portal do Azure, colete a ID da Assinatura e o nome do grupo de recursos associado do cofre dos Serviços de Recuperação que você deseja excluir.

1. No PowerShell, execute o comando a seguir usando sua ID de assinatura, o nome do grupo de recursos e o nome do cofre dos Serviços de Recuperação. Quando você executa o comando, ele exclui o cofre e todas as dependências.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
   O cofre deverá estar vazio antes que você possa excluí-lo. Caso contrário, você receberá um erro citando "O cofre não pode ser excluído porque há recursos existentes nesse cofre". O comando a seguir demonstra como remover um contêiner dentro de um cofre:

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```
   
1. Entre na sua assinatura no portal do Azure e verifique se o cofre foi excluído.


## <a name="remove-vault-dependencies-and-delete-vault"></a>Remover as dependências do cofre e excluir o cofre

Para remover manualmente as dependências do cofre, exclua a configuração entre cada item ou o servidor e o cofre dos Serviços de Recuperação. Ao percorrer o procedimento a seguir, use o menu **Itens de Backup** (confira a imagem) para:

* Backups do Armazenamento do Azure (Arquivos do Azure)
* SQL Server em backups de VM do Azure
* Backups de máquinas virtuais do Azure
* Backups de agente dos Serviços de Recuperação do Microsoft Azure

Use o menu **Backup de Infraestrutura** (confira a imagem) para:

* Backups de Servidor de Backup do Azure
* Backups do System Center DPM

    ![selecione seu cofre para abrir o painel dele](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

1. No menu do painel do cofre, role até a seção Itens Protegidos e clique em **Itens de Backup**. Nesse menu, você pode parar e excluir Servidores de Arquivos do Azure, SQL Servers em VMs do Azure e máquinas virtuais do Azure. Neste exemplo, vamos remover dados de backup de um servidor de arquivos do Azure.

    ![selecione seu cofre para abrir o painel dele](./media/backup-azure-delete-vault/selected-backup-items.png)

1. Selecione um tipo de backup para exibir todos os itens desse tipo.

    ![selecione o tipo de backup](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

1. Para todos os itens na lista, clique com o botão direito do mouse no item e, no menu de contexto, selecione **Parar backup**.

    ![selecione o tipo de backup](./media/backup-azure-delete-vault/stop-backup-item.png) 

    O menu Parar Backup é aberto.

1. No menu **Parar Backup**, no menu **Escolher uma opção**, selecione **Excluir Dados de Backup**, digite o nome do item e clique em **Parar backup**.

    Digite o nome do item para verificar se você deseja excluí-lo. O botão **Parar Backup** é ativado depois que o item é verificado. Se você mantiver os dados, não poderá excluir o cofre.

    ![Excluir dados de backup](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Se quiser, você poderá fornecer um motivo por que está excluindo os dados e adicionar comentários. Para verificar se o trabalho foi concluído, veja as Mensagens do Azure ![delete backup data](./media/backup-azure-delete-vault/messages.png). <br/>
    Quando o trabalho for concluído, o serviço enviará uma mensagem: *o processo de backup foi interrompido e os dados de backup foram excluídos*.

1. Depois de excluir um item na lista, no menu **Itens de Backup**, clique em **Atualizar** para ver os itens no cofre.

      ![Excluir dados de backup](./media/backup-azure-delete-vault/empty-items-list.png)

      Quando não houver itens na lista, role até o painel **Essentials** no menu do cofre de Serviços de Recuperação. Não deve haver **Itens de Backup**, **Servidores de gerenciamento de backup** ou **Itens replicados** listados. Se os itens ainda forem exibidos no cofre, retorne à etapa três e escolha outra lista de tipos de item.  

1. Quando não houver nenhum outro item na barra de ferramentas do cofre, clique em **Excluir**.

    ![Excluir dados de backup](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Para verificar se você deseja excluir o cofre, clique em **Sim**.

    O cofre é excluído e o portal retorna para o menu de serviço **Novo** .

## <a name="removing-azure-backup-server-or-dpm"></a>Removendo o Servidor de Backup do Azure ou o DPM

1. No menu do painel do cofre, role até a seção Gerenciar e clique em **Backup de Infraestrutura**. 

1. No submenu, clique em **Servidores de Gerenciamento de Backup** para exibir os Servidores de Backup do Azure e o servidor System Center DPM. Você pode parar e excluir Servidores de Arquivos do Azure, SQL Servers em VMs do Azure e máquinas virtuais do Azure. 

    ![selecione seu cofre para abrir o painel dele](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

1. Clique com o botão direito do mouse no item que deseja excluir e selecione **Excluir** no submenu.

    ![selecione o tipo de backup](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

    O menu Parar Backup é aberto.

1. No menu **Parar Backup**, no menu **Escolher uma opção**, selecione **Excluir Dados de Backup**, digite o nome do item e clique em **Parar backup**.

    Para confirmar a exclusão, digite o nome dele. O botão **Parar Backup** é ativado depois que o item é verificado. Se você mantiver os dados, não poderá excluir o cofre.

    ![Excluir dados de backup](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Opcionalmente, você pode fornecer um motivo por que está excluindo os dados e adicionar comentários. Para verificar se o trabalho foi concluído, veja as Mensagens do Azure ![delete backup data](./media/backup-azure-delete-vault/messages.png). <br/>
    Quando o trabalho for concluído, o serviço enviará uma mensagem: o processo de backup foi interrompido e os dados de backup foram excluídos.

1. Depois de excluir um item na lista, no menu **Itens de Backup**, clique em **Atualizar** para ver os itens restantes no cofre.

      ![Excluir dados de backup](./media/backup-azure-delete-vault/empty-items-list.png)

      Quando não houver itens na lista, role até o painel **Essentials** no menu do cofre de Serviços de Recuperação. Não deve haver **Itens de Backup**, **Servidores de gerenciamento de backup** ou **Itens replicados** listados. Se os itens ainda forem exibidos no cofre, retorne à etapa três e escolha outra lista de tipos de item.  
1. Quando não houver nenhum outro item no cofre, clique em **Excluir** no painel do cofre.

    ![Excluir dados de backup](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Para verificar se você deseja excluir o cofre, clique em **Sim**.

    O cofre é excluído e o portal retorna para o menu de serviço **Novo** .


## <a name="removing-azure-backup-agent-recovery-points"></a>Removendo pontos de recuperação de agente do Backup do Azure

1. No menu do painel do cofre, role até a seção Gerenciar e clique em **Backup de Infraestrutura**.

1. No submenu, clique em **Servidores Protegidos** para exibir a lista de tipos de servidores protegidos, incluindo o agente de Backup do Azure.

    ![selecione seu cofre para abrir o painel dele](./media/backup-azure-delete-vault/identify-protected-servers.png)

1. Na lista **Servidores Protegidos**, clique em Agente de Backup do Azure.

    ![selecione o tipo de backup](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

    A lista de servidores protegidos usando o agente de Backup do Azure é aberta.

    ![selecione o servidor protegido específico](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

1. Na lista de servidores, clique em um para abrir o menu.

    ![exibir o painel do servidor selecionado](./media/backup-azure-delete-vault/selected-protected-server.png)

1. No menu do painel do servidor selecionado, clique em **Excluir**.

    ![exclua o servidor selecionado](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

1. No menu **Excluir**, digite o nome do item e clique em **Excluir**.

    Digite o nome do item para verificar se você deseja excluí-lo. O botão **Excluir** é ativado depois que o item é verificado.

    ![Excluir dados de backup](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

    Opcionalmente, você pode fornecer um motivo por que está excluindo os dados e adicionar comentários. Para verificar se o trabalho foi concluído, veja as Mensagens do Azure ![delete backup data](./media/backup-azure-delete-vault/messages.png). <br/>
    Quando o trabalho for concluído, o serviço enviará uma mensagem: o processo de backup foi interrompido e os dados de backup foram excluídos.

1. Depois de excluir um item na lista, no menu **Itens de Backup**, clique em **Atualizar** para ver os itens restantes no cofre.

      ![Excluir dados de backup](./media/backup-azure-delete-vault/empty-items-list.png)

      Quando não houver itens na lista, role até o painel **Essentials** no menu do cofre de Serviços de Recuperação. Não deve haver **Itens de Backup**, **Servidores de gerenciamento de backup** ou **Itens replicados** listados. Se os itens ainda forem exibidos no cofre, retorne à etapa três e escolha outra lista de tipos de item.  
1. Quando não houver nenhum outro item no cofre, clique em **Excluir** no painel do cofre.

    ![Excluir dados de backup](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Para verificar se você deseja excluir o cofre, clique em **Sim**.

    O cofre é excluído e o portal retorna para o menu de serviço **Novo** .

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>E se eu parar o processo de backup, mas mantiver os dados?

Se você interromper o processo de backup, mas acidentalmente *mantiver* os dados, deverá excluir os dados de backup antes de excluir o cofre. Para excluir os dados de backup:

1. No menu **Itens de Backup**, clique com o botão direito do mouse no item e, no menu de contexto, clique em **Excluir dados de backup**.

    ![Excluir dados de backup](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    O menu **Excluir Dados do Backup** será aberto.
1. No menu **Excluir Dados de Backup**, digite o nome do item e clique em **Excluir**.

    ![Excluir dados de backup](./media/backup-azure-delete-vault/delete-retained-vault.png)

    Depois de excluir os dados, retorne à etapa 4c e continue com o processo.
