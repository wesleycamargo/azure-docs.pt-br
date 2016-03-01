<properties
   pageTitle="Primeira impressão: como fazer backup de um Windows Server ou de um cliente Windows no Azure | Microsoft Azure"
   description="Saiba como fazer backup de um Windows Server usando o Backup do Azure com estas etapas simples"
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
	 ms.date="02/21/2016"
	 ms.author="jimpark;"/>

# Fazer backup de um Windows Server ou de um cliente no Azure

Em apenas algumas etapas, você pode fazer backup do seu computador com o Windows (cliente Windows ou Windows Server) no Azure. Quando tiver concluído as quatro etapas abaixo, você terá:

- Configurado uma assinatura do Azure (se necessário).
- Criado um cofre de backup e baixado os componentes necessários.
- Preparado seu Windows Server ou cliente Windows ao instalar e registrar esses componentes.
- Feito backup dos dados.

## Etapa 1: Obtenha uma assinatura do Azure

Se você não tiver uma assinatura do Azure, poderá criar uma [conta gratuita](https://azure.microsoft.com/free/), que permitirá o acesso a qualquer serviço do Azure.

>[AZURE.NOTE] Se você já tiver uma assinatura do Azure, ignore esta etapa.

## Etapa 2: criar um cofre de backup

Para fazer o backup de arquivos e de dados do seu computador com o Windows no Azure, você deverá criar um cofre de backup na região geográfica em que deseja armazenar os dados.

1. Se ainda não tiver feito isso, entre no [Portal do Azure](https://portal.azure.com/) usando sua assinatura do Azure.

2. Clique em **Novo > Integração Híbrida > Backup**.

    ![Criar cofre](./media/backup-try-azure-backup-in-10-mins/second-blade-backup.png)

    a. Na tela resultante, em **Nome**, insira um nome amigável para identificar o cofre de backup. Digite um nome que contenha de 2 a 50 caracteres. Ele deve começar com uma letra e pode conter apenas letras, números e hifens.

    b. Para **Região**, selecione a região geográfica para o cofre de backup. Ao escolher uma região geográfica próxima à sua localidade, você poderá reduzir a latência de rede ao fazer backup no Azure.

    c. Clique em **Criar cofre**.

    ![Criar cofre](./media/backup-try-azure-backup-in-10-mins/demo-vault-name.png)

    Para o status, você pode monitorar as notificações na parte inferior do portal.

    ![Criando cofre](./media/backup-try-azure-backup-in-10-mins/creatingvault1.png)

    Depois que o cofre de backup for criado, você o verá na lista de recursos dos Serviços de Recuperação como **Ativo**.

    ![Status de criação de cofre](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

3. Selecione as opções de **redundância de armazenamento**.

    O melhor momento para selecionar a opção de redundância de armazenamento é logo após a criação de um cofre e antes de qualquer computador ser registrado nele. Depois que um item tiver sido registrado no cofre, a opção de redundância de armazenamento será bloqueada e não poderá ser modificada.

    Se você estiver usando o Azure como ponto de extremidade de armazenamento de backup principal (por exemplo, estiver fazendo backup de um Windows Server no Azure), deverá considerar a possibilidade de escolher a opção de armazenamento com redundância geográfica (padrão).

    Se você estiver usando o Azure como um ponto de extremidade de armazenamento de backup terciário (por exemplo, estiver usando SCDPM para ter uma cópia de backup local e usando o Azure para suas necessidades de retenção de longo prazo), deverá considerar a possibilidade de escolher Armazenamento com Redundância Local. Isso reduz o custo de armazenamento de dados no Azure, além de oferecer um nível mais baixo de durabilidade para seus dados que pode ser aceitável para cópias terciárias.

    a. Clique no cofre que você acabou de criar.

    b. Na página Início Rápido, selecione **Configurar**.

    ![Configurar o status do cofre](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

    c. Escolha a opção de redundância de armazenamento apropriada.

    Você precisará clicar em **Salvar** se tiver selecionado **Localmente Redundante**, pois **Geograficamente Redundante** é a opção padrão.

    ![GRS](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

    >[AZURE.NOTE] Saiba mais sobre como escolher opções de redundância de armazenamento neste artigo de [visão geral](../storage/storage-redundancy.md).

    d. Clique em **Serviços de Recuperação** no painel de navegação à esquerda para retornar à lista de recursos para **Serviços de Recuperação**.

    ![Selecionar cofre de backup](./media/backup-try-azure-backup-in-10-mins/rs-left-nav.png)

    Seu computador com o Windows agora precisa ser autenticado no cofre de backup que você acabou de criar. A autenticação é feita com as credenciais do cofre.

4.  Clique no cofre que você acabou de criar.

    ![Status de criação de cofre](./media/backup-try-azure-backup-in-10-mins/demo-vault-active-full-screen.png)

5. Na página **Início Rápido**, clique em **Baixar credenciais do cofre**.

    ![Baixar](./media/backup-try-azure-backup-in-10-mins/downloadvc.png)

    O portal gerará uma credencial de cofre usando uma combinação do nome do cofre e a data atual.

    >[AZURE.NOTE] O arquivo de credenciais do cofre é usado somente durante o fluxo de trabalho de registro e expira após 48 horas.

6. Clique em **Salvar** para baixar as credenciais do cofre para a pasta **Downloads** da conta local ou selecione **Salvar como** no menu **Salvar**, para especificar um local para as credenciais do cofre.

    ![Selecionar cofre de backup](./media/backup-try-azure-backup-in-10-mins/save.png)

    Salve as credenciais do cofre em um local que possa ser acessado em seu computador. Se o arquivo for armazenado em um compartilhamento de arquivos/SMB, verifique as permissões de acesso.

    >[AZURE.NOTE] Você não precisa abrir as credenciais do cofre no momento.

    Em seguida, você precisará baixar o agente de backup

7. Clique em **Serviços de Recuperação** no painel de navegação à esquerda e selecione o cofre de backup em que você deseja registrar um servidor.

    ![Selecionar cofre de backup](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

8. Na página Início Rápido, clique em **Agente para Windows Server ou System Center Data Protection Manager ou Windows Client > Salvar** ou selecione **Salvar como** no menu **Salvar** para especificar um local para o agente).

    ![Salvar agente](./media/backup-try-azure-backup-in-10-mins/agent.png)

Neste ponto, você terá terminado de criar um cofre de backup e de baixar os componentes necessários. Agora, você instalará o agente de backup.

## Etapa 3: instalar o agente de Backup do Azure no Windows Server ou no cliente Windows

1. Após a conclusão do download, clique duas vezes no arquivo **MARSagentinstaller.exe** no local de salvamento (ou você pode clicar em **Executar** em vez de salvar o arquivo na etapa anterior).

2. Escolha a **Pasta de instalação** e a **Pasta de cache ** necessárias para o agente.

    O local do cache especificado deve ter espaço livre igual a pelo menos 5% dos dados de backup.

    Clique em **Próximo**.

    ![Usar o cache](./media/backup-try-azure-backup-in-10-mins/recovery-services-agent-setup-wizard-1.png)

3. Você pode continuar para se conectar à Internet por meio das configurações de proxy padrão ou, se usar um servidor proxy para se conectar à Internet, na tela **Configuração de proxy**, marque a caixa **Usar configurações personalizadas de proxy** e insira os detalhes do servidor proxy.

    Se você usar um proxy autenticado, insira os detalhes de nome de usuário e de senha.

    Clique em **Próximo**.

    ![Configuração de proxy](./media/backup-try-azure-backup-in-10-mins/proxy-configuration.png)

4. Clique em **Instalar**.

    ![Configuração de proxy](./media/backup-try-azure-backup-in-10-mins/agent-installation.png)

    O agente do Backup do Azure instala o .NET Framework 4.5 e o Windows PowerShell (se eles ainda não estiverem instalados) para concluir a instalação.

5. Após a instalação do agente, clique em **Prosseguir com o registro** para continuar com o fluxo de trabalho.

    ![Registrar](./media/backup-try-azure-backup-in-10-mins/register.png)

6. Na tela **Identificação do cofre**, navegue até o **arquivo de credenciais do cofre** que você baixou anteriormente e selecione-o. Verifique se o arquivo de credenciais do cofre está disponível em um local que pode ser acessado pelo aplicativo de instalação.

    Clique em **Próximo**.

    ![Credenciais do cofre](./media/backup-try-azure-backup-in-10-mins/vault-identification-with-creds.png)

7. Na tela **Configuração de criptografia**, você pode gerar uma senha ou fornecer uma (mínio de 16 caracteres). Lembre-se de salvar a senha em um local seguro.

    > [AZURE.WARNING] Se a senha for perdida ou esquecida, a Microsoft não poderá ajudar na recuperação dos dados de backup. O usuário final é proprietário da senha de criptografia, isto é, a Microsoft não tem visibilidade da senha que é usada pelo usuário final. Salve o arquivo em um local seguro, pois ela é obrigatória durante uma operação de recuperação.

    Clique em **Concluir**.

    ![Criptografia](./media/backup-try-azure-backup-in-10-mins/encryption.png)

    O **Assistente para Registrar Servidor** registrará o servidor no Backup do Microsoft Azure.

    ![Criptografia](./media/backup-try-azure-backup-in-10-mins/registering-server.png)

8. Assim que a **chave de criptografia** for definida, deixe a caixa de seleção **Iniciar o Agente de Serviços de Recuperação do Microsoft Azure** marcada e clique em **Fechar**.

    ![Criptografia](./media/backup-try-azure-backup-in-10-mins/close-server-registration.png)

    O computador agora foi registrado com êxito no cofre e você está pronto para configurar e agendar suas opções de backup.

## Etapa 4: fazer backup de arquivos e pastas

1. No snap-in do MMC (que foi aberto automaticamente caso você tenha deixado a caixa de seleção **Iniciar o Agente de Serviços de Recuperação do Microsoft Azure ** marcada), clique em **Agendar Backup**.

    ![Agendar um Backup do Windows Server](./media/backup-try-azure-backup-in-10-mins/snap-in-schedule-backup-closeup.png)

2. Na tela **Introdução**, clique em **Avançar**.

    ![Agendar um Backup do Windows Server](./media/backup-try-azure-backup-in-10-mins/getting-started.png)

3. Na tela **Selecionar itens para Backup**, clique em **Adicionar Itens**.

    ![Agendar um Backup do Windows Server](./media/backup-try-azure-backup-in-10-mins/add-items.png)

    Selecione os itens dos quais você deseja fazer backup e clique em **OK**. O Backup do Azure em um cliente Windows ou em um Windows Server permite que você proteja arquivos e pastas.

    ![Itens para o backup do Windows Server](./media/backup-try-azure-backup-in-10-mins/added-items.png)

    Clique em **Próximo**.

    ![Itens para o backup do Windows Server](./media/backup-try-azure-backup-in-10-mins/selected-items.png)

4. Especifique o **agendamento de backup** e clique em **Avançar**.

    Você pode agendar backups diários (no máximo três vezes por dia) ou backups semanais.

    ![Itens para o backup do Windows Server](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule.png)

5. Selecione a **Política de Retenção** para a cópia de backup. Você pode modificar a política de retenção diária, semanal, mensal e anual para atender às suas necessidades.

    >[AZURE.NOTE] O agendamento de backup é explicado em detalhes neste [artigo](backup-azure-backup-cloud-as-tape.md).

     Clique em **Próximo**.

    ![Itens para o backup do Windows Server](./media/backup-try-azure-backup-in-10-mins/select-retention-policy.png)

6. Escolha o tipo de backup inicial.

    Você pode fazer backup automaticamente pela rede ou pode fazer backup offline. O restante deste artigo acompanha o processo de backup automático. Se preferir fazer um backup offline, examine este artigo para obter informações adicionais sobre o [fluxo de trabalho do backup offline no Backup do Azure](backup-azure-backup-import-export.md).

    Clique em **Próximo**.

    ![Backup inicial do Windows Server](./media/backup-try-azure-backup-in-10-mins/choose-initial-backup-type.png)

7. Na tela **Confirmação**, examine as informações e clique em **Concluir**.

    ![Backup inicial do Windows Server](./media/backup-try-azure-backup-in-10-mins/confirmation.png)

8. Depois que o assistente terminar de criar o **agendamento de backup**, clique em **Fechar**.

    ![Backup inicial do Windows Server](./media/backup-try-azure-backup-in-10-mins/backup-schedule-created.png)

9. No snap-in do MMC, clique em **Fazer backup agora** para concluir a propagação inicial pela rede.

    ![Fazer backup do Windows Server agora](./media/backup-try-azure-backup-in-10-mins/snap-in-backup-now.png)

10. Na tela **Confirmação**, examine as configurações que o assistente usará para fazer backup do computador e clique em**Fazer backup**.

    ![Fazer backup do Windows Server agora](./media/backup-try-azure-backup-in-10-mins/backup-now-confirmation.png)

11. Clique em **Fechar** para fechar o assistente. Você pode fazer isso antes da conclusão do **processo de backup** e ele continuará a ser executado em segundo plano.

    ![Fazer backup do Windows Server agora](./media/backup-try-azure-backup-in-10-mins/backup-progress.png)

12. Depois que o backup inicial for concluído, a exibição **Trabalhos** no console do Backup do Azure indicará o status “Trabalho concluído”.

    ![IR completo](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

Parabéns, você concluiu com êxito o backup de seus arquivos e de suas pastas no Backup do Azure.

## Próximas etapas
- Para saber mais sobre o Backup do Azure, confira [Visão geral do backup do Azure](backup-introduction-to-azure-backup.md)
- Obtenha mais detalhes sobre a [preparação do seu ambiente para fazer backup de computadores com o Windows](backup-configure-vault.md)
- Obtenha mais detalhes sobre o [backup de um Windows Server](backup-azure-backup-windows-server.md)
- Visite o [Fórum de backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0224_2016-->