<properties linkid="manage-services-recovery-configure-backup-vault" urlDisplayName="Configurar um cofre de backup" pageTitle="Configurar Serviços de Recuperação do Azure para fazer o backup da recuperação de desastres do Windows Server de forma rápida e fácil" metaKeywords="" description="Use este tutorial para aprender a usar o serviço de Backup na nuvem do Azure da Microsoft oferecendo backup do Windows Server para a nuvem." metaCanonical="" services="recovery-services" documentationCenter="" title="Configurar o backup do Azure para fazer o backup do Windows Server de forma rápida e fácil" authors=""  solutions="" writer="starra" manager="cynthn" editor="tysonn"  />



<h1><a id="configure-a-backup-vault-tutorial"></a>Configurar o Backup do Azure para fazer backup rápido e fácil do Windows Server</h1>
<div class="dev-callout"> 
<strong>Observação</strong>
 
<p>Para concluir este tutorial, você precisa de uma conta do Azure com o recurso Backup do Azure habilitado.</p>
<ul> 
<li>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos Para obter detalhes, consulte <a href="/pt-br/pricing/free-trial/">Avaliação gratuita do Azure</a>.</li> 
 
<li>Se você tiver uma conta existente, mas precisar habilitar a visualização Backup do Azure, consulte <a href="/pt-br/develop/net/tutorials/create-a-windows-azure-account/#enable" target="_blank">Habilitar os recursos de visualização do Azure</a>.</li>
</ul>
 
<p>Após solicitar a entrada no programa de visualização de Backup, aguarde para que seu status se torne ativo. Estamos aprovando automaticamente todos os clientes para que isso não leve muito tempo para ocorrer.</p> 
</div>
  

Para fazer o backup de arquivos e dados do seu Windows Server para Azure, você deve criar um cofre de backup na região geográfica em que deseja armazenar os dados. Este tutorial vai orientá-lo através da criação de cofre que será usado para armazenar backups, o carregamento de um certificado no cofre, a instalação de um agente de backup e uma visão geral das tarefas de gerenciamento de backup disponíveis por meio do portal de gerenciamento.

<div class="dev-callout"> 
<strong>Antes de começar</strong> 
<p>Para concluir este tutorial, você deve ter um certificado x. 509 v3 para registrar seus servidores com cofres de backup.  O certificado deve ter um comprimento de chave de pelo menos 2.048 bits e residir no repositório de certificados do seu computador local. Quando o certificado é instalado no seu servidor, ele deve conter a chave privada do certificado. Para carregar o certificado para o Portal de Gerenciamento do Azure, você deve exportar a chave pública como um arquivo de formato .cer.</p> 

<p>Você pode usar:</p> 
<ul>
<li>Seu próprio certificado autoassinado criado usando a ferramenta makecert, OU</li> 

<li>Qualquer certificado SSL válido emitido por uma autoridade de certificação (CA) que é confiável pela Microsoft e cujos certificados raiz são distribuídos via Microsoft Root Certificate Program. Para obter mais informações sobre este programa, consulte <a href="http://go.microsoft.com/fwlink/p/?LinkId=294666">Membros do Windows Root Certificate Program</a>.</li>
</ul> 

<p>Outros atributos que você precisa para garantir que os certificados são:</p> 

<ul>
<li>Ter um EKU ClientAuthentication válido</li>

<li>É válido no momento com um período de validade que não exceda 3 anos</li>  
</ul>

<p>Para usar seu próprio certificado autoassinado, siga estas etapas: </p>
<ol>
<li>Baixe a <a href="http://go.microsoft.com/fwlink/p/?LinkID=294662">ferramenta Criação de certificado (MakeCert.exe)</a>.</li>  


<li>Abra o prompt de comando (cmd.exe) com privilégios de administrador e execute o seguinte comando, substituindo <i>CertificateName</i> com o nome do seu certificado e especificando a data de vencimento real do seu certificado após - e: 
<code>
makecert.exe -r -pe -n CN=CertificateName -ss my -sr localmachine -eku 1.3.6.1.5.5.7.3.2 -len 2048 -e 01/01/2016 CertificateName.cer</code></li>
</ol>
<p>
Se você irá registrar um servidor diferente daquele que você usou para criar o certificado, você precisa exportar o arquivo. pfx (que contém a chave particular), copiar para o outro servidor e importar para o armazenamento de certificados pessoais do servidor. 
</p>
<p>
Para obter informações detalhadas sobre o processo de carregamento do certificado do cofre e obter mais informações sobre como exportar e importar os arquivos. pfx, consulte <a href="http://go.microsoft.com/fwlink/p/?LinkID=294662">Gerenciar certificados de cofre</a>.</p> 
</div>

<h2><a id="create"></a>Criar um cofre de backup</h2>

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).

	[WACOM.INCLUDE [isenção de responsabilidade](../includes/disclaimer.md)]

