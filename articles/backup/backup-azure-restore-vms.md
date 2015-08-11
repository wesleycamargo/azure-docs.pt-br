
<properties
	pageTitle="Backup do Azure - restaurar uma máquina virtual"
	description="Aprenda a restaurar uma máquina virtual do Azure"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/17/2015"
	ms.author="trinadhk"; "jimpark"/>

# Restaurar uma máquina virtual
Você pode restaurar uma máquina virtual em uma nova VM por meio de backups armazenados no cofre de backup do Azure usando a ação de restauração.

## Escolha um item para restaurar

1. Navegue até a guia **Itens Protegidos** e selecione a máquina virtual que você deseja restaurar em uma nova VM.

    ![Itens protegidos](./media/backup-azure-restore-vms/protected-items.png)

    A coluna **Ponto de Recuperação** na página **Itens Protegidos** informará o número de pontos de recuperação para uma máquina virtual. A coluna **Ponto de Recuperação Mais Recente** indica a hora do backup mais recente do qual uma máquina virtual pode ser restaurada.

2. Clique em **Restaurar** para abrir o assistente **Restaurar um Item**.

    ![Restaurar um item](./media/backup-azure-restore-vms/restore-item.png)

## Selecione um ponto de recuperação

1. Na tela **Selecionar um ponto de recuperação**, você pode restaurar do ponto de recuperação mais recente ou de um ponto anterior no tempo. A opção padrão selecionada quando o assistente é aberto é *Ponto de Recuperação Mais Recente*.

    ![Selecione um ponto de recuperação](./media/backup-azure-restore-vms/select-recovery-point.png)

2. Para selecionar um ponto anterior no tempo, escolha a opção **Selecionar Data** na lista suspensa e selecione uma data no controle de calendário, clicando no **ícone de calendário**. No controle, todas as datas que têm pontos de recuperação são preenchidas com um tom cinza claro e podem ser selecionadas pelo usuário.

    ![Selecione uma data](./media/backup-azure-restore-vms/select-date.png)

    Quando você clica em uma data no controle de calendário, os pontos de recuperação disponíveis nessa data serão mostrados na tabela de pontos de recuperação abaixo. A coluna **Tempo** indica a hora em que o instantâneo foi criado. A coluna **Tipo** exibe a [consistência](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) do ponto de recuperação. O cabeçalho da tabela mostra o número de pontos de recuperação disponíveis nesse dia entre parênteses.

    ![Pontos de recuperação](./media/backup-azure-restore-vms/recovery-points.png)

3. Selecione o ponto de recuperação na tabela **Pontos de Recuperação** e clique na seta Avançar para ir para a próxima tela.

## Especifique um local de destino

