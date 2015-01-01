<properties urlDisplayName="Command Line Administration" pageTitle="Administrando um serviço móvel na linha de comando - tutorial do Azure" metaKeywords="" description="Learn how to create, deploy, and manage your Azure Mobile Service using command-line tools." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Automate mobile services with command-line tools" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Automatizar os serviços móveis com ferramentas de linha de comando 

Este tópico mostra como usar as ferramentas de linha de comando do Azure para automatizar a criação e o gerenciamento dos Serviços Móveis do Azure. Este tópico mostra como instalar e usar as ferramentas de linha de comando e como usá-las para executar as seguintes tarefas dos Serviços Móveis:

-	[Criar um novo serviço móvel] 
-	[Criar uma nova tabela]
-   [Registrar um script para uma operação de tabela][Register a new table script]
-   [Listar tabelas]
- 	[Excluir uma tabela existente]
-	[Listar serviços móveis]
-   [Excluir um serviço móvel existente]
 
Quando combinadas em um único script ou arquivo em lotes, estes comandos individuais automatizam os processos de criação, verificação e exclusão de um serviço móvel. 

Para usar as ferramentas de linha de comando do Azure para gerenciar Serviços Móveis, você precisará de uma conta do Azure que tenha o recurso Serviços Móveis habilitado.

+ Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais informações, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/" target="_blank">Avaliação gratuita do Azure</a>.

+ Se você tiver uma conta existente, mas precisar habilitar a visualização dos Serviços Móveis do Azure, consulte <a href="http://azure.microsoft.com/pt-br/documentation/articles/php-create-account/#enable" target="_blank">Habilitar os recursos de visualização do Azure</a>.

Este tópico aborda uma seleção de tarefas para administração comuns compatíveis com as ferramentas de linha de comando do Azure. Para obter mais informações, consulte [Documentação das ferramentas de linha de comando do Azure][reference-docs].

<!--+  You must download and install the Azure command-line tools to your local machine. To do this, follow the instructions in the first section of this topic. 

+ (Optional) To be able to execute HTTP requests directly from the command-line, you must use cURL or an equivalent tool. cURL runs on a variety of platforms. Locate and install cURL for your specific platform from the <a href=http://go.microsoft.com/fwlink/p/?LinkId=275676 target="_blank">cURL download  page</a>.-->

<h2><a name="install"></a>Instalar ferramentas da linha de comando do Azure</h2>

A lista a seguir contém informações para instalar as ferramentas de linha de comando, dependendo do seu sistema operacional:

* **Windows**: Baixe o [Instalador de ferramentas da linha de comando do Azure][windows-installer].Abra o arquivo .msi baixado e conclua as etapas de instalação de acordo com o que for solicitado.

* **Mac**: Baixe o [Instalador do SDK do Azure][mac-installer].Abra o arquivo .pkg baixado e conclua as etapas de instalação quando for solicitado.

* **Linux**: Instale a versão mais recente do [Node.js][nodejs-org] (consulte [Instalar o Node.js por meio do Gerenciador de Pacotes][install-node-linux]), em seguida, execute o seguinte comando:

		npm install azure-cli -g

Para testar a instalação, digite 'azure' no prompt de comando. Quando a instalação for concluída com êxito, você verá uma lista de todos os comandos `azure` disponíveis.
<h2><a name="import-account"></a>Como baixar e importar as configurações de publicação</h2>

Para iniciar, você deve primeiro baixar e importar suas configurações de publicação. Em seguida, você poderá usar as ferramentas para criar e gerenciar os Serviços do Azure. Para baixar as configurações de sua publicação, use o comando 'download de conta':

		azure account download

Isto abrirá o navegador padrão e solicitará que você entre no Portal de Gerenciamento. Depois de entrar, seu arquivo `.publishsettings` é baixado. Observe o local onde este arquivo foi salvo.

