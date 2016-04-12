<properties
	pageTitle="Proteger VMs no Azure com o Backup do Azure | Microsoft Azure"
	description="Proteja VMs do Azure com o serviço Backup do Azure. O tutorial explica como criar um cofre, registrar VMs, criar uma política e proteger VMs no Azure."
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="03/30/2016"
	ms.author="markgal; jimpark"/>


# Introdução: Fazendo backup de máquinas virtuais do Azure

> [AZURE.SELECTOR]
- [Fazer backup das VMs ARM](backup-azure-vms-first-look-arm.md)
- [Fazer backup das VMs do modo Clássico](backup-azure-vms-first-look.md)

Este artigo é um tutorial que mostrará a você o conjunto de etapas para a preparação do seu ambiente do Azure para fazer backup de uma máquina virtual (VM) do Azure. Este tutorial presume que você já tenha uma VM em sua assinatura do Azure e que já tenha tomado as medidas para permitir que o serviço de backup acesse a VM. Em um alto nível, veja as etapas que você concluirá.

![Visão geral do processo de backup de VM](./media/backup-azure-vms-first-look/BackupAzureVM.png)


1. Crie ou entre em sua assinatura do Azure.
2. Crie um cofre de backup ou identifique um cofre de backup existente *na mesma região que a VM*.
3. Use o portal do Azure para descobrir e registrar as máquinas virtuais na assinatura.
4. Instale o Agente de VM na máquina virtual (se você estiver usando uma VM da galeria do Azure, o Agente de VM já estará presente).
5. Crie a política para proteger as máquinas virtuais.
6. Execute o backup.

>[AZURE.NOTE] O Azure tem dois modelos de implantação para a criação e o trabalho com recursos: [Gerenciador de Recursos e clássico](../resource-manager-deployment-model.md). Atualmente, o serviço Backup do Azure não oferece suporte a máquinas virtuais baseadas no Azure Resource Manager (ARM), também conhecidas como máquinas virtuais IaaS V2. Como as VMs IaaS V2 surgiram com o lançamento do novo portal do Azure, este tutorial foi projetado para ser usado com o tipo de máquinas virtuais que podem ser criadas no portal clássico do Azure.


## Etapa 1 - Criar um cofre de backup para uma VM

O cofre de backup é uma entidade que armazena todos os pontos de backups e de recuperação criados ao longo do tempo. O cofre de backup também contém as políticas de backup que serão aplicadas às máquinas virtuais incluídas no backup.

Esta imagem mostra os relacionamentos entre as várias entidades do Backup do Azure: ![Relação e entidades do Backup do Azure](./media/backup-azure-vms-prepare/vault-policy-vm.png)

Para criar um cofre de backup:

