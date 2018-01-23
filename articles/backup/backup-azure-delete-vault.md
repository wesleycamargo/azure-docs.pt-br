---
title: " Exclua um cofre de Serviços de Recuperação no Azure | Microsoft Docs "
description: "Esse artigo explica como excluir um cofre de Serviços de Recuperação. O artigo inclui etapas de solução de problemas quando você tenta excluir um cofre, mas não é possível."
services: service-name
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 5fa08157-2612-4020-bd90-f9e3c3bc1806
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/20/2017
ms.author: markgal;trinadhk
ms.openlocfilehash: 4f4a92159b01b197984130c15195419e1b166fd3
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2017
---
# <a name="delete-a-recovery-services-vault"></a>Excluir um cofre dos Serviços de Recuperação
Esse artigo explica como excluir um cofre de Serviços de Recuperação no Portal do Azure. Se você tiver cofres de Backup, eles terão sido convertidos em cofres dos Serviços de Recuperação.   

A exclusão de um cofre dos Serviços de Recuperação é um processo de uma etapa - *desde que o cofre não contenha recursos*. Antes de excluir um cofre dos Serviços de Recuperação, você deverá remover ou excluir todos os recursos do cofre. Se você tentar excluir um cofre que contém recursos, receberá um erro como o da seguinte imagem:

![Erro de exclusão de cofre](./media/backup-azure-delete-vault/vault-deletion-error.png) <br/>

Até que você tenha limpado os recursos do cofre, clicar em **Tentar novamente** produzirá o mesmo erro. Se você estiver parado nessa mensagem de erro, clique em **Cancelar** e use as etapas a seguir para excluir os recursos do cofre.

## <a name="removing-items-from-a-vault-protecting-a-vm"></a>Remover os itens de um cofre que protege uma VM
Se você já tiver o cofre dos Serviços de Recuperação aberto, vá para a segunda etapa.

