<properties
	pageTitle="Administrando um Serviço móvel na linha de comando | Microsoft Azure"
	description="Aprenda a criar, implantar e gerenciar seu serviço móvel do Azure usando as ferramentas de linha de comando."
	services="mobile-services"
	documentationCenter="Mobile"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="NA"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="11/02/2015"
	ms.author="glenga"/>

# Automatizar os serviços móveis com ferramentas de linha de comando

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


##Visão geral

Este tópico mostra como usar as ferramentas de linha de comando do Azure para automatizar a criação e o gerenciamento dos Serviços Móveis do Azure. Este tópico mostra como instalar e usar as ferramentas de linha de comando e como usá-las para executar Serviços Móveis essenciais.

Quando combinadas em um único script ou arquivo em lotes, estes comandos individuais automatizam os processos de criação, verificação e exclusão de um serviço móvel.

Este tópico aborda uma seleção de tarefas para administração comuns compatíveis com as ferramentas de linha de comando do Azure. Para obter mais informações, consulte [Documentação das ferramentas de linha de comando do Azure][reference-docs].

##Instalar as ferramentas de linha de comando do Azure

A lista a seguir contém informações para instalar as ferramentas de linha de comando, dependendo do seu sistema operacional:

* **Windows**: baixe o [Instalador de Ferramentas de Linha de Comando do Azure][windows-installer]. Abra o arquivo .msi baixado e conclua as etapas de instalação de acordo com o que for solicitado.

* **Mac**: baixe o [Instalador do SDK do Azure][mac-installer]. Abra o arquivo .pkg baixado e conclua as etapas de instalação quando for solicitado.

* **Linux**: instale a versão mais recente do [Node.js][nodejs-org] (confira [Instalar o Node.js por meio do Gerenciador de Pacotes][install-node-linux]) e execute o seguinte comando:

	npm install azure cli - g

Para testar a instalação, digite `azure` no prompt de comando. Quando a instalação tiver sido executada com êxito, você verá uma lista de todos os comandos `azure` disponíveis.

##Como baixar e importar as configurações de publicação

Para iniciar, você deve primeiro baixar e importar suas configurações de publicação. Em seguida, você poderá usar as ferramentas para criar e gerenciar os Serviços do Azure. Para baixar as configurações de publicação, use o comando `account download`:

	azure account download

Isso abre o navegador padrão e solicita que você se conecte no Portal clássico do Azure. Depois de entrar, seu arquivo `.publishsettings` será baixado. Observe o local onde este arquivo foi salvo.

Em seguida, importe o arquivo `.publishsettings` executando o seguinte comando, substituindo `<path-to-settings-file>` pelo caminho do arquivo `.publishsettings`:

	azure account import <path-to-settings-file>

Você pode remover todas as informações armazenadas pelo comando <code>import</code> usando o comando <code>account clear</code>:

	azure account clear

Para ver uma lista de opções de comandos `account`, use a opção `-help`:

	azure account -help

Depois de importar as configurações de publicação, exclua o arquivo `.publishsettings` por motivos de segurança. Para obter mais informações, consulte [Como instalar as Ferramentas de Linha de Comando do Azure para Mac e Linux]. Agora você está pronto para começar a criar e gerenciar os Serviços Móveis do Azure a partir da linha de comando ou em arquivos em lotes.

##Como criar um serviço móvel

Você pode usar as ferramentas de linha de comando para criar uma nova instância de serviço móvel. Ao criar o serviço móvel, você também criará uma instância do Banco de Dados SQL em um novo servidor.

O comando abaixo cria uma nova instância do serviço móvel em sua assinatura, onde `<service-name>` é o nome do novo serviço móvel, `<server-admin>` é o nome de logon do novo servidor e `<server-password>` é a senha para o novo logon:

	azure mobile create <service-name> <server-admin> <server-password>

O comando `mobile create` falha quando existe o serviço móvel especificado. Em seus scripts de automação, você deve tentar excluir um serviço móvel antes de tentar recriá-lo.

##Como listar serviços móveis existentes em uma assinatura

> [AZURE.NOTE]Comandos na CLI relacionadas a "lista" e "script" funcionam apenas com o back-end do JavaScript.

O comando a seguir retorna uma lista de todos os serviços móveis em uma assinatura do Azure:

	azure mobile list

Este comando também mostra o estado atual e a URL de cada serviço móvel.

##Como excluir um serviço móvel existente

Você pode usar as ferramentas de linha de comando para excluir um serviço móvel existente, juntamente com os respectivos Banco de Dados SQL e servidor. O comando abaixo exclui o serviço móvel, onde `<service-name>` é o nome do serviço móvel a ser excluído:

	azure mobile delete <service-name> -a -q

