
<properties
	pageTitle="Gerenciar e monitorar os backups da máquina virtual implantados pelo Gerenciador de Recursos | Microsoft Azure"
	description="Saiba como gerenciar e monitorar os backups da máquina virtual implantados pelo Gerenciador de Recursos"
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
	ms.date="06/03/2016"
	ms.author="jimpark; markgal;"/>

# Gerenciar e monitorar backups de máquinas virtuais do Azure

> [AZURE.SELECTOR]
- [Gerenciar backups da VM do Azure](backup-azure-manage-vms.md)
- [Gerenciar backups da VM Clássica](backup-azure-manage-vms-classic.md)

Este artigo fornece orientação sobre como gerenciar os backups de suas máquinas virtuais (VM), bem como explica as informações de backup disponíveis no painel do portal. As diretrizes neste artigo se aplicam ao uso das VMs com cofres dos Serviços de Recuperação. Este artigo não aborda a criação das máquinas virtuais, nem explica como protegê-las. Para ter instruções elementares sobre como proteger as VMs implantadas pelo Azure Resource Manager no Azure com um cofre dos Serviços de Recuperação, consulte [Primeiro examinar: fazer backup das VMs em um cofre dos Serviços de Recuperação](backup-azure-vms-first-look-arm.md).

## Acessar cofres e máquinas virtuais protegidas

No portal do Azure, o painel do cofre dos Serviços de Recuperação fornece acesso a informações sobre o cofre, inclusive:

- o instantâneo de backup mais recente, que também é o ponto de restauração mais recente <br>
- política de backup <br>
- tamanho total de todos os instantâneos do backup <br>
- número de máquinas virtuais que são protegidas com o cofre <br>

Muitas tarefas de gerenciamento com um backup da máquina virtual começam abrindo o cofre no painel de controle. No entanto, como os cofres podem ser usados para proteger vários itens (ou várias máquinas virtuais), para exibir os detalhes sobre uma máquina virtual específica, você precisa abrir o painel do item do cofre. O procedimento a seguir mostra como abrir o *painel do cofre*, em seguida, ir para o *painel do item do cofre*. Há "dicas" em ambos os procedimentos que mostram como adicionar o cofre e o item do cofre ao painel do Azure usando o Pin no comando do painel. Fixar no painel é uma maneira de criar um atalho para o cofre ou o item. Você também pode executar os comandos comuns a partir do atalho.

>[AZURE.TIP] Se você tiver vários painéis e folhas abertos, poderá usar o controle azul escuro na parte inferior da janela para deslizar a exibição no painel do Azure.

![Exibição completa com controle deslizante](./media/backup-azure-manage-vms/bottom-slider.png)

### Abra um cofre dos Serviços de Recuperação no painel:

1. Entre no [Portal do Azure](https://portal.azure.com/).

2. No menu Hub, clique em **Procurar** e na lista de recursos, digite **Serviços de Recuperação**. Quando você começar a digitar, a lista será filtrada com base em sua entrada. Clique em **Cofre dos Serviços de Recuperação**.

    ![Criar Cofre de Serviços de Recuperação - etapa 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png) <br/>

    A lista de cofres de Serviços de Recuperação será exibida.

    ![Listar cofres de Serviços de Recuperação](./media/backup-azure-manage-vms/list-o-vaults.png) <br/>

    >[AZURE.TIP] Se você fixar um cofre no Painel do Azure, esse cofre ficará imediatamente acessível quando você abrir o portal do Azure. Para fixar um cofre no painel, na lista de cofres, clique com o botão direito no cofre e selecione **Fixar no painel**.

3. Na lista de cofres, escolha o cofre para abrir seu painel. Quando você seleciona o cofre, o painel do cofre e a folha **Configurações** são abertos. Na imagem a seguir, o painel **Cofre Contoso** é destacado.

    ![Abrir o painel do cofre e a folha de Configurações](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### Abra o painel do item do cofre

No procedimento anterior, você abriu o painel do cofre. Para abrir o painel do item do cofre:

1. No painel do cofre, no bloco **Itens de Backup**, clique em **Máquinas Virtuais do Azure**.

    ![Bloco Abrir itens de backup](./media/backup-azure-manage-vms/contoso-vault.png)

    A folha **Itens de Backup** lista o último trabalho de backup de cada item. Neste exemplo, há uma máquina virtual, demovm-markgal, protegida por esse cofre.

    ![Bloco Itens de backup](./media/backup-azure-manage-vms/backup-items-blade.png)

    >[AZURE.TIP] Para facilitar o acesso, você pode fixar um item do cofre no Painel do Azure. Para fixar um item do cofre, na lista de itens do cofre, clique com o botão direito no item e selecione **Fixar no painel**.

2. Na folha **Itens de Backup**, clique no item para abrir o painel do item do cofre.

    ![Bloco Itens de backup](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    O painel do item do cofre e sua folha **Configurações** são abertos.

    ![Painel dos itens de backup com a folha Configurações](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    No painel de itens do cofre, você pode executar muitas tarefas de gerenciamento principais, como:

    - alterar as políticas ou criar uma nova política de backup <br>
	- exibir pontos de restauração e ver seu estado de consistência <br>
	- fazer um backup sob demanda de uma máquina virtual <br>
	- interromper a proteção das máquinas virtuais <br>
	- retomar a proteção de uma máquina virtual <br>
	- excluir os dados do backup (ou ponto de recuperação) <br>
	- [restaurar um backup (ou o ponto de recuperação)](./backup-azure-arm-restore-vms.md#restore-a-recovery-point) <br>

Para os procedimentos a seguir, o ponto de partida é o painel de itens do cofre.

## Alterar as políticas ou Criar uma nova política de backup

1. No [painel de itens do cofre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **Todas as Configurações** para abrir a folha **Configurações**.

    ![Folha Política de backup](./media/backup-azure-manage-vms/all-settings-button.png)

2. Na folha **Configurações**, clique em **Política de backup** para abrir essa folha.

    Na folha, são mostrados os detalhes do intervalo de retenção e da frequência dos backups.

    ![Folha Política de backup](./media/backup-azure-manage-vms/backup-policy-blade.png)

3. No menu **Escolher política de backup**:
    - Para alterar as políticas, selecione uma política diferente e clique em **Salvar**. A nova política será aplicada imediatamente no cofre. <br>
    - Para criar uma nova política, selecione **Criar Nova**.

    ![Backup de máquinas virtuais](./media/backup-azure-manage-vms/backup-policy-create-new.png)

    Para obter instruções sobre como criar uma política de backup, consulte [Definindo uma política de backup](backup-azure-manage-vms.md#defining-a-backup-policy).


## Backup sob demanda de uma máquina virtual
Você pode obter um backup sob demanda de uma máquina virtual quando ela estiver configurada para proteção. Se o backup inicial estiver pendente para a máquina virtual, o backup sob demanda criará uma cópia completa da máquina virtual no cofre dos Serviços de Recuperação. Se o backup inicial for concluído, um backup sob demanda enviará apenas as alterações do instantâneo anterior para o cofre dos Serviços de Recuperação, ou seja, é sempre incremental.

>[AZURE.NOTE] O intervalo de retenção para um backup sob demanda é o valor de retenção especificado para o ponto de backup Diário na política. Se nenhum ponto de backup Diário for selecionado, o ponto de backup Semanal será usado.

Para inicializar um backup sob demanda de uma máquina virtual:

1. No [painel de itens do cofre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **Fazer backup agora**.

    ![Botão Fazer backup agora](./media/backup-azure-manage-vms/backup-now-button.png)

    O portal verificará você deseja iniciar um trabalho de backup sob demanda. Clique em **Sim** para iniciar o trabalho de backup.

    ![Botão Fazer backup agora](./media/backup-azure-manage-vms/backup-now-check.png)

    O trabalho de backup cria um novo ponto de recuperação. O intervalo de retenção do ponto de recuperação criado é o mesmo do especificado na política associada à máquina virtual. Para acompanhar o progresso do trabalho, no painel do cofre, clique no bloco **Trabalhos de Backup**.


## Interromper a proteção de máquinas virtuais
Se você optar por interromper a proteção de uma máquina virtual, será perguntado se deseja manter os pontos de recuperação. Há duas maneiras de interromper a proteção das máquinas virtuais: parar todos os futuros trabalhos de backup e excluir todos os pontos de recuperação, ou interromper todos os futuros trabalhos de backup, mas deixar os pontos de recuperação. Há um custo associado a deixar os pontos de recuperação no armazenamento. No entanto, a vantagem de deixar os pontos de recuperação é que você pode restaurar a máquina virtual mais tarde, se desejado. Para obter detalhes de preço dessas máquinas virtuais, clique [aqui](https://azure.microsoft.com/pricing/details/backup/). Se você optar por excluir todos os pontos de recuperação, não haverá nenhuma opção para restaurar a máquina virtual.

Para interromper a proteção para uma máquina virtual:

1. No [painel de itens do cofre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **Interromper backup**.

    ![Botão Interromper backup](./media/backup-azure-manage-vms/stop-backup-button.png)

    A folha Interromper Backup é aberta.

    ![Folha Interromper backup](./media/backup-azure-manage-vms/stop-backup-blade.png)

2. Na folha **Interromper Backup**, escolha se deseja manter ou excluir os dados do backup. A caixa de informações fornece detalhes sobre sua escolha.

    ![Parar a proteção](./media/backup-azure-manage-vms/retain-or-delete-option.png)

3. Se você escolheu manter os dados do backup, vá para a etapa 4. Se escolheu excluir os dados do backup, confirme se deseja interromper os trabalhos de backup e excluir os pontos de recuperação - digite o nome do item.

    ![Interromper verificação](./media/backup-azure-manage-vms/item-verification-box.png)

    Se você não tiver certeza do nome do item, passe o mouse sobre o ponto de exclamação para exibir o nome. Além disso, o nome do item está sob **Interromper Backup** na parte superior da folha.

4. Como opção, forneça um **Motivo** ou **Comentário**.

5. Para interromper o trabalho de backup do item atual, clique em ![Botão Interromper backup](./media/backup-azure-manage-vms/stop-backup-button-blue.png)

    Uma mensagem de notificação permite que você conheça os trabalhos de backup que foram interrompidos.

    ![Confirmar a interrupção da proteção](./media/backup-azure-manage-vms/stop-message.png)


## Retomar a proteção de uma máquina virtual
Se a opção **Reter Dados do Backup** foi selecionada quando a proteção da máquina virtual foi interrompida, será possível retomar a proteção. Se a opção **Excluir Dados do Backup** foi escolhida, então, a proteção da máquina virtual não poderá retomar.

Retomar a proteção da máquina virtual

1. No [painel de itens do cofre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **Retomar backup**.

    ![Retomar proteção](./media/backup-azure-manage-vms/resume-backup-button.png)

    A folha Política de Backup será aberta.

    >[AZURE.NOTE] Ao proteger novamente a máquina virtual, você pode escolher uma política diferente da política com a qual a máquina virtual foi inicialmente protegida.

2. Siga as etapas em [Alterar políticas ou Criar uma nova política de backup](backup-azure-manage-vms.md#change-policies-or-create-a-new-backup-policy), para atribuir a política da máquina virtual.

    Depois da política de backup ser aplicada à máquina virtual, você verá a seguinte mensagem.

    ![VM protegida com êxito](./media/backup-azure-manage-vms/success-message.png)

## Excluir dados de backup
Você pode excluir os dados de backup associados a uma máquina virtual durante o trabalho **Interromper backup** a qualquer momento depois dos backups terem sido interrompidos. Se for útil permitir a interrupção do trabalho de backup para uma máquina virtual e aguardar dias ou semanas antes de decidir se deseja excluir os pontos de recuperação, então, isso será possível. Ao contrário de restaurar os pontos de recuperação, ao excluir os dados do backup, você não pode escolher os pontos de recuperação específicos para excluir. Se você escolher excluir, apagará todos os pontos de recuperação associados ao item.

O procedimento a seguir pressupõe que o trabalho de Backup da máquina virtual foi interrompido ou desabilitado. Apenas quando o trabalho de Backup foi desabilitado, as opções **Retomar backup** e **Excluir backup** estarão disponíveis no painel de itens do cofre.

![Botões para retomar e excluir](./media/backup-azure-manage-vms/resume-delete-buttons.png)

Para excluir os dados de backup em uma máquina virtual com o *Backup desabilitado*:

1. No [painel de itens do cofre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), clique em **Excluir backup**.

    ![Tipo de VM](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    A folha **Excluir Dados do Backup** será aberta.

    ![Tipo de VM](./media/backup-azure-manage-vms/delete-backup-blade.png)

2. Você deve digitar o nome do item para confirmar que deseja excluir os pontos de recuperação.

    ![Interromper verificação](./media/backup-azure-manage-vms/item-verification-box.png)

    Se você não tiver certeza do nome do item, passe o mouse sobre o ponto de exclamação para exibir o nome. Além disso, o nome do item está sob **Excluir Dados do Backup** na parte superior da folha.

3. Como opção, forneça um **Motivo** ou **Comentário**.

4. Para excluir os dados do backup do item atual, clique em ![Botão Interromper backup](./media/backup-azure-manage-vms/delete-button.png)

    Uma mensagem de notificação permite que você conheça os dados do backup que foram excluídos.

## Operações de auditoria
Você pode examinar a operação e os logs de evento para ver as operações de gerenciamento executadas no cofre dos Serviços de Recuperação. Os logs de operações permitem um ótimo suporte a auditoria e análise posterior das operações de backup. Pode usar o recurso logs de Auditoria para exibir os logs para todas as operações *na assinatura*. Para obter informações adicionais sobre os eventos, operações e logs de auditoria, consulte o artigo [Exibir eventos e logs de auditoria](../azure-portal/insights-debugging-with-events.md). Use a configuração **Logs de auditoria** para exibir os eventos e os logs das operações específicos de um cofre dos Serviços de Recuperação ou um item do cofre.

As seguintes operações são registradas nos logs de Auditoria:

- Registrar
- Cancelar o registro
- Configurar a proteção
- Backup (backup agendado ou sob demanda)
- Restaurar
- Parar a proteção
- Excluir dados de backup
- Adicionar política
- Excluir política
- Atualizar política
- Cancelar trabalho

Para exibir os logs de evento para um cofre dos Serviços de Recuperação:

1. No [painel do cofre](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard), procure e clique em **Logs de Auditoria** para abrir a folha **Eventos**.

    ![Logs de Auditoria](./media/backup-azure-manage-vms/audit-logs.png)

    A folha Eventos será aberta para os eventos operacionais filtrados apenas para o cofre atual. A folha mostra a lista de eventos Crítico, Erro, Aviso e Informativo na última semana. O período de tempo é um valor padrão definido nas configurações **Filtro**. A folha **Eventos** também mostra um gráfico de barras controlando quando os eventos ocorreram. Se você não quiser ver o gráfico de barras no menu **Eventos**, clique em **Mostrar gráfico** para desativar o gráfico.

    ![Filtro de Logs da Auditoria](./media/backup-azure-manage-vms/audit-logs-filter.png)

2. Para obter informações adicionais sobre uma determinada operação, na lista de Operações, clique nessa operação para abrir sua folha. A folha contém informações detalhadas sobre a operação e uma lista dos Eventos ocorridos no Período de tempo.

    ![Detalhes da Operação](./media/backup-azure-manage-vms/audit-logs-details-window.png)

3. Para exibir informações detalhadas sobre um evento específico, na lista de eventos, clique na operação para abrir sua folha Detalhes.

    ![Detalhes do Evento](./media/backup-azure-manage-vms/audit-logs-details-window-deep.png)

    As informações no nível do Evento são tão detalhadas quanto as informações obtidas. O restante deste procedimento explica como editar ou alterar as informações disponíveis.

4. Para editar a lista de filtros disponíveis, no menu **Eventos**, clique em **Filtrar** abrir essa folha.

    ![abrir folha do filtro](./media/backup-azure-manage-vms/audi-logs-filter-button.png)

5. Na folha **Filtrar**, ajustar os filtros **Nível**, **Período** e **Chamador**. Os outros filtros não estão disponíveis depois que foram definidos para fornecer as informações atuais para o cofre dos Serviços de Recuperação.

    ![Detalhes da consulta dos Logs de Auditoria](./media/backup-azure-manage-vms/filter-blade.png)

    Você pode especificar o **Nível** do evento: Crítico, Erro, Aviso ou Informativo. Você pode escolher qualquer combinação de Níveis de evento, mas deve ter, pelo menos, um Nível selecionado. Ative ou desative o Nível. O filtro **Período** permite que você especifique o período de tempo para capturar os eventos. Se você usar um Período personalizado, pode definir as horas de início e término.

6. Quando estiver pronto para consultar os logs de operações usando o filtro, clique em **Atualizar**. Os resultados serão exibidos na folha **Eventos**.

    ![Detalhes da Operação](./media/backup-azure-manage-vms/edited-list-of-events.png)


## Notificações de alerta
Você pode obter notificações de alerta personalizadas para os trabalhos no portal. Para obter esses trabalhos, defina regras de alerta baseadas no PowerShell nos eventos de logs operacionais. Use o *PowerShell versão 1.3.0 ou posterior*.

Para definir uma notificação personalizada para o alerta das falhas de backup, use um comando como este:

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.Backup/RecoveryServicesVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/RecoveryServicesVault/trinadhVault -Actions $actionEmail
```

**ResourceId**: você pode obter essas informações nos logs de Auditoria. O ResourceId é fornecido na coluna Recurso dos logs de Operação.

**OperationName**: isto terá o formato  
"Microsoft.RecoveryServices/recoveryServicesVault/<EventName>" em que EventName é um Register, Unregister, ConfigureProtection, Backup, Restore, StopProtection, DeleteBackupData, CreateProtectionPolicy, DeleteProtectionPolicy, UpdateProtectionPolicy

**Status**: os valores com suporte são Started, Succeeded e Failed.

**ResourceGroup** : esse é o Grupo de Recursos ao qual o recurso pertence. Você pode adicionar a coluna Grupo de Recursos aos logs gerados. O Grupo de Recursos é um dos tipos disponíveis das informações dos eventos.

**Name**: o nome da Regra de Alerta.

**CustomEmail**: especifica o endereço de email personalizado para o qual você deseja enviar a notificação de alerta

**SendToServiceOwners**: essa opção envia a notificação de alerta para todos os administradores e coadministradores da assinatura. Ele pode ser usado no cmdlet **New-AzureRmAlertRuleEmail**

### Limitações sobre alertas
Os alertas baseados em eventos estão sujeitos às seguintes limitações:

1. Os alertas são disparados em todas as máquinas virtuais no cofre dos Serviços de Recuperação. Não é possível personalizar o alerta para um conjunto específico de máquinas virtuais em um cofre dos Serviços de Recuperação.
2. Esse recurso está na Visualização. [Saiba mais](../azure-portal/insights-powershell-samples.md#create-alert-rules)
3. Os alertas são enviados de "alerts-noreply@mail.windowsazure.com". No momento, você não pode modificar o remetente do email.

[AZURE.INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## Próximas etapas

Para obter informações sobre como recriar uma máquina virtual a partir de um ponto de recuperação, verifique [Restaurar VMs do Azure](backup-azure-restore-vms.md). Se você precisar de informações sobre como proteger suas máquinas virtuais, consulte [Primeiro examinar: fazer backup das VMs em um cofre dos Serviços de Recuperação](backup-azure-vms-first-look-arm.md).

<!---HONumber=AcomDC_0608_2016-->