2. Clique em **Serviços de Recuperação**, em seguida, clique em **Criar Novo**, aponte para **Cofre de Backup ** e clique em **Criação Rápida**.

	![Novo cofre de backup][new-backup-vault]

3. Em **Nome**, digite um nome amigável para identificar o cofre de backup.

4. Em **Região**, selecione a região geográfica para o cofre de backup.  

5. Em **Assinatura**, digite a assinatura do Azure que você deseja usar com o cofre de backup. 


6. Clique em **Criar cofre de backup**.

	Pode levar algum tempo para que o cofre de backup seja criado. Para verificar o status, você pode monitorar as notificações na parte inferior do portal. Após ter sido criado o cofre backup, uma mensagem informará que o cofre foi criado com êxito e ele será listado nos recursos para Serviços de Recuperação como **Online**. 

	![Criação de um cofre de backup][backup-vault-create]

<h2><a id="upload"></a>Carregar um certificado</h2>
1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).

2. Clique em **Serviços de Recuperação**, clique no nome do cofre de backup que será identificado pelo certificado e clique em **Gerenciar certificado**.
	
	![Gerenciar certificado][manage-cert]

3. Na caixa de diálogo **Gerenciar Certificado**, clique em Procurar seu computador para localizar o arquivo .cer a ser usado com este cofre de backup.
<h2><a id="download"></a>Baixe e instale um agente de backup</h2>
1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).

2. Clique em **Serviços de Recuperação**, em seguida, clique no nome do cofre de backup para exibir o painel de controle do cofre.

3. Clique em **Instalar Agente** 
	
	![Instalar agente][install-agent]
4. Você verá uma caixa de diálogo onde é possível escolher qual agente fazer o download:
	* Agente do Windows Server 2012 e o System Center 2012 SP1 - Data Protection Manager
	* Agente do Windows Server 2012 Essentials
5. Selecione o agente apropriado. Você será redirecionado para Centro de Download da Microsoft para baixar o software do agente. Para obter mais informações, consulte:

	* [Instalar o Azure Backup Agent para Windows Server 2012 e System Center 2012 SP1 - Data Protection Manager](http://technet.microsoft.com/pt-br/library/hh831761.aspx#BKMK_installagent)
	* [Instalar o Azure Backup Agent para o Windows Server 2012 Essentials](http://technet.microsoft.com/pt-br/library/jj884318.aspx)

Depois que o agente está instalado, você pode usar a interface de gerenciamento local apropriada (como o snap-in Microsoft Management Console, o System Center Data Protection Manager Console ou o painel do Windows Server Essentials) para configurar a política de backup para o servidor.  

<h2><a id="manage"></a>Gerenciar servidores e cofres de backup</h2>
1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).

2. Clique em **Serviços de Recuperação**, em seguida, clique no nome do cofre de backup para exibir o painel de controle do cofre. A partir daqui, você pode executar as seguintes tarefas:
	* **Gerenciar certificado**. Usado para atualizar o certificado carregado anteriormente.
	* **Excluir**. Exclui o cofre de backup atual. Se não estiver sendo usado um cofre de backup, você poderá excluir para liberar mais espaço de armazenamento. **Excluir** é ativado somente depois que todos os servidores registrados foram excluídos do cofre. 

3. Clique em **Itens Protegidos** para ver os itens que foram colocados em backup dos servidores. Esta lista é somente para fins informativos.  
![Itens Protegidos][protected-itmes]

4. Clique em **Servidores** para exibir os nomes dos servidores que devem ser registrados neste cofre. A partir daqui, você pode executar as seguintes tarefas:
	* **Permitir Registrar Novamente** Quando esta opção estiver selecionada para um servidor, você pode usar o Assistente de Registro no agente para registrar o servidor com o cofre de backup uma segunda vez. Talvez você precise registrar novamente devido a um erro no certificado ou se um servidor tiver que ser refeito. O novo registro é permitido somente uma vez por nome do servidor.
	* **Excluir**. Exclui um servidor do cofre de backup. Todos os dados armazenados associados ao servidor serão excluídos imediatamente.

		![Servidor excluído][deleted-server]

<h2><a id="next"></a>Próximas etapas</h2>

- Para saber mais sobre o Backup do Azure, consulte [Visão geral do backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=222425) 

- Visite o [Fórum de backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

[new-backup-vault]: ./media/backup-configure-vault/RS_howtobackup1.png
[backup-vault-create]: ./media/backup-configure-vault/RS_howtobackup2.png
[manage-cert]: ./media/backup-configure-vault/RS_howtoupload1.png
[install-agent]: ./media/backup-configure-vault/RS_howtodownload1.png
[deleted-server]: ./media/backup-configure-vault/RS_deletedserver.png
[protected-itmes]: ./media/backup-configure-vault/RS_protecteditems.png


