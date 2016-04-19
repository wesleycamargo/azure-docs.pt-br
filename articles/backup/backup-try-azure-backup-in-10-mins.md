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
   ms.date="04/09/2016"
   ms.author="jimpark;"/>

# Introdução: fazer backup de arquivos e pastas do Windows Server ou do cliente para o Azure

Este artigo explica como fazer backup de arquivos e pastas do Windows Server (ou cliente Windows) para o Azure usando o Backup do Azure. É um tutorial que pretende explicar os conceitos básicos. Se deseja obter uma introdução ao uso do Backup do Azure, você está no lugar certo.

Se você quiser saber mais sobre o Backup do Azure, leia esta [visão geral](backup-introduction-to-azure-backup.md).

O backup de arquivos e de pastas no Azure exige estas atividades:

![Etapa 1](./media/backup-try-azure-backup-in-10-mins/step-1.png) Obter uma assinatura do Azure (caso você ainda não tenha uma).<br> ![Etapa 2](./media/backup-try-azure-backup-in-10-mins/step-2.png) Criar um cofre de backup e baixar os itens necessários.<br> ![Etapa 3](./media/backup-try-azure-backup-in-10-mins/step-3.png) Instalar e registrar o agente de backup.<br> ![Etapa 4](./media/backup-try-azure-backup-in-10-mins/step-4.png) Fazer backup de arquivos e pastas


![Como fazer backup de um computador Windows com o Backup do Azure](./media/backup-try-azure-backup-in-10-mins/windows-machine-backup-process.png)

## Etapa 1: Obtenha uma assinatura do Azure

Se não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/), que permitirá o acesso a qualquer serviço do Azure.

## Etapa 2: Criar um cofre de backup e baixar os itens necessários

Para fazer backup de seus arquivos e pastas, você precisa criar um cofre de backup na região onde deseja armazenar os dados. Você também determina como o armazenamento é replicado e baixa as credenciais e o agente de backup.

### Para criar um cofre de backup

1. Se ainda não tiver feito isso, entre no [Portal do Azure](https://portal.azure.com/) usando sua assinatura do Azure.

2. Clique em **Novo > Integração Híbrida > Backup**.

    ![Comece a se preparar para fazer backup de seus arquivos e pastas](./media/backup-try-azure-backup-in-10-mins/second-blade-backup.png)

3. Em **Nome**, insira um nome amigável para identificar o cofre de backup.

4. Para **Região**, selecione a região mais próxima ao local para obter transferências de arquivos mais rápidas.

5. Clique em **CRIAR COFRE**.

    ![Criar um cofre](./media/backup-try-azure-backup-in-10-mins/demo-vault-name.png)

    Quando o cofre de backup estiver pronto, ele será listado nos recursos dos Serviços de Recuperação como **Ativo**.

    ![O status do cofre é ativo](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

Depois de criar o cofre, selecione como o armazenamento será replicado.

>[AZURE.NOTE] Você deve escolher como o armazenamento será replicado logo após a criação de um cofre e antes que todas as máquinas sejam registradas nele. Depois que um item tiver sido registrado no cofre, a replicação de armazenamento será bloqueada e não poderá ser modificada.

### Para selecionar como o armazenamento é replicado

1. Clique no cofre que você criou.
2. Na página Início Rápido, selecione **Configurar**.

    ![Configure o cofre](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

3. Escolha a opção de armazenamento apropriada.

    Se você estiver usando o Azure como seu backup principal, escolha [armazenamento com redundância geográfica](../storage/storage-redundancy.md#geo-redundant-storage). Se você estiver usando o Azure como seu backup terciário, escolha [armazenamento com redundância local](../storage/storage-redundancy.md#locally-redundant-storage).

    ![Escolher a opção de replicação de armazenamento](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

4. Se você tiver selecionado **Localmente Redundante**, clique em **Salvar**, pois **Redundância Geográfica** é o padrão.

Use as credenciais do cofre para autenticar o computador no cofre de backup. Veja como baixar essas credenciais.

### Para baixar as credenciais do cofre
O arquivo de credenciais do cofre é usado somente durante o processo de registro e expira após 48 horas.

1. Para retornar para a página **Início Rápido** para seu cofre, clique em ![Selecione o novo cofre](./media/backup-try-azure-backup-in-10-mins/quick-start-icon.png)

2. Clique em **Baixar as credenciais do cofre > Salvar**.

Em seguida, baixe o agente de backup.

### Para baixar o agente de backup

Clique em **Agente para Windows Server ou System Center Data Protection Manager ou Windows Client > Salvar**.

![Salve o agente de backup](./media/backup-try-azure-backup-in-10-mins/agent.png)

Agora que o cofre foi criado e que você baixou tudo, instale e registre o agente de backup.

## Etapa 3: Instalar e registrar o agente de backup

1. Clique duas vezes em **MARSagentinstaller.exe** a partir do local salvo.
2. Conclua o Assistente de Instalação do Agente do Serviços de Recuperação do Microsoft Azure. Para concluir o assistente, você precisará:
    - Escolher um local para a instalação e a pasta de cache.
    - Fornecer as informações de seu servidor proxy se você usar um servidor proxy para conectar-se à Internet.
    - Forneça os detalhes do seu nome de usuário e de sua senha se usar um proxy autenticado.
    - Salve a senha de criptografia em um local seguro.

    >[AZURE.NOTE] Se você perder ou esquecer a senha, a Microsoft não poderá ajudar a recuperar os dados de backup. Salve o arquivo em um local seguro. Isso é necessário para restaurar um backup.

Agora, o agente está instalado e seu computador está registrado no cofre. Você está pronto para configurar e agendar o backup.

## Etapa 4: Fazer backup de arquivos e de pastas
Se o agente de backup ainda não estiver aberto, você poderá localizá-lo ao pesquisar o Backup do Microsoft Azure em seu computador.

1. No **agente de Backup**, clique em **Agendar Backup**.

    ![Agendar um Backup do Windows Server](./media/backup-try-azure-backup-in-10-mins/snap-in-schedule-backup-closeup.png)

2. Conclua o Assistente de Agendamento de Backup. Ao concluir o assistente, você irá:

    - Selecionará os arquivos e as pastas dos quais você deseja fazer backup.
    - Especificar o agendamento de backup (diário ou semanal).
    - Determinar sua política de retenção.
    - Escolher como você deseja concluir o backup inicial (pela rede ou offline).

    Saiba mais sobre a [conclusão do backup inicial offline](backup-azure-backup-import-export.md). <br><br>

3. Quando o assistente for concluído, volte para o **Agente de Backup** e clique em **Fazer Backup Agora** para concluir o backup inicial pela rede.

    ![Fazer backup do Windows Server agora](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

4. Na tela **Confirmação**, clique em **Fazer Backup**. Se você fechar o assistente antes da conclusão do processo de backup, ele continuará a ser executado em segundo plano.

    Quando o backup inicial for concluído, a exibição **Trabalhos** no console mostrará que o trabalho foi concluído.

    ![Backup inicial concluído](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

Parabéns, você concluiu com êxito o backup de seus arquivos e de suas pastas no Backup do Azure.

## Próximas etapas
- Obtenha mais detalhes sobre o [backup de computadores que usam o Windows](backup-configure-vault.md).
- Agora que você faz backup de seus arquivos e pastas, poderá [gerenciar seus servidores e cofres](backup-azure-manage-windows-server.md).
- Se você precisar restaurar um backup, use este artigo para [restaurar os arquivos para um computador que usa o Windows](backup-azure-restore-windows-server.md).

<!---HONumber=AcomDC_0413_2016-->