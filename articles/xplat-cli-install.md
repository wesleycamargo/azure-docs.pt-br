<properties
	pageTitle="Instalar a interface de linha de comando do Azure | Microsoft Azure"
	description="Instalar a CLI do Azure para Mac, Linux e Windows para começar a usar os serviços do Azure"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/18/2015"
	ms.author="danlep"/>

# Instalar a CLI do Azure

Este artigo descreve como instalar a Interface de Linha de Comando do Azure (CLI do Azure). A CLI do Azure fornece um conjunto de comandos de software livre baseados em shell para criar e gerenciar os recursos do Microsoft Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-include.md)]Este artigo aborda a criação e gerenciamento de recursos com o modelo de implantação do Gerenciador de Recursos ou com o modelo de implantação clássico.

A CLI do Azure é escrita em JavaScript e requer o [Node.js](https://nodejs.org). Ela é implementada usando-se o [SDK do Azure para Node](https://github.com/azure/azure-sdk-for-node) e liberada com uma licença do Apache 2.0. O repositório do projeto fica localizado em [https://github.com/azure/azure-xplat-cli](https://github.com/azure/azure-xplat-cli).

> [AZURE.NOTE]Se você já instalou a CLI do Azure, conecte-a com os recursos do Azure. Para mais informações, confira [Como se conectar à assinatura do Azure](xplat-cli-connect.md#configure).

<a id="install"></a>
## Como instalar a CLI do Azure

Existem algumas maneiras de instalar a CLI do Azure.

1. Usar um instalador
2. Instalar o Node.js e o npm, então usar o comando **npm install**
3. Executar a CLI do Azure como um contêiner do Docker

Quando a CLI do Azure estiver instalada, você poderá usar o comando **azure** na sua interface de linha de comando (Bash, Terminal, Prompt de comando, e assim por diante) para acessar os comandos da CLI do Azure.

## Usar um instalador

Os seguintes pacotes de instalador estão disponíveis:

* [Windows installer][windows-installer]

* [Instalador do OS X](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Instalador do Linux][linux-installer]


## Instalar o Node.js e npm

Se o Node.js já estiver instalado em seu sistema, use o seguinte comando para instalar a CLI do Azure:

	npm install azure-cli -g

> [AZURE.NOTE]Nas distribuições Linus, talvez você precise usar `sudo` para executar o comando __npm__ com êxito.

### Instalar o node.js e o npm em distribuições Linux que usam o gerenciamento de pacotes [dpkg](http://en.wikipedia.org/wiki/Dpkg)
Em geral, essas distribuições usam a [ferramenta de empacotamento avançada (apt)](http://en.wikipedia.org/wiki/Advanced_Packaging_Tool) ou outras ferramentas com base no `.deb`formato do pacote. Alguns exemplos são Ubuntu e Debian.

A maioria das distribuições mais recentes exige a instalação de **nodejs-legacy** para obter uma ferramenta **npm** corretamente configurada para instalar a CLI do Azure. O código a seguir mostra os comandos que instalam o **npm** corretamente no Ubuntu 14.04.

	sudo apt-get install nodejs-legacy
	sudo apt-get install npm
	sudo npm install -g azure-cli

Algumas das distribuições mais antigas, como o Ubuntu 12.04 exigem instalando a distribuição binária atual do node.js. O código a seguir mostra como fazer isso instalando e usando **curl**.

>[AZURE.NOTE]Os comandos aqui são tirados das instruções de instalação Joyent encontradas [aqui](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager). No entanto, ao usar o **sudo** como um destino de pipe, você sempre deve verificar os scripts que está instalando e validar se eles fazem exatamente o que você espera antes de executá-los pelo **sudo**. Quanto mais poder, mais responsabilidade.

	sudo apt-get install curl
	curl -sL https://deb.nodesource.com/setup | sudo bash -
	sudo apt-get install -y nodejs
	sudo npm install -g azure-cli

### Instalar o node.js e o npm em distribuições Linux que usam o gerenciamento de pacotes [rpm](http://en.wikipedia.org/wiki/RPM_Package_Manager)

Instalar o node.js em distribuições baseadas em RPM requer a habilitação do repositório EPEL. O código a seguir mostra as práticas recomendadas para instalação no CentOS 7. (Observe que, na primeira linha abaixo, o '-' (hífen) é importante!)

	su -
	yum update [enter]
	yum upgrade –y [enter]
	yum install epel-release [enter]
	yum install nodejs [enter]
	yum install npm [enter]
	npm install -g azure-cli  [enter]

### Instalar o node.js e npm no Windows e Mac OS X

Você pode instalar o node. js e o npm no Windows e no OS X com os instaladores de [Nodejs.org](https://nodejs.org/download/). Talvez seja necessário reiniciar o computador para concluir a instalação. Verificar se o node e o npm foram instalados corretamente, abra o prompt de comando e digitar

	npm -v

Se for exibida a versão do npm instalado, você pode ir em frente e instalar a CLI do Azure com

	npm install -g azure-cli

No final da instalação, você verá algo semelhante ao seguinte:

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

>[AZURE.NOTE]Para os sistemas Linux, você também pode instalar a CLI do Azure com a compilação do [código-fonte](http://go.microsoft.com/fwlink/?linkid=253472). Para saber mais sobre como compilar a partir do código-fonte, confira o arquivo INSTALL incluído no arquivo morto.

## Usar um contêiner do Docker

Em um host Docker, execute: ```
	docker run -it microsoft/azure-cli
```

## Executar comandos da CLI do Azure

Quando a CLI do Azure estiver instalada, você poderá usar o comando **azure** na sua interface do usuário de linha de comando (Bash, Terminal, Prompt de comando, e assim por diante) para acessar os comandos da CLI do Azure. Por exemplo, para executar o comando de ajuda no Windows, inicie uma janela Comando e digite o seguinte:

```
	c:> azure help
```

Agora você está pronto! Em seguida, você pode [conectar-se à sua assinatura do Azure na CLI do Azure](xplat-cli-connect.md) e começar a usar os comandos **azure**.


<a id="additional-resources"></a>
## Recursos adicionais

* [Usar a CLI do Azure com os comandos de Gerenciamento de Recursos][cliarm]

* [Usar a CLI do Azure com os comandos de Gerenciamento de Serviços (clássico)][cliasm]

* Para saber mais sobre a CLI do Azure, baixar o código-fonte, relatar problemas ou colaborar com o projeto, visite o [Repositório GitHub para a CLI do Azure](https://github.com/azure/azure-xplat-cli).

* Se tiver problemas ao usar a CLI do Azure ou o Azure, visite os [Fóruns do Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).



[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[linux-installer]: http://go.microsoft.com/fwlink/?linkid=253472
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=Sept15_HO4-->