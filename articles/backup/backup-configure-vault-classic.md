---
title: Fazer backup de um Windows Server ou Client para o Azure usando o modelo de implantação clássico | Microsoft Docs
description: Faça backup de Windows servers ou clientes Windows no Azure criando um cofre de backup, baixando credenciais, instalando o agente de backup e concluindo um backup inicial de seus arquivos e pastas.
services: backup
documentationcenter: ''
author: markgalioto
manager: cfreeman
editor: ''
keywords: cofre de backup; fazer backup de um Windows server; backup do windows;

ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: jimpark; trinadhk; markgal

---
# Fazer backup de um Windows Server ou Client para o Azure usando o modelo de implantação clássico
> [!div class="op_single_selector"]
> * [Portal clássico](backup-configure-vault-classic.md)
> * [Portal do Azure](backup-configure-vault.md)
> 
> 

Este artigo aborda os procedimentos que você precisa realizar para preparar seu ambiente e fazer backup de um Windows server (ou cliente Windows) no Azure. Ele também aborda considerações para a implantação de sua solução de backup. Se você estiver interessado em experimentar o Backup do Azure pela primeira vez, este artigo orientará você rapidamente pelo processo.

![Criar cofre](./media/backup-configure-vault-classic/initial-backup-process.png)

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: Gerenciador de Recursos e Clássico. Este artigo aborda o uso do modelo de implantação clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.
> 
> 

## Antes de começar
Para fazer backup de um servidor ou cliente no Azure, você precisará de uma conta do Azure. Se não tiver uma, você poderá criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.

## Etapa 1: criar um cofre de backup
Para fazer o backup de arquivos e pastas de um servidor ou cliente, você deverá criar um cofre de backup na região geográfica em que deseja armazenar os dados.