1. Abra o portal do Azure e, no Painel, abra o cofre que você deseja excluir.

   Se você não tiver o cofre dos Serviços de Recuperação fixado ao Painel, no menu Hub, clique em **Mais Serviços** e, na lista de recursos, digite **Serviços de Recuperação**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Clique em **Cofres dos Serviços de Recuperação**.

   ![Criar Cofre de Serviços de Recuperação - etapa 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   A lista de cofres dos Serviços de Recuperação é exibida. Na lista, selecione o cofre que você deseja excluir.

   ![escolher o cofre na lista](./media/backup-azure-work-with-vaults/choose-vault-to-delete.png)
2. Na exibição de cofre, observe o painel **Essentials** . Para excluir um cofre, não pode haver itens protegidos. Se os **Itens de Backup** ou os **Servidores de gerenciamento de backup** não mostrarem zero, você deverá remover esses itens. Não será possível excluir o cofre se ele contiver dados.

    ![Procurar os itens protegidos no painel Essentials](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

    As máquinas virtuais e os Arquivos/Pastas são considerados como Itens de Backup e são listados na área **Itens de Backup** do painel Essentials. Um servidor DPM está listado na área **Servidor de Gerenciamento de Backup** do painel Essentials. **Itens Duplicados** pertencem ao serviço Azure Site Recovery.
3. Para iniciar a remoção dos itens protegidos do cofre, localize os itens no cofre. No painel do cofre, clique em **Configurações** e em **Itens de Backup** para abrir esse menu.

    ![escolher o cofre na lista](./media/backup-azure-delete-vault/open-settings-and-backup-items.png)

    O menu **Itens de Backup** tem listas separadas, baseadas no Tipo de Item: Máquinas Virtuais do Azure ou Pastas de Arquivos (confira a imagem). A lista de Tipo de Item padrão mostrada é de Máquinas Virtuais do Azure. Para exibir a lista de itens de Pastas de Arquivos no cofre, selecione **Pastas de Arquivos** no menu suspenso.
4. Antes de excluir um item do cofre que está protegendo uma máquina virtual, você deverá parar o trabalho de backup do item e excluir os dados de ponto de recuperação. Para cada item no cofre, siga estas etapas:

    a. No menu **Itens de Backup**, clique com o botão direito do mouse no item e, no menu de contexto, selecione **Parar backup**.

    ![parar o trabalho de backup](./media/backup-azure-delete-vault/stop-the-backup-process.png)

    O menu Parar Backup é aberto.

    b. No menu **Parar Backup**, no menu **Escolha uma opção**, selecione **Excluir Dados de Backup** > digite o nome do item > e clique em **Parar backup**.

    Digite o nome do item para verificar se você deseja excluí-lo. O botão **Parar Backup** é ativado depois que o item é verificado. Se você não vir a caixa de diálogo para digitar o nome do item de backup, isso significará que você escolheu a opção **Reter Dados de Backup**.

    ![Excluir dados de backup](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Opcionalmente, você pode fornecer um motivo por que está excluindo os dados e adicionar comentários. Depois de clicar em **Parar Backup**, permita que o trabalho de exclusão seja concluído antes de tentar excluir o cofre. Para verificar se o trabalho foi concluído, veja as Mensagens do Azure ![delete backup data](./media/backup-azure-delete-vault/messages.png). <br/>
    Quando o trabalho for concluído, o serviço enviará uma mensagem: o processo de backup foi interrompido e os dados de backup foram excluídos.

    c. Depois de excluir um item na lista, no menu **Itens de Backup**, clique em **Atualizar** para ver os itens restantes no cofre.

      ![Excluir dados de backup](./media/backup-azure-delete-vault/empty-items-list.png)

      Quando não houver itens na lista, role até o painel **Essentials** no menu do cofre de Serviços de Recuperação. Não deve haver **Itens de Backup**, **Servidores de gerenciamento de backup** ou **Itens replicados** listados. Se os itens ainda forem exibidos no cofre, retorne à etapa três e escolha outra lista de tipos de item.  
5. Quando não houver nenhum outro item na barra de ferramentas do cofre, clique em **Excluir**.

    ![Excluir dados de backup](./media/backup-azure-delete-vault/delete-vault.png)
6. Para verificar se você deseja excluir o cofre, clique em **Sim**.

    O cofre é excluído e o portal retorna para o menu de serviço **Novo** .

## <a name="what-if-i-stopped-the-backup-process-but-retained-the-data"></a>E se eu tiver parado o processo de backup, mas tiver mantido os dados?
Se você tiver interrompido o processo de backup, mas acidentalmente *reteve* os dados, deverá excluir os dados de backup antes de excluir o cofre. Para excluir os dados de backup:

1. No menu **Itens de Backup**, clique com o botão direito do mouse no item e, no menu de contexto, clique em **Excluir dados de backup**.

    ![Excluir dados de backup](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    O menu **Excluir Dados do Backup** será aberto.
2. No menu **Excluir Dados de Backup**, digite o nome do item e clique em **Excluir**.

    ![Excluir dados de backup](./media/backup-azure-delete-vault/delete-retained-vault.png)

    Depois de excluir os dados, retorne à etapa 4c e continue com o processo.

## <a name="delete-a-vault-used-to-protect-a-dpm-server"></a>Excluir um cofre usado para proteger um servidor DPM
Antes de excluir um cofre usado para proteger um servidor DPM, você deverá limpar os pontos de recuperação que foram criados e, em seguida, cancelar o registro do servidor do cofre.

Para excluir os dados associados a um grupo de proteção:

1. No Console do Administrador do DPM, clique em **Proteção** > selecione um grupo de proteção > selecione o Membro do Grupo de Proteção > e, na faixa de opções da ferramenta, clique em **Remover**.

  Selecione o Membro do Grupo de Proteção para ativar o botão **Remover** na faixa de opções da ferramenta. No exemplo, o membro é **dummyvm9**. Para selecionar vários membros no grupo de proteção, mantenha pressionada a tecla Ctrl enquanto clica nos membros.

    ![Excluir dados de backup](./media/backup-azure-delete-vault/az-portal-delete-protection-group.png)

    O diálogo **Parar Proteção** é aberto.
2. No diálogo **Parar Proteção**, selecione **Excluir dados protegidos** e clique em **Parar Proteção**.

    ![Excluir dados de backup](./media/backup-azure-delete-vault/delete-dpm-protection-group.png)

    Para excluir um cofre, é necessário limpar, ou excluir, o cofre de dados protegidos. Se houver muitos pontos de recuperação e dados no grupo de proteção, poderá levar vários minutos para excluir os dados. O diálogo **Parar Proteção** mostrará quando o trabalho for concluído.

    ![Excluir dados de backup](./media/backup-azure-delete-vault/success-deleting-protection-group.png)
3. Prossiga nesse processo para todos os membros de todos os grupos de proteção.

    Remova todos os dados protegidos e grupos de proteção.
4. Depois de excluir todos os membros do grupo de proteção, alterne para o portal do Azure. Abra o painel do cofre e verifique se não há **Itens de Backup**, **Servidores de gerenciamento de backup** ou **Itens replicados**. Na barra de ferramentas do cofre, clique em **Excluir**.

    ![Excluir dados de backup](./media/backup-azure-delete-vault/delete-vault.png)

    Se houver servidores de gerenciamento de Backup registrados no cofre, você não poderá excluir o cofre, mesmo se não houver dados no cofre. Se você excluiu os servidores de gerenciamento de Backup associados ao cofre, mas há servidores listados no painel **Dados Básicos**, consulte [Localizar os servidores de gerenciamento de Backup registrados no cofre](backup-azure-delete-vault.md#find-the-backup-management-servers-registered-to-the-vault).
5. Para verificar se você deseja excluir o cofre, clique em **Sim**.

    O cofre é excluído e o portal retorna para o menu de serviço **Novo** .

## <a name="delete-a-vault-used-to-protect-a-production-server"></a>Excluir um cofre usado para proteger um servidor de Produção
Antes de excluir um cofre usado para proteger um servidor de Produção, você deverá excluir ou cancelar o registro do servidor do cofre.

Para excluir o servidor de Produção associado ao cofre:

1. No portal do Azure, abra o painel do cofre e clique em **Configurações** > **Infraestrutura de Backup** > **Servidores de Produção**.

    ![abrir o menu Servidores de Produção](./media/backup-azure-delete-vault/delete-production-server.png)

    O menu **Servidores de Produção** abre e lista todos os servidores de Produção no cofre.

    ![lista de Servidores de Produção](./media/backup-azure-delete-vault/list-of-production-servers.png)
2. No menu **Servidores de Produção**, clique com o botão direito do mouse no servidor e clique em **Excluir**.

    ![excluir servidor de produção ](./media/backup-azure-delete-vault/delete-server-on-production-server-blade.png)

    O menu **Excluir** é aberto.

    ![excluir servidor de produção ](./media/backup-azure-delete-vault/delete-blade.png)
3. No menu **Excluir**, confirme o nome do servidor e clique em **Excluir**. É necessário nomear o servidor corretamente para ativar o botão **Excluir**.

    Depois que o cofre é excluído, você recebe uma mensagem informando que o cofre foi excluído. Depois de excluir todos os servidores no cofre, role de volta para o painel Essentials no painel do cofre.
4. No painel do cofre, verifique se não há **Itens de Backup**, **Servidores de gerenciamento de backup** ou **Itens replicados**. Na barra de ferramentas do cofre, clique em **Excluir**.
5. Para verificar se você deseja excluir o cofre, clique em **Sim**.

    O cofre é excluído e o portal retorna para o menu de serviço **Novo** .

## <a name="find-the-backup-management-servers-registered-to-the-vault"></a>Localizar os servidores de gerenciamento de Backup registrados no cofre
Se você tiver vários servidores registrados em um cofre, pode ser difícil se lembrar deles. Para ver os servidores registrados no cofre e excluí-los:

1. Abra o painel do cofre.
2. No painel **Essentials**, clique em **Configurações** para abrir esse menu.

    ![abrir o menu de configurações](./media/backup-azure-delete-vault/backup-vault-click-settings.png)
3. No menu **Configurações**, clique em **Infraestrutura de Backup**.
4. No menu **Infraestrutura de Backup**, clique em **Servidores de Gerenciamento de Backup**. O menu Servidores de Gerenciamento de Backup será aberto.

    ![lista de servidores de gerenciamento de backup](./media/backup-azure-delete-vault/list-of-backup-management-servers.png)
5. Para excluir um servidor da lista, clique com o botão direito do mouse no nome do servidor e, em seguida, clique em **Excluir**.
    O menu **Excluir** é aberto.
6. No menu **Excluir**, forneça o nome do servidor. Se for um nome longo, você poderá copiá-lo e colá-lo da lista de Servidores de Gerenciamento de Backup. Em seguida, clique em **Excluir**.  
