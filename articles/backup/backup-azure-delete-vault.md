---
title: Excluir um cofre de Backup do Azure | Microsoft Docs
description: 'Como excluir um cofre de Backup do Azure. Solução do problema de não poder excluir um cofre de backup. '
services: service-name
documentationcenter: dev-center-name
author: markgalioto
manager: cfreeman
editor: ''

ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/29/2016
ms.author: markgal;trinadhk

---
# Excluir um Cofre de Backup do Azure
O serviço de Backup do Azure tem dois tipos de cofres – o cofre de Backup e o cofre de Serviços de Recuperação. O Cofre de Backup veio primeiro. Em seguida, o Cofre dos Serviços de Recuperação surgiu no mercado para oferecer suporte às implantações expandidas do Gerenciador de Recursos. Devido aos recursos expandidos e às dependências de informações que devem ser armazenados no cofre, excluir um cofre dos Serviços de Recuperação pode parecer mais difícil do que precisa ser.

| **Tipo de implantação** | **Portal** | **Nome do cofre** |
| --- | --- | --- |
| Clássico |Clássico |Cofre de backup |
| Gerenciador de Recursos |As tabelas |Cofre dos Serviços de Recuperação |

> [!NOTE]
> Cofres de backup não podem ser usados para proteger soluções implantadas pelo Resource Manager. No entanto, você pode usar um cofre de Serviços de Recuperação para proteger VMs e servidores implantados de modo clássico.
> 
> 

Neste artigo, usamos o termo cofre para nos referirmos à forma genérica do cofre de Backup ou ao cofre dos Serviços de Recuperação. Usamos o nome formal, cofre de Backup ou Cofre dos Serviços de Recuperação, quando for necessário fazer a distinção entre os cofres.

## Excluir um cofre dos Serviços de Recuperação
A exclusão de um cofre dos Serviços de Recuperação é um processo de uma etapa - *desde que o cofre não contenha recursos*. Antes de excluir um cofre dos Serviços de Recuperação, você deverá remover ou excluir todos os recursos do cofre. Se você tentar excluir um cofre que contenha recursos, obterá um erro como o da imagem a seguir.

![Erro de exclusão de cofre](./media/backup-azure-delete-vault/vault-deletion-error.png) <br/>

Até que você tenha limpado os recursos do cofre, clicar em **Tentar novamente** produzirá o mesmo erro. Se você estiver parado nesta mensagem de erro, clique em **Cancelar** e siga as etapas abaixo para excluir os recursos no cofre dos Serviços de Recuperação.

### Remover os itens de um cofre que protege uma máquina virtual
Se você já tiver o cofre dos Serviços de Recuperação aberto, vá para a segunda etapa.

