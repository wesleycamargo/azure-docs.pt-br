<properties linkid="script-xplat-intro" urlDisplayName="Azure Cross-Platform Command-Line Interface" pageTitle="The Azure Cross-Platform Command-Line Interface" title="The Azure Cross-Platform Command-Line Interface" metaKeywords="Azure cross-platform command-line interface, Azure command-line, azure command-line, azure cli" description="Install and configure the Azure Cross-Platform Command-Line Interface to manage Azure Services" metaCanonical="http://www.windowsazure.com/pt-br/script/xplat-cli-intro" umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="paulettm" documentationCenter="" solutions="" authors="carolz" services="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="carolz"></tags>

# Instalar e configurar a interface de linha de comando entre plataformas do Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/manage/install-and-configure-windows-powershell/" title="PowerShell">PowerShell</a><a href="/pt-br/manage/install-and-configure-cli/" title="CLI entre plataformas" class="current">CLI entre plataformas</a></div>

A interface de linha de comando entre plataformas do Azure (xplat-cli) oferece um conjunto de comandos entre plataformas, de software livre, para o trabalho com a plataforma Azure. A xplat-cli oferece praticamente a mesma funcionalidade encontrada no Portal de Gerenciamento do Azure, como a capacidade de gerenciar sites, máquinas virtuais, serviços móveis, Banco de Dados SQL e outros serviços fornecidos pela plataforma Azure.

A xplat-cli é escrita em JavaScript e requer o Node.js. Ela é implementada usando-se o SDK do Azure para Node.js e liberada sob uma licença do Apache 2.0. O repositório do projeto está localizado em [][]<https://github.com/WindowsAzure/azure-sdk-tools-xplat></a>.

Este documento descreve como instalar e configurar a interface de linha de comando entre plataformas do Azure e como usá-la para realizar tarefas básicas com a plataforma Azure.

## Neste documento

-   [Como instalar a interface de linha de comando entre plataformas do Azure][Como instalar a interface de linha de comando entre plataformas do Azure]
-   [Como se conectar à assinatura do Azure][Como se conectar à assinatura do Azure]
-   [Como usar a interface de linha de comando entre plataformas do Azure][Como usar a interface de linha de comando entre plataformas do Azure]
-   [Como criar scripts para a interface de linha de comando entre plataformas do Azure][Como criar scripts para a interface de linha de comando entre plataformas do Azure]
-   [Recursos adicionais][Recursos adicionais]

## <span id="install"></span></a>Como instalar a interface de linha de comando entre plataformas do Azure

Há duas maneiras de instalar a xplat-cli: usando pacotes do instalador do Windows e do OS X ou, se o Node.js estiver instalado no sistema, o comando **npm**.

Em sistemas Linux, você deve ter o Node.js instalado e usar **npm** para instalar a xplat-cli, conforme descrito abaixo, ou compilá-la a partir do código-fonte. O código-fonte está disponível em [][1]<http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409></a>. Para obter mais informações sobre como compilar a partir do código-fonte, consulte o arquivo INSTALL incluído no arquivo morto.

Quando a xplat-cli estiver instalada, você poderá usar o comando **azure** na sua interface de linha de comando (Bash, Terminal, prompt de comando) para acessar os comandos da xplat-cli.

### Usando um instalador

Os seguintes pacotes de instalador estão disponíveis:

-   [Instalador do Windows][Instalador do Windows]

-   [Instalador do OS X][Instalador do OS X]

### Usando npm

Se o Node.js estiver instalado em seu sistema, use o seguinte comando para instalar a xplat-cli:

    npm install azure-cli -g

> [WACOM.NOTE] Talvez você precise usar `sudo` para executar o comando **npm** com êxito.

