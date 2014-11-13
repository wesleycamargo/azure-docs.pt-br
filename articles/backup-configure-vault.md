<properties urlDisplayName="Configure a Backup Vault" pageTitle="Configurar os Servi&ccedil;os de Recupera&ccedil;&atilde;o do Azure para fazer backup r&aacute;pido e f&aacute;cil do Windows Server" metaKeywords="disaster recovery" description="Use este tutorial para aprender a usar o servi&ccedil;o de backup na oferta de nuvem do Microsoft Azure para fazer o backup do Windows Server na nuvem." metaCanonical="" services="recovery-services" documentationCenter="" title="Configurar o Backup do Azure para fazer backup r&aacute;pido e f&aacute;cil do Windows Server" authors="raynew" solutions="" manager="johndaw" editor="tysonn" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# <span id="configure-a-backup-vault-tutorial"></span></a>Configurar o Backup do Azure para fazer backup rápido e fácil do Windows Server

<div class="dev-callout"> 
<strong>Observa&ccedil;&atilde;o</strong>
 
<p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure. Este tutorial o levar&aacute; atrav&eacute;s da ativa&ccedil;&atilde;o do recurso de Backup do Azure. Anteriormente, voc&ecirc; precisaria criar ou adquirir um certificado X.509 v3, a fim de registrar seu servidor de backup. Os certificados ainda n&atilde;o suportados, mas agora para facilitar o registro do cofre do Azure a um servidor, voc&ecirc; pode gerar uma credencial de cofre direto da p&aacute;gina de In&iacute;cio R&aacute;pido. </p>
<ul> 
<li>Se voc&ecirc; n&atilde;o tiver uma conta, poder&aacute; criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="/pt-br/pricing/free-trial/">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</li> 
 

</ul>
 

</div>

Para fazer o backup de arquivos e dados do seu Windows Server para Azure, você deve criar um cofre de backup na região geográfica em que deseja armazenar os dados. Este tutorial vai orientá-lo através da criação de cofre que será usado para armazenar backups, o download de um certificado de cofre, a instalação de um agente de backup e uma visão geral das tarefas de gerenciamento de backup disponíveis por meio do portal de gerenciamento.

## <span id="create"></span></a>Criar um cofre de backup

1.  Entre no [Portal de Gerenciamento][Portal de Gerenciamento].

2.  Clique em **Novo**, em seguida, aponte para **Serviços de dados**, aponte para **Recuperar Serviços**, em seguida clique em **Cofre de Backup** e em **Criação Rápida**.

3.  Em **Nome**, digite um nome amigável para identificar o cofre de backup.

4.  Em **Região**, selecione a região geográfica para o cofre de backup.
     ![Novo cofre de backup][Novo cofre de backup]

5.  Clique em **Criar cofre**.

    Pode levar algum tempo para que o cofre de backup seja criado. Para verificar o status, você pode monitorar as notificações na parte inferior do portal. Após ter sido criado o cofre backup, uma mensagem informará que o cofre foi criado com êxito e ele será listado nos recursos para Recuperação como **Ativo**.
    ![criação de cofre backup][criação de cofre backup]

6.  Se você tem várias assinaturas associadas com sua conta organizacional, escolha a conta correta para associar ao cofre de backup.

## <span id="upload"></span></a>Baixar uma credencial do cofre

As credenciais do sofre substituem os certificados como forma de registrar o seu serviço do Azure com o seu servidor. Você ainda pode usar certificados, no entanto, as credenciais de cofre são fáceis de usar pois você usa o portal do Azure para gerar e baixar as credenciais de cofre.

1.  Entre no [Portal de Gerenciamento][Portal de Gerenciamento].

2.  Clique em **Serviços de Recuperação**, em seguida, selecione o cofre de backup que você quer registrar com um servidor. A página de Início Rápido para esse cofre de backup aparece.

3.  Na página de Início Rápido, clique em **Baixar as credenciais de cofre** para solicitar que o porta gere e baixe as credenciais de cofre que você usará para registrar seu servidor com o cofre de backup.

4.  O portal gerará uma credencial de cofre usando uma combinação do nome do cofre e a data atual. Clique em **Salvar** para baixar as credenciais do cofre para a pasta de donwloads da conta local, ou selecione **Salvar Como** a partir do menu **Salvar**, para especificar um local para as credenciais do cofre. Você não pode editar as credenciais do cofre, então não existe razão para clicar em Abrir. Uma vez que as credenciais foram baixadas, você será solicitado a Abrir a pasta. Clique em **x** para fechar este menu.

