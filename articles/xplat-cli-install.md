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
	ms.date="12/29/2015"
	ms.author="danlep"/>

# Instalar a CLI do Azure

Instale rapidamente a CLI (Interface de linha de comando) do Azure para usar um conjunto de comandos de software livre baseados em shell para criar e gerenciar os recursos do Microsoft Azure. Use um dos pacotes de instalação fornecidos para instalar a CLI do Azure no seu sistema operacional, instale a CLI usando Node.js e **npm** ou instale a CLI do Azure como um contêiner em um host Docker. Para obter mais opções e um histórico, consulte o repositório do projeto no [GitHub](https://github.com/azure/azure-xplat-cli).

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-both-include.md)]

Quando a CLI do Azure estiver instalada, você poderá [conectá-la à sua assinatura do Azure](xplat-cli-connect.md) e executar os comandos **azure** na sua interface de linha de comando (Bash, Terminal, Prompt de comando e assim por diante) para trabalhar com seus recursos do Azure.




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

>[AZURE.NOTE]Os comandos são tirados das instruções de instalação encontradas [aqui](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager). No entanto, ao usar o **sudo** como um destino de pipe, você sempre deve verificar os scripts que está instalando e validar se eles fazem exatamente o que você espera antes de executá-los pelo **sudo**. Quanto mais poder, mais responsabilidade.

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

Você pode instalar o node. js e o npm no Windows e no OS X com os instaladores de [Nodejs.org](https://nodejs.org/en/download/). Talvez seja necessário reiniciar o computador para concluir a instalação. Verifique se o node e o npm foram instalados corretamente abrindo uma janela de Comando ou Terminal e digitando

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

Em um host Docker, execute:

```
docker run -it microsoft/azure-cli
```

## Executar comandos da CLI do Azure
Quando a CLI do Azure estiver instalada, você poderá executar o comando **azure** na sua interface do usuário de linha de comando (Bash, Terminal, Prompt de comando, e assim por diante). Por exemplo, para executar o comando de ajuda, digite o seguinte:

```
azure help
```

Para ver a versão da CLI do Azure que você instalou, digite o seguinte:

```
azure --version
```

Agora você está pronto! Para acessar todos os comandos da CLI para trabalhar com seus próprios recursos, [conecte à sua assinatura do Azure da CLI do Azure](xplat-cli-connect.md).

## Atualizar a CLI

Com frequência, a Microsoft lança versões atualizadas da CLI do Azure. Reinstale a CLI usando o instalador de seu sistema operacional ou, se o Node. js e o npm estiverem instalados, atualize digitando o seguinte (em distribuições Linux, talvez você precise usar **sudo**).

```
npm upgrade -g azure-cli
```

## Recursos adicionais

* [Usar a CLI do Azure com o Gerenciador de Recursos do Azure][cliarm]

* [Usar a CLI do Azure com o Gerenciamento de Serviços do Azure][cliasm]

* Para saber mais sobre a CLI do Azure, baixar o código-fonte, relatar problemas ou colaborar com o projeto, visite o [Repositório GitHub para a CLI do Azure](https://github.com/azure/azure-xplat-cli).

* Se tiver dúvidas quanto ao uso da CLI do Azure ou o Azure, visite os [Fóruns do Azure](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=azurescripting).



[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[linux-installer]: http://go.microsoft.com/fwlink/?linkid=253472
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0107_2016-->