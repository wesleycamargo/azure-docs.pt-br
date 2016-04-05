<properties
   pageTitle="Saiba como fazer backup de arquivos e pastas do Windows para o Azure | Microsoft Azure"
   description="Saiba como fazer backup de dados do Windows Server criando um cofre, instalando o agente de backup e fazendo backup de seus arquivos e pastas no Azure."
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="como fazer backup; como fazer backup"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="hero-article"
	 ms.date="03/28/2016"
	 ms.author="jimpark;"/>

# Introdução: fazer backup de arquivos e pastas do Windows Server ou do cliente para o Azure

Este artigo explica como fazer backup de arquivos e pastas do Windows Server (ou cliente Windows) para o Azure usando o Backup do Azure. Se deseja ver como é simples usar o Backup do Azure, você está no lugar certo.

O backup de seus arquivos e pastas requer somente estas etapas:

![Etapa 1](./media/backup-try-azure-backup-in-10-mins/step-1.png) Obtenha uma assinatura do Azure (se necessário).<br> ![Etapa 2](./media/backup-try-azure-backup-in-10-mins/step-2.png) Crie um cofre de backup e baixe os componentes necessários.<br> ![Etapa 3](./media/backup-try-azure-backup-in-10-mins/step-3.png) Prepare seu Windows Server ou cliente instalando e registrando esses componentes.<br> ![Etapa 4](./media/backup-try-azure-backup-in-10-mins/step-4.png) Fazer backup dos dados.


![Como fazer backup de um computador Windows com o Backup do Azure](./media/backup-try-azure-backup-in-10-mins/windows-machine-backup-process.png)

## Etapa 1: Obtenha uma assinatura do Azure