## <span id="download"></span></a>Baixe e instale um agente de backup

1.  No [Portal de gerenciamento][Portal de Gerenciamento].

2.  Clique em **Serviços de Recuperação**, em seguida, clique no cofre de backup para exibir a página de Início Rápido.

3.  Na página de Início Rápido, selecione o tipo de agente que você quer baixar. Você pode escolher **Baixar o Agente de Backup do Azure**, **Windows Server e System Center Data Protection Manager**, ou **Windows Server Essentials**. Para obter mais informações, consulte:

    -   [Instalar o Azure Backup Agent para Windows Server 2012 e System Center 2012 SP1 - Data Protection Manager][Instalar o Azure Backup Agent para Windows Server 2012 e System Center 2012 SP1 - Data Protection Manager]
    -   [Instalar o Azure Backup Agent para o Windows Server 2012 Essentials][Instalar o Azure Backup Agent para o Windows Server 2012 Essentials]

Depois que o agente está instalado, você pode usar a interface de gerenciamento local apropriada (como o snap-in Microsoft Management Console, o System Center Data Protection Manager Console ou o painel do Windows Server Essentials) para configurar a política de backup para o servidor.

## <span id="manage"></span></a>Gerenciar servidores e cofres de backup

1.  Entre no [Portal de Gerenciamento][Portal de Gerenciamento].

2.  Clique em **Serviços de Recuperação**, em seguida, clique no nome do cofre de backup para exibir a página de Início Rápido.

3.  Clique em **Painel** para ver a visão geral do uso para o servidor. Na parte inferior do Painel, você pode executar as seguintes tarefas:

    -   **Gerenciar certificados**. Se um certificado foi usado para registrar o servidor, então use isso para atualizar o certificado. Se estiver usando as credenciais do cofre, não use **Gerenciar certificados**.
    -   **Excluir**. Exclui o cofre de backup atual. Se não estiver sendo usado um cofre de backup, você poderá excluir para liberar mais espaço de armazenamento. **Excluir** é ativado somente depois que todos os servidores registrados foram excluídos do cofre.
    -   **Credenciais do cofre**. Use este item de menu de Visão rápida para configurar suas credenciais de cofre.

4.  Clique em **Itens Protegidos** para ver os itens que foram colocados em backup dos servidores. Esta lista é somente para fins informativos.
    ![Itens protegidos][Itens protegidos]

5.  Clique em **Servidores** para exibir os nomes dos servidores que devem ser registrados neste cofre. A partir daqui, você pode executar as seguintes tarefas:

    -   **Permitir novo registro**. Quando esta opção estiver selecionada para um servidor, você pode usar o Assistente de Registro no agente para registrar o servidor com o cofre de backup uma segunda vez. Talvez você precise registrar novamente devido a um erro no certificado ou se um servidor tiver que ser refeito. O novo registro é permitido somente uma vez por nome do servidor.
    -   **Excluir**. Exclui um servidor do cofre de backup. Todos os dados armazenados associados ao servidor serão excluídos imediatamente.

        ![Servidor excluído][Servidor excluído]

## <span id="next"></span></a>Próximas etapas

-   Para saber mais sobre o Backup do Azure, consulte [Visão geral do backup do Azure][Visão geral do backup do Azure].

-   Visite o [Fórum de backup do Azure][Fórum de backup do Azure].

  [Portal de Gerenciamento]: https://manage.windowsazure.com
  [Novo cofre de backup]: http://i.imgur.com/506c7ch.png
  [criação de cofre backup]: http://i.imgur.com/grtLcKM.png
  [Instalar o Azure Backup Agent para Windows Server 2012 e System Center 2012 SP1 - Data Protection Manager]: http://technet.microsoft.com/pt-br/library/hh831761.aspx#BKMK_installagent
  [Instalar o Azure Backup Agent para o Windows Server 2012 Essentials]: http://technet.microsoft.com/pt-br/library/jj884318.aspx
  [Itens protegidos]: ./media/backup-configure-vault/RS_protecteditems.png
  [Servidor excluído]: ./media/backup-configure-vault/RS_deletedserver.png
  [Visão geral do backup do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=222425
  [Fórum de backup do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=290933
