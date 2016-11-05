---
title: Aprenda a fazer backup de arquivos e pastas do cliente ou servidor do Azure com o Backup do Azure usando o modelo de implantação do Gerenciador de Recursos | Microsoft Docs
description: Saiba como fazer backup de dados do Windows Server criando um cofre, instalando o agente dos Serviços de Recuperação e fazendo backup de seus arquivos e pastas no Azure.
services: backup
documentationcenter: ''
author: markgalioto
manager: cfreeman
editor: ''
keywords: como fazer backup; como fazer backup

ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/27/2016
ms.author: markgal;

---
# Introdução: fazer backup de arquivos e pastas com o Backup do Azure usando o modelo de implantação do Gerenciador de Recursos
Este artigo explica como fazer backup de arquivos e pastas do Windows Server (ou cliente Windows) para o Azure com o Backup do Azure usando o Gerenciador de Recursos. É um tutorial que pretende explicar os conceitos básicos. Se deseja obter uma introdução ao uso do Backup do Azure, você está no lugar certo.

Se você quiser saber mais sobre o Backup do Azure, leia esta [visão geral](backup-introduction-to-azure-backup.md).

O backup de arquivos e de pastas no Azure exige estas atividades:

![Etapa 1](./media/backup-try-azure-backup-in-10-mins/step-1.png) Obter uma assinatura do Azure (caso você ainda não tenha uma).<br> ![Etapa 2](./media/backup-try-azure-backup-in-10-mins/step-2.png)Criar um cofre dos Serviços de Recuperação.<br> ![Etapa 3](./media/backup-try-azure-backup-in-10-mins/step-3.png) Baixar os arquivos necessários.<br> ![Etapa 4](./media/backup-try-azure-backup-in-10-mins/step-4.png) Instalar e registrar o agente dos Serviços de Recuperação.<br> ![Etapa 5](./media/backup-try-azure-backup-in-10-mins/step-5.png) Fazer o backup de arquivos e pastas.

![Como fazer backup de um computador Windows com o Backup do Azure](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

## Etapa 1: Obtenha uma assinatura do Azure
Se não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/), que permitirá o acesso a qualquer serviço do Azure.

## Etapa 2: Criar um cofre dos Serviços de Recuperação
Para fazer backup de seus arquivos e pastas, você precisa criar um cofre de Serviços de Recuperação na região onde deseja armazenar os dados. Você também precisa determinar como deseja que o armazenamento seja replicado.