Isso instalará a xplat-cli e as dependências necessárias. No final da instalação, você verá algo semelhante ao seguinte:

    azure-cli@0.8.0 ..\node_modules\azure-cli
    |-- easy-table@0.0.1
    |-- eyes@0.1.8
    |-- xmlbuilder@0.4.2
    |-- colors@0.6.1
    |-- node-uuid@1.2.0
    |-- async@0.2.7
    |-- underscore@1.4.4
    |-- tunnel@0.0.2
    |-- omelette@0.1.0
    |-- github@0.1.6
    |-- commander@1.0.4 (keypress@0.1.0)
    |-- xml2js@0.1.14 (sax@0.5.4)
    |-- streamline@0.4.5
    |-- winston@0.6.2 (cycle@1.0.2, stack-trace@0.0.7, async@0.1.22, pkginfo@0.2.3, request@2.9.203)
    |-- kuduscript@0.1.2 (commander@1.1.1, streamline@0.4.11)
    |-- azure@0.7.13 (dateformat@1.0.2-1.2.3, envconf@0.0.4, mpns@2.0.1, mime@1.2.10, validator@1.4.0, xml2js@0.2.8, wns@0.5.3, request@2.25.0)

> [WACOM.NOTE] O Node.js pode ser instalado em [http://nodejs.org][http://nodejs.org].

## <span id="Configure"></span></a>Como se conectar à assinatura do Azure

Embora alguns comandos fornecidos pela xplat-cli funcionem sem uma assinatura do Azure, a maioria dos comandos exige uma assinatura. Para configurar a xplat-cli para trabalhar com a assinatura, você pode:

-   Baixar e usar um arquivo de configurações para publicação.

OU

-   Fazer logon no Azure usando uma conta institucional. Quando você faz logon, o Active Directory do Azure é usado para autenticar as credenciais.

Para ajudar a escolher o método de autenticação apropriado para suas necessidades, considere o seguinte:

-   O método de logon pode facilitar o gerenciamento do acesso à assinatura, mas pode atrapalhar a automação, porque as credenciais podem atingir tempo limite e exigir que você faça logon novamente.

    > [WACOM.NOTE] O método de logon só funciona com contas institucionais. Uma conta institucional é um usuário gerenciado pela organização e definido no locatário do Active Directory do Azure da organização. Se não tiver uma conta institucional e estiver usando uma conta da Microsoft para fazer logon na assinatura do Azure, você poderá criar facilmente uma usando as etapas a seguir.
    >
    > 1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure]e clique em **Active Directory**.
    >
    > 2.  Se não houver diretório, selecione **Criar o diretório** e forneça as informações solicitadas.
    >
    > 3.  Selecione o diretório e adicione um novo usuário. Esse novo usuário é uma conta institucional.
    >
    >     Durante a criação do usuário, você receberá um endereço de email para o usuário e uma senha temporária. Salve essas informações como elas são usadas em outra etapa.
    >
    > 4.  No Portal de Gerenciamento, selecione **Configurações** e **Administradores**. Selecione **Adicionar** e adicione o novo usuário como um coadministrador. Isso permite que a conta institucional gerencie a assinatura do Azure.
    >
    > 5.  Por fim, faça logoff do portal do Azure e refaça logon usando a nova conta institucional. Se este for o primeiro logon usando essa conta, você deverá alterar a senha.
    >
    > Para obter mais informações sobre contas institucionais no Microsoft Azure, consulte [Inscreva-se no Microsoft Azure como uma organização][Inscreva-se no Microsoft Azure como uma organização].

-   O método do arquivo de configurações de publicação instala um certificado que permite realizar tarefas de gerenciamento, desde que a assinatura e o certificado sejam válidos. Esse método facilita o uso da automação para tarefas de execução longa. Depois de baixar e importar as informações, você não precisa fornecê-las novamente. Porém, esse método dificulta o gerenciamento do acesso a uma assinatura porque qualquer pessoa com acesso ao certificado pode gerenciar a assinatura.

Para obter mais informações sobre a autenticação e o gerenciamento da assinatura, consulte ["Qual é a diferença entre autenticação com base na conta e autenticação com base no certificado"]["Qual é a diferença entre autenticação com base na conta e autenticação com base no certificado"]

Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].

### Usar o método de logon

Para fazer logon usando uma conta institucional, use o seguinte comando:

    azure login [username] [password]

> [WACOM.NOTE] Se este for o primeiro logon usando essas credenciais, você receberá um aviso dizendo que deverá verificar se deseja armazenar em cache um token de autenticação. Esse aviso também ocorrerá se você já tiver usado o comando `azure logout` descrito abaixo. Para ignorar esse aviso em cenários de automação, use o parâmetro `-q` com o comando `azure login`.
>
> Quando você faz a autenticação usando uma conta institucional, as informações para acessar a assinatura do Azure são armazenadas em um diretório `.azure` localizado no diretório `user`. O diretório `user` é protegido pelo seu sistema operacional. No entanto, é recomendável que você execute etapas adicionais para criptografar seu diretório `user`. Você pode fazer isso das seguintes maneiras:

Para fazer logoff, use o seguinte comando:

    azure logout [username]

> [WACOM.NOTE] Se as assinaturas associadas à conta forem autenticadas apenas com o Active Directory, o logout excluirá as informações de assinatura do perfil local. Porém, se um arquivo de configurações de publicação também tiver sido importado para as assinaturas, o logoff só excluirá as informações relacionadas ao Active Directory do perfil local.

> [WACOM.NOTE] Os seguintes comandos não funcionarão corretamente durante a autenticação com uma conta:
>
> -   `azure vm`
> -   `azure network`
> -   `azure mobile`
>
> Se você precisar trabalhar com esses comandos, use um arquivo de configurações de publicação para se autenticar no Azure conforme descrito na seção a seguir.

### Usar o método do arquivo de configurações de publicação

Para baixar as configurações de publicação de sua conta, use o seguinte comando:

    azure account download

Isso abrirá o navegador padrão e você deverá entrar no Portal de Gerenciamento do Azure. Depois de entrar, o arquivo `.publishsettings` será baixado. Anote onde esse arquivo está salvo.

> [WACOM.NOTE] Se a conta estiver associada a vários locatários do Active Directory do Azure, você deverá selecionar para qual Active Directory deseja baixar um arquivo de configurações de publicação.
>
> Depois de selecionado por meio da página de download ou acesso ao Portal de Gerenciamento do Azure, o Active Directory escolhido se torna o padrão usado pelo portal e pela página de download. Depois que um padrão tiver sido estabelecido, você verá o texto \`**clique aqui para retornar à página de seleção**\` na parte superior da página de download. Use o link fornecido para retornar à página de seleção.

Em seguida, importe o arquivo `.publishsettings` executando o seguinte comando, substituindo `[path to .publishsettings file]` pelo caminho do arquivo `.publishsettings`:

    azure account import [path to .publishsettings file]

> [WACOM.NOTE] Quando você importa as configurações de publicação, as informações para acessar sua assinatura do Azure são armazenadas em um diretório `.azure`, localizado em seu diretório `user`. O diretório `user` é protegido pelo seu sistema operacional. No entanto, é recomendável que você execute etapas adicionais para criptografar seu diretório `user`. Você pode fazer isso das seguintes maneiras:
>
> -   No Windows, modifique as propriedades do diretório ou use o BitLocker.
> -   No Mac, ative o FileVault para o diretório.
> -   No Ubuntu, use o recurso de diretório inicial criptografado. Outras distribuições Linux oferecem recursos equivalentes.

Depois de importar as configurações de publicação, você deverá excluir o arquivo `.publishsettings`, porque ele não é mais exigido pelas Ferramentas de linha de comando e oferece um risco à segurança, pois pode ser usado para que se tenha acesso à assinatura.

### Várias assinaturas

Se você tiver várias assinaturas do Azure, o logon dará acesso a todas as assinaturas associadas às credenciais. Se você estiver usando um arquivo de configurações de publicação, o arquivo `.publishsettings` conterá informações de todas as assinaturas. Com qualquer um dos métodos, uma assinatura será selecionada como a assinatura padrão usada pela xplat-cli durante a realização das operações. Você pode exibir as assinaturas, bem como qual delas é a padrão, mas usando o comando `azure account list`. Esse comando retornará informações semelhantes às seguintes:

    info:    Executing command account list
    data:    Name              Id                                    Current
    data:    ----------------  ------------------------------------  -------
    data:    Azure-sub-1       ####################################  true
    data:    Azure-sub-2       ####################################  false

Na lista acima, a coluna **Atual** indica a assinatura padrão atual como Azure-sub-1. Para alterar a assinatura padrão, use o comando `azure account set` e especifique a assinatura que você deseja que seja a padrão. Por exemplo:

    azure account set Azure-sub-2

Isso irá alterar a assinatura padrão para Azure-sub-2.

> [WACOM.NOTE] A alteração da assinatura padrão entra em vigor imediatamente, e é uma alteração global. Novos comandos xplat, sejam executados na mesma instância de linha de comando ou em outra instância, usarão a nova assinatura padrão.

Se quiser usar uma assinatura não padrão com a xplat-cli, mas não quiser alterar o padrão atual, você poderá usar a opção `--subscription` e fornecer o nome da assinatura que deseja usar na operação.

## <span id="use"></span></a>Como usar a interface de linha de comando entre plataformas do Azure

A xplat-cli é acessada usando-se o comando `azure`. Para ver uma lista de comandos disponíveis, use o comando `azure`sem parâmetros. Você deve consultar a informações da ajuda semelhantes a:

    info:             _    _____   _ ___ ___
    info:            /_\  |_  / | | | _ \ __|
    info:      _ ___/ _ \__/ /| |_| |   / _|___ _ _
    info:    (___  /_/ \_\/___|\___/|_|_\___| _____)
    info:       (_______ _ _)         _ ______ _)_ _
    info:              (______________ _ )   (___ _ _)
    info:
    info:    Windows Azure: Microsoft's Cloud Platform
    info:
    info:    Tool version 0.8.0
    help:
    help:    Display help for a given command
    help:      help [options] [command]
    help:
    help:    Opens the portal in a browser
    help:      portal [options]
    help:
    help:    Commands:
    help:      account        Commands to manage your account information and publish settings
    help:      config         Commands to manage your local settings
    help:      hdinsight      Commands to manage your HDInsight accounts
    help:      mobile         Commands to manage your Mobile Services
    help:      network        Commands to manage your Networks
    help:      sb             Commands to manage your Service Bus configuration
    help:      service        Commands to manage your Cloud Services
    help:      site           Commands to manage your Web Sites
    help:      sql            Commands to manage your SQL Server accounts
    help:      storage        Commands to manage your Storage objects
    help:      vm             Commands to manage your Virtual Machines
    help:
    help:    Options:
    help:      -h, --help     output usage information
    help:      -v, --version  output the application version

Os comandos de nível superior listados acima contêm comandos para trabalhar com uma área específica do Azure. Por exemplo, o comando `azure account` contém comandos relacionados à assinatura do Azure, como as configurações de `download` e `import` usadas anteriormente.

A maioria dos comandos é formatada como `azure <command> <operation> [parameters]` e realiza uma operação em um serviço ou objeto, como as configurações da conta. Outros comandos fornecem subcomandos e seguem o formato `azure <command> <subcommand> <operation> [parameters]`. Os seguintes são comandos de exemplo que funcionam com a configuração de sua conta:

-   Para exibir as assinaturas que você importou, use:

        azure account list

-   Se você tiver importado assinaturas, use o seguinte para definir uma como padrão:

        azure account set <subscription>

O parâmetro `--help` ou `-h` pode ser usado para exibir a ajuda de comandos específicos. Como alternativa, o formato `azure help [command] [options]` também pode ser usado para retornar as mesmas informações. Por exemplo, todos os seguintes comandos retornam as mesmas informações:

    azure account set --help

    azure account set -h

    azure help account set

Em caso de dúvida sobre os parâmetros necessários para um comando, consulte a ajuda usando `--help`, `-h` ou `azure help [command]`.

### Definindo o modo de configuração

A xplat-cli permite realizar operações de gerenciamento em *recursos* individuais, que são entidades gerenciadas pelo usuário, como um servidor de banco de dados, um banco de dados ou um site. Esse é o modo de operação padrão para a xplat-cli e é conhecido como **Gerenciamento de Serviços do Azure**. Porém, quando você tem uma solução complexa que consiste em vários recursos, é útil ser capaz de gerenciar toda a solução como uma única unidade.

Para dar suporte ao gerenciamento de um grupo de recursos como uma única unidade lógica, ou *grupo de recursos*, apresentamos uma visualização do **Gerenciador de Recursos** como uma nova forma de gerenciamento dos recursos do Azure.

> [WACOM.NOTE] O Gerenciador de Recursos está em visualização neste momento e não oferece o mesmo nível de recursos de gerenciamento, como o Gerenciamento de Serviços do Azure.

Para dar suporte ao novo Gerenciador de recursos, a xplat-cli permite alternar esses “modos” de gerenciamento por meio do comando `azure config mode`.

A xplat-cli usa como padrão o modo de gerenciamento de serviços do Azure. Para alternar para o modo Gerenciador de Recursos, use o seguinte para habilitar o comando:

    azure config mode arm

Para retornar ao modo de gerenciamento de serviços do Azure, use o seguinte comando:

    azure config mode asm 

> [WACOM.NOTE] O modo do Gerenciador de recursos e o modo do Gerenciamento de serviços do Azure são mutuamente excludentes. Ou seja, recursos criados em um modo não podem ser gerenciados no outro modo.

Para obter mais informações sobre como trabalhar com o Gerenciador de Recursos usando a xplat-cli, consulte [Usando a interface de linha de comando da plataforma cruzada do Microsoft Azure com o Gerenciador de Recursos][Usando a interface de linha de comando da plataforma cruzada do Microsoft Azure com o Gerenciador de Recursos].

### Trabalhando com serviços no modo de gerenciamento de serviços do Azure

A xplat-cli permite gerenciar facilmente os serviços do Azure. Neste exemplo, você aprenderá a usar a xplat-cli para gerenciar um site do Azure.

1.  Use o comando a seguir para criar um novo site do Azure. Substitua **mywebsite** por um nome exclusivo.

        azure site create mywebsite

    Será solicitado que você especifique a região em que o site será criado. Selecione uma região que seja geograficamente próxima a você. Após a conclusão desse comando, o site estará disponível em <http://mywebsite.azurewebsites.net> (substitua **mywebsite** pelo nome especificado.)

    > [WACOM.NOTE] Se usar o Git no controle do código-fonte do projeto, você poderá especificar o parâmetro `--git` para criar um repositório Git no Azure para esse site. Isso também irá inicializar um repositório Git no diretório do qual o comando foi executado, caso ele ainda não exista. Também cria um Git remoto chamado **azure**, que pode ser usado para enviar por push as implantações no Site do Azure usando o comando `git push azure master`.

    > [WACOM.NOTE] Se você receber um erro de que “site” não é um comando do Azure, é provável que a xplat-cli esteja no modo de grupo de recursos. Para retornar ao modo de recurso, use o comando `azure config mode asm`.

2.  Use o seguinte comando para listar sites para a assinatura:

        azure site list

    A lista deve conter o site criado na etapa anterior.

3.  Use o comando a seguir para parar o site. Substitua **mywebsite** pelo nome do site.

        azure site stop mywebsite

    Depois que o comando for concluído, você poderá atualizar o navegador para verificar se o site foi parado.

4.  Use o comando a seguir para iniciar o site. Substitua **mywebsite** pelo nome do site.

        azure site start mywebsite

    Depois que o comando for concluído, você poderá atualizar o navegador para verificar se o site foi iniciado.

5.  Use o comando a seguir para excluir o site. Substitua **mywebsite** pelo nome do site.

        azure site delete mywebsite

    Após a conclusão do comando, use o comando `azure site list` para verificar se o site não existe mais.

## <span id="script"></span></a>Como criar scripts para a interface de linha de comando entre plataformas do Azure

Embora seja possível usar a xplat-cli para emitir comandos manualmente, você também pode criar fluxos de trabalho complexos de automação utilizando os recursos de seu interpretador de linha de comando e outros utilitários de linha de comando disponíveis no sistema. Por exemplo, o seguinte comando irá parar todos os sites do Azure em execução:

    azure site list | grep 'Running' | awk '{system("azure site stop "$2)}'

Este exemplo redireciona uma lista de sites para o comando `grep`, que inspeciona a cadeia de caracteres “Running” em cada linha. Todas as linhas que corresponderem serão redirecionadas para o comando `awk`, que chama `azure site stop` e usa a segunda coluna passada para ele (o nome do site em execução) como o nome do site a ser parado.

Embora isso demonstre como é possível encadear comandos, você também pode criar scripts mais elaborados usando os recursos de script fornecidos pelo seu interpretador de linha de comando. Interpretadores de linha de comando diferentes têm sintaxe e recursos de script diferentes. O Bash, provavelmente, é o interpretador de linha de comando mais amplamente usado para sistemas baseados em UNIX, incluindo o Linux e o OS X.

Para obter informações sobre como criar scripts com o Bash, consulte [Guia de script Bash avançado][Guia de script Bash avançado].

Para obter mais informações sobre scripts de sistemas baseados em Linux ou OS X, consulte [Script de shell][Script de shell].

Para obter informações sobre scripts de sistemas baseados no Windows usando arquivos em lotes, consulte [Referência de A a Z de linha de comando][Referência de A a Z de linha de comando].

### Compreendendo os resultados

Ao criar scripts, você geralmente precisa capturar a saída de um comando e passar essa saída para outro comando ou executar uma operação na saída, como a verificação de um valor específico. A xplat-cli gera saída para STDOUT e STDERR. Cada linha é prefixada pelas cadeias de caracteres `info:`, para mensagens de status informativas, e `data:`, para dados retornados sobre um serviço. Porém, você pode instruir a xplat-cli a retornar informações mais detalhadas usando o parâmetro `--verbose` ou `-v`. Isso retornará informações adicionais prefixadas pela cadeia de caracteres `verbose:`.

Por exemplo, a seguinte saída é retornada pelo comando `azure site list`:

    info:    Executing command site list
    + Enumerating sites
    data:    Name           Status   Mode  Host names
    data:    -------------  -------  ----  -------------------------------
    data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
    info:    site list command OK

Se o parâmetro `--verbose` ou `-v` for especificado, informações semelhantes às seguintes serão retornadas:

    info:    Executing command site list
    verbose: Subscription ####################################
    verbose: Enumerating sites
    verbose: [
    verbose:     {
    verbose:         ComputeMode: 'Shared',
    verbose:         HostNameSslStates: {
    verbose:             HostNameSslState: [
    verbose:                 {
    verbose:                     IpBasedSslResult: {
    verbose:                         $: { i:nil: 'true' }
    verbose:                     },
    verbose:                     SslState: 'Disabled',
    verbose:                     ToUpdateIpBasedSsl: {
    verbose:                         $: { i:nil: 'true' }
    verbose:                     },
    verbose:                     VirtualIP: {
    verbose:                         $: { i:nil: 'true' }
    verbose:                     },
    verbose:                     Thumbprint: {
    verbose:                         $: { i:nil: 'true' }
    verbose:                     },
    verbose:                     ToUpdate: {
    verbose:                         $: { i:nil: 'true' }
    verbose:                     },
    verbose:                     Name: 'myawesomesite.azurewebsites.net'
    verbose:                 },
    ...
    verbose:     }
    verbose: ]
    data:    Name           Status   Mode  Host names
    data:    -------------  -------  ----  -------------------------------
    data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
    info:    site list command OK

Observe que as informações `verbose:` parecem ser dados formatados em JSON. Você poderá usar o parâmetro `--json` para retornar as informações no formato JSON, se estiver trabalhando com utilitários que compreendam JSON nativamente, como [jsawk][jsawk] e [jq][jq]. Por exemplo:

    azure site list --json | jsawk -n 'out(this.Name)' | xargs -L 1 azure site delete -q 

O comando acima recupera uma lista de sites como JSON e, em seguida, usa jsawk para recuperar os nomes dos sites e, finalmente, usa xargs para executar um comando site delete para cada site, passando o nome do site como um parâmetro.

> [WACOM.NOTE] O parâmetro `--json` bloqueia a geração de informações de status ou de dados (cadeias de caracteres prefixadas por `info:` e `data:`). Por exemplo, se o parâmetro `--json` for usado com o `azure site create`, nenhuma saída será retornada, uma vez que esse comando não retorna nenhum dado diferente de `info:`.

### Trabalhando com erros

Embora a xplat-cli registre informações de erro para STDERR, informações adicionais sobre erros também podem ser registradas em log em um arquivo **azure.err** no diretório no qual o script foi executado. Se as informações forem armazenadas nesse arquivo, o seguinte será gravado para STDOUT:

    info:    Error information has been recorded to azure.err

### Status da saída

Alguns dos comandos da xplat-cli não retornam um status da saída diferente de zero se os parâmetros necessários estiverem ausentes. Em vez disso, eles solicitarão uma entrada do usuário. Por exemplo, ao usar o comando `azure site create` para criar um novo site, se nenhum nome de site ou parâmetro `--location` for especificado, você deverá fornecer esses valores.

Se estiver escrevendo um script que dependa do status da saída, verifique se os comandos da xplat-cli que você está usando não solicitam uma entrada do usuário.

## <span id="additional-resources"></span></a>Recursos adicionais

-   Para obter mais informações sobre a xplat-cli, para baixar o código-fonte, relatar problemas ou contribuir com o projeto, visite o [Repositório GitHub da interface de linha de comando entre plataformas do Azure][Repositório GitHub da interface de linha de comando entre plataformas do Azure].

-   Se você tiver problemas ao usar a xplat-cli ou o Azure, visite os [fóruns do Azure][fóruns do Azure].

-   Para obter mais informações sobre o Azure, consulte [][2][http://azure.microsoft.com][http://azure.microsoft.com]</a>.

  [PowerShell]: /pt-br/manage/install-and-configure-windows-powershell/ "PowerShell"
  [CLI entre plataformas]: /pt-br/manage/install-and-configure-cli/ "CLI entre plataformas"
  []: https://github.com/WindowsAzure/azure-sdk-tools-xplat
  [Como instalar a interface de linha de comando entre plataformas do Azure]: #install
  [Como se conectar à assinatura do Azure]: #configure
  [Como usar a interface de linha de comando entre plataformas do Azure]: #use
  [Como criar scripts para a interface de linha de comando entre plataformas do Azure]: #script
  [Recursos adicionais]: #additional-resources
  [1]: http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409
  [Instalador do Windows]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
  [Instalador do OS X]: http://go.microsoft.com/fwlink/?LinkId=252249
  [http://nodejs.org]: http://nodejs.org/
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
  [Inscreva-se no Microsoft Azure como uma organização]: http://www.windowsazure.com/pt-br/documentation/articles/sign-up-organization/
  ["Qual é a diferença entre autenticação com base na conta e autenticação com base no certificado"]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A7171371E
  [Usando a interface de linha de comando da plataforma cruzada do Microsoft Azure com o Gerenciador de Recursos]: /pt-br/documentation/articles/xplat-cli-azure-resource-manager/
  [Guia de script Bash avançado]: http://tldp.org/LDP/abs/html/
  [Script de shell]: http://en.wikipedia.org/wiki/Shell_script
  [Referência de A a Z de linha de comando]: http://technet.microsoft.com/pt-br/library/bb490890.aspx
  [jsawk]: https://github.com/micha/jsawk
  [jq]: http://stedolan.github.io/jq/
  [fóruns do Azure]: http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home
  [2]: http://azure.microsoft.com
  [http://azure.microsoft.com]: http://azure.microsoft.com/
