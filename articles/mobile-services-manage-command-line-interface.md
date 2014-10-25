<properties linkid="develop-mobile-tutorials-command-line-administration" urlDisplayName="Command Line Administration" pageTitle="Administering a Mobile Service at the command line - Azure tutorial" metaKeywords="" description="Learn how to create, deploy, and manage your Azure Mobile Service using command-line tools." metaCanonical="" services="" documentationCenter="Mobile" title="Automate mobile services with command-line tools" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Automatizar os serviços móveis com ferramentas de linha de comando

Este tópico mostra como usar as ferramentas de linha de comando do Azure para automatizar a criação e o gerenciamento dos Serviços Móveis do Azure. Este tópico mostra como instalar e usar as ferramentas de linha de comando e como usá-las para executar as seguintes tarefas dos Serviços Móveis:

-   [Criar um novo serviço móvel][Criar um novo serviço móvel]
-   [Criar uma nova tabela][Criar uma nova tabela]
-   [Registrar um script para uma operação de tabela][Registrar um script para uma operação de tabela]
-   [Listar tabelas][Listar tabelas]
-   [Excluir uma tabela existente][Excluir uma tabela existente]
-   [Listar serviços móveis][Listar serviços móveis]
-   [Excluir um serviço móvel existente][Excluir um serviço móvel existente]

Quando combinadas em um único script ou arquivo em lotes, estes comandos individuais automatizam os processos de criação, verificação e exclusão de um serviço móvel.

Para usar as ferramentas de linha de comando do Azure para gerenciar Serviços Móveis, você precisará de uma conta do Azure que tenha o recurso Serviços Móveis habilitado.

-   Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].

-   Se você tiver uma conta existente, mas precisar habilitar a visualização dos Serviços Móveis do Azure, consulte [Habilitar recursos de visualização do Azure][Habilitar recursos de visualização do Azure].

Este tópico aborda uma seleção de tarefas para administração comuns compatíveis com as ferramentas de linha de comando do Azure. Para obter mais informações, consulte [Documentação das ferramentas de linha de comando do Azure][Documentação das ferramentas de linha de comando do Azure].

<!--+  You must download and install the Azure command-line tools to your local machine. To do this, follow the instructions in the first section of this topic.   + (Optional) To be able to execute HTTP requests directly from the command-line, you must use cURL or an equivalent tool. cURL runs on a variety of platforms. Locate and install cURL for your specific platform from the <a href=http://go.microsoft.com/fwlink/p/?LinkId=275676 target="_blank">cURL download  page</a>.-->

## <a name="install"></a><span class="short-header">Instalar as ferramentas</span>Instalar as Ferramentas de Linha de Comando do Azure

A lista a seguir contém informações para instalar as ferramentas de linha de comando, dependendo do seu sistema operacional:

-   **Windows**: Baixe o [Instalador de Ferramentas de Linha de Comando do Azure][Instalador de Ferramentas de Linha de Comando do Azure]. Abra o arquivo .msi baixado e conclua as etapas de instalação de acordo com o que for solicitado.

-   **Mac**: Baixe o [Instalador do SDK do Azure][Instalador do SDK do Azure]. Abra o arquivo .pkg baixado e conclua as etapas de instalação quando for solicitado.

-   **Linux**: instale a versão mais recente do [Node.js][Node.js] (consulte [Instalar o Node.js por meio do Gerenciador de Pacotes][Instalar o Node.js por meio do Gerenciador de Pacotes]), em seguida, execute o seguinte comando:

        npm install azure-cli -g

Para testar a instalação, digite `azure` no prompt de comando. Quando a instalação tiver sido executada com êxito, você verá uma lista de todos os comandos `azure`
 disponíveis.

## <a name="import-account"></a><span class="short-header">Importar configurações</span>Como baixar e importar configurações de publicação

</p>
Para iniciar, você deve primeiro baixar e importar suas configurações de publicação. Em seguida, você poderá usar as ferramentas para criar e gerenciar os Serviços do Azure. Para baixar as configurações de publicação, use o comando `account download`:

        azure account download

