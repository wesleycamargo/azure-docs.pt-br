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
	ms.date="04/07/2016"
	ms.author="danlep"/>

# Instalar a CLI do Azure

Instale rapidamente a CLI (Interface de linha de comando) do Azure para usar um conjunto de comandos de software livre baseados em shell para criar e gerenciar os recursos do Microsoft Azure. Você tem várias opções de instalação: use um dos pacotes de instalação fornecidos para diferentes sistemas operacionais, instale de um pacote npm ou instale a CLI do Azure como um contêiner em um host do Docker. Para obter mais opções e um histórico, consulte o repositório do projeto no [GitHub](https://github.com/azure/azure-xplat-cli).


Quando a CLI do Azure estiver instalada, você poderá [conectá-la à sua assinatura do Azure](xplat-cli-connect.md) e executar os comandos **azure** na sua interface de linha de comando (Bash, Terminal, Prompt de comando e assim por diante) para trabalhar com seus recursos do Azure.


## Usar um instalador

Os seguintes pacotes de instalador estão disponíveis:

* [Windows installer][windows-installer]

* [Instalador do OS X](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Instalador do Linux][linux-installer]


## Instalar um pacote npm

Como alternativa, se o último Node.js e npm já estiverem instalados em seu sistema, execute o comando a seguir para instalar o pacote da CLI do Azure. (Nas distribuições Linux, talvez você precise usar **sudo** para executar o comando __npm__ com êxito.)

	npm install azure-cli -g

> [AZURE.NOTE]Se você precisar instalar ou atualizar o Node.js e npm para seu sistema operacional, consulte a documentação em [Nodejs.org](https://nodejs.org/en/download/package-manager/). Recomendamos que você instale a versão mais recente do Node.js LTS (4.x). Se você usar uma versão mais antiga, poderá obter erros de instalação.


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

Agora você está pronto! Para acessar todos os comandos da CLI para trabalhar com seus próprios recursos, [conecte-se à sua assinatura do Azure por meio da CLI do Azure](xplat-cli-connect.md).


## Atualizar a CLI

Com frequência, a Microsoft lança versões atualizadas da CLI do Azure. Reinstale a CLI usando o instalador de seu sistema operacional ou, se você tiver o Node.js e o npm mais recentes instalados, atualize-os digitando o seguinte (em distribuições Linux, talvez você precise usar **sudo**).

```
npm update -g azure-cli
```

## Próximas etapas 

* [Conecte-se da CLI à sua assinatura do Azure](xplat-cli-connect.md) para criar e gerenciar recursos do Azure.

* Para saber mais sobre a CLI do Azure, baixar o código-fonte, relatar problemas ou colaborar com o projeto, visite o [Repositório GitHub para a CLI do Azure](https://github.com/azure/azure-xplat-cli).

* Se tiver dúvidas quanto ao uso da CLI do Azure ou do Azure, visite os [Fóruns do Azure](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=azurescripting).

* Para os sistemas Linux, você também pode instalar a CLI do Azure com a compilação do [código-fonte](http://aka.ms/linux-azure-cli). Para saber mais sobre como compilar por meio do código-fonte, confira o arquivo INSTALL incluído no arquivo morto de origem.

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=windowsazurexplatcli&mode=new
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md

<!---HONumber=AcomDC_0420_2016-->