1. Entre no [Portal do Azure](http://manage.windowsazure.com/).

2. No portal do Azure, clique em **Novo** > **Serviços de Dados** > **Serviços de Recuperação** > **Cofre de Backup** > **Criação Rápida** (veja a imagem abaixo).

    ![Criar cofre de backup](./media/backup-azure-vms-first-look/backup-vaultcreate.png)

3. Em **Nome**, insira um nome amigável para identificar o cofre. O nome precisa ser exclusivo para a assinatura do Azure. Digite um nome que contenha de 2 a 50 caracteres. Ele deve começar com uma letra e pode conter apenas letras, números e hifens.

4. Em **Região**, selecione a região geográfica para o cofre. O cofre **deve** estar na mesma região que as máquinas virtuais que você deseja proteger.

    Se não souber ao certo qual região contém sua VM, feche a caixa de diálogo de criação do cofre e acesse a lista de Máquinas Virtuais no portal. Se você tiver máquinas virtuais em várias regiões, precisará criar um cofre de backup em cada região. No entanto, conclua a criação do cofre na primeira região antes de prosseguir para a próxima. Não é necessário especificar contas de armazenamento para armazenar os dados de backup; o cofre de backup e o serviço de Backup do Azure cuidarão disso automaticamente.

5. Em **Assinatura**, selecione a assinatura que você deseja associar ao cofre de backup. Só haverá múltiplas opções se sua conta organizacional estiver associada a várias assinaturas do Azure.

6. Clique em **Criar cofre**. Pode levar algum tempo para que o cofre de backup seja criado. Monitore as notificações de status na parte inferior do portal.

    ![Criar notificação de cofre](./media/backup-azure-vms-first-look/create-vault-demo.png)

    Uma mensagem confirmará que o cofre foi criado com êxito. Ele será listado na página de **serviços de recuperação** como **Ativo**.

    ![Criar notificação de cofre](./media/backup-azure-vms-first-look/create-vault-demo-success.png)

7. Na lista de cofres na página **Serviços de Recuperação**, clique no cofre que você criou para iniciar a página **Início Rápido**.

    ![Lista de cofres de backup](./media/backup-azure-vms-first-look/active-vault-demo.png)

8. Na página **Início Rápido**, clique em **Configurar** para abrir a opção de replicação de armazenamento. ![Lista de cofres de backup](./media/backup-azure-vms-first-look/configure-storage.png)

9. Na opção **replicação de armazenamento**, escolha a opção de replicação para o cofre.

    ![Lista de cofres de backup](./media/backup-azure-vms-first-look/backup-vault-storage-options-border.png)

    Por padrão, seu cofre tem armazenamento com redundância geográfica. Se você estiver usando o Azure como um ponto de extremidade de armazenamento de backup principal, será recomendável continuar usando o armazenamento com redundância geográfica. Se você estiver usando o Azure como ponto de extremidade de armazenamento de backup não principal, poderá considerar a escolha do armazenamento com redundância local, o que reduzirá o custo de armazenar dados no Azure. Leia mais sobre as opções de armazenamento [com redundância geográfica](../storage/storage-redundancy.md#geo-redundant-storage) e [com redundância local](../storage/storage-redundancy.md#locally-redundant-storage) nesta [visão geral](../storage/storage-redundancy.md).

Depois de escolher a opção de armazenamento para o cofre, você estará pronto para associar a VM ao cofre. Para iniciar a associação, você deverá descobrir e registrar as máquinas virtuais do Azure.

## Etapa 2 - Descobrir e registrar máquinas virtuais do Azure
Antes de registrar a VM em um cofre, execute o processo de descoberta para garantir que todas as novas máquinas virtuais adicionadas à assinatura sejam identificadas. O processo consulta o Azure quanto à lista de máquinas virtuais na assinatura, juntamente com informações adicionais, como o nome do serviço de nuvem e a região.

1. Entre no [portal do Azure](http://manage.windowsazure.com/)

2. No portal clássico do Azure, clique em **Serviços de Recuperação** para abrir a lista de cofres dos Serviços de Recuperação. ![Selecionar carga de trabalho](./media/backup-azure-vms-first-look/recovery-services-icon.png)

3. Na lista de cofres dos **Serviços de Recuperação**, selecione o cofre que você deseja usar para fazer backup de uma VM.

    Quando você selecionar o cofre, ele será aberto na página **Início Rápido**

4. No menu do cofre (na parte superior da página), clique em **Itens Registrados**.

5. No menu **Tipo**, selecione **Máquina Virtual do Azure**.

    ![Selecionar carga de trabalho](./media/backup-azure-vms/discovery-select-workload.png)

6. Clique em **DESCOBRIR** na parte inferior da página. ![Botão Descobrir](./media/backup-azure-vms/discover-button-only.png)

    O processo de descoberta pode ser executado por alguns minutos, enquanto as máquinas virtuais estão sendo tabuladas. Há uma notificação na parte inferior da tela que informa você de que o processo está sendo executado.

    ![Descobrir VMs](./media/backup-azure-vms/discovering-vms.png)

    As alterações de notificação quando o processo é concluído.

    ![Descoberta concluída](./media/backup-azure-vms-first-look/discovery-complete.png)

7. Clique em **REGISTRAR** na parte inferior da página. ![Botão Registrar](./media/backup-azure-vms-first-look/register-icon.png)

8. No menu de atalho **Registrar Itens**, selecione as máquinas virtuais que você deseja registrar. Se houver duas ou mais máquinas virtuais com o mesmo nome, use o serviço de nuvem para fazer a distinção entre elas.

    >[AZURE.TIP] Várias máquinas virtuais podem ser registradas ao mesmo tempo.

    Um trabalho é criado para cada máquina virtual selecionada.

9. Clique em **Exibir Trabalho** na notificação para ir para a página **Trabalhos**.

    ![Registrar trabalho](./media/backup-azure-vms/register-create-job.png)

    A máquina virtual também aparece na lista de itens registrados junto com o status da operação de registro.

    ![Status de registro 1](./media/backup-azure-vms/register-status01.png)

    Quando a operação for concluída, o status no portal será alterado para refletir o estado *registrado*.

    ![Status de registro 2](./media/backup-azure-vms/register-status02.png)

## Etapa 3 - Instalar o agente de VM na máquina virtual.

O Agente de VM do Azure deve ser instalado na máquina virtual do Azure para a extensão de Backup funcionar. Se sua VM tiver sido criada da galeria do Azure, o agente de VM já estará presente na máquina virtual. No entanto, as máquinas virtuais migradas de datacenters locais não teriam o Agente de VM instalado. Nesse caso, o Agente de VM precisa ser instalado explicitamente. Antes de tentar fazer backup da máquina virtual do Azure, verifique se o agente de VM do Azure está instalado corretamente na máquina virtual (veja a tabela abaixo). Se você estiver criando uma VM personalizada, [verifique se a caixa de seleção **Instalar o Agente de VM** está marcada](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md) antes que a máquina virtual seja provisionada.

Saiba mais sobre o [Agente de VM](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) e [como instalá-lo](../virtual-machines/virtual-machines-windows-classic-manage-extensions.md).

A tabela a seguir oferece informações adicionais sobre o Agente de VM para VMs do Windows e do Linux.

| **Operação** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalação do agente de VM | <li>Baixe e instale o [agente MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisará de privilégios de Administrador para concluir a instalação. <li>[Atualize a propriedade de VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. | <li> Instale o [agente Linux](https://github.com/Azure/WALinuxAgent) mais recente do GitHub. Você precisará de privilégios de Administrador para concluir a instalação. <li> [Atualize a propriedade de VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. |
| Atualizar o Agente de VM | Atualizar o agente de VM é tão simples quanto reinstalar os [Binários do Agente de VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Verifique se nenhuma operação de backup está em execução durante a atualização do agente de VM. | Siga as instruções em [como atualizar o Agente de VM do Linux](../virtual-machines-linux-update-agent.md). <br>Verifique se nenhuma operação de backup está em execução durante a atualização do Agente de VM. |
| Validação da instalação do Agente de VM | <li>Navegue até a pasta *C:\\WindowsAzure\\Packages* na VM do Azure. <li>Você deve encontrar o arquivo WaAppAgent.exe presente.<li> Clique com o botão direito do mouse no arquivo, vá para **Propriedades** e selecione a guia **Detalhes**. O campo Versão do Produto deve ser 2.6.1198.718 ou mais recente. | N/D |


### Extensão de backup

Assim que o Agente de VM for instalado na máquina virtual, o serviço Backup do Azure instalará a extensão de backup no Agente de VM. O serviço do Backup do Azure atualiza e corrige perfeitamente a extensão de backup sem intervenção adicional do usuário.

A extensão de backup será instalada pelo serviço Backup, esteja a VM em execução ou não. Uma VM em execução oferece uma maior chance de obter um ponto de recuperação consistente com o aplicativo. No entanto, o serviço do Backup do Azure continuará a realizar o backup da VM mesmo quando ela estiver desativada e a extensão não puder ser instalada. Isso é conhecido como VM Offline. Nesse caso, o ponto de recuperação será *consistente com a falha*.


## Etapa 4 - Proteger máquinas virtuais do Azure
Agora você pode configurar uma política de backup e de retenção para a máquina virtual. Várias máquinas virtuais podem ser protegidas usando uma única ação de proteção. Os cofres do Backup do Azure criados depois de maio de 2015 poderão vir com uma política padrão interna ao cofre. Essa política padrão é fornecida com uma retenção padrão de 30 dias e agendamento de backup de uma vez por dia.

1. Navegue até o cofre de backup em **Serviços de Recuperação** no portal do Azure e clique em **Itens Registrados**.
2. Selecione **Máquina Virtual do Azure** no menu suspenso.

    ![Selecionar a carga de trabalho no portal](./media/backup-azure-vms/select-workload.png)

3. Na parte inferior da página, clique em **PROTEGER**.![Clique em Proteger](./media/backup-azure-vms-first-look/protect-icon.png)

    O **assistente Proteger Itens** é mostrado e lista *apenas* as máquinas virtuais registradas e não protegidas.

    ![Configurar proteção em escala](./media/backup-azure-vms/protect-at-scale.png)

4. Selecione as máquinas virtuais que deseja proteger.

    Se houver duas ou mais máquinas virtuais com o mesmo nome, use o Serviço de Nuvem para distinguir entre elas.

5. Em **Configurar proteção** escolha uma política existente ou crie uma nova política para proteger as máquinas virtuais que você identificou.

    Cada política de backup pode ter várias máquinas virtuais associadas a ela. No entanto, a máquina virtual só pode estar associada a apenas uma política em um determinado ponto no tempo.

    ![Proteger com nova política](./media/backup-azure-vms/policy-schedule.png)

    >[AZURE.NOTE] Uma política de backup também inclui um esquema de retenção para os backups agendados. Se você selecionar uma política de backup, não será possível modificar as opções de retenção na próxima etapa.

6. Em **Intervalo de Retenção**, defina o escopo diário, semanal, mensal e anual para os pontos de backup específicos.

    ![Proteger com retenção flexível](./media/backup-azure-vms/policy-retention.png)

    A política de retenção especifica o período de armazenamento de um backup. Você pode especificar políticas de retenção diferentes com base em quando o backup é feito. Por exemplo, um ponto de backup criado no final de cada trimestre talvez precise ser preservado por um período maior (para fins de auditoria), enquanto o ponto de backup feito diariamente, que funciona como um ponto de recuperação operacional, só deverá ser preservado por 90 dias.

    ![O backup da máquina virtual é realizado com ponto de recuperação](./media/backup-azure-vms/long-term-retention.png)

    Nesta imagem de exemplo:

    - **Política de retenção diária**: os backups diários são armazenados por 30 dias.
    - **Política de retenção semanal**: os backups semanais aos domingos serão armazenados por 104 semanas.
    - **Política de retenção mensal**: os backups mensais no último domingo do mês serão armazenados por 120 meses.
    - **Política de retenção anual**: os backups no primeiro domingo de cada mês de janeiro serão armazenados por 99 anos.

    Um trabalho é criado para configurar a política de proteção e associar as máquinas virtuais a essa política para cada máquina virtual selecionada.

6. Clique na guia **Trabalho** e escolha o filtro correto para exibir a lista de trabalhos do tipo **Configurar Proteção**.

    ![Configurar o trabalho de proteção](./media/backup-azure-vms/protect-configureprotection.png)


## Etapa 5 - Backup inicial

Após uma máquina virtual ser protegida com uma política, você poderá exibir essa relação na guia **Itens Protegidos**. Até que o backup inicial de uma VM tenha ocorrido, o **Status de Proteção** será mostrado como **Protegido - (backup inicial pendente)**. Por padrão, o primeiro backup agendado é o *backup inicial*.

![Backup pendente](./media/backup-azure-vms-first-look/protection-pending-border.png)

Para disparar o backup inicial imediatamente após a configuração de proteção:

1. Na página **Itens Protegidos**, clique no botão **Fazer Backup Agora** na parte inferior da página. ![Ícone de Fazer Backup Agora](./media/backup-azure-vms-first-look/backup-now-icon.png)

    O serviço de Backup do Azure cria um trabalho de backup para a operação de backup inicial.

2. Clique na guia **Trabalhos** para exibir a lista de trabalhos.

    ![Backup em andamento](./media/backup-azure-vms-first-look/protect-inprogress.png)

    >[AZURE.NOTE] Como parte da operação de backup, o serviço de Backup do Azure emite um comando para a extensão de backup em cada máquina virtual para limpar todas as gravações e capturar um instantâneo consistente.

    Após a conclusão do backup inicial, o status da máquina virtual na guia **Itens Protegidos** será *Protegida*.

    ![O backup da máquina virtual é realizado com ponto de recuperação](./media/backup-azure-vms/protect-backedupvm.png)

    >[AZURE.NOTE] O backup de máquinas virtuais é um processo local. Você não pode fazer backup de máquinas virtuais de uma região em um cofre de backup em outra região. Assim, para todas as regiões do Azure que tenham VMs que precisem de backup, pelo menos um cofre de backup deverá ser criado nessa região.

## Próximas etapas
Agora que você já fez um backup de uma VM, há várias etapas subsequentes pode poderiam ser interessantes. A etapa mais lógica é se familiarizar com a restauração de dados em uma VM, mas há tarefas de gerenciamento que ajudarão você a entender como manter seus dados seguros e como reduzir os custos.

- [Gerenciar e monitorar suas máquinas virtuais](backup-azure-manage-vms.md)
- [Restaurar máquinas virtuais](backup-azure-restore-vms.md)
- [Diretrizes de solução de problemas](backup-azure-vms-troubleshoot.md)


## Perguntas?
Se você tiver dúvidas ou gostaria de ver algum recurso incluído, [envie-nos seus comentários](http://aka.ms/azurebackup_feedback).

<!---HONumber=AcomDC_0406_2016-->