Isto abrirá o navegador padrão e solicitará que você entre no Portal de Gerenciamento. Depois de entrar, seu arquivo `.publishsettings` será baixado. Observe o local onde este arquivo foi salvo.

Em seguida, importe o arquivo `.publishsettings` executando o seguinte comando, substituindo `<path-to-settings-file>` pelo caminho do arquivo `.publishsettings`:

        azure account import <path-to-settings-file>

Você pode remover todas as informações armazenadas pelo comando `import` usando o comando `account clear`:

        azure account clear

Para ver uma lista de opções de comandos `account`, use a opção `-help`:

        azure account -help

Depois de importar as configurações de publicação, exclua o arquivo `.publishsettings` por motivos de segurança. Para obter mais informações, consulte [Como instalar as Ferramentas de Linha de Comando do Azure para Mac e Linux][Como instalar as Ferramentas de Linha de Comando do Azure para Mac e Linux]. Agora você está pronto para começar a criar e gerenciar os Serviços Móveis do Azure a partir da linha de comando ou em arquivos em lotes.

## <a name="create-service"></a><span class="short-header">Criar serviço</span>Como criar um serviço móvel

Você pode usar as ferramentas de linha de comando para criar uma nova instância de serviço móvel. Ao criar o serviço móvel, você também criará uma instância do Banco de Dados SQL em um novo servidor.

O comando a seguir cria uma nova instância do serviço móvel em sua assinatura, onde `<service-name>` é o nome do novo serviço móvel, `<server-admin>` é o nome de logon do novo servidor e `<server-password>` é a senha para o novo logon:

        azure mobile create <service-name> <server-admin> <server-password>

O comando `mobile create` falhará quando o serviço móvel especificado existir. Em seus scripts de automação, você deve tentar excluir um serviço móvel antes de tentar recriá-lo.

## <a name="list-services"></a><span class="short-header">Listar serviços</span>Como listar serviços móveis existentes em uma assinatura

O comando a seguir retorna uma lista de todos os serviços móveis em uma assinatura do Azure:

        azure mobile list

Este comando também mostra o estado atual e a URL de cada serviço móvel.

## <a name="delete-service"></a><span class="short-header">Excluir serviço</span>Como excluir um serviço móvel existente

Você pode usar as ferramentas de linha de comando para excluir um serviço móvel existente, juntamente com os respectivos Banco de Dados SQL e servidor. O comando a seguir exclui o serviço móvel, onde `<service-name>` é o nome do serviço móvel a ser excluído:

        azure mobile delete <service-name> -a -q

Com a inclusão dos parâmetros `-a` e `-q`, este comando também exclui o Banco de Dados SQL e o servidor usados pelo serviço móvel, sem exibir um prompt.

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> 
   <p>Se n&atilde;o especificar o par&acirc;metro <code data-inline="1">-q</code> juntamente com <code data-inline="1">-a</code> ou <code data-inline="1">-d</code>, a execu&ccedil;&atilde;o &eacute; interrompida e voc&ecirc; ser&aacute; solicitado a selecionar excluir op&ccedil;&otilde;es para seu Banco de Dados SQL. Use o par&acirc;metro <code data-inline="1">-a</code> somente quando nenhum outro servi&ccedil;o estiver usando o banco de dados ou servidor. Caso esteja, use o par&acirc;metro <code data-inline="1">-d</code> para excluir apenas os dados que perten&ccedil;am ao servi&ccedil;o m&oacute;vel que est&aacute; sendo exclu&iacute;do.</p>
</div>

## <a name="create-table"></a><span class="short-header">Criar tabela</span>Como criar uma tabela no serviço móvel

O comando a seguir cria uma tabela no serviço móvel especificado, onde `<service-name>` é o nome do serviço móvel e `<table-name>` é o nome da tabela a ser criada:

        azure mobile table create <service-name> <table-name>

Isto cria uma nova tabela com as permissões padrão, `application`, para as operações de tabela: `insert`, `read`, `update` e `delete`.

O comando a seguir cria uma nova tabela com permissão para `read` pública, mas com a permissão para `delete` concedida apenas a administradores:

        azure mobile table create <service-name> <table-name> -p read=public,delete=admin

