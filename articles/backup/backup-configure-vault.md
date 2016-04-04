<properties
	pageTitle="Fazer backup de um Windows Server ou de um cliente Windows no Azure | Microsoft Azure"
	description="Faça backup de servidores ou clientes Windows no Azure criando um cofre de backup, baixando credenciais, instalando o agente de backup e concluindo um backup inicial de arquivos e pastas."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="cofre de backup; fazer backup de um Windows Server; backup do windows;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/19/2016"
	ms.author="jimpark; trinadhk; markgal"/>


# Fazer backup de um Windows Server ou de um cliente no Azure
Este artigo aborda os procedimentos que você precisa realizar para preparar seu ambiente e fazer backup de um Windows Server (ou cliente) no Azure. Também aborda considerações sobre a implantação de sua solução de backup. Se você estiver interessado em experimentar o Backup do Azure pela primeira vez, [este artigo](backup-configure-vault.md) orientará você rapidamente pelo processo.

![Criar cofre](./media/backup-configure-vault/initial-backup-process.png)

## Antes de começar
Para fazer backup do Windows Server (ou cliente) para o Azure, você precisará de uma conta do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.

## Etapa 1: criar um cofre de backup
Para fazer o backup de arquivos e pastas de um Windows Server ou cliente, você deverá criar um cofre de backup na região geográfica em que deseja armazenar os dados.

### Para criar um cofre de backup

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com/)

2. Clique em **Novo** > **Serviços de Dados** > **Serviços de Recuperação** > **Cofre de Backup** e escolha **Criação Rápida**.

3. Para o parâmetro **Nome**, digite um nome amigável para identificar o cofre de backup. Digite um nome que contenha de 2 a 50 caracteres. Ele deve começar com uma letra e pode conter apenas letras, números e hifens. Ele precisa ser exclusivo para cada assinatura.

    No parâmetro **Região**, selecione a região geográfica para o cofre de backup. A escolha determina a região geográfica para a qual os dados de backup são enviados. Ao escolher uma região geográfica próxima à sua localidade, você poderá reduzir a latência de rede ao fazer backup no Azure.

    Clique em **Criar cofre**.

    ![Criar cofre](./media/backup-configure-vault/demo-vault-name.png)

    Pode levar algum tempo para que o cofre de backup seja criado. Para verificar o status, você pode monitorar as notificações na parte inferior do portal.

    Depois que o cofre de backup tiver sido criado, você verá uma mensagem que informará que o cofre foi criado com êxito. O cofre também é listado nos recursos dos Serviços de Recuperação como **Ativo**.

    ![Status de criação de cofre](./media/backup-configure-vault/recovery-services-select-vault.png)