1. Na tela **Selecionar instância de restauração**, especifique os detalhes de onde restaurar a máquina virtual.

  - Especifique o nome da máquina virtual: em um determinado serviço de nuvem, o nome da máquina virtual deve ser exclusivo. Se você pretende substituir uma VM existente com o mesmo nome, primeiro exclua a máquina virtual existente e os discos de dados e, em seguida, restaure os dados do Backup do Azure.
  - Selecione um serviço de nuvem para a VM: isso é obrigatório para a criação de uma VM. Você pode optar por usar um serviço de nuvem existente ou criar um novo serviço de nuvem.

        O nome do serviço de nuvem deve ser globalmente exclusivo. Geralmente, o nome do serviço de nuvem é associado a uma URL pública no formato [serviçodenuvem].cloudapp.net. O Azure não permitirá a criação de um novo serviço de nuvem se o nome já estiver em uso. Se você optar por criar, selecione "Criar novo serviço de nuvem". O serviço receberá o mesmo nome que a máquina virtual, portanto, o nome escolhido para a VM deverá ser exclusivo o suficiente para ser aplicado ao serviço de nuvem associado.

        Só exibimos serviços de nuvem e redes virtuais que não estejam associados a nenhum grupo de afinidade nos detalhes da instância de restauração. [Saiba mais](https://msdn.microsoft.com/pt-br/library/azure/jj156085.aspx).

2. Selecione uma conta de armazenamento para a VM: isso é obrigatório para a criação de uma VM. Você pode selecionar entre contas de armazenamento existentes na mesma região que o cofre de backup do Azure. Não há suporte para contas de armazenamento com redundância de zona ou do tipo de armazenamento Premium.

    Se não houver nenhuma conta de armazenamento com uma configuração com suporte, crie uma conta de armazenamento com uma configuração com suporte antes de iniciar a operação de restauração.

    ![Selecione uma rede virtual](./media/backup-azure-restore-vms/restore-sa.png)

3. Selecione uma rede virtual: a rede virtual (VNET) para a máquina virtual deve ser selecionada no momento da criação da VM. A interface do usuário de restauração mostra todas as VNETs nessa assinatura que podem ser usadas. Não é obrigatório selecionar uma VNET para a VM restaurada – você poderá se conectar à máquina virtual restaurada pela Internet mesmo que a VNET não esteja aplicada.

    Se o serviço de nuvem selecionado estiver associado a uma rede virtual, você não poderá alterar a rede virtual.

    ![Selecione uma rede virtual](./media/backup-azure-restore-vms/restore-cs-vnet.png)

4. Selecione uma sub-rede: no caso de a VNET ter sub-redes, por padrão, a primeira sub-rede será selecionada. Selecione a sub-rede de sua escolha entre as opções do menu suspenso. Para obter detalhes de sub-rede, acesse a extensão Redes na [home page do portal](https://manage.windowsazure.com/), vá para **Redes Virtuais**, selecione a rede virtual e detalhe em Configurar para ver os detalhes de sub-rede.

    ![Selecione uma sub-rede](./media/backup-azure-restore-vms/select-subnet.png)

5. Clique no ícone **Enviar** no assistente para enviar os detalhes e criar um trabalho de restauração.

## Rastrear a operação de restauração
Depois que você inserir todas as informações no assistente de restauração e enviar, o Backup do Azure tentará criar um trabalho para rastrear a operação de restauração.

![Criando um trabalho de restauração](./media/backup-azure-restore-vms/create-restore-job.png)

Se a criação do trabalho for bem-sucedida, você verá uma notificação do sistema indicando que o trabalho foi criado. Você pode obter mais detalhes clicando no botão **Exibir Trabalho**, que o levará para a guia **Trabalhos**.

![Trabalho de restauração criado](./media/backup-azure-restore-vms/restore-job-created.png)

Quando a operação de restauração for concluída, ela será marcada como concluída na guia **Trabalhos**.

![Trabalho de restauração concluído](./media/backup-azure-restore-vms/restore-job-complete.png)

Depois de restaurar a máquina virtual, talvez seja necessário reinstalar as extensões existentes na VM original e [modificar os pontos de extremidade](virtual-machines-set-up-endpoints) para a máquina virtual no Portal do Azure.

## Solucionar erros
Para a maioria dos erros, siga a ação recomendada sugerida nos detalhes do erro. Aqui estão alguns pontos adicionais para ajudar com a solução de problemas:

| Operação de backup | Detalhes do erro | Solução alternativa |
| -------- | -------- | -------|
| Restaurar | A restauração falhou com erro interno de nuvem | <ol><li>O serviço de nuvem no qual você está tentando restaurar está definido com configurações de DNS. Você pode verificar <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" 	Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Se houver um endereço configurado, significa que as configurações de DNS estão definidas.<br> <li>O serviço de nuvem no qual você está tentando restaurar está configurado com IP Reservado e as VMs existentes no serviço de nuvem estão no estado “interrompido”.<br>Você pode verificar se um serviço de nuvem tem IP reservado usando os seguintes cmdlets do PowerShell:<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName</ol> |

## Próximas etapas
- [Gerenciar máquinas virtuais](backup-azure-manage-vms)

<!------HONumber=July15_HO4-->