<properties
	pageTitle="Proteger VMs ARM com o Backup do Azure | Microsoft Azure"
	description="Proteja as VMs ARM com o serviço de Backup do Azure. Use os backups de VMs ARM e VMs de armazenamento Premium para proteger seus dados. Crie e registre um cofre de Serviços de Recuperação. Registre as VMs, crie uma política e proteger as VMs no Azure."
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keyword="backups; vm backup"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="03/31/2016"
	ms.author="markgal; jimpark"/>


# Introdução: fazer backup das VMs ARM em um cofre de Serviços de Recuperação

> [AZURE.SELECTOR]
- [Fazer backup das VMs ARM](backup-azure-vms-first-look-arm.md)
- [Fazer backup das VMs do modo Clássico](backup-azure-vms-first-look.md)

Este tutorial apresenta as etapas para criar um cofre de Serviços de Recuperação e fazer backup de uma VM (máquina virtual) do Azure. Os cofres dos Serviços de Recuperação protegem:

- VMs do ARM (Azure Resource Manager)
- VMs clássicas
- VMs de armazenamento Padrão
- VMs de armazenamento Premium

Para obter informações adicionais sobre como proteger VMs de armazenamento Premium, confira [Backup e restauração de VMs de armazenamento Premium](backup-introduction-to-azure-backup.md#back-up-and-restore-premium-storage-vms)

>[AZURE.NOTE] Este tutorial presume que você já tenha uma VM em sua assinatura do Azure e tenha tomado medidas para permitir que o serviço de backup acesse a VM. O Azure tem dois modelos de implantação para a criação e o trabalho com recursos: [Gerenciador de Recursos e clássico](../resource-manager-deployment-model.md). Este artigo é para usar com as VMs baseadas no ARM e no Gerenciador de Recursos.

Em um alto nível, veja as etapas que você concluirá.

1. Criar um cofre de Serviços de Recuperação para uma VM.
2. Use o portal do Azure para selecionar um Cenário, definir a Política e identificar os itens a proteger.
3. Execute o backup inicial.



## Etapa 1: Criar um cofre de Serviços de Recuperação para uma VM

Um cofre de Serviços de Recuperação é uma entidade que armazena todos os pontos de backup e recuperação criados ao longo do tempo. O cofre de Serviços de Recuperação também contêm a política de backup aplicada às VMs protegidas.

>[AZURE.NOTE] Fazer backup das VMs é um processo local. Não é possível fazer backup das VMs de um local para um cofre de Serviços de Recuperação em outro local. Portanto, para cada local do Azure com VMs que devem sofrer backup, pelo menos um cofre de Serviços de Recuperação deve existir nesse local.


Para criar um cofre de Serviços de Recuperação:

1. Entre no [Portal do Azure](https://portal.azure.com/).

2. No menu Hub, clique em **Procurar** e, na lista de recursos, digite **Serviços de Recuperação**. Quando você começar a digitar, a lista será filtrada com base em sua entrada. Clique em **Cofre de Serviços de Recuperação**.

    ![Criar Cofre de Serviços de Recuperação - etapa 1](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

    A lista de cofres de Serviços de Recuperação será exibida.

3. No menu **Cofres de Serviços de Recuperação**, clique em **Adicionar**.

    ![Criar Cofre de Serviços de Recuperação - etapa 2](./media/backup-azure-vms-first-look-arm/rs-vault-menu.png)

    A folha do cofre de Serviços de Recuperação será aberta, solicitando que você forneça o **Nome**, a **Assinatura**, o **Grupo de recursos** e o **Local**.

    ![Criar Cofre de Serviços de Recuperação - etapa 5](./media/backup-azure-vms-first-look-arm/rs-vault-attributes.png)

4. Em **Nome**, insira um nome amigável para identificar o cofre. O nome precisa ser exclusivo para a assinatura do Azure. Digite um nome que contenha de 2 a 50 caracteres. Ele deve começar com uma letra e pode conter apenas letras, números e hifens.

5. Clique em **Assinatura** para ver a lista de assinaturas disponíveis. Se você não tiver certeza sobre qual assinatura usar, utilize a assinatura padrão (ou sugerida). Só haverá múltiplas opções se sua conta organizacional estiver associada a várias assinaturas do Azure.

6. Clique em **Grupo de recursos** para ver a lista dos Grupos de recursos disponíveis ou clique em **Novo** para criar um novo Grupo de recursos. Para saber mais sobre os Grupos de recursos, confira [Uso do Portal do Azure para implantar e gerenciar os recursos do Azure](../azure-portal/resource-group-portal.md).

7. Clique em **Local** para selecionar a região geográfica do cofre. O cofre **deve** estar na mesma região que as máquinas virtuais que você deseja proteger.

    >[AZURE.IMPORTANT] Se você não souber ao certo em qual local sua VM existe, feche a caixa de diálogo de criação do cofre e vá para a lista de Máquinas Virtuais no portal. Se você tiver máquinas virtuais em várias regiões, será necessário criar um cofre de Serviços de Recuperação em cada região. Crie o cofre no primeiro local antes de ir para o próximo local. Não é necessário especificar as contas de armazenamento para armazenar os dados de backup - o cofre de Serviços de Recuperação e o serviço de Backup do Azure cuidarão disso automaticamente.

8. Clique em **Criar**. Pode levar algum tempo para o cofre de Serviços de Recuperação ser criado. Monitore as notificações de status na área superior direita no portal. Depois que o cofre é criado, ele é aberto no portal.

9. No cofre, clique em **Todas as configurações** > **Configuração de Backup** para exibir o **Tipo de replicação de armazenamento**. Escolha a opção de replicação de armazenamento para o cofre.

    ![Lista de cofres de backup](./media/backup-azure-vms-first-look-arm/choose-storage-configuration.png)

    Por padrão, seu cofre tem armazenamento com redundância geográfica. Se você estiver usando o Azure como um ponto de extremidade de armazenamento de backup principal, será recomendável continuar usando o armazenamento com redundância geográfica. Se você estiver usando o Azure como ponto de extremidade de armazenamento de backup não principal, poderá considerar a escolha do armazenamento com redundância local, o que reduzirá o custo de armazenar dados no Azure. Leia mais sobre as opções de armazenamento [com redundância geográfica](../storage/storage-redundancy.md#geo-redundant-storage) e [com redundância local](../storage/storage-redundancy.md#locally-redundant-storage) nesta [visão geral](../storage/storage-redundancy.md).

    Depois de escolher a opção de armazenamento para o cofre, você estará pronto para associar a VM ao cofre. Para iniciar a associação, você deverá descobrir e registrar as máquinas virtuais do Azure.

## Etapa 2: selecionar a política de configuração do cenário e definir os itens a proteger
Antes de registrar uma VM em um cofre, execute o processo de descoberta para garantir que todas as novas máquinas virtuais adicionadas à assinatura sejam identificadas. O processo consulta o Azure quanto à lista de máquinas virtuais na assinatura, juntamente com informações adicionais, como o nome do serviço de nuvem e a região.

1. Se você já tiver um cofre de Serviços de Recuperação aberto, vá para a etapa 2. Se você não tiver um cofre de Serviços de Recuperação aberto, mas estiver no portal do Azure, no menu Hub, clique em **Procurar**.

    - Na lista de recursos, digite **Serviços de Recuperação**.
    - Quando você começar a digitar, a lista será filtrada com base em sua entrada. Quando vir a opção **Cofres de Serviços de Recuperação**, clique nela.

    ![Criar Cofre de Serviços de Recuperação - etapa 1](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

    A lista de cofres de Serviços de Recuperação aparecerá.

    - Na lista de cofres de Serviços de Recuperação, selecione um cofre.

    O painel de cofres selecionados será aberto.

    ![Abrir a folha do cofre](./media/backup-azure-vms-first-look-arm/vault-settings.png)

2. No menu do painel de cofres, clique em **Backup** para abrir a folha Backup.

    ![Abrir a folha Backup](./media/backup-azure-vms-first-look-arm/backup-button.png)

    Quando a folha for aberta, o serviço de Backup pesquisará novas VMs na assinatura.

    ![Descobrir VMs](./media/backup-azure-vms-first-look-arm/discovering-new-vms.png)

3. Na folha Backup, clique em **Cenário** para abrir a folha Cenário.

    ![Abrir a folha Cenário](./media/backup-azure-vms-first-look-arm/select-backup-scenario-one.png)

4. Na folha Cenário, no menu **Tipo de Backup**, selecione **Backup da máquina virtual do Azure** e clique em **OK**.

    ![Abrir a folha Cenário](./media/backup-azure-vms-first-look-arm/select-rs-backup-scenario-two.png)

    A folha Cenário fecha e a folha Política de Backup abre.

5. Na folha Backup, selecione a política de backup que você deseja aplicar no cofre e clique em **OK**.

    ![Selecionar a política de backup](./media/backup-azure-vms-first-look-arm/setting-rs-backup-policy.png)

    A política padrão é listada nos detalhes. Se você quiser criar uma nova política, selecione **Criar Nova**. Para obter instruções sobre como definir uma política de backup, confira [Definição de uma política de backup](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). Quando você clicar em OK, a política de backup será associada ao cofre. Em seguida, escolha as VMs para associar ao cofre.

6. Escolha as máquinas virtuais para associar à política especificada e clique em **Selecionar**.

    ![Selecionar carga de trabalho](./media/backup-azure-vms-first-look-arm/select-vms-to-backup.png)

    Se não encontrar a VM desejada na lista, clique em **Atualizar**. Se você ainda não vir a VM desejada, verifique se ela existe no mesmo local do Azure do cofre de Serviços de Recuperação.

7. Agora que você definiu todas as configurações para o cofre, na folha Backup, clique em **Habilitar Backup** na parte inferior da página. Isso implanta a política para o cofre e as VMs.

    ![Habilitar o backup](./media/backup-azure-vms-first-look-arm/enable-backup-settings.png)


## Etapa 3: Backup inicial

Após a implantação de uma política de backup na máquina virtual, isso não significa que os dados sofreram backup. Por padrão, o primeiro backup agendado (como definido na política de backup) é o backup inicial. Até que o backup inicial ocorra, o Status do Último Backup na folha **Trabalhos de Backup** aparece como **Aviso (backup inicial pendente)**.

![Backup pendente](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

A menos que o backup inicial esteja prestes a começar, é recomendável que você execute o **Backup Agora**.

Para executar o **Backup Agora**:

1. No painel do cofre, no bloco **Backup**, clique em **Máquinas Virtuais do Azure** <br/> ![Ícone Configurações](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

    A folha **Itens de Backup** será aberta.

2. Na folha **Itens de Backup**, clique com o botão direito do mouse no cofre do qual deseja fazer backup e clique em **Fazer backup agora**.

    ![Ícone Configurações](./media/backup-azure-vms-first-look-arm/back-up-now.png)

    O Trabalho de backup será disparado. <br/>

    ![Trabalho de backup iniciado](./media/backup-azure-vms-first-look-arm/backup-triggered.png)

3. Para exibir que o backup inicial foi concluído, no painel do cofre, no bloco **Trabalhos de Backup**, clique em **Máquinas virtuais do Azure**.

    ![Bloco dos Trabalhos de Backup](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)

    A folha Trabalhos de Backup será aberta.

4. Na folha Trabalhos de Backup, você pode ver o status de todos os trabalhos.

    ![Bloco dos Trabalhos de Backup](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)

    >[AZURE.NOTE] Como parte da operação de backup, o serviço de Backup do Azure emite um comando para a extensão de backup em cada máquina virtual para limpar todas as gravações e capturar um instantâneo consistente.

    Quando o trabalho de backup for concluído, o status será *Concluído*.

## Definindo uma política de backup

Uma política de backup define uma matriz de quando os instantâneos de dados são obtidos e por quanto tempo esses instantâneos são mantidos. Ao definir uma política para fazer backup de uma VM, você pode disparar um trabalho de backup *uma vez por dia*. Quando você cria uma nova política, ela é aplicada ao cofre. A interface da política de backup fica assim:

![Política do backup](./media/backup-azure-vms-first-look-arm/backup-policy-daily-raw.png)

Para criar uma política:

1. Para o **Nome da Política**, forneça um nome.

2. Podem ser feitos instantâneos dos dados em intervalos Diários ou Semanais. Use o menu suspenso **Frequência do Backup** para escolher se os instantâneos de dados são feitos Diariamente ou Semanalmente.

    - Se você escolher um intervalo Diário, use o controle destacado para selecionar a hora do dia para o instantâneo. Para alterar a hora, cancele a seleção da hora e selecione a nova hora.

    ![Política de backup diário](./media/backup-azure-vms-first-look-arm/backup-policy-daily.png) <br/>

    - Se você escolher um intervalo Semanal, use os controles destacados para selecionar o(s) dia(s) da semana e a hora do dia para fazer o instantâneo. No menu do dia, selecione um ou vários dias. No menu de hora, selecione uma hora. Para alterar a hora, cancele a seleção da hora escolhida e selecione a nova hora.

    ![Política de backup semanal](./media/backup-azure-vms-first-look-arm/backup-policy-weekly.png)

3. Por padrão, todas as opções de **Intervalo de Retenção** são selecionadas. Desmarque qualquer limite do intervalo de retenção que você não deseja usar.

    >[AZURE.NOTE] Ao proteger uma VM, um trabalho de backup é executado uma vez por dia. O tempo durante o qual o backup é executado é o mesmo para cada intervalo de retenção.

    Nos controles correspondentes, especifique o(s) intervalo(s) a usar. Os intervalos de retenção Mensal e Anual permitem que você especifique os instantâneos com base em um incremento diário ou semanal.

4. Depois de definir todas as opções para a política, na parte inferior da folha, clique em **OK**.

    A nova política é definida para ser aplicada no cofre assim que as configurações do cofre de Serviços de Recuperação são concluídas. Retorne à etapa 6 da seção e escolha [Selecionar política de configuração do cenário e definir os itens a proteger](backup-azure-vms-first-look-arm.md#step-2---select-scenario-set-policy-and-define-items-to-protect)

## Instalar o Agente de VM na máquina virtual

Essas informações são fornecidas no caso de ser necessário. O Agente de VM do Azure deve ser instalado na máquina virtual do Azure para a extensão de Backup funcionar. Contudo, se sua VM foi criada a partir da galeria do Azure, o Agente de VM já estará presente na máquina virtual. As VMs migradas dos datacenters locais não teriam o Agente de VM instalado. Nesse caso, o Agente de VM precisa ser instalado. Se você tiver problemas para fazer backup da VM do Azure, verifique se o Agente de VM do Azure está instalado corretamente na máquina virtual (veja a tabela abaixo). Se você estiver criando uma VM personalizada, [verifique se a caixa de seleção **Instalar o Agente de VM** está marcada](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md) antes que a máquina virtual seja provisionada.

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

## Informações sobre solução de problemas
Se você tiver problemas para realizar algumas das tarefas neste artigo, consulte as [Diretrizes da solução de problemas](backup-azure-vms-troubleshoot.md).


## Perguntas?
Se você tiver dúvidas ou gostaria de ver algum recurso incluído, [envie-nos seus comentários](http://aka.ms/azurebackup_feedback).

<!---HONumber=AcomDC_0420_2016-->