4. Selecione a opção **redundância de armazenamento**.

    >[AZURE.IMPORTANT] O melhor momento para identificar sua opção de redundância de armazenamento é logo após a criação de um cofre e antes de qualquer máquina virtual ser registrada no cofre. Depois que um item tiver sido registrado no cofre, a opção de redundância de armazenamento será bloqueada e não poderá ser modificada.

    Se você estiver usando o Azure como ponto de extremidade de armazenamento de backup principal (por exemplo, estiver fazendo backup de um Windows Server no Azure), deverá considerar a possibilidade de escolher a opção [de armazenamento com redundância geográfica](../storage/storage-redundancy.md#geo-redundant-storage) (padrão).

    Se estiver usando o Azure como um ponto de extremidade de armazenamento de backup terciário (por exemplo, se estiver usando SCDPM para ter uma cópia de backup local e usando o Azure para suas necessidades de retenção de longo prazo), considere a possibilidade de escolher o [armazenamento com redundância local](../storage/storage-redundancy.md#locally-redundant-storage). Isso reduz o custo de armazenamento de dados no Azure, além de oferecer um nível mais baixo de durabilidade para seus dados que pode ser aceitável para cópias terciárias.

    **Para selecionar a opção de redundância de armazenamento**.

    a. Clique no cofre que você acabou de criar.

    b. Na página **Início Rápido**, selecione **Configurar**.

    ![Configurar o status do cofre](./media/backup-configure-vault/configure-vault.png)

    c. Escolha a opção de redundância de armazenamento apropriada.

    Você precisará clicar em **Salvar** se tiver selecionado **Localmente Redundante**, pois **Geograficamente Redundante** é a opção padrão.

    d. Clique em **Serviços de Recuperação** no painel de navegação à esquerda para retornar à lista de recursos para **Serviços de Recuperação**.

## Etapa 2: baixar o arquivo de credencial do cofre
A máquina local (Windows Server ou cliente do Windows) precisa ser autenticada em um cofre de backup antes de poder fazer backup de dados no Azure. A autenticação é feita com as *credenciais do cofre*. O arquivo de credencial do cofre é baixado por meio de um canal seguro no Portal do Azure. O serviço de Backup do Azure não tem conhecimento da chave privada do certificado, que não é mantida no portal nem no serviço.

Saiba mais sobre como [usar as credenciais do cofre para autenticação no serviço do Backup do Azure](backup-introduction-to-azure-backup.md#what-is-the-vault-credential-file).

### Para baixar o arquivo de credenciais do cofre para um computador local

1. Clique em **Serviços de Recuperação** no painel de navegação à esquerda e selecione o cofre de backup criado.

    ![IR completo](./media/backup-configure-vault/rs-left-nav.png)

2.  Na página **Início Rápido**, clique em **Baixar credenciais do cofre**.

    O portal gerará uma credencial de cofre usando uma combinação do nome do cofre e a data atual. O arquivo de credenciais do cofre é usado somente durante o fluxo de trabalho de registro e expira após 48 horas.

    O arquivo de credenciais do cofre pode ser baixado no portal.

3. Clique em **Salvar** para baixar as credenciais do cofre para a pasta de donwloads da conta local, ou selecione **Salvar Como** a partir do menu **Salvar**, para especificar um local para as credenciais do cofre.

    >[AZURE.NOTE] Salve o arquivo de credenciais do cofre em um local que possa ser acessado em seu computador. Se forem armazenadas em um compartilhamento de arquivos/SMB, verifique as permissões de acesso.

## Etapa 3: Baixar, instalar e registrar o agente do Backup do Azure
Depois de criar o cofre de Backup do Azure e baixar as credenciais do cofre, será necessário instalar um agente em cada uma das suas máquinas com Windows (Windows Server ou cliente do Windows).

### Para baixar, instalar e registrar o agente

1. Clique em **Serviços de Recuperação**, em seguida, selecione o cofre de backup que você quer registrar com um servidor.

2. Na página Início Rápido, clique em **Agente para Windows Server ou System Center Data Protection Manager ou Windows Client > Salvar**.

    ![Salvar agente](./media/backup-configure-vault/agent.png)

3. Depois que o download de *MARSagentinstaller.exe* for concluído, clique em **Executar** (ou clique duas vezes em **MARSAgentInstaller.exe** no local salvo).

4. 	Escolha a *pasta de instalação* e a *pasta de cache* necessárias para o agente e clique em **Avançar**.

    O local do cache especificado deve ter espaço livre igual a pelo menos 5% dos dados de backup.

5. Você pode continuar e se conectar à Internet por meio das configurações de proxy padrão ou, se usar um servidor proxy para se conectar à Internet, na tela **Configuração de proxy**, marque a caixa **Usar configurações personalizadas de proxy** e insira os detalhes do servidor proxy.

    Se você usar um proxy autenticado, insira os detalhes de nome de usuário e de senha.

    Clique em **Próximo**.

6. Clique em **Instalar** para a instalação do agente.

    O agente de Backup do Azure instala o .NET Framework 4.5 e o Windows PowerShell (se ele ainda não estiver instalado) para concluir a instalação.

7. Após a instalação do agente, clique em **Prosseguir com o Registro** para continuar com o fluxo de trabalho.

8. Na tela **Identificação do Cofre**, navegue até o *arquivo de credenciais do cofre* que você baixou anteriormente e selecione-o.

    O arquivo de credenciais do cofre é válido apenas por 48 horas (após o download do portal). Se você encontrar qualquer erro nessa tela (por exemplo "o arquivo de credenciais do cofre fornecido expirou"), faça logon no Portal do Azure e baixe o arquivo de credenciais do cofre novamente.

    Verifique se o arquivo de credenciais do cofre está disponível em um local que pode ser acessado pelo aplicativo de instalação. Se você encontrar erros relacionados a acesso, copie o arquivo de credenciais do cofre para um local temporário nesse computador e repita a operação.

    Se você encontrar um erro de credencial de cofre inválida (por exemplo, “Credenciais do cofre fornecidas inválidas”), significa que o arquivo está corrompido ou não tem as últimas credenciais associadas ao serviço de recuperação. Repita a operação depois de baixar um novo arquivo de credencial de cofre no portal. Normalmente, esse erro será exibido se o usuário clicar na opção *Baixar credencial do cofre* em uma sequência rápida. Nesse caso, apenas o último arquivo de credencial de cofre é válido.

9. Na tela **Configuração de criptografia**, você pode *gerar* uma senha ou *fornecer* uma (mínimo de 16 caracteres). Lembre-se de salvar a senha em um local seguro.

    Clique em **Concluir**.

    > [AZURE.WARNING] Se a senha for perdida ou esquecida, a Microsoft não poderá ajudar na recuperação dos dados de backup. O usuário final é proprietário da senha de criptografia, isto é, a Microsoft não tem visibilidade da senha que é usada pelo usuário final. Salve o arquivo em um local seguro, pois ela é obrigatória durante uma operação de recuperação.

    O **Assistente para Registrar Servidor** registrará o servidor no Backup do Microsoft Azure.

10. Quando a **chave de criptografia** for definida, deixe a caixa de seleção **Iniciar o Agente de Serviços de Recuperação do Microsoft Azure** marcada e clique em **Fechar**.

## Etapa 4: Completar o backup inicial.

O backup inicial é composto por duas tarefas principais: **criar o agendamento de backup** e **fazer backup de arquivos e pastas pela primeira vez**. Depois de concluir o backup inicial, com base no agendamento que você define, a política de backup criará pontos de backup que você pode usar se for necessário recuperar os dados.

### Agendar o backup

1. Abra o **Agente do Backup do Microsoft Azure** (ele abrirá automaticamente se você deixou a caixa de seleção **Iniciar o Agente dos Serviços de Recuperação do Microsoft Azure** marcada quando fechou o **Assistente para Registrar Servidor**, ou você pode localizá-lo pesquisando em seu computador por *Backup do Microsoft Azure*).

    ![Agendar um Backup do Windows Server](./media/backup-configure-vault/snap-in-search.png)

2. No **Agente do Backup do Microsoft Azure** clique em **Agendar Backup**.

    ![Agendar um Backup do Windows Server](./media/backup-configure-vault/schedule-backup-close.png)

3. Na tela **Introdução ao Assistente para Agendar Backup**, clique em **Avançar**.

4. Na tela **Selecionar Itens para Backup**, clique em **Adicionar Itens**.

5. Selecione os arquivos e pastas dos quais você deseja fazer backup e clique em **Ok**.

6. Clique em **Próximo**.

7. Na tela **Especificar o Agendamento do Backup**, especifique o **agendamento do backup** e clique em **Avançar**.

    Você pode agendar backups diários (no máximo três vezes por dia) ou backups semanais.

    ![Itens para o backup do Windows Server](./media/backup-configure-vault/specify-backup-schedule-close.png)

    >[AZURE.NOTE] A especificação do agendamento de backup é explicada em detalhes neste [artigo](backup-azure-backup-cloud-as-tape.md).

8. Na tela **Selecionar Política de Retenção**, escolha a **Política de Retenção** da cópia de backup.

    A política de retenção especifica a duração de armazenamento do backup. Em vez de especificar apenas uma “política simples” para todos os pontos de backup, você pode especificar políticas de retenção diferentes com base em quando o backup é feito. Você pode modificar as políticas de retenção diária, semanal, mensal e anual para atender às suas necessidades.

     Clique em **Próximo**.

9. Na tela **Escolher Tipo de Backup Inicial**, escolha o tipo de backup inicial**.

    Você pode fazer backup automaticamente pela rede ou pode fazer backup offline. O restante deste artigo segue o processo de backup automático. Se preferir fazer um backup offline, examine este artigo para obter informações adicionais sobre o [fluxo de trabalho do backup offline no Backup do Azure](backup-azure-backup-import-export.md).

    Deixe a opção **Automaticamente pela rede** selecionada e clique em **Avançar**.

10. Na tela **Confirmação**, examine as informações e clique em **Concluir**.

11. Depois que o assistente terminar de criar o **agendamento de backup**, clique em **Fechar**.

### Habilitar a limitação da rede (opcional)

O agente do Backup do Azure fornece a limitação de rede. A limitação controles como a largura de banda de rede é usada durante a transferência de dados. Esse controle pode ser útil se você precisa fazer backup de dados durante o horário de expediente, mas não quer que o processo de backup interfira no outro tráfego de Internet. A limitação da transferência de dados aplica-se a atividades de backup e restauração.

1. Em **Agente de backup**, clique em **Alterar Propriedades**.

    ![Alterar propriedades](./media/backup-configure-vault/change-properties.png)

2. Na guia **Limitação**, marque a caixa de seleção **Habilitar limitação de uso de largura de banda da internet para operações de backup**.

    ![Limitação de rede](./media/backup-configure-vault/throttling-dialog.png)

3. Depois de habilitar a limitação, especifique a largura de banda permitida para transferência de dados de backup durante as **Horas úteis** e **Horas não úteis**.

    Os valores de largura de banda começam em 512 quilobytes por segundo (Kbps) e podem ir até 1023 megabytes por segundo (Mbps). Você também pode indicar o início e o término para **Horas úteis** e quais dias da semana são considerados dias úteis. O tempo fora das horas úteis indicadas é considerado hora não útil.

4. Clique em **OK**.

### Fazer backup agora

1. No **Agente de backup** clique em **Fazer Backup Agora** para concluir a propagação inicial pela rede.

    ![Fazer backup do Windows Server agora](./media/backup-configure-vault/backup-now.png)

2. Na tela **Confirmação**, examine as configurações que o assistente usará para fazer backup do computador e clique em**Fazer Backup**.

3. Clique em **Fechar** para fechar o assistente. Você pode fazer isso antes da conclusão do **processo de backup**, e ele continuará a ser executado em segundo plano.

Depois que o backup inicial for concluído, o status *Trabalho concluído* será refletido no **console do Backup do Azure**.

![IR completo](./media/backup-configure-vault/ircomplete.png)

## Próximas etapas
- Inscreva-se para obter uma [conta do Azure gratuita](https://azure.microsoft.com/free/)

Para saber mais sobre como fazer backup de VMs ou de outras cargas de trabalho, confira:

- [Fazer backup de VMs IaaS](backup-azure-vms-prepare.md)
- [Backup de cargas de trabalho no Azure com o Servidor de Backup do Microsoft Azure](backup-azure-microsoft-azure-backup.md)
- [Backup de cargas de trabalho no Azure com o DPM](backup-azure-dpm-introduction.md)

<!---HONumber=AcomDC_0323_2016-->