Com a inclusão dos parâmetros `-a` e `-q`, esse comando também exclui o Banco de Dados SQL e o servidor usados pelo serviço móvel, sem exibir um prompt.

> [AZURE.NOTE]Se você não especificar o parâmetro <code>-q</code> juntamente com <code>-a</code> ou <code>-d</code>, a execução é interrompida e você será solicitado a selecionar opções de exclusão para seu Banco de Dados SQL. Somente utilize o parâmetro <code>-a</code> quando nenhum outro serviço usar o banco de dados ou servidor; caso contrário, use o parâmetro <code>-d</code> para excluir apenas os dados pertencentes ao serviço móvel que está sendo excluído.

##Como criar uma tabela no serviço móvel

O comando abaixo cria uma tabela no serviço móvel especificado, onde `<service-name>` é o nome do serviço móvel e `<table-name>` é o nome da tabela a ser criada:

	azure mobile table create <service-name> <table-name>

Isso cria uma nova tabela com as permissões padrão,`application`, para as operações de tabela:`insert`, `read`, `update` e `delete` .

O comando abaixo cria uma nova tabela com permissão para `read` pública, mas com a permissão para `delete` concedida apenas a administradores:

	azure mobile table create <service-name> <table-name> -p read=public,delete=admin

A tabela a seguir mostra o valor da permissão de script em comparação ao valor da permissão no [Portal clássico do Azure].

|Valor do script|Valor do Portal| |========|========| |`public`| Todos| |`application`(padrão) |Qualquer pessoa com a chave do aplicativo |`user`| Somente usuários autenticados| |`admin`|Somente scripts e admins|

O comando `mobile table create` falha quando a tabela especifica já existe. Em seus scripts de automação, você deve tentar excluir uma tabela antes de tentar recriá-la.

##Como listar tabelas existentes em um serviço móvel

O comando abaixo retorna uma lista de todas as tabelas em um serviço móvel, onde `<service-name>` é o nome do serviço móvel:

	azure mobile table list <service-name>

Este comando também mostra o número de índices em cada tabela e o número de linhas de dados existentes atualmente na tabela.

##Como excluir uma tabela existente do serviço móvel

O comando abaixo exclui uma tabela do serviço móvel, onde `<service-name>` é o nome do serviço móvel e `<table-name>` é o nome da tabela a ser excluída:

	azure mobile table delete <service-name> <table-name> -q

Em scripts de automação, use o parâmetro `-q` para excluir a tabela sem exibir um prompt de confirmação que bloqueia a execução.

##Como registrar um script para uma operação de tabela

O comando a seguir carrega e registra uma função para uma operação em uma tabela, onde `<service-name>` é o nome do serviço móvel, `<table-name>` é o nome da tabela e `<operation>` é a operação de tabela, que poderá ser `read`, `insert`, `update` ou `delete`:

	azure mobile script upload <service-name> table/<table-name>.<operation>.js

Observe que esta operação carregará um arquivo JavaScript (.js) a partir do computador local. O nome do arquivo deve ser composto dos nomes da tabela e da operação e deve estar localizado na subpasta `table` relativa ao local onde o comando é executado. Por exemplo, a seguinte operação carrega e registra um novo script `insert`, que pertence à tabela `TodoItems`:

	azure mobile script upload todolist table/todoitems.insert.js

A declaração de função no arquivo de script também deve coincidir com a operação de tabela registrada. Isto significa que para um script `insert`, o script carregado contém uma função com a seguinte assinatura:

	function insert(item, user, request) {
	    ...
	}

Para obter mais informações sobre como registrar scripts, consulte [Referência para script de servidor dos Serviços Móveis]

<!-- Anchors. -->
[Download and install the command-line tools]: #install
[Download and import publish settings]: #import
[Create a new mobile service]: #create-service
[Get the master key]: #get-master-key
[Create a new table]: #create-table
[Register a new table script]: #register-script
[Delete an existing table]: #delete-table
[Delete an existing mobile service]: #delete-service
[Test the mobile service]: #test-service
[List mobile services]: #list-services
[List tables]: #list-tables
[Next steps]: #next-steps

<!-- Images. -->











<!-- URLs. -->
[Referência para script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/p?LinkId=262293

[Portal clássico do Azure]: https://manage.windowsazure.com/
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager

[mac-installer]: http://go.microsoft.com/fwlink/p?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/p?LinkID=275464
[reference-docs]: http://azure.microsoft.com/documentation/articles/virtual-machines-command-line-tools/#Commands_to_manage_mobile_services
[Como instalar as Ferramentas de Linha de Comando do Azure para Mac e Linux]: http://go.microsoft.com/fwlink/p/?LinkId=275795

<!---HONumber=AcomDC_1203_2015-->