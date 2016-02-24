<properties
	pageTitle="Preparar o ambiente para fazer backup de um servidor do Windows ou computador cliente | Microsoft Azure"
	description="Prepare o ambiente para fazer backup do Windows criando um cofre de backup, baixando credenciais e instalando o agente de backup."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="cofre de backup; agente de backup; backup do Windows;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="02/05/2016"
	ms.author="trinadhk; jimpark; markgal"/>


# Preparar o ambiente para fazer backup de computadores do Windows para o Azure
Este artigo lista o que você precisa fazer para preparar seu ambiente para fazer backup de um computador do Windows para o Azure.

| Etapa | Nome | Detalhes |
| :------: | ---- | ------- |
| 1 | [Criar um cofre](#create-a-backup-vault) | Criar um cofre no [portal de gerenciamento do Backup do Azure](http://manage.windowsazure.com) |
| 2 | [Baixar as credenciais do cofre](#download-the-vault-credential-file) | Baixe as credenciais do cofre que serão usadas para registrar o computador do Windows com o cofre de backup |
| 3 | [Instalar o Agente de Backup do Azure ](#download-install-and-register-the-azure-backup-agent) | Instale o agente e registre o servidor no cofre de backup usando as credenciais do cofre |

Se já realizou todas as etapas de alto nível acima, você pode iniciar o [backup dos computadores do Windows](backup-azure-backup-windows-server.md). Caso contrário, prossiga com as etapas detalhadas abaixo para verificar se seu ambiente está preparado.

## Antes de começar
Para preparar o ambiente para fazer backup de computadores do Windows, você precisa de uma conta do Azure. Se você não tiver uma, poderá criar uma [conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

## Criar um cofre de backup
Para fazer backup de arquivos e dados de um computador do Windows ou DPM (Data Protection Manager) para o Azure ou ao fazer backup de VMs de IaaS no Azure, você precisa criar um cofre de backup na região geográfica em que deseja armazenar os dados.

**Para criar um cofre de Backup:**

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com/)

2. Clique em **Novo** > **Serviços de Dados** > **Serviços de Recuperação** > **Cofre de Backup** e escolha **Criação Rápida**.

    ![Criar cofre](./media/backup-configure-vault/createvault1.png)

3. Para o parâmetro **Nome**, digite um nome amigável para identificar o cofre de backup. Ele precisa ser exclusivo para cada assinatura.

    No parâmetro **Região**, selecione a região geográfica para o cofre de backup. A escolha determina a região geográfica para a qual os dados de backup são enviados. Ao escolher uma área geográfica próxima à sua localidade, você pode reduzir a latência de rede ao fazer backup no Azure.

    Clique em **Criar Cofre** para concluir o fluxo de trabalho.

    Pode levar algum tempo para que o cofre de backup seja criado. Para verificar o status, você pode monitorar as notificações na parte inferior do portal.

    ![Criando cofre](./media/backup-configure-vault/creatingvault1.png)

    Depois que o cofre de backup tiver sido criado, você verá uma mensagem que informará que o cofre foi criado com êxito. O cofre também é listado nos recursos dos Serviços de Recuperação como **Ativo**. ![Status de criação de cofre](./media/backup-configure-vault/backupvaultstatus1.png)

> [AZURE.IMPORTANT] O melhor momento para identificar sua opção de redundância de armazenamento é logo após a criação de um cofre e antes de qualquer máquina virtual ser registrada no cofre. Depois que um item tiver sido registrado no cofre, a opção de redundância de armazenamento será bloqueada e não poderá ser modificada.
>
> **Saiba mais sobre como escolher opções de redundância de armazenamento nesta [visão geral](backup-azure-storage-redundancy-options.md).**

## Baixe o arquivo de credencial do cofre
O servidor local (cliente Windows, Windows Server ou servidor do Data Protection Manager) precisa ser autenticado em um cofre de backup para que o backup de dados possa ser feito no Azure. A autenticação é feita com as "credenciais do cofre". O arquivo de credenciais do cofre é baixado por meio de um canal seguro do portal do Azure, e o serviço de Backup do Azure não tem conhecimento da chave privada do certificado, que não persiste no portal nem o serviço.

Para saber mais sobre como [usar as credenciais do cofre para autenticação no serviço Backup do Azure](backup-introduction-to-azure-backup.md#what-is-the-vault-credential-file).

**Para baixar o arquivo de credenciais do cofre para um computador local:**

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com/)

2. Clique em **Serviços de Recuperação** no painel de navegação à esquerda e selecione o cofre de backup que você criou.

3.  Clique no ícone de nuvem para ir para a exibição de *Início Rápido* do cofre de backup.

    ![Visualização rápida](./media/backup-configure-vault/quickview.png)

4. Na página **Início Rápido**, clique em **Baixar credenciais do cofre**.

    ![Baixar](./media/backup-configure-vault/downloadvc.png)

    O portal gerará uma credencial de cofre usando uma combinação do nome do cofre e a data atual. O arquivo de credenciais do cofre é usado somente durante o fluxo de trabalho de registro e expira após 48 horas.

    O arquivo de credenciais do cofre pode ser baixado no portal.

5. Clique em **Salvar** para baixar as credenciais do cofre para a pasta de donwloads da conta local, ou selecione **Salvar Como** a partir do menu *Salvar*, para especificar um local para as credenciais do cofre.

    Você não precisa abrir as credenciais do cofre no momento.

    Salve as credenciais do cofre em um local que possa ser acessado em seu computador. Se forem armazenadas em um compartilhamento de arquivos/SMB, verifique as permissões de acesso.

## Baixar, instalar e registrar o agente de Backup do Azure
Após a criação do cofre de Backup do Azure, um agente deverá ser instalado em cada um dos computadores com o Windows (o Windows Server, o cliente do Windows, o servidor do System Center Data Protection Manager ou o computador do Azure Backup Server), o que permitirá o backup de dados e de aplicativos no Azure.

**Para baixar, instalar e registrar o agente:**

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com/)

2. Clique em **Serviços de Recuperação**, em seguida, selecione o cofre de backup que você quer registrar com um servidor.

3. Na página Início Rápido, clique em **Agente para Windows Server ou System Center Data Protection Manager ou Windows Client > Salvar**.

    ![Salvar agente](./media/backup-configure-vault/agent.png)

4. Depois que o download de *MARSagentinstaller.exe* for concluído, clique em **Executar** (ou clique duas vezes em **MARSAgentInstaller.exe** no local salvo). Escolha a *pasta de instalação* e a *pasta de cache* necessárias para o agente e clique em **Avançar**.

    O local do cache especificado deve ter espaço livre igual a pelo menos 5% dos dados de backup.

    ![Usar o cache](./media/backup-configure-vault/recovery-services-agent-setup-wizard-1.png)

5. Se você usar um servidor proxy para se conectar à Internet, na tela **Configuração de Proxy**, insira os detalhes do servidor proxy. Se você usar um proxy autenticado, insira os detalhes de nome de usuário e senha e clique em **Avançar**.

    O agente de Backup do Azure instala o .NET Framework 4.5 e o Windows PowerShell (se ele ainda não estiver instalado) para concluir a instalação.

6. Depois que o agente estiver instalado, clique em **Prosseguir com o Registro** para continuar com o fluxo de trabalho.

    ![Registrar](./media/backup-configure-vault/register.png)

7. Na tela **Identificação do Cofre**, navegue até e selecione o *arquivo de credenciais do cofre* que você baixou anteriormente.

    ![Credenciais do cofre](./media/backup-configure-vault/vc.png)

    O arquivo de credenciais do cofre é válido apenas por 48 horas (após o download do portal). Se você encontrar qualquer erro nessa tela (por exemplo, "o arquivo de credenciais do cofre fornecido expirou"), faça logon no Portal do Azure e baixe o arquivo de credenciais do cofre novamente.

    Verifique se o arquivo de credenciais do cofre está disponível em um local que pode ser acessado pelo aplicativo de instalação. Se você encontrar erros relacionados a acesso, copie o arquivo de credenciais do cofre para um local temporário nesse computador e repita a operação.

    Se você encontrar um erro de credenciais inválidas do cofre (por exemplo, "Credenciais do cofre fornecidas inválidas"), isso significa que o arquivo está corrompido ou não tem as últimas credenciais associadas ao serviço de recuperação. Repita a operação depois de baixar um novo arquivo de credencial de cofre no portal. Esse erro normalmente será exibido se o usuário clicar na opção *Baixar credencial de cofre* em uma sequência rápida. Nesse caso, apenas o último arquivo de credencial de cofre é válido.

8. Na tela **Configuração de criptografia**, você pode *gerar* uma senha ou *fornecer* uma (mínio de 16 caracteres). Lembre-se de salvar a senha em um local seguro.

    ![Criptografia](./media/backup-configure-vault/encryption.png)

    > [AZURE.WARNING] Se a senha for perdida ou esquecida, a Microsoft não poderá ajudar na recuperação dos dados de backup. O usuário final é proprietário da senha de criptografia, isto é, a Microsoft não tem visibilidade da senha que é usada pelo usuário final. Salve o arquivo em um local seguro, pois ela é obrigatória durante uma operação de recuperação.

9. Clique em **Concluir**.

    O computador agora está registrado com êxito no cofre e você está pronto para iniciar o backup para o Microsoft Azure.

## Próximas etapas
- Inscrever-se para obter uma [conta de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/)
- [Fazer backup de um servidor do Windows ou um computador cliente](backup-azure-backup-windows-server.md).
- Se você ainda tiver perguntas sem resposta, confira as [Perguntas frequentes do Backup do Azure](backup-azure-backup-faq.md).
- Visite o [Fórum de backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=AcomDC_0211_2016-->