<properties
	pageTitle="Fazer backup de um Windows Server ou Client para o Azure com o Backup do Azure usando o modelo de implantação do Gerenciador de Recursos | Microsoft Azure"
	description="Faça backup de Windows servers ou clientes Windows no Azure criando um cofre de backup, baixando credenciais, instalando o agente de backup e concluindo um backup inicial de seus arquivos e pastas."
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="cfreeman"
	editor=""
	keywords="cofre de backup; fazer backup de um Windows server; backup do windows;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="jimpark; trinadhk; markgal"/>

# Fazer backup de um cliente ou servidor do Microsoft Azure usando o modelo de implantação do Gerenciador de Recursos

> [AZURE.SELECTOR]
- [Portal do Azure](backup-configure-vault.md)
- [Portal clássico](backup-configure-vault-classic.md)

Este artigo explica como fazer backup dos seus arquivos e pastas do Windows Server (ou cliente Windows) no Azure com o Backup do Azure usando o modelo de implantação do Gerenciador de recursos.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] modelo de implantação clássica.

![Etapas do processo de backup](./media/backup-configure-vault/initial-backup-process.png)


## Antes de começar
Para fazer backup de um servidor ou cliente no Azure, você precisará de uma conta do Azure. Se não tiver uma, você poderá criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.

## Etapa 1: criar um cofre dos Serviços de Recuperação

Um cofre dos Serviços de Recuperação é uma entidade que armazena todos os backups e pontos de recuperação criados ao longo do tempo. O cofre dos Serviços de Recuperação também contém a política de backup aplicada às pastas e arquivos protegidos. Quando você cria um cofre dos Serviços de Recuperação, também deve selecionar a opção de redundância de armazenamento apropriada.

### Para criar um cofre de Serviços de Recuperação

