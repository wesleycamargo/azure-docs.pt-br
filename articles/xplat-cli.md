<properties
	pageTitle="CLI do Azure para Mac, Linux e Windows"
	description="Instale e configure a CLI do Azure para Mac, Linux e Windows para gerenciar os Serviços do Azure"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="squillace"
	services=""/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/10/2015"
	ms.author="rasquill"/>

# Instale e configure a CLI do Azure

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [Azure CLI](xplat-cli.md)

A CLI do Azure fornece um conjunto de comandos entre plataformas de software livre para trabalhar com a Plataforma Azure. A CLI do Azure oferece praticamente a mesma funcionalidade encontrada no Portal de Gerenciamento do Azure, como a capacidade de gerenciar sites, máquinas virtuais, serviços móveis, Banco de Dados SQL e outros serviços fornecidos pela plataforma Azure.

A CLI do Azure é escrita em JavaScript e requer o Node.js. Ela é implementada usando-se o SDK do Azure para Node.js e liberada sob uma licença do Apache 2.0. O repositório do projeto fica localizado em [https://github.com/azure/azure-xplat-cli](https://github.com/azure/azure-xplat-cli).

Este documento descreve como instalar e configurar a CLI do Azure para Mac, Linux e Windows, bem como usá-la para executar tarefas básicas com a plataforma Azure.

<a id="install"></a>
## Como instalar a CLI do Azure

Para aprender as etapas de instalação da CLI do Azure, leia a página [Instalar a CLI do Azure](xplat-cli-install.md).


<a id="configure"></a>
## Como se conectar à assinatura do Azure

Embora alguns comandos fornecidos pela CLI do Azure funcionem sem uma assinatura do Azure, a maioria dos comandos exige uma assinatura. Para aprender como configurar a CLI do Azure para trabalhar com sua assinatura, acesse [Conectar-se a uma assinatura do Azure a partir da interface de linha de comando do Azure](xplat-cli-connect.md).


<a id="use"></a>
## Como usar a CLI do Azure

A CLI do Azure é acessada usando o comando `azure`. Para ver uma lista de comandos disponíveis, use o comando `azure` sem parâmetros. Você deve consultar a informações da ajuda semelhantes a:

	info:             _    _____   _ ___ ___
	info:            /_\  |_  / | | | _ \ __|
	info:      _ ___/ _ __/ /| |_| |   / _|___ _ _
	info:    (___  /_/ _/___|___/|_|____| _____)
	info:       (_______ _ _)         _ ______ _)_ _
	info:              (______________ _ )   (___ _ _)
	info:
	info:    Microsoft Azure: Microsoft's Cloud Platform
	info:
	info:    Tool version 0.8.10
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

Os comandos de nível superior listados acima contêm comandos para trabalhar com uma área específica do Azure. Por exemplo, o comando `azure account` contém comandos relacionados à assinatura do Azure, tais como as configurações `download` e `import` usadas anteriormente. Consulte [Como usar a CLI do Azure para Mac, Linux e Windows] para obter detalhes sobre os comandos e opções disponíveis.

A maioria dos comandos é formatada como `azure <command> <operation> [parameters]` e realiza uma operação em um serviço ou objeto, como as configurações da conta. Outros comandos fornecem subcomandos e seguem o formato `azure <command> <subcommand> <operation> [parameters]`. Os seguintes são comandos de exemplo que funcionam com a configuração de sua conta:

* Para exibir as assinaturas que você importou, use:

		azure account list

* Se você tiver importado assinaturas, use o seguinte para definir uma como padrão:

		azure account set <subscription>

O parâmetro `--help` ou `-h` pode ser usado para exibir a ajuda de comandos específicos. Como alternativa, o formato `azure help [command] [options]` também pode ser usado para retornar as mesmas informações. Por exemplo, todos os seguintes comandos retornam as mesmas informações:

	azure account set --help

	azure account set -h

	azure help account set

Em caso de dúvida sobre os parâmetros necessários para um comando, consulte a ajuda usando `--help`, `-h` ou `azure help [command]` .

### Definindo o modo de configuração

A CLI do Azure permite realizar operações de gerenciamento em _recursos_ individuais, que são entidades gerenciadas pelo usuário, como um servidor de banco de dados, um banco de dados ou um site. Esse é o modo de operação padrão para a CLI do Azure e é conhecido como **Gerenciamento de Serviços do Azure**. Porém, quando você tem uma solução complexa que consiste em vários recursos, é útil ser capaz de gerenciar toda a solução como uma única unidade.