1. Abra o portal do Azure e, no Painel, abra o cofre que você deseja excluir.
   
   Se você não tiver o cofre dos Serviços de Recuperação fixado ao Painel, no menu Hub, clique em **Mais Serviços** e, na lista de recursos, digite **Serviços de Recuperação**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Clique em **Cofres dos Serviços de Recuperação**.
   
   ![Criar Cofre de Serviços de Recuperação - etapa 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>
   
   A lista de cofres dos Serviços de Recuperação é exibida. Na lista, selecione o cofre que você deseja excluir.
   
   ![escolher o cofre na lista](./media/backup-azure-work-with-vaults/choose-vault-to-delete.png)
2. Na exibição de cofre, observe o painel **Essentials**. Para excluir um cofre, não pode haver itens protegidos. Se você vir um número diferente de zero, em **Itens de Backup** ou em **Fazer backup de servidores de gerenciamento**, deverá remover esses itens antes de poder excluir o cofre.
   
    ![Procurar os itens protegidos no painel Essentials](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)
   
    As máquinas virtuais e os Arquivos/Pastas são considerados como Itens de Backup e são listados na área **Itens de Backup** do painel Essentials. Um servidor DPM está listado na área **Servidor de Gerenciamento de Backup** do painel Essentials. Os **Itens Duplicados** pertencem ao serviço Azure Site Recovery.
3. Para iniciar a remoção dos itens protegidos do cofre, localize os itens no cofre. No painel do cofre, clique em **Configurações**, e em **Itens de Backup** para abrir essa folha.
   
    ![escolher o cofre na lista](./media/backup-azure-delete-vault/open-settings-and-backup-items.png)
   
    A folha **Itens de Backup** tem listas separadas, baseadas no Tipo de Item: máquinas Virtuais do Azure ou Pastas de Arquivos (confira a imagem). A lista de Tipo de Item padrão mostrada é de Máquinas Virtuais do Azure. Para exibir a lista de itens de Pastas de Arquivos no cofre, selecione **Pastas de Arquivos** no menu suspenso.
4. Antes de excluir um item do cofre que está protegendo uma máquina virtual, você deverá parar o trabalho de backup do item e excluir os dados de ponto de recuperação. Para cada item no cofre, siga estas etapas:
   
    a. Na folha **Itens de Backup**, clique com o botão direito do mouse no item e, no menu de contexto, selecione **Parar backup**.
   
    ![parar o trabalho de backup](./media/backup-azure-delete-vault/stop-the-backup-process.png)
   
    A folha Interromper Backup é aberta.
   
    b. Na folha **Parar Backup**, no menu **Escolha uma opção**, selecione **Excluir Dados de Backup** > digite o nome do item > e clique em **Parar backup**.
   
      Digite o nome do item para verificar se você deseja excluí-lo. O botão **Parar Backup** não será ativado até que você verifique o item a ser parado. Se você tiver escolhido a opção **Reter Dados de Backup**, não verá a caixa de diálogo para digitar o nome do item de backup.
   
    ![excluir dados de backup](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)
   
      Opcionalmente, você pode fornecer um motivo por que está excluindo os dados e adicionar comentários. Depois de clicar em **Parar Backup**, permita que o trabalho de exclusão seja concluído antes de tentar excluir o cofre. Para verificar se o trabalho foi concluído, veja as Mensagens do Azure ![excluir dados de backup](./media/backup-azure-delete-vault/messages.png). <br/> Quando o trabalho for concluído, você receberá uma mensagem informando que o processo de backup foi interrompido e que os dados de backup foram excluídos para aquele item.
   
    c. Depois de excluir um item na lista, no menu **Itens de Backup**, clique em **Atualizar** para ver os itens restantes no cofre.
   
      ![excluir dados de backup](./media/backup-azure-delete-vault/empty-items-list.png)
   
      Quando não houver itens na lista, role até o painel **Essentials** na folha Cofre de Backup. Não deve haver **Itens de Backup**, **Servidores de gerenciamento de backup** ou **Itens replicados** listados. Se os itens ainda aparecerem no cofre, retorne para a etapa três acima e escolha uma lista de tipos de item diferentes.
5. Quando não houver nenhum outro item na barra de ferramentas do cofre, clique em **Excluir**.
   
    ![excluir dados de backup](./media/backup-azure-delete-vault/delete-vault.png)
6. Quando for solicitado que você verifique se deseja excluir o cofre, clique em **Sim**.
   
    O cofre é excluído e o portal retorna para o menu de serviço **Novo**.

## E se eu tiver parado o processo de backup, mas tiver mantido os dados?
Se você tiver interrompido o processo de backup, mas acidentalmente *reteve* os dados, deverá excluir os dados de backup antes de excluir o cofre. Para excluir os dados de backup:

1. Na folha **Itens de Backup**, clique com o botão direito do mouse no item e, no menu de contexto, selecione **Excluir dados de backup**.
   
    ![excluir dados de backup](./media/backup-azure-delete-vault/delete-backup-data-menu.png)
   
    A folha **Excluir Dados do Backup** será aberta.
2. Na folha **Excluir Dados de Backup**, digite o nome do item e clique em **Excluir**.
   
    ![excluir dados de backup](./media/backup-azure-delete-vault/delete-retained-vault.png)
   
    Depois de excluir os dados, vá para a etapa 4c acima e prossiga com o processo.

## Excluir um cofre usado para proteger um servidor DPM
Antes de excluir um cofre usado para proteger um servidor DPM, você deverá limpar os pontos de recuperação que foram criados e, em seguida, cancelar o registro do servidor do cofre.

Para excluir os dados associados a um grupo de proteção:

1. No Console do Administrador do DPM, clique em **Proteção**, selecione um grupo de proteção, selecione o Membro do Grupo de Proteção e, na faixa de opções da ferramenta, clique em **Remover**. Você deve selecionar o membro para que o botão **Remover** apareça na faixa de opções da ferramenta. No exemplo, o membro é **dummyvm9**. Se houver vários membros no grupo de proteção, mantenha pressionada a tecla Ctrl para selecionar vários membros.
   
    ![excluir dados de backup](./media/backup-azure-delete-vault/az-portal-delete-protection-group.png)
   
    O diálogo **Parar Proteção** é aberto.
2. No diálogo **Parar Proteção**, selecione **Excluir dados protegidos** e clique em **Parar Proteção**.
   
    ![excluir dados de backup](./media/backup-azure-delete-vault/delete-dpm-protection-group.png)
   
    Você não deseja manter os dados protegidos porque precisa limpar o cofre para excluí-lo. Dependendo de quantos pontos de recuperação e de quantos dados há no grupo de proteção, a exclusão dos dados pode demorar alguns segundos a alguns minutos. O diálogo **Parar Proteção** mostrará o status quando o trabalho for concluído.
   
    ![excluir dados de backup](./media/backup-azure-delete-vault/success-deleting-protection-group.png)
3. Prossiga nesse processo para todos os membros de todos os grupos de proteção.
   
    Você deve remover os dados protegidos e os grupos de proteção.
4. Depois de excluir todos os membros do grupo de proteção, alterne para o portal do Azure. Abra o painel do cofre e verifique se não há **Itens de Backup**, **Servidores de gerenciamento de backup** ou **Itens replicados**. Na barra de ferramentas do cofre, clique em **Excluir**.
   
    ![excluir dados de backup](./media/backup-azure-delete-vault/delete-vault.png)
   
    Se houver servidores de gerenciamento de Backup registrados no cofre, você não poderá excluir o cofre, mesmo se não houver dados no cofre. Se você achou que tinha excluído os servidores de gerenciamento de Backup associados ao cofre, mas ainda existem servidores exibidos no painel **Essentials**, confira [Localizar os servidores de gerenciamento de Backup registrados no cofre](backup-azure-delete-vault.md#find-the-backup-management-servers-registered-to-the-vault).
5. Quando for solicitado que você verifique se deseja excluir o cofre, **Sim**.
   
    O cofre é excluído e o portal retorna para o menu de serviço **Novo**.

## Excluir um cofre usado para proteger um servidor de Produção
Antes de excluir um cofre usado para proteger um servidor de Produção, você deverá excluir ou cancelar o registro do servidor do cofre.

Para excluir o servidor de Produção associado ao cofre:

1. No portal do Azure, abra o painel do cofre e clique em **Configurações** > **Infraestrutura de Backup** > **Servidores de Produção**.
   
    ![abrir a folha Servidores de Produção](./media/backup-azure-delete-vault/delete-production-server.png)
   
    A folha **Servidores de Produção** abre e lista todos os servidores de produção no cofre.
   
    ![lista de Servidores de Produção](./media/backup-azure-delete-vault/list-of-production-servers.png)
2. Na folha **Servidores de Produção**, clique com o botão direito do mouse no servidor e clique em **Excluir**.
   
    ![excluir servidor de produção](./media/backup-azure-delete-vault/delete-server-on-production-server-blade.png)
   
    A folha **Excluir** será aberta.
   
    ![excluir servidor de produção](./media/backup-azure-delete-vault/delete-blade.png)
3. Na folha **Excluir**, confirme o nome do servidor a ser excluído e clique em **Excluir**. Você deve inserir corretamente o nome do servidor para ativar o botão **Excluir**.
   
    Depois que o cofre tiver sido excluído, você receberá uma mensagem informando que o cofre foi excluído. Depois de excluir todos os servidores no cofre, role de volta para o painel Essentials no painel do cofre.
4. No painel do cofre, verifique se não há **Itens de Backup**, **Servidores de gerenciamento de backup** ou **Itens replicados**. Na barra de ferramentas do cofre, clique em **Excluir**.
5. Quando for solicitado que você verifique se deseja excluir o cofre, **Sim**.
   
    O cofre é excluído e o portal retorna para o menu de serviço **Novo**.

## Excluir um cofre de Backup
As instruções a seguir são para excluir um cofre de Backup no portal clássico. Um cofre de Backup e o cofre dos Serviços de Recuperação são iguais: antes de excluir o cofre, exclua os itens e os dados mantidos.

1. Abra o portal Clássico.
2. Na lista de cofres de backup, selecione o cofre que você deseja excluir.
   
    ![excluir dados de backup](./media/backup-azure-delete-vault/classic-portal-delete-vault-open-vault.png)
   
    O painel do cofre será aberto. Observe o número de Windows Servers e/ou de máquinas virtuais do Azure associados ao cofre. Além disso, examine o armazenamento total consumido no Azure. Você precisará parar todos os trabalhos de backup e excluir dados existentes antes de excluir o cofre.
3. Clique na guia **Itens Protegidos** e clique em **Parar Proteção**
   
    ![excluir dados de backup](./media/backup-azure-delete-vault/classic-portal-delete-vault-stop-protect.png)
   
    O diálogo **Parar proteção de 'seu cofre'** aparecerá.
4. No diálogo **Parar proteção de 'seu cofre'**, marque **Excluir dados de backup associados** e clique em ![marca de seleção](./media/backup-azure-delete-vault/checkmark.png). <br/> Opcionalmente, você pode escolher um motivo para interromper a proteção e fornecer um comentário.
   
    ![excluir dados de backup](./media/backup-azure-delete-vault/classic-portal-delete-vault-verify-stop-protect.png)
   
    Depois de excluir os itens no cofre, o cofre estará vazio.
   
    ![excluir dados de backup](./media/backup-azure-delete-vault/classic-portal-delete-vault-post-delete-data.png)
5. Na lista de guias, clique em **Itens Registrados**. Para cada item registrado no cofre, selecione o item e clique em **Cancelar Registro**.
   
    ![excluir dados de backup](./media/backup-azure-delete-vault/classic-portal-unregister.png)
6. Na lista de guias, clique em **Painel** para abrir essa guia. Verifique se não existem servidores registrados ou máquinas virtuais do Azure protegidos na nuvem. Além disso, verifique se não há dados no armazenamento. Clique em **Excluir** para excluir o cofre.
   
    ![excluir dados de backup](./media/backup-azure-delete-vault/classic-portal-list-of-tabs-dashboard.png)
   
    A tela de confirmação Excluir cofre de Backup será aberta. Selecione o motivo pelo qual você está excluindo o cofre e clique em ![marca de seleção](./media/backup-azure-delete-vault/checkmark.png). <br/>
   
    ![excluir dados de backup](./media/backup-azure-delete-vault/classic-portal-delete-vault-confirmation-1.png)
   
    O cofre é excluído e você retorna para o painel do portal clássico.

### Localizar os servidores de Gerenciamento de Backup registrados no cofre
Se você tiver vários servidores registrados em um cofre, pode ser difícil se lembrar deles. Para ver os servidores registrados no cofre e excluí-los:

1. Abra o painel do cofre.
2. No painel **Essentials**, clique em **Configurações** para abrir essa folha.
   
    ![abrir folha de configurações](./media/backup-azure-delete-vault/backup-vault-click-settings.png)
3. Na **folha Configurações**, clique em **Infraestrutura de Backup**.
4. Na folha **Infraestrutura de Backup**, clique em **Servidores de Gerenciamento de Backup**. A folha Servidores de Gerenciamento de Backup será aberta.
   
    ![lista de servidores de gerenciamento de backup](./media/backup-azure-delete-vault/list-of-backup-management-servers.png)
5. Para excluir um servidor da lista, clique com o botão direito do mouse no nome do servidor e, em seguida, clique em **Excluir**. A folha **Excluir** será aberta.
6. Na folha **Excluir**, forneça o nome do servidor. Se for um nome longo, você poderá copiá-lo e colá-lo da lista de Servidores de Gerenciamento de Backup. Em seguida, clique em **Excluir**.

<!---HONumber=AcomDC_0914_2016-->