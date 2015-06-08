<properties
	pageTitle="Backup do Azure - restaurar uma máquina virtual"
	description="Saiba como restaurar uma máquina virtual do Azure"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/27/2015"
	ms.author="aashishr"/>

# Restaurar uma máquina virtual
Você pode restaurar uma máquina virtual para uma nova VM os backups armazenados no cofre de backup do Azure usando a ação de restauração.

## Escolha um item para restaurar

1. Navegue até o **itens protegidos** guia e selecione a máquina virtual que você deseja restaurar uma nova VM.

    ![Itens protegidos](./media/backup-azure-restore-vms/protected-items.png)

    O **ponto de recuperação** coluna o **itens protegidos** página informará o número de pontos de recuperação para uma máquina virtual. O **ponto de recuperação mais recente** coluna indica a hora do backup mais recente do que uma máquina virtual pode ser restaurada.

2. Clique em **restauração** para abrir o **restaurar um Item** assistente.

    ![Restaurar um item](./media/backup-azure-restore-vms/restore-item.png)

## Selecione um ponto de recuperação

1. No **Selecionar um ponto de recuperação** tela, você pode restaurar de ponto de recuperação mais recente ou de um ponto anterior no tempo. A opção padrão selecionada quando o assistente é aberto é o ponto de recuperação mais recente.

    ![Selecione um ponto de recuperação](./media/backup-azure-restore-vms/select-recovery-point.png)

2. Para obter um ponto anterior no tempo, escolha o **Selecionar data** opção na lista suspensa e selecione uma data no controle de calendário, clicando no ícone do calendário. No controle, todas as datas que têm pontos de recuperação são preenchidas com um tom cinza claro em podem ser selecionadas pelo usuário.

    ![Selecione uma data](./media/backup-azure-restore-vms/select-date.png)

    Quando você clicar em uma data no controle de calendário, os pontos de recuperação disponível em que data será mostrada na tabela de pontos de recuperação abaixo. O **tempo** coluna indica a hora em que o instantâneo foi tirado. O **tipo** coluna exibe o [consistência](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) do ponto de recuperação. O cabeçalho da tabela mostra o número de pontos de recuperação disponíveis naquele dia entre parênteses.

    ![Pontos de recuperação](./media/backup-azure-restore-vms/recovery-points.png)

3. Selecione o ponto de recuperação do **pontos de recuperação** a tabela e clique na seta Avançar para ir para a próxima tela.

## Especifique um local de destino

1. No **Selecionar instância de restauração** tela especificar detalhes de como restaurar a máquina virtual.

  - Especifique o nome da máquina virtual: em um determinado serviço de nuvem, o nome da máquina virtual deve ser exclusivo. Se você pretende substituir uma VM existente com o mesmo nome, primeiro exclua a máquina virtual existente e discos de dados e, em seguida, restaurar os dados do Backup do Azure.
  - Selecione um serviço de nuvem de VM: isso é obrigatório para a criação de uma máquina virtual. Você pode optar por usar um serviço de nuvem existente ou criar um novo serviço de nuvem.

        Whatever cloud service name is picked should be globally unique. Typically, the cloud service name gets associated with a public-facing URL in the form of [cloudservice].cloudapp.net. Azure will not allow you to create a new cloud service if the name has already been used. If you choose to create select create a new cloud service, it will be given the same name as the virtual machine – in which case the VM name picked should be unique enough to be applied to the associated cloud service.

        We only display cloud services and virtual networks that are not associated with any affinity groups in the restore instance details. [Learn More](https://msdn.microsoft.com/en-us/library/azure/jj156085.aspx).

2. Selecione uma conta de armazenamento para a VM: isso é obrigatório para criação da máquina virtual. Você pode selecionar de contas de armazenamento existente na mesma região que o Cofre de Backup do Azure. Não há suporte para contas de armazenamento são zona redundante ou do tipo de armazenamento Premium.

    Se não houver nenhuma conta de armazenamento com configuração com suporte, crie uma conta de armazenamento de configuração com suporte antes de iniciar a operação de restauração.

    ![Selecione uma rede virtual](./media/backup-azure-restore-vms/restore-sa.png)

3. Selecione uma rede Virtual: A rede virtual (VNET) para a máquina virtual deve ser selecionada no momento da criação da máquina virtual. A restauração a interface do usuário mostra todos os VNETs nessa assinatura pode ser usado. Não é obrigatório para selecionar uma VNET para a máquina virtual restaurada – você será capaz de se conectar à máquina virtual restaurada pela internet mesmo que não é aplicada a VNET.

    Se o serviço de nuvem selecionado estiver associado uma rede virtual, você não pode alterar a rede virtual.

    ![Selecione uma rede virtual](./media/backup-azure-restore-vms/restore-cs-vnet.png)

4. Selecione uma sub-rede: no caso de VNET tiver sub-redes, por padrão a primeira sub-rede será selecionada. Escolha as opções de menu suspenso da sub-rede de sua escolha. Para obter detalhes de sub-rede, vá para a extensão de redes no [home page do portal](https://manage.windowsazure.com/), vá para redes virtuais e selecione a rede virtual e Detalhar configurar para ver os detalhes de sub-rede.

    ![Selecione uma sub-rede](./media/backup-azure-restore-vms/select-subnet.png)

5. Clique no **Enviar** ícone no Assistente para enviar os detalhes e criar um trabalho de restauração.

## Controlar a operação de restauração
Depois de inserir todas as informações no Assistente de restauração e enviou o Azure Backup tentará criar um trabalho para controlar a operação de restauração.

![Criando um trabalho de restauração](./media/backup-azure-restore-vms/create-restore-job.png)

Se a criação de trabalho for bem-sucedida, você verá que um trabalho indicando de notificação do sistema é criado. Você pode obter mais detalhes clicando na **Exibir trabalho** botão que levará para **trabalhos** guia.

![Trabalho de restauração criado](./media/backup-azure-restore-vms/restore-job-created.png)

Quando a operação de restauração for concluída, ele será marcado como concluído no **trabalhos** guia.

![Conclusão do trabalho de restauração](./media/backup-azure-restore-vms/restore-job-complete.png)

Depois de restaurar a máquina virtual talvez seja necessário reinstalar as extensões existente na VM original e [recriar os pontos de extremidade](virtual-machines-set-up-endpoints) para a máquina virtual no portal do Azure.

## Solucionar erros
Para a maioria dos erros, siga a ação recomendada sugerida nos detalhes do erro. Aqui estão alguns pontos adicionais para ajudar com a solução de problemas:

| Operação de backup | Detalhes do erro | Solução alternativa |
| -------- | -------- | -------|
| Restaurar | Restauração falhou com erro interno de nuvem | <ol><li>Serviço de nuvem à qual você está tentando restaurar é configurado com as configurações de DNS. Você pode verificar <br>$deployment = Get-AzureDeployment - ServiceName "ServiceName"-Slot "Produção" Get-AzureDns - DnsSettings $deployment. DnsSettings<br>se não houver endereço configurado, isso significa que as configurações de DNS são configuradas.<br> <li>Serviço de nuvem para o qual você está tentando restaurar é configurado com o IP reservado e máquinas virtuais existentes no serviço de nuvem estão no estado parado.<br>Você pode verificar a um serviço de nuvem reservou IP usando os seguintes cmdlets do powershell:<br>$deployment = "servicename" Get-AzureDeployment - ServiceName-DEP Slot $ "Produção". ReservedIPName</ol> |

## Próximas etapas
- [Gerenciar máquinas virtuais](backup-azure-manage-vms)

<!---HONumber=GIT-SubDir-->