Em seguida, importe o arquivo `.publishsettings` executando o seguinte comando, substituindo `<path-to-settings-file> pelo caminho de seu arquivo `.publishsettings`:

		azure account import <path-to-settings-file>

É possível remover todas as informações armazenadas pelo comando <code>import</code> usando o comando de <code>exclusão de conta</code> :

		azure account clear

Para ver uma lista de opções de comandos`account` use a opção `-help`:

		azure account -help

Depois de importar suas configurações de publicação, você deverá excluir o arquivo `.publishsettings` por motivos de segurança.   Para obter mais informações, consulte [Como instalar as ferramentas de linha de comando do Azure para Mac e Linux].Agora você está pronto para começar a criar e gerenciar os Serviços Móveis do Azure por meio da linha de comando ou em arquivos em lotes.  

<h2><a name="create-service"></a>Como criar um Serviço Móvel</h2>

Você pode usar as ferramentas de linha de comando para criar uma nova instância de serviço móvel. Ao criar o serviço móvel, você também criará uma instância do Banco de Dados SQL em um novo servidor. 

O comando a seguir cria uma nova instância do serviço móvel em sua assinatura, onde `<service-name>` é o nome do novo serviço móvel, `<server-admin>` é o nome de logon do novo servidor e `<server-password>` é a senha para o novo logon:

		azure mobile create <service-name> <server-admin> <server-password>

Se já houver o serviço móvel especificado, o comando `mobile create` falhará. Em seus scripts de automação, você deve tentar excluir um serviço móvel antes de tentar recriá-lo.

<h2><a name="list-services"></a>Como listar serviços móveis existentes em uma assinatura</h2>

O comando a seguir retorna uma lista de todos os serviços móveis em uma assinatura do Azure:

		azure mobile list

Este comando também mostra o estado atual e a URL de cada serviço móvel.

<h2><a name="delete-service"></a>Como excluir um serviço móvel existente</h2>

Você pode usar as ferramentas de linha de comando para excluir um serviço móvel existente, juntamente com os respectivos Banco de Dados SQL e servidor. O comando a seguir exclui o serviço móvel, onde `<service-name>` é o nome do serviço móvel a ser excluído:

		azure mobile delete <service-name> -a -q

Com a inclusão dos parâmetros`-a` e `-q`, este comando também exclui o Banco de Dados SQL e o servidor usados pelo serviço móvel, sem exibir um prompt.

<div class="dev-callout"><strong>Observação</strong> 
   <p>Se você não especificar o parâmetro <code>-q</code> com <code>-a</code> ou <code>-d</code>, a execução será pausada e você será solicitado a selecionar opções de exclusão para o seu banco de dados SQL. Use apenas o parâmetro <code>-a</code> quando nenhum outro serviço utilizar o banco de dados ou servidor; caso contrário, use o parâmetro <code>-d</code> para apagar apenas os dados que pertencem ao serviço móvel que está sendo excluído.</p>
</div>

<h2><a name="create-table"></a>Como criar uma tabela no serviço móvel</h2>

O comando a seguir cria uma tabela no serviço móvel especificado, onde `<service-name>` é o nome do serviço móvel e `<table-name>` é o nome da tabela a ser criada:

		azure mobile table create <service-name> <table-name>

Isso cria uma nova tabela com as permissões padrão,`application`, para as operações de tabela: `insert`, `read`, `update`, e`delete`. 

O comando a seguir cria uma nova tabela com permissão `read` pública, mas com a permissão`delete` concedida apenas a administradores:

		azure mobile table create <service-name> <table-name> -p read=public,delete=admin

A tabela a seguir mostra o valor da permissão de script em comparação com o valor da permissão no [Portal de Gerenciamento do Azure].

<table border="1" width="100%"><tr><th>Valor do script</th><th>Valor do portal de gerenciamento</th></tr>
<tr><td><code>public</code></td><td>Todos</td></tr>
<tr><td><code>application</code> (padrão)</td><td>Qualquer pessoa com a chave de aplicativo</td></tr>
<tr><td><code>user</code></td><td>Somente usuários autenticados</td></tr>
<tr><td><code>admin	</code></td><td>Somente scripts e administradores</td></tr></table>

O comando `mobile table create` falhará se a tabela especificada já existir. Em seus scripts de automação, você deve tentar excluir uma tabela antes de tentar recriá-la.

<h2><a name="list-tables"></a>Como listar tabelas existentes em um serviço móvel</h2>

O comando a seguir retorna uma lista de todas as tabelas em um serviço móvel, onde `<service-name>` é o nome do serviço móvel:

		azure mobile table list <service-name>

Este comando também mostra o número de índices em cada tabela e o número de linhas de dados existentes atualmente na tabela.

<h2><a name="delete-table"></a>Como excluir uma tabela existente do serviço móvel</h2>

O comando a seguir exclui uma tabela do serviço móvel, em que `<service-name>` é o nome do serviço móvel e`<table-name>` é o nome da tabela a ser excluída:

		azure mobile table delete <service-name> <table-name> -q

Em scripts de automação, use o parâmetro `-q` para excluir a tabela sem exibir um prompt de confirmação que bloqueará a execução.

<h2><a name="register-script"></a>Como registrar um script em uma operação de tabela</h2>

O comando a seguir carrega e registra uma função em uma operação de tabela, em que`<service-name>` é o nome do serviço móvel, `<table-name>` é o nome da tabela e `<operation>` é a operação da tabela, que pode ser `read`, `insert`, `update` ou `delete`:

		azure mobile script upload <service-name> table/<table-name>.<operation>.js

Observe que esta operação carregará um arquivo JavaScript (.js) do computador local. O nome do arquivo deve ser composto dos nomes da tabela e da operação e deve estar localizado na subpasta`table` relativa ao local onde o comando é executado. Por exemplo, a seguinte operação carrega e registra um novo script `insert`, que pertence à tabela `TodoItems`:

		azure mobile script upload todolist table/todoitems.insert.js

A declaração de função no arquivo de script também deve coincidir com a operação de tabela registrada. Isso significa que para um script `insert`, o script carregado contém uma função com a seguinte assinatura:

		function insert(item, user, request) {
		    ...
		} 

Para obter mais informações sobre como registrar scripts, consulte[Referência para script de servidor dos Serviços Móveis].

<!--<h2><a name="test-service"></a>Test the new mobile service</h2>

When you are automating the creation of your mobile service, you can optionally use cURL or another command-line request generator to 

## <a name="nextsteps"> </a>Next Steps
Next steps here....
-->
<!-- Anchors. -->
[Baixar e instalar as ferramentas de linha de comando]: #install
[Baixar e importar configurações de publicação]: #import
[Criar um novo serviço móvel]: #create-service
[Obter a chave mestra]: #get-master-key
[Criar uma nova tabela]: #create-table
[Registrar novo script de tabela]: #register-script
[Excluir uma tabela existente]: #delete-table
[Excluir um serviço móvel existente]: #delete-service
[Testar o serviço móvel]: #test-service
[Listar serviços móveis]: #list-services
[Listar tabelas]: #list-tables
[Próximas etapas]: #next-steps

<!-- Images. -->











<!-- URLs. -->
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/p?LinkId=262293

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager

[mac-installer]: http://go.microsoft.com/fwlink/p?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/p?LinkID=275464
[reference-docs]: /pt-br/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services
[Como instalar ferramentas da linha de comando do Azure para Mac e Linux]: http://go.microsoft.com/fwlink/p/?LinkId=275795


<!--HONumber=35_1-->