A tabela a seguir mostra o valor da permissão de script em comparação com o valor da permissão no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure]

| Valor do script        | Valor do portal de gerenciamento          |
|------------------------|-------------------------------------------|
| `public`               | Todos                                     |
| `application` (padrão) | Qualquer pessoa com a chave de aplicativo |
| `user`                 | Somente usuários autenticados             |
| `admin `               | Somente scripts e administradores         |

O comando `mobile table create`falhará quando a tabela especifica já existir. Em seus scripts de automação, você deve tentar excluir uma tabela antes de tentar recriá-la.

## <a name="list-tables"></a><span class="short-header">Listar tabelas</span>Como listar tabelas existentes em um serviço móvel

O comando a seguir fornece uma lista de todas as tabelas em um serviço móvel, onde `<service-name>` é o nome do serviço móvel:

        azure mobile table list <service-name>

Este comando também mostra o número de índices em cada tabela e o número de linhas de dados existentes atualmente na tabela.

## <a name="delete-table"></a><span class="short-header">Excluir tabela</span>Como excluir uma tabela existente do serviço móvel

O comando a seguir exclui uma tabela do serviço móvel, onde `<service-name>` é o nome do serviço móvel e `<table-name>` é o nome da tabela a ser excluída:

        azure mobile table delete <service-name> <table-name> -q

Em scripts de automação, use o parâmetro `-q` para excluir a tabela sem exibir um prompt de confirmação que bloqueará a execução.

## <a name="register-script"></a><span class="short-header">Registrar um script</span>Como registrar um script em uma operação de tabela

O comando a seguir carrega e registra uma função para uma operação em uma tabela, onde `<service-name>` é o nome do serviço móvel, `<table-name>` é o nome da tabela e `<operation>` é a operação de tabela, que poderá ser `read`, `insert`, `update` ou `delete`:

        azure mobile script upload <service-name> table/<table-name>.<operation>.js

Observe que esta operação carregará um arquivo JavaScript (.js) a partir do computador local. O nome do arquivo deve ser composto dos nomes da tabela e da operação e deve estar localizado na subpasta `table` relativa ao local onde o comando é executado. Por exemplo, a seguinte operação carrega e registra um novo script `insert`, que pertence à tabela `TodoItems`:

        azure mobile script upload todolist table/todoitems.insert.js

A declaração de função no arquivo de script também deve coincidir com a operação de tabela registrada. Isto significa que para um script `insert`, o script carregado contém uma função com a seguinte assinatura:

        function insert(item, user, request) {
            ...
        } 

Para obter mais informações sobre como registrar scripts, consulte [Referência para script de servidor dos Serviços Móveis][Referência para script de servidor dos Serviços Móveis]

<!--<h2><a name="test-service"></a><span class="short-header">Test the service</span>Test the new mobile service</h2>  When you are automating the creation of your mobile service, you can optionally use cURL or another command-line request generator to   ## <a name="nextsteps"> </a>Next Steps Next steps here....
 --> 

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Criar um novo serviço móvel]: #create-service
  [Criar uma nova tabela]: #create-table
  [Registrar um script para uma operação de tabela]: #register-script
  [Listar tabelas]: #list-tables
  [Excluir uma tabela existente]: #delete-table
  [Listar serviços móveis]: #list-services
  [Excluir um serviço móvel existente]: #delete-service
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/
  [Habilitar recursos de visualização do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/php-create-account/#enable
  [Documentação das ferramentas de linha de comando do Azure]: /pt-br/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services
  [Instalador de Ferramentas de Linha de Comando do Azure]: http://go.microsoft.com/fwlink/p?LinkID=275464
  [Instalador do SDK do Azure]: http://go.microsoft.com/fwlink/p?LinkId=252249
  [Node.js]: http://nodejs.org/
  [Instalar o Node.js por meio do Gerenciador de Pacotes]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
  [Como instalar as Ferramentas de Linha de Comando do Azure para Mac e Linux]: http://go.microsoft.com/fwlink/p/?LinkId=275795
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [Referência para script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/p?LinkId=262293