Se não tiver uma assinatura do Azure, você poderá criar uma [conta gratuita](https://azure.microsoft.com/free/), que permitirá o acesso a qualquer serviço do Azure.

>[AZURE.NOTE] Se já tem uma assinatura do Azure, você pode ignorar essa etapa.

## Etapa 2: Crie um cofre de backup e baixe os componentes necessários.

Para fazer backup de seus arquivos e pastas, você precisa criar um cofre de backup na região geográfica onde deseja armazenar os dados.

1. Se ainda não tiver feito isso, entre no [Portal do Azure](https://portal.azure.com/) usando sua assinatura do Azure.

2. Clique em **Novo > Integração Híbrida > Backup**.

    ![Comece a se preparar para fazer backup de seus arquivos e pastas](./media/backup-try-azure-backup-in-10-mins/second-blade-backup.png)

    a. Na tela resultante, em **Nome**, insira um nome amigável para identificar o cofre de backup. Digite um nome que contenha de 2 a 50 caracteres. Ele deve começar com uma letra e pode conter apenas letras, números e hifens.

    b. Para **Região**, selecione a região geográfica do cofre de backup. Ao escolher uma região geográfica próxima à sua localidade, você poderá reduzir a latência de rede ao fazer backup no Azure.

    c. Clique em **Criar cofre**.

    ![Criar um cofre](./media/backup-try-azure-backup-in-10-mins/demo-vault-name.png)

    Para o status, você pode monitorar as notificações na parte inferior do portal.

    ![Processo de cofre](./media/backup-try-azure-backup-in-10-mins/creatingvault1.png)

    Depois que o cofre de backup for criado, ele será listado nos recursos dos Serviços de Recuperação como **Ativo**.

    ![O status do cofre é ativo](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

3. Selecione as opções de **redundância de armazenamento**.

    O melhor momento para selecionar a opção de redundância de armazenamento é logo após a criação de um cofre e antes de qualquer computador ser registrado nele. Depois que um item tiver sido registrado no cofre, a opção de redundância de armazenamento será bloqueada e não poderá ser modificada.

    Se você estiver usando o Azure como um ponto de extremidade de armazenamento de backup principal, considere a opção (padrão) de [Armazenamento com redundância geográfica](../storage/storage-redundancy.md#geo-redundant-storage).

    Se você estiver usando o Azure como um ponto de extremidade de armazenamento de backup terciário, considere a escolha do [Armazenamento com redundância local](../storage/storage-redundancy.md#locally-redundant-storage). Essa opção reduz o custo de armazenamento de dados no Azure, além de oferecer um nível mais baixo de durabilidade que pode ser aceitável para cópias terciárias.

    a. Clique no cofre que você acabou de criar.

    b. Na página Início Rápido, selecione **Configurar**.

    ![Configure o cofre](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

    c. Escolha a opção de redundância de armazenamento apropriada.

    ![Escolha a opção de redundância de armazenamento](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

    Se você selecionou **Localmente Redundante**, clique em **Salvar**, pois **Redundância Geográfica** é a opção padrão.

    d. Para retornar à lista de recursos para **Serviços de Recuperação**, clique em **Serviços de Recuperação** no painel de navegação à esquerda.

    ![Serviços de recuperação](./media/backup-try-azure-backup-in-10-mins/rs-left-nav.png)

    Agora você precisa autenticar o computador Windows com o cofre de backup que acabou de criar. A autenticação é feita com as credenciais do cofre.

4.  Clique no cofre que você acabou de criar.

    ![Selecione o novo cofre](./media/backup-try-azure-backup-in-10-mins/demo-vault-active-full-screen.png)

5. Na página **Início Rápido**, clique em **Baixar credenciais do cofre**.

    ![Baixar as credenciais do cofre](./media/backup-try-azure-backup-in-10-mins/downloadvc.png)

    O portal gera uma credencial de cofre usando uma combinação do nome do cofre e da data atual.

    >[AZURE.NOTE] O arquivo de credenciais do cofre é usado somente durante o fluxo de trabalho de registro e expira após 48 horas.

6. Clique em **Salvar** para baixar as credenciais do cofre para a pasta local **Downloads**. Você também pode selecionar **Salvar como** no menu **Salvar** para especificar um local para as credenciais do cofre.

    Salve as credenciais do cofre em um local que possa ser acessado em seu computador. Se o arquivo for armazenado em um compartilhamento de arquivos/SMB, verifique as permissões de acesso.

    >[AZURE.NOTE] Você não precisa abrir as credenciais do cofre neste momento no procedimento.

    Em seguida, você precisará baixar o agente de backup

7. Clique em **Serviços de Recuperação** no painel de navegação à esquerda e clique no cofre de backup que você deseja registrar em um servidor.

    ![Selecione o cofre de backup](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

8. Na página Início Rápido, clique em **Agente para Windows Server ou System Center Data Protection Manager ou Windows Client > Salvar**.

    ![Salve o agente de backup](./media/backup-try-azure-backup-in-10-mins/agent.png)

Neste ponto, você terá terminado de criar um cofre de backup e de baixar os componentes necessários. Agora, você instalará o agente de backup.

## Etapa 3: prepare seu Windows Server ou cliente instalando e registrando os componentes de backup.

1. Após a conclusão do download, clique duas vezes no arquivo **MARSagentinstaller.exe** no local de salvamento (ou você pode clicar em **Executar** em vez de salvar o arquivo na etapa anterior).

2. Escolha a **Pasta de instalação** e a **Pasta de cache ** necessárias para o agente.

    O local do cache especificado deve ter espaço livre igual a pelo menos 5% dos dados de backup.

    Clique em **Próximo**.

    ![Pasta de instalação e cache](./media/backup-try-azure-backup-in-10-mins/recovery-services-agent-setup-wizard-1.png)

3. (Opcional) Se você usar um servidor proxy para se conectar à Internet, na tela **Configuração de proxy**, marque a caixa **Usar configurações de proxy personalizadas** e insira os detalhes do servidor proxy.

    Se você usar um proxy autenticado, insira os detalhes de nome de usuário e de senha.

    Clique em **Próximo**.

    ![Configuração de proxy](./media/backup-try-azure-backup-in-10-mins/proxy-configuration.png)

4. Clique em **Instalar**.

    O agente do Backup do Azure instala o .NET Framework 4.5 e o Windows PowerShell (se eles ainda não estiverem instalados) para concluir a instalação.

5. Após a instalação do agente, clique em **Prosseguir com o Registro** para continuar com o fluxo de trabalho.

    ![Registrar](./media/backup-try-azure-backup-in-10-mins/register.png)

6. Na tela **Identificação do Cofre**, navegue até o **arquivo de credenciais do cofre** que você baixou anteriormente e selecione-o. Verifique se o arquivo de credenciais do cofre está disponível em um local que pode ser acessado pelo aplicativo de instalação.

    Clique em **Próximo**.

    ![Identifique o cofre](./media/backup-try-azure-backup-in-10-mins/vault-identification-with-creds.png)

7. Na tela **Configuração de criptografia**, você pode gerar uma senha ou fornecer uma (mínio de 16 caracteres). Lembre-se de salvar a senha em um local seguro.

    > [AZURE.WARNING] Se você perder ou esquecer a senha, a Microsoft não poderá ajudar na recuperação dos dados de backup. Você é proprietário da senha de criptografia, e a Microsoft não tem visibilidade da senha que é usada pelo usuário final. Salve o arquivo em um local seguro, pois ela é obrigatória durante uma operação de recuperação.

    Clique em **Concluir**.

    ![Configuração de criptografia](./media/backup-try-azure-backup-in-10-mins/encryption.png)

    O **Assistente para Registrar Servidor** registrará o servidor no Backup do Microsoft Azure.

8. Quando a **chave de criptografia** for definida, deixe a caixa de seleção **Iniciar o Agente de Serviços de Recuperação do Microsoft Azure** marcada e clique em **Fechar**.

    O computador agora foi registrado com êxito no cofre e você está pronto para configurar e agendar suas opções de backup.

## Etapa 4: fazer backup de dados

1. No **Agente de backup** (que foi aberto automaticamente se você deixou a caixa de seleção **Iniciar o Agente de Serviços de Recuperação do Microsoft Azure** marcada), clique em **Agendar Backup**.

    ![Agendar um Backup do Windows Server](./media/backup-try-azure-backup-in-10-mins/snap-in-schedule-backup-closeup.png)

2. Na tela **Introdução**, clique em **Avançar**.

3. Na tela **Selecionar Itens para Backup**, clique em **Adicionar Itens**. O Backup do Azure em um cliente Windows ou em um Windows Server permite que você proteja arquivos e pastas.

    ![Agendar um Backup do Windows Server](./media/backup-try-azure-backup-in-10-mins/add-items.png)

    Selecione os itens dos quais você deseja fazer backup e clique em **OK**.

    Clique em **Próximo**.

4. Especifique o **agendamento de backup** e clique em **Avançar**.

    Você pode agendar backups diários (no máximo três vezes por dia) ou backups semanais.

    ![Especifique o agendamento de backup](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule.png)

5. Selecione a **Política de Retenção** para a cópia de backup. Você pode modificar a política de retenção diária, semanal, mensal e anual para atender às suas necessidades.

    >[AZURE.NOTE] O agendamento de backup é explicado em detalhes neste [artigo](backup-azure-backup-cloud-as-tape.md).

     Clique em **Próximo**.

    ![Selecione a política de retenção](./media/backup-try-azure-backup-in-10-mins/select-retention-policy.png)

6. Escolha o tipo de backup inicial.

    Você pode fazer backup automaticamente pela rede ou pode fazer backup offline. O restante deste artigo acompanha o processo de backup automático. Se preferir fazer um backup offline, examine este artigo para obter informações adicionais sobre o [fluxo de trabalho do backup offline no Backup do Azure](backup-azure-backup-import-export.md).

    Clique em **Próximo**.

    ![Tipo de backup inicial](./media/backup-try-azure-backup-in-10-mins/choose-initial-backup-type.png)

7. Na tela **Confirmação**, examine as informações e clique em **Concluir**.

8. Depois que o assistente terminar de criar o **agendamento de backup**, clique em **Fechar**.

9. No **Agente de backup** clique em **Fazer Backup Agora** para concluir a propagação inicial pela rede.

    ![Fazer backup do Windows Server agora](./media/backup-try-azure-backup-in-10-mins/snap-in-backup-now.png)

10. Na tela **Confirmação**, examine as configurações que o assistente usa para fazer backup do computador e clique em**Fazer Backup**.

11. Clique em **Fechar** para fechar o assistente. Você pode fechar o assistente antes da conclusão do processo de backup, e ele continua a ser executado em segundo plano.

12. Depois que o backup inicial for concluído, a exibição **Trabalhos** no console do Backup do Azure indicará o status "Trabalho concluído".

    ![Backup inicial concluído](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

Parabéns, você concluiu com êxito o backup de seus arquivos e de suas pastas no Backup do Azure.

## Próximas etapas
- Para saber mais sobre o Backup do Azure, consulte [Visão geral do backup do Azure](backup-introduction-to-azure-backup.md).
- Obtenha mais detalhes sobre a [preparação de seu ambiente e o backup de computadores com o Windows](backup-configure-vault.md)
- Saiba como fazer backup de máquinas virtuais do Azure com este [Artigo de introdução](backup-azure-vms-first-look.md).
- Leia as últimas discussões sobre o Backup do Azure no [Fórum do Backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0330_2016-->