### Para criar um cofre de backup
1. Entre no [portal clássico](https://manage.windowsazure.com/).
2. Clique em **Novo** > **Serviços de Dados** > **Serviços de Recuperação** > **Cofre de Backup** e escolha **Criação Rápida**.
3. Para o parâmetro **Nome**, digite um nome amigável para o cofre de backup. Digite um nome que contenha de 2 a 50 caracteres. Ele deve começar com uma letra e pode conter apenas letras, números e hifens. Esse nome precisa ser exclusivo para cada assinatura.
4. No parâmetro **Região**, selecione a região geográfica para o cofre de backup. Essa escolha determina a região geográfica para a qual os dados de backup são enviados. Ao escolher uma região geográfica próxima à sua localidade, você poderá reduzir a latência de rede ao fazer backup no Azure.
5. Clique em **Criar cofre**.
   
    ![Criar um cofre de backup](./media/backup-configure-vault-classic/demo-vault-name.png)
   
    Pode levar algum tempo para que o cofre de backup seja criado. Para verificar o status, monitore as notificações na parte inferior do portal clássico.
   
    Depois que o cofre de backup tiver sido criado, você verá uma mensagem que informará que o cofre foi criado com êxito. Ele também aparece como **Ativo** na lista de recursos **Serviços de Recuperação**.
   
    ![Criando status do cofre](./media/backup-configure-vault-classic/recovery-services-select-vault.png)
6. Selecione a opção de redundância de armazenamento seguindo as etapas descritas aqui.
   
   > [!IMPORTANT]
   > O melhor momento para identificar sua opção de redundância de armazenamento é logo após a criação de um cofre e antes de qualquer máquina virtual ser registrada no cofre. Depois que um item tiver sido registrado no cofre, a opção de redundância de armazenamento será bloqueada e não poderá ser modificada.
   > 
   > 
   
    Se você estiver usando o Azure como ponto de extremidade de armazenamento de backup principal (por exemplo, estiver fazendo backup de um Windows Server para o Azure), considere a possibilidade de escolher a opção [armazenamento com redundância geográfica](../storage/storage-redundancy.md#geo-redundant-storage) (padrão).
   
    Se estiver usando o Azure como um ponto de extremidade de armazenamento de backup terciário (por exemplo, se estiver usando o System Center Data Protection Manager para armazenar uma cópia de backup local e usando o Azure para suas necessidades de retenção de longo prazo), considere a possibilidade de escolher o [armazenamento com redundância local](../storage/storage-redundancy.md#locally-redundant-storage). Isso reduz o custo de armazenamento de dados no Azure, além de oferecer um nível mais baixo de durabilidade para seus dados que pode ser aceitável para cópias terciárias.
   
    **Para selecionar a opção de redundância de armazenamento.**
   
    a. Clique no cofre que você acabou de criar.
   
    b. Na página Início Rápido, selecione **Configurar**.
   
    ![Configurar status do cofre](./media/backup-configure-vault-classic/configure-vault.png)
   
    c. Escolha a opção de redundância de armazenamento apropriada.
   
    Se você selecionar **Localmente Redundante**, precisará clicar em **Salvar** (porque **Redundância Geográfica** é a opção padrão).
   
    d. No painel de navegação esquerdo, clique em **Serviços de Recuperação** para retornar à lista de recursos dos Serviços de Recuperação.

## Etapa 2: baixar o arquivo de credencial do cofre
O computador local precisa ser autenticado em um cofre de backup antes de poder fazer backup de dados no Azure. A autenticação é obtida por meio das *credenciais do cofre*. O arquivo de credencial do cofre é baixado por meio de um canal seguro no portal clássico. A chave privada do certificado não persiste no portal ou serviço.

Saiba mais sobre como [usar as credenciais do cofre para autenticação no serviço do Backup](backup-introduction-to-azure-backup.md#what-is-the-vault-credential-file).

### Para baixar o arquivo de credenciais do cofre para um computador local
1. Clique em **Serviços de Recuperação** no painel de navegação à esquerda e selecione o cofre de backup criado.
   
    ![IR completo](./media/backup-configure-vault-classic/rs-left-nav.png)
2. Na página Início Rápido, clique em **Baixar credenciais do cofre**.
   
   O portal clássico gera uma credencial de cofre usando uma combinação do nome do cofre e a data atual. O arquivo de credenciais do cofre é usado somente durante o fluxo de trabalho de registro e expira após 48 horas.
   
   O arquivo de credenciais do cofre pode ser baixado do portal.
3. Clique em **Salvar** para baixar o arquivo de credenciais do cofre para a pasta local Downloads da conta local. Você também pode selecionar **Salvar Como** no menu **Salvar** para especificar um local para o arquivo de credenciais do cofre.
   
   > [!NOTE]
   > Salve o arquivo de credenciais do cofre em um local que possa ser acessado em seu computador. Se ele estiver armazenado em um protocolo SMB ou em um compartilhamento de arquivos, verifique se você tem permissões para acessá-lo.
   > 
   > 

## Etapa 3: Baixar, instalar e registrar o Agente de backup
Depois de criar o cofre de backup e baixar o arquivo de credenciais do cofre, um agente deve ser instalado em cada uma de suas máquinas do Windows.

### Para baixar, instalar e registrar o agente
1. Clique em **Serviços de Recuperação**, depois selecione o cofre de backup que você quer registrar com um servidor.
2. Na página Início Rápido, clique em **Agente para Windows Server ou System Center Data Protection Manager ou cliente Windows**. Em seguida, clique em **Salvar**.
   
    ![Salvar agente](./media/backup-configure-vault-classic/agent.png)
3. Depois que o download de MARSagentinstaller.exe for concluído, clique em **Executar** (ou clique duas vezes em **MARSAgentInstaller.exe** no local em que ele foi salvo).
4. Escolha a pasta de instalação e a pasta de cache necessárias para o agente e clique em **Avançar**. O local do cache especificado deve ter espaço livre igual a pelo menos 5% dos dados de backup.
5. Você pode continuar a conectar-se à Internet por meio das configurações de proxy padrão. Se você usar um servidor proxy para conectar-se à Internet, na página Configuração de Proxy, marque a caixa de seleção **Usar configurações de proxy personalizadas** e insira os detalhes do servidor proxy. Se você usar um proxy autenticado, insira os detalhes de nome de usuário e senha e clique em **Avançar**.
6. Clique em **Instalar** para iniciar a instalação do agente. O Agente de backup instala o .NET Framework 4.5 e o Windows PowerShell (se ele ainda não estiver instalado) para concluir a instalação.
7. Após a instalação do agente, clique em **Prosseguir com o Registro** para continuar com o fluxo de trabalho.
8. Na página Identificação do Cofre, navegue até o arquivo de credenciais do cofre que você baixou anteriormente e selecione-o.
   
    O arquivo de credenciais do cofre é válido apenas por 48 horas após seu download do portal. Se você encontrar qualquer erro nessa página (por exemplo "O arquivo de credenciais do cofre fornecido expirou"), entre no portal e baixe o arquivo de credenciais do cofre novamente.
   
    Verifique se o arquivo de credenciais do cofre está disponível em um local que pode ser acessado pelo aplicativo de instalação. Se você encontrar erros relacionados a acesso, copie o arquivo de credenciais do cofre para um local temporário no mesmo computador e repita a operação.
   
    Se você encontrar um erro de credencial de cofre (por exemplo, “Credenciais do cofre fornecidas inválidas”), significará que o arquivo está danificado ou não tem as credenciais mais recentes associadas ao serviço de recuperação. Repita a operação depois de baixar um novo arquivo de credencial de cofre no portal. Esse erro também poderá ocorrer se o usuário clicar várias vezes, em sucessão rápida, na opção **Baixar credencial do cofre**. Nesse caso, apenas o último arquivo de credencial de cofre é válido.
9. Na tela Configuração de Criptografia, você pode gerar uma senha ou fornecer uma (com no mínimo 16 caracteres). Lembre-se de salvar a senha em um local seguro.
10. Clique em **Concluir**. O Assistente de Registro do Servidor registrará o servidor no Backup.
    
    > [!WARNING]
    > Se você perder ou esquecer a senha, a Microsoft não poderá ajudar a recuperar os dados de backup. Você é proprietário da senha de criptografia, e a Microsoft não tem visibilidade da senha que é usada por você. Salve o arquivo em um local seguro, porque ele será necessário durante uma eventual operação de recuperação.
    > 
    > 
11. Quando a chave de criptografia for definida, deixe a caixa de seleção **Iniciar o Agente de Serviços de Recuperação do Microsoft Azure** marcada e clique em **Fechar**.

## Etapa 4: Completar o backup inicial.
O backup inicial inclui duas tarefas principais:

* Criar o agendamento de backup
* Fazendo backup de arquivos e pastas pela primeira vez

Após a política de backup concluir o backup inicial, ela criará pontos de backup que poderão ser usados se você precisar recuperar os dados. A política de backup faz isso com base no agendamento que você definir.

### Para agendar o backup
1. Abra o Agente de Backup do Microsoft Azure. (Ele será aberto automaticamente se você tiver deixado a caixa de seleção **Iniciar o Agente de Serviços de Recuperação do Microsoft Azure** marcada ao fechar o Assistente de Registro do Servidor.) Você pode localizá-la pesquisando o seu computador por **Backup do Microsoft Azure**.
   
    ![Iniciar o agente de Backup do Azure](./media/backup-configure-vault-classic/snap-in-search.png)
2. No agente de Backup, clique em **Agendar Backup**.
   
    ![Agendar um backup do Windows Server](./media/backup-configure-vault-classic/schedule-backup-close.png)
3. Na página de Introdução do Assistente de Agendamento de Backup, clique em **Avançar**.
4. Na tela Selecionar Itens para Backup, clique em **Adicionar Itens**.
5. Selecione os arquivos e pastas dos quais você deseja fazer backup e clique em **Ok**.
6. Clique em **Próximo**.
7. Na tela **Especificar Agendamento de Backup**, especifique o **agendamento de backup** e clique em **Avançar**.
   
    Você pode agendar backups diários (com uma taxa máxima de três vezes por dia) ou backups semanais.
   
    ![Itens para o backup do Windows Server](./media/backup-configure-vault-classic/specify-backup-schedule-close.png)
   
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

### Habilitar a limitação da rede (opcional)
O Agente de backup fornece limitação de rede. A limitação controles como a largura de banda de rede é usada durante a transferência de dados. Esse controle poderá ser útil se você precisar fazer backup de dados durante o horário de expediente, mas não quiser que o processo de backup interfira em outro tráfego de Internet. A limitação aplica-se a atividades de backup e restauração.

**Para habilitar a limitação de rede**

1. No agente de Backup, clique em **Alterar Propriedades**.
   
    ![Alterar propriedades](./media/backup-configure-vault-classic/change-properties.png)
2. Na guia **Limitação**, marque a caixa de seleção **Habilitar limitação de uso de largura de banda da Internet para operações de backup**.
   
    ![Limitação de rede](./media/backup-configure-vault-classic/throttling-dialog.png)
3. Depois de habilitar a limitação, especifique a largura de banda permitida para transferência de dados de backup durante as **Horas úteis** e as **Horas não úteis**.
   
    Os valores de largura de banda começam em 512 quilobits por segundo (Kbps) e podem ir até 1.023 megabytes por segundo (Mbps). Você também pode indicar o início e o término para **Horas úteis** e quais dias da semana são considerados dias úteis. Horas fora das horas úteis designadas são consideradas horas não úteis.
4. Clique em **OK**.

### Fazer backup agora
1. No Agente de backup, clique em **Fazer Backup Agora** para concluir a propagação inicial pela rede.
   
    ![Fazer backup do Windows Server agora](./media/backup-configure-vault-classic/backup-now.png)
2. Na página Confirmação, examine as configurações que o Assistente Fazer Backup Agora usará para fazer backup do computador. Em seguida, clique em **Fazer Backup**.
3. Clique em **Fechar** para fechar o assistente. Se você fizer isso antes da conclusão do processo de backup, o assistente continuará a ser executado em segundo plano.

Depois que o backup inicial for concluído, o status **Trabalho concluído** aparecerá no Console de backup.

![IR completo](./media/backup-configure-vault-classic/ircomplete.png)

## Próximas etapas
* Inscreva-se para obter uma [conta do Azure gratuita](https://azure.microsoft.com/free/).

Para saber mais sobre como fazer backup de VMs ou de outras cargas de trabalho, confira:

* [Fazer backup de VMs IaaS](backup-azure-vms-prepare.md)
* [Backup de cargas de trabalho no Azure com o Servidor de Backup do Microsoft Azure](backup-azure-microsoft-azure-backup.md)
* [Fazer backup de cargas de trabalho no Azure com o DPM](backup-azure-dpm-introduction.md)

<!---HONumber=AcomDC_0810_2016-->