1. Se ainda não tiver feito isso, entre no [Portal do Azure](https://portal.azure.com/) usando a sua assinatura do Azure.

2. No menu Hub, clique em **Procurar** e, na lista de recursos, digite **Serviços de Recuperação**. Quando você começar a digitar, a lista será filtrada com base em sua entrada. Clique em **Cofres dos Serviços de Recuperação**.

    ![Criar Cofre de Serviços de Recuperação - etapa 1](./media/backup-configure-vault/browse-to-rs-vaults.png) <br/>

    A lista de cofres dos Serviços de Recuperação é exibida.

3. No menu **Cofres dos Serviços de Recuperação**, clique em **Adicionar**.

    ![Criar Cofre de Serviços de Recuperação - etapa 2](./media/backup-configure-vault/rs-vault-menu.png)

    A folha do cofre dos Serviços de Recuperação será aberta, solicitando que você forneça o **Nome**, a **Assinatura**, o **Grupo de recursos** e o **Local**.

    ![Criar Cofre de Serviços de Recuperação - etapa 5](./media/backup-configure-vault/rs-vault-attributes.png)

4. Em **Nome**, insira um nome amigável para identificar o cofre. O nome precisa ser exclusivo para a assinatura do Azure. Digite um nome que contenha de 2 a 50 caracteres. Ele deve começar com uma letra e pode conter apenas letras, números e hifens.

5. Clique em **Assinatura** para ver a lista de assinaturas disponíveis. Se você não tiver certeza sobre qual assinatura usar, utilize a assinatura padrão (ou sugerida). Só haverá múltiplas opções se sua conta organizacional estiver associada a várias assinaturas do Azure.

6. Clique em **Grupo de recursos** para ver a lista dos Grupos de recursos disponíveis ou clique em **Novo** para criar um novo Grupo de recursos. Para obter informações completas sobre Grupos de recursos, confira [Visão geral do Azure Resource Manager](../resource-group-overview.md)

7. Clique em **Local** para selecionar a região geográfica do cofre. Essa escolha determina a região geográfica para a qual os dados de backup são enviados. Ao escolher uma região geográfica próxima à sua localidade, você poderá reduzir a latência de rede ao fazer backup no Azure.

8. Clique em **Criar**. Talvez demore um pouco para o cofre de Serviços de Recuperação ser criado. Monitore as notificações de status na área superior direita no portal. Depois de criar o seu cofre, ele deve abrir no portal. Se você não vir o seu cofre listado depois de ter sido concluído, clique em **Atualizar**. Quando a lista for atualizada, clique no nome do cofre.

### Para determinar a redundância de armazenamento
Quando você cria um cofre dos Serviços de Recuperação, determina como o armazenamento é replicado.

1. Na folha **Configurações**, que abre automaticamente com o painel do seu cofre, clique em **Infraestrutura de Backup**.

2. Na folha Infraestrutura de Backup, clique em **Configuração de Backup** para exibir o **Tipo de replicação de armazenamento**.

    ![Criar Cofre de Serviços de Recuperação - etapa 5](./media/backup-configure-vault/backup-infrastructure.png)

3. Escolha a opção de replicação de armazenamento para o cofre.

    ![Listar cofres de Serviços de Recuperação](./media/backup-configure-vault/choose-storage-configuration.png)

    Por padrão, seu cofre tem armazenamento com redundância geográfica. Se você estiver usando o Azure como um ponto de extremidade de armazenamento de backup principal, continue usando o armazenamento com redundância geográfica. Se você estiver usando o Azure como um ponto de extremidade de armazenamento de backup não principal, escolha o armazenamento com redundância local, o que reduzirá o custo de armazenar dados no Azure. Leia mais sobre as opções de armazenamento [com redundância geográfica](../storage/storage-redundancy.md#geo-redundant-storage) e [com redundância local](../storage/storage-redundancy.md#locally-redundant-storage) nesta [visão geral](../storage/storage-redundancy.md).

    Depois de escolher a opção de armazenamento para o seu cofre, você está pronto para associar os seus arquivos e pastas com o cofre.

Agora que você criou um cofre, prepare a sua infraestrutura para fazer backup de arquivos e pastas, baixando e instalando o agente dos Serviços de Recuperação do Microsoft Azure, baixar credenciais de cofre e usar essas credenciais para registrar o agente no cofre.

## Etapa 2: baixar arquivos

>[AZURE.NOTE] A habilitação do backup pelo portal do Azure estará disponível em breve. Neste momento, você pode usar o agente dos Serviços de Recuperação do Microsoft Azure no local para fazer backup de seus arquivos e pastas.

1. Clique em **Configurações** no painel do cofre dos Serviços de Recuperação.

    ![Abrir folha de meta backup](./media/backup-configure-vault/settings-button.png)

2. Clique em **Introdução > Backup** na folha Configurações.

    ![Abrir folha de meta backup](./media/backup-configure-vault/getting-started-backup.png)

3. Clique em **Meta de Backup** na folha Backup.

    ![Abrir folha de meta backup](./media/backup-configure-vault/backup-goal.png)

4. Selecione **Local** no menu Onde a sua carga de trabalho está sendo executada?

5. Selecione **Arquivos e pastas** no menu Do que você deseja fazer backup? e clique em **OK**.

#### Baixar o agente dos Serviços de Recuperação

1. Clique em **Baixar agente do Windows Server ou Windows Client** na folha **Preparar infraestrutura**.

    ![preparar infraestrutura](./media/backup-configure-vault/prepare-infrastructure-short.png)

2. Clique em **Salvar** no pop-up de download. Por padrão, o arquivo **MARSagentinstaller.exe** será salvo em sua pasta Downloads.

#### Baixar as credenciais do cofre

1. Clique em **Baixar > Salvar** na folha Preparar infraestrutura.

    ![preparar infraestrutura](./media/backup-configure-vault/prepare-infrastructure-download.png)

## Etapa 3: instalar e registrar o agente

1. Localize e clique duas vezes no **MARSagentinstaller.exe** na pasta Downloads (ou em outro local salvo).

2. Conclua o Assistente de Instalação do Agente do Serviços de Recuperação do Microsoft Azure. Para concluir o assistente, você precisa fazer o seguinte:

    - Escolher um local para a instalação e a pasta de cache.
    - Fornecer as informações de seu servidor proxy se você usar um servidor proxy para conectar-se à Internet.
    - Forneça os detalhes do seu nome de usuário e de sua senha se usar um proxy autenticado.
    - Forneça as credenciais do cofre baixado
    - Salve a senha de criptografia em um local seguro.

    >[AZURE.NOTE] Se você perder ou esquecer a senha, a Microsoft não poderá ajudar a recuperar os dados de backup. Salve o arquivo em um local seguro. Isso é necessário para restaurar um backup.

Agora, o agente está instalado e seu computador está registrado no cofre. Você está pronto para configurar e agendar o backup.

### Confirmar a instalação

Para confirmar que o agente foi instalado e registrado corretamente, você poderá verificar os itens de backup na seção **Servidor de Produção** do portal de gerenciamento. Para fazer isso:

1. Entre no [Portal do Azure](https://portal.azure.com/) usando sua assinatura do Azure.

2. No menu Hub, clique em **Procurar** e, na lista de recursos, digite **Serviços de Recuperação**. Quando você começar a digitar, a lista será filtrada com base em sua entrada. Clique em **Cofres dos Serviços de Recuperação**.

    ![Criar Cofre de Serviços de Recuperação - etapa 1](./media/backup-configure-vault/browse-to-rs-vaults.png) <br/>

    A lista de cofres dos Serviços de Recuperação é exibida.

2. Selecione o nome do cofre que você criou.

    A folha de painel do cofre dos Serviços de Recuperação é aberta.

    ![painel do cofre dos serviços de recuperação](./media/backup-configure-vault/rs-vault-dashboard.png) <br/>

3. Clique no botão **Configurações** na parte superior da página.

4. Clique em **Infraestrutura do Backup > Servidores de Produção**.

    ![Servidores de produção](./media/backup-configure-vault/production-server-verification.png)

Se você vir os servidores na lista, terá a confirmação de que o agente foi instalado e registrado corretamente.

## Etapa 4: Completar o backup inicial.

O backup inicial inclui duas tarefas principais:

- Agendar o backup
- Fazer backup de arquivos e pastas pela primeira vez

Para concluir o backup inicial, você deve usar o agente de backup do Microsoft Azure.

### Para agendar o backup

1. Abra o Agente de Backup do Microsoft Azure. Você pode localizá-lo pesquisando no seu computador por **Backup do Microsoft Azure**.

    ![Iniciar o agente de Backup do Azure](./media/backup-configure-vault/snap-in-search.png)

2. No agente de Backup, clique em **Agendar Backup**.

    ![Agendar um backup do Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

3. Na página de Introdução do Assistente de Agendamento de Backup, clique em **Avançar**.

4. Na tela Selecionar Itens para Backup, clique em **Adicionar Itens**.

5. Selecione os arquivos e pastas dos quais você deseja fazer backup e clique em **Ok**.

6. Clique em **Próximo**.

7. Na tela **Especificar Agendamento de Backup**, especifique o **agendamento de backup** e clique em **Avançar**.

    Você pode agendar backups diários (com uma taxa máxima de três vezes por dia) ou backups semanais.

    ![Itens para o backup do Windows Server](./media/backup-configure-vault/specify-backup-schedule-close.png)

    >[AZURE.NOTE] Para saber mais sobre como especificar o agendamento de backup, confira o artigo [Usar o Backup do Azure para substituir a sua infraestrutura de fita](backup-azure-backup-cloud-as-tape.md).

8. Na página **Selecionar Política de Retenção**, escolha a **Política de Retenção** para a cópia de backup.

    A política de retenção especifica a duração de armazenamento do backup. Em vez de especificar apenas uma “política simples” para todos os pontos de backup, você pode especificar políticas de retenção diferentes com base em quando o backup ocorre. Você pode modificar as políticas de retenção diária, semanal, mensal e anual para atender às suas necessidades.

9. Na tela Escolher Tipo de Backup Inicial, escolha o tipo de backup inicial. Deixe a opção **Automaticamente pela rede** selecionada e clique em **Avançar**.

    Você pode fazer backup automaticamente pela rede ou pode fazer backup offline. O restante deste artigo descreve o processo para realização de backup automático. Se preferir fazer um backup offline, examine o artigo [Fluxo de trabalho de backup offline no Backup do Azure](backup-azure-backup-import-export.md) para obter informações adicionais.

10. Na página Confirmação, examine as informações e clique em **Concluir**.

11. Depois que o assistente terminar de criar o agendamento de backup, clique em **Fechar**.

### Habilitar a limitação da rede (opcional)

O agente de backup fornece limitação de rede. A limitação controles como a largura de banda de rede é usada durante a transferência de dados. Esse controle poderá ser útil se você precisar fazer backup de dados durante o horário de expediente, mas não quiser que o processo de backup interfira em outro tráfego de Internet. A limitação aplica-se a atividades de backup e restauração.

>[AZURE.NOTE] A limitação de rede não está disponível no Windows Server 2008 R2 SP1, Windows Server 2008 SP2 ou Windows 7 (com service packs). A limitação de recurso de rede do Backup do Azure envolve a QoS (Qualidade de Serviço) no sistema operacional local. Embora o Backup do Azure possa proteger esses sistemas operacionais, a versão de QoS disponível nessas plataformas não funciona com a limitação de rede do Backup do Azure. A limitação de rede pode ser usada em todos os outros [sistemas operacionais com suporte](backup-azure-backup-faq.md#installation-amp-configuration).

**Para habilitar a limitação de rede**

1. No agente de backup, clique em **Alterar Propriedades**.

    ![Alterar propriedades](./media/backup-configure-vault/change-properties.png)

2. Na guia **Limitação**, marque a caixa de seleção **Habilitar limitação de uso de largura de banda da Internet para operações de backup**.

    ![Limitação de rede](./media/backup-configure-vault/throttling-dialog.png)

3. Depois de habilitar a limitação, especifique a largura de banda permitida para transferência de dados de backup durante as **Horas úteis** e as **Horas não úteis**.

    Os valores de largura de banda começam em 512 quilobits por segundo (Kbps) e podem ir até 1.023 megabytes por segundo (Mbps). Você também pode indicar o início e o término para **Horas úteis** e quais dias da semana são considerados dias úteis. Horas fora das horas úteis designadas são consideradas horas não úteis.

4. Clique em **OK**.

### Para fazer backup de arquivos e pastas pela primeira vez

1. No agente de backup, clique em **Fazer Backup Agora** para concluir a propagação inicial pela rede.

    ![Fazer backup do Windows Server agora](./media/backup-configure-vault/backup-now.png)

2. Na página Confirmação, examine as configurações que o Assistente Fazer Backup Agora usará para fazer backup do computador. Em seguida, clique em **Fazer Backup**.

3. Clique em **Fechar** para fechar o assistente. Se você fizer isso antes da conclusão do processo de backup, o assistente continuará a ser executado em segundo plano.

Depois que o backup inicial for concluído, o status **Trabalho concluído** aparecerá no Console de backup.

![IR completo](./media/backup-configure-vault/ircomplete.png)

## Perguntas?
Se você tiver dúvidas ou gostaria de ver algum recurso incluído, [envie-nos seus comentários](http://aka.ms/azurebackup_feedback).

## Próximas etapas
Para saber mais sobre como fazer backup de VMs ou de outras cargas de trabalho, confira:

- Agora que você faz backup de seus arquivos e pastas, poderá [gerenciar seus servidores e cofres](backup-azure-manage-windows-server.md).
- Se você precisar restaurar um backup, use este artigo para [restaurar os arquivos para um computador que usa o Windows](backup-azure-restore-windows-server.md).

<!---HONumber=AcomDC_0817_2016-->