Para dar suporte ao gerenciamento de um grupo de recursos como uma única unidade lógica, ou _grupo de recursos_, apresentamos uma visualização do **Gerenciador de Recursos** como uma nova forma de gerenciamento dos recursos do Azure.

>[AZURE.NOTE]O Gerenciador de Recursos está em visualização no momento e não oferece o mesmo nível de recursos de gerenciamento que o Gerenciamento de Serviços do Azure.

Para dar suporte ao novo Gerenciador de Recursos, a CLI do Azure permite alternar os “modos” de gerenciamento por meio do comando `azure config mode`.

A CLI do Azure usa como padrão o modo de Gerenciamento de Serviços do Azure. Para alternar para o modo Gerenciador de Recursos, use o seguinte para habilitar o comando:

	azure config mode arm

Para retornar ao modo de gerenciamento de serviços do Azure, use o seguinte comando:

	azure config mode asm

>[AZURE.NOTE]O modo do Gerenciador de Recursos e o modo do Gerenciamento de Serviços do Azure são mutuamente excludentes. Ou seja, recursos criados em um modo não podem ser gerenciados no outro modo.

Para obter mais informações sobre como trabalhar com o Gerenciador de Recursos usando a CLI do Azure, consulte [Usando a interface de linha de comando da plataforma cruzada do Microsoft Azure com o Gerenciador de Recursos][cliarm].

### Trabalhando com serviços no modo de gerenciamento de serviços do Azure

A CLI do Azure permite gerenciar facilmente os serviços do Azure. Neste exemplo, você aprenderá a usar a CLI do Azure para gerenciar um site do Azure.

1. Use o comando a seguir para criar um novo site do Azure. Substitua **mywebsite** por um nome exclusivo.

		azure site create mywebsite

	Será solicitado que você especifique a região em que o site será criado. Selecione uma região próxima a você. Após a conclusão do comando, o site estará disponível em http://mywebsite.azurewebsites.net (substituir **mywebsite** com o nome especificado por você.)

	> [AZURE.NOTE]Se usar Git no controle do código-fonte do projeto, você poderá especificar o parâmetro `--git` para criar um repositório Git no Azure para esse site. Isso também irá inicializar um repositório Git no diretório do qual o comando foi executado, caso ele ainda não exista. Também criará um Git remoto chamado __azure__, que pode ser usado para enviar por push as implantações no Site do Azure usando o comando `git push azure master`.

	> [AZURE.NOTE]Se você receber um erro de que 'site' não é um comando do Azure, a CLI do Azure deverá estar no modo do grupo de recursos. Para retornar ao modo de recurso, use o comando `azure config mode asm`.

2. Use o seguinte comando para listar sites para a assinatura:

		azure site list

	A lista deve conter o site criado na etapa anterior.

2. Use o comando a seguir para parar o site. Substitua **mywebsite** pelo nome do site.

		azure site stop mywebsite

	Depois que o comando for concluído, você poderá atualizar o navegador para verificar se o site foi parado.

3. Use o comando a seguir para iniciar o site. Substitua **mywebsite** pelo nome do site.

		azure site start mywebsite

	Depois que o comando for concluído, você poderá atualizar o navegador para verificar se o site foi iniciado.

4. Use o comando a seguir para excluir o site. Substitua **mywebsite** pelo nome do site.

		azure site delete mywebsite

	Após a conclusão do comando, use o comando `azure site list` para verificar se o site não existe mais.

<a id="script"></a>
## Como fazer um script da CLI do Azure para Mac, Linux e Windows

Embora seja possível usar a CLI do Azure para emitir comandos manualmente, você também pode criar fluxos de trabalho de automação complexos utilizando os recursos de seu interpretador de linha de comando e outros utilitários de linha de comando disponíveis no sistema. Por exemplo, o seguinte comando irá parar todos os sites do Azure em execução:

	azure site list | grep 'Running' | awk '{system("azure site stop "$2)}'

Este exemplo redireciona uma lista de sites para o comando `grep`, que inspeciona a cadeia de caracteres “Running” em cada linha. Todas as linhas que corresponderem serão redirecionadas para o comando `awk`, que chama `azure site stop` e usa a segunda coluna passada para ele (o nome do site em execução) como o nome do site a ser parado.

Embora isso demonstre como é possível encadear comandos, você também pode criar scripts mais elaborados usando os recursos de script fornecidos pelo seu interpretador de linha de comando. Interpretadores de linha de comando diferentes têm sintaxe e recursos de script diferentes. O Bash, provavelmente, é o interpretador de linha de comando mais amplamente usado para sistemas baseados em UNIX, incluindo o Linux e o OS X.

Para obter informações sobre como criar scripts com o Bash, consulte [Guia de script Bash avançado][advanced-bash].

Para obter mais informações sobre scripts de sistemas baseados em Linux ou OS X, consulte [Script de shell][script].

Para obter informações sobre scripts de sistemas baseados no Windows usando arquivos em lotes, consulte [Referência de A a Z de linha de comando][batch].

### Compreendendo os resultados

Ao criar scripts, você geralmente precisa capturar a saída de um comando e passar essa saída para outro comando ou executar uma operação na saída, como a verificação de um valor específico. A CLI do Azure gera saída para STDOUT e STDERR. Cada linha é prefixada pelas cadeias de caracteres `info:`, para mensagens de status informativas, e `data:`, para dados retornados sobre um serviço. Porém, você pode instruir a CLI do Azure a retornar informações mais detalhadas usando o parâmetro `--verbose` ou `-v`. Isso retornará informações adicionais prefixadas pela cadeia de caracteres `verbose:`.

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

Observe que as informações `verbose:` parecem ser dados formatados em JSON. Você poderá usar o parâmetro `--json` para retornar as informações no formato JSON, se estiver trabalhando com utilitários que compreendam JSON nativamente, como [jsawk](https://github.com/micha/jsawk) e [jq](http://stedolan.github.io/jq/). Por exemplo:

	azure site list --json | jsawk -n 'out(this.Name)' | xargs -L 1 azure site delete -q

O comando acima recupera uma lista de sites como JSON e, em seguida, usa jsawk para recuperar os nomes dos sites e, finalmente, usa xargs para executar um comando site delete para cada site, passando o nome do site como um parâmetro.

>[AZURE.NOTE]O parâmetro `--json` bloqueia a geração das informações de status ou de dados (cadeias de caracteres prefixadas por `info:` e `data:`). Por exemplo, se o parâmetro `--json` for usado com o `azure site create`, nenhuma saída será retornada, uma vez que esse comando não retorna nenhum dado diferente de `info:`.

### Trabalhando com erros

Embora a CLI do Azure registre informações de erro para STDERR, informações adicionais sobre erros também podem ser registradas em log em um arquivo **azure.err** no diretório no qual o script foi executado. Se as informações forem armazenadas nesse arquivo, o seguinte será gravado para STDOUT:

	info:    Error information has been recorded to azure.err

### Status da saída

Alguns dos comandos da CLI do Azure não retornam um status da saída diferente de zero se os parâmetros necessários estiverem ausentes. Em vez disso, eles solicitarão uma entrada do usuário. Por exemplo, ao usar o comando `azure site create` para criar um novo site, se nenhum nome de site ou parâmetro `--location` for especificado, você deverá fornecer esses valores.

Se estiver escrevendo um script que dependa do status da saída, verifique se os comandos da CLI do Azure que você está usando não solicitam uma entrada do usuário.

<a id="additional-resources"></a>

## Recursos adicionais

* [Lista de comandos de Gerenciamento de Serviços detalhados][Using the Azure CLI]

* [Usando a CLI do Azure para Mac, Linux e Windows](cli-cli-azure-resource-manager)

* [Usando a CLI do Azure com o Gerenciamento de Recursos][cliarm]

* Para saber mais sobre a CLI do Azure, baixar o código fonte, relatar problemas ou contribuir com o projeto, visite o [Repositório GitHub da CLI do Azure](https://github.com/azure/azure-xplat-cli).

* Se tiver problemas ao usar a CLI do Azure ou o Azure, visite os [Fóruns do Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).

* Para saber mais sobre o Azure, confira [http://azure.microsoft.com/](http://azure.microsoft.com).




[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert

[Azure Web Site]: ../media/freetrial.png
[select a preview feature]: ../media/antares-iaas-preview-02.png
[select subscription]: ../media/antares-iaas-preview-03.png
[free-trial]: http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E
[advanced-bash]: http://tldp.org/LDP/abs/html/
[script]: http://en.wikipedia.org/wiki/Shell_script
[batch]: http://technet.microsoft.com/library/bb490890.aspx
[cliarm]: xplat-cli-azure-resource-manager.md
[portal]: https://manage.windowsazure.com
[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[Using the Azure CLI]: virtual-machines-command-line-tools.md

<!---HONumber=July15_HO1-->