### Para criar um cofre de Serviços de Recuperação
1. Se ainda não tiver feito isso, entre no [Portal do Azure](https://portal.azure.com/) usando a sua assinatura do Azure.
2. No menu Hub, clique em **Procurar** e, na lista de recursos, digite **Serviços de Recuperação** e clique em **cofres dos Serviços de Recuperação**.
   
    ![Criar Cofre de Serviços de Recuperação - etapa 1](./media/backup-try-azure-backup-in-10-mins/browse-to-rs-vaults.png) <br/>
3. No menu **Cofres de Serviços de Recuperação**, clique em **Adicionar**.
   
    ![Criar Cofre de Serviços de Recuperação - etapa 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)
   
    A folha do cofre dos Serviços de Recuperação será aberta, solicitando que você forneça o **Nome**, a **Assinatura**, o **Grupo de recursos** e o **Local**.
   
    ![Criar Cofre de Serviços de Recuperação - etapa 5](./media/backup-try-azure-backup-in-10-mins/rs-vault-attributes.png)
4. Em **Nome**, insira um nome amigável para identificar o cofre.
5. Clique em **Assinatura** para ver a lista de assinaturas disponíveis.
6. Clique em **Grupo de recursos** para ver a lista dos Grupos de recursos disponíveis ou clique em **Novo** para criar um novo Grupo de recursos.
7. Clique em **Local** para selecionar a região geográfica do cofre. Essa escolha determina a região geográfica para a qual os dados de backup são enviados.
8. Clique em **Criar**.
   
    Se você não vir seu cofre listado depois de ter sido concluído, clique em **Atualizar**. Quando a lista for atualizada, clique no nome do cofre.

### Para determinar a redundância de armazenamento
Quando você cria um cofre dos Serviços de Recuperação, determina como o armazenamento é replicado.

1. Clique no novo cofre para abrir o painel.
2. Na folha **Configurações**, que abre automaticamente com o painel do cofre, clique em **Infraestrutura de Backup**.
3. Na folha Infraestrutura de Backup, clique em **Configuração de Backup** para exibir o **Tipo de replicação de armazenamento**.
   
    ![Criar Cofre de Serviços de Recuperação - etapa 5](./media/backup-try-azure-backup-in-10-mins/backup-infrastructure.png)
4. Escolha a opção de replicação de armazenamento adequada para o cofre.
   
    ![Listar cofres de Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)
   
    Por padrão, seu cofre tem armazenamento com redundância geográfica. Se você estiver usando o Azure como um ponto de extremidade de armazenamento de backup principal, continue usando o armazenamento com redundância geográfica. Se você estiver usando o Azure como um ponto de extremidade de armazenamento de backup não principal, escolha o armazenamento com redundância local, o que reduzirá o custo de armazenar dados no Azure. Leia mais sobre as opções de armazenamento [com redundância geográfica](../storage/storage-redundancy.md#geo-redundant-storage) e [com redundância local](../storage/storage-redundancy.md#locally-redundant-storage) nesta [visão geral](../storage/storage-redundancy.md).

Agora que você criou um cofre, prepare sua infraestrutura para fazer backup de arquivos e pastas baixando as credenciais do agente dos Serviços de Recuperação do Microsoft Azure e do cofre.

## Etapa 3: baixar arquivos
1. Clique em **Configurações** no painel do cofre dos Serviços de Recuperação.
   
    ![Abrir folha de meta backup](./media/backup-try-azure-backup-in-10-mins/settings-button.png)
2. Clique em **Introdução > Backup** na folha Configurações.
   
    ![Abrir folha de meta backup](./media/backup-try-azure-backup-in-10-mins/getting-started-backup.png)
3. Clique em **Meta de Backup** na folha Backup.
   
    ![Abrir folha de meta backup](./media/backup-try-azure-backup-in-10-mins/backup-goal.png)
4. Selecione **Local** no menu Onde a sua carga de trabalho está sendo executada?
5. Selecione **Arquivos e pastas** no menu Do que você deseja fazer backup? e clique em **OK**.

### Baixar o agente dos Serviços de Recuperação
1. Clique em **Baixar agente do Windows Server ou Windows Client** na folha **Preparar infraestrutura**.
   
    ![preparar infraestrutura](./media/backup-try-azure-backup-in-10-mins/prepare-infrastructure-short.png)
2. Clique em **Salvar** no pop-up de download. Por padrão, o arquivo **MARSagentinstaller.exe** será salvo em sua pasta Downloads.

### Baixar as credenciais do cofre
1. Clique em **Baixar > Salvar** na folha Preparar infraestrutura.
   
    ![preparar infraestrutura](./media/backup-try-azure-backup-in-10-mins/prepare-infrastructure-download.png)

## Etapa 4: instalar e registrar o agente
> [!NOTE]
> A habilitação do backup pelo portal do Azure estará disponível em breve. Neste momento, você pode usar o agente dos Serviços de Recuperação do Microsoft Azure no local para fazer backup de seus arquivos e pastas.
> 
> 

1. Localize e clique duas vezes no **MARSagentinstaller.exe** da pasta Downloads (ou outro local salvo).
2. Conclua o Assistente de Instalação do Agente do Serviços de Recuperação do Microsoft Azure. Para concluir o assistente, você precisa fazer o seguinte:
   
   * Escolher um local para a instalação e a pasta de cache.
   * Fornecer as informações de seu servidor proxy se você usar um servidor proxy para conectar-se à Internet.
   * Forneça os detalhes do seu nome de usuário e de sua senha se usar um proxy autenticado.
   * Forneça as credenciais do cofre baixado
   * Salve a senha de criptografia em um local seguro.
     
     > [!NOTE]
     > Se você perder ou esquecer a senha, a Microsoft não poderá ajudar a recuperar os dados de backup. Salve o arquivo em um local seguro. Isso é necessário para restaurar um backup.
     > 
     > 

Agora, o agente está instalado e seu computador está registrado no cofre. Você está pronto para configurar e agendar o backup.

## Etapa 5: fazer backup de arquivos e de pastas
O backup inicial inclui duas tarefas principais:

* Agendar o backup
* Fazer backup de arquivos e pastas pela primeira vez

Para concluir o backup inicial, você deverá usar o agente dos Serviços de Recuperação do Microsoft Azure.

### Para agendar o backup
1. Abra o agente dos Serviços de Recuperação do Microsoft Azure Você pode localizá-la pesquisando seu computador por **Backup do Microsoft Azure**.
   
    ![Inicialize o agente dos Serviços de Recuperação do Azure](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)
2. O agente dos Serviços de Recuperação, clique em **Agendar Backup**.
   
    ![Agendar um backup do Windows Server](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)
3. Na página de Introdução do Assistente de Agendamento de Backup, clique em **Avançar**.
4. Na tela Selecionar Itens para Backup, clique em **Adicionar Itens**.
5. Selecione os arquivos e pastas dos quais você deseja fazer backup e clique em **Ok**.
6. Clique em **Próximo**.
7. Na tela **Especificar Agendamento de Backup**, especifique o **agendamento de backup** e clique em **Avançar**.
   
    Você pode agendar backups diários (com uma taxa máxima de três vezes por dia) ou backups semanais.
   
    ![Itens para o backup do Windows Server](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule-close.png)
   
   > [!NOTE]
   > Para saber mais sobre como especificar o agendamento de backup, confira o artigo [Usar o Backup do Azure para substituir a sua infraestrutura de fita](backup-azure-backup-cloud-as-tape.md).
   > 
   > 
8. Na página **Selecionar Política de Retenção**, escolha a **Política de Retenção** para a cópia de backup.
   
    A política de retenção especifica a duração de armazenamento do backup. Em vez de especificar apenas uma “política simples” para todos os pontos de backup, você pode especificar políticas de retenção diferentes com base em quando o backup ocorre. Você pode modificar as políticas de retenção diária, semanal, mensal e anual para atender às suas necessidades.
9. Na tela Escolher Tipo de Backup Inicial, escolha o tipo de backup inicial. Deixe a opção **Automaticamente pela rede** selecionada e clique em **Avançar**.
   
    Você pode fazer backup automaticamente pela rede ou pode fazer backup offline. O restante deste artigo descreve o processo para realização de backup automático. Se preferir fazer um backup offline, examine o artigo [Fluxo de trabalho de backup offline no Backup do Azure](backup-azure-backup-import-export.md) para obter informações adicionais.
10. Na página Confirmação, examine as informações e clique em **Concluir**.
11. Depois que o assistente terminar de criar o agendamento de backup, clique em **Fechar**.

### Para fazer backup de arquivos e pastas pela primeira vez
1. No Agente dos Serviços de Recuperação, clique em **Fazer Backup Agora** para concluir a propagação inicial pela rede.
   
    ![Fazer backup do Windows Server agora](./media/backup-try-azure-backup-in-10-mins/backup-now.png)
2. Na página Confirmação, examine as configurações que o Assistente Fazer Backup Agora usará para fazer backup do computador. Em seguida, clique em **Fazer Backup**.
3. Clique em **Fechar** para fechar o assistente. Se você fizer isso antes da conclusão do processo de backup, o assistente continuará a ser executado em segundo plano.

Depois que o backup inicial for concluído, o status **Trabalho concluído** aparecerá no Console de backup.

![IR completo](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## Perguntas?
Se você tiver dúvidas ou gostaria de ver algum recurso incluído, [envie-nos seus comentários](http://aka.ms/azurebackup_feedback).

## Próximas etapas
* Obtenha mais detalhes sobre o [backup de computadores que usam o Windows](backup-configure-vault.md).
* Agora que você faz backup de seus arquivos e pastas, poderá [gerenciar seus servidores e cofres](backup-azure-manage-windows-server.md).
* Se você precisar restaurar um backup, use este artigo para [restaurar os arquivos para um computador que usa o Windows](backup-azure-restore-windows-server.md).

<!---HONumber=AcomDC_0928_2016-->