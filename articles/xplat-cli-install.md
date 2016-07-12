<properties
	pageTitle="Instalar a interface de linha de comando do Azure | Microsoft Azure"
	description="Instalar a CLI (Interface de Linha de Comando) do Azure para Mac, Linux e Windows para começar a usar os serviços do Azure"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="virtual-machines-linux,virtual-network,storage,azure-resource-manager"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/23/2016"
	ms.author="danlep"/>
    
# Instalar a CLI do Azure

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [CLI do Azure](xplat-cli-install.md)

Instale rapidamente a CLI (Interface de linha de comando) do Azure para usar um conjunto de comandos de software livre baseados em shell para criar e gerenciar os recursos do Microsoft Azure. Você tem várias opções de instalação: instalar a partir de um pacote npm (requer Node.js e npm), usar um dos pacotes de instalador fornecidos para diferentes sistemas operacionais ou instalar a CLI do Azure como um contêiner em um host do Docker. Para obter mais opções e um histórico, consulte o repositório do projeto no [GitHub](https://github.com/azure/azure-xplat-cli).


Quando a CLI do Azure estiver instalada, você poderá [conectá-la à sua assinatura do Azure](xplat-cli-connect.md) e executar os comandos **azure** na sua interface de linha de comando (Lote, Terminal, Prompt de comando e assim por diante) para trabalhar com seus recursos do Azure.



## Instalar um pacote npm

Para instalar a CLI a partir de um pacote npm, você precisará do Node. js e do npm mais recentes instalados em seu sistema. Em seguida, para instalar a CLI do Azure, use o seguinte comando. (Nas distribuições Linux, talvez você precise usar **sudo** para executar o comando __npm__ com êxito.)

	npm install azure-cli -g

> [AZURE.NOTE]Se você precisar instalar ou atualizar o Node.js e npm no seu sistema operacional, consulte a documentação em [Nodejs.org](https://nodejs.org/en/download/package-manager/). Recomendamos que você instale a versão mais recente do Node.js LTS (4.x). Se você usar uma versão mais antiga, poderá obter erros de instalação.

## Usar um instalador

Os seguintes pacotes de instalador estão disponíveis também para download:


* [Instalador do OS X][mac-installer]

* [Windows installer][windows-installer]

* [Arquivo tar de Linux][linux-installer] (requer o Node. js e npm) - instale executando `sudo npm install -g <path to downloaded tar file>`


## Usar um contêiner do Docker

Em um host Docker, execute:

```
docker run -it microsoft/azure-cli
```

## Executar comandos da CLI do Azure
Quando a CLI do Azure estiver instalada, você poderá executar o comando **azure** na sua interface do usuário de linha de comando (Lote, Terminal, Prompt de comando, e assim por diante). Por exemplo, para executar o comando de ajuda, digite o seguinte:

```
azure help
```
> [AZURE.NOTE]Em algumas distribuições Linux, você poderá receber um erro, /usr/bin/env: ‘node’: arquivo ou diretório inexistente, isso vem de instalações recentes do nodejs que estão sendo instaladas em /usr/bin/nodejs. Para corrigir esse erro, crie um link simbólico para /usr/bin/node executando o comando a seguir

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Para ver a versão da CLI do Azure que você instalou, digite o seguinte:

```
azure --version
```

Agora você está pronto! Para acessar todos os comandos da CLI para trabalhar com seus próprios recursos, [conecte-se à sua assinatura do Azure por meio da CLI do Azure](xplat-cli-connect.md).

>[AZURE.NOTE] Quando você usar a CLI do Azure versão 0.9.20 ou posterior, você verá uma mensagem perguntando se você deseja permitir que a Microsoft colete informações sobre como usar a CLI. A participação é voluntária. Se você optar por participar, você poderá parar a qualquer momento executando `azure telemetry --disable`. Para habilitar a participação a qualquer momento, execute `azure telemetry --enable`.


## Atualizar a CLI

Com frequência, a Microsoft lança versões atualizadas da CLI do Azure. Reinstale a CLI usando o instalador de seu sistema operacional ou, se você tiver o Node.js e o npm mais recentes instalados, atualize-os digitando o seguinte (em distribuições Linux, talvez você precise usar **sudo**).

```
npm update -g azure-cli
```

## Ativar o recurso auto-completar com TAB

Há suporte para o recurso auto-completar de comandos da CLI para Mac e Linux.

Para habilitá-lo no zsh, execute:

```
echo '. <(azure --completion)' >> .zshrc
```

Para habilitá-lo no bash, execute:

```
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## Próximas etapas 

* [Conecte-se a partir da CLI à sua assinatura do Azure](xplat-cli-connect.md) para criar e gerenciar recursos do Azure.

* Para saber mais sobre a CLI do Azure, baixar o código-fonte, relatar problemas ou colaborar com o projeto, visite o [Repositório GitHub para a CLI do Azure](https://github.com/azure/azure-xplat-cli).

* Se tiver dúvidas quanto ao uso da CLI do Azure ou do Azure, visite os [Fóruns do Azure](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=azurescripting).

* Para os sistemas Linux, você também pode instalar a CLI do Azure com a compilação do [código-fonte](http://aka.ms/linux-azure-cli). Para saber mais sobre como compilar por meio do código-fonte, confira o arquivo INSTALL incluído no arquivo morto de origem.

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=windowsazurexplatcli&mode=new
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md

<!---HONumber=AcomDC_0629_2016-->