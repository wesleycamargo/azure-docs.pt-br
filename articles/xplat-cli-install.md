---
title: Instalar a interface de linha de comando do Azure | Microsoft Docs
description: Instalar a CLI (Interface de Linha de Comando) do Azure para Mac, Linux e Windows para começar a usar os serviços do Azure
editor: ''
manager: timlt
documentationcenter: ''
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management

ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: command-line-interface
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: rasquill

---
# <a name="install-the-azure-cli"></a>Instalar a CLI do Azure
> [!div class="op_single_selector"]
> * [PowerShell](powershell-install-configure.md)
> * [CLI do Azure](xplat-cli-install.md)
> 
> 

Instale rapidamente a CLI (Interface de linha de comando) do Azure para usar um conjunto de comandos de software livre baseados em shell para criar e gerenciar os recursos do Microsoft Azure. Você tem várias opções para instalar essas ferramentas de plataforma cruzada em seu computador: 

* **pacote npm** – Execute o npm (o gerenciador de pacotes para JavaScript) para instalar o pacote mais recente da CLI do Azure em sua distribuição do Linux ou sistema operacional. Exige o node.js e o npm em seu computador.
* **Installer** – Baixe um instalador para facilitar a instalação no Mac ou Windows.
* **Contêiner do Docker** – Comece a usar a CLI mais recente em um contêiner do Docker pronto para ser executado. Exige um host do Docker em seu computador.

Para obter mais opções e um histórico, consulte o repositório do projeto no [GitHub](https://github.com/azure/azure-xplat-cli). 

Quando a CLI do Azure estiver instalada, [conecte-a à sua assinatura do Azure](xplat-cli-connect.md) e execute os comandos **azure** na interface de linha de comando (Bash, Terminal, Prompt de comando e assim por diante) para trabalhar com os recursos do Azure.

## <a name="option-1:-install-an-npm-package"></a>Opção 1: Instalar um pacote npm
Para instalar a CLI de um pacote npm, você precisa baixar e instalar os [Node.js e o npm mais recentes](https://nodejs.org/en/download/package-manager/). Em seguida, execute **npm install** para instalar o pacote azure-cli: 

    npm install -g azure-cli

Em distribuições Linux, talvez você precise usar **sudo** para executar o comando **npm** com êxito, como segue:

    sudo npm install -g azure-cli

> [!NOTE]
> Se precisar instalar ou atualizar o Node.js e o npm em sua distribuição ou SO do Linux, é recomendável que você instale a versão mais recente do Node.js LTS (4. x). Se você usar uma versão mais antiga, poderá obter erros de instalação. 
> 
> 

Se preferir, baixe o [arquivo tar][linux-installer] mais recente do Linux para o pacote npm localmente. Em seguida, instale o pacote npm baixado da seguinte maneira (em distribuições Linux, talvez seja necessário usar **sudo**):

    npm install -g <path to downloaded tar file>

## <a name="option-2:-use-an-installer"></a>Opção 2: Usar um instalador
Se você usar um computador com Windows ou Mac, os instaladores de CLI a seguir estarão disponíveis para download:

* [Instalador do Mac OS X][mac-installer]
* [Windows MSI][windows-installer] 

> [!TIP]
> No Windows, você também pode baixar o [Web Platform Installer](https://go.microsoft.com/?linkid=9828653) para instalar a CLI. Esse instalador lhe dá a opção de instalar o SDK do Azure e ferramentas de linha de comando adicionais após a instalação da CLI. 
> 
> 

## <a name="option-3:-use-a-docker-container"></a>Opção 3: Usar um contêiner do Docker
Se você tiver configurado seu computador como um host do [Docker](https://docs.docker.com/engine/understanding-docker/) , execute a CLI mais recente do Azure em um contêiner do Docker. Execute o seguinte comando (em distribuições Linux, talvez você precise usar **sudo**):

```
docker run -it microsoft/azure-cli
```


## <a name="run-azure-cli-commands"></a>Executar comandos da CLI do Azure
Quando a CLI do Azure estiver instalada, execute o comando **azure** na interface do usuário de linha de comando (Bash, Terminal, Prompt de comando e assim por diante). Por exemplo, para executar o comando de ajuda, digite o seguinte:

```
azure help
```
> [!NOTE]
> Em algumas distribuições Linux, você poderá receber um erro semelhante a `/usr/bin/env: ‘node’: No such file or directory`. Esse erro ocorre quando instalações recentes do Node.js são instaladas em /usr/bin/nodejs. Para corrigi-lo, crie um link simbólico para /usr/bin/node executando este comando:
> 
> 

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Para ver a versão da CLI do Azure que você instalou, digite o seguinte:

```
azure --version
```

Agora você está pronto! Para acessar todos os comandos da CLI para trabalhar com seus próprios recursos, [conecte-se à sua assinatura do Azure por meio da CLI do Azure](xplat-cli-connect.md).

> [!NOTE]
> Ao usar a CLI do Azure pela primeira vez, você verá uma mensagem perguntando se deseja permitir que a Microsoft colete informações de uso. A participação é voluntária. Se optar por participar, você poderá parar a qualquer momento executando `azure telemetry --disable`. Para habilitar a participação a qualquer momento, execute `azure telemetry --enable`.
> 
> 

## <a name="update-the-cli"></a>Atualizar a CLI
Com frequência, a Microsoft lança versões atualizadas da CLI do Azure. Reinstale a CLI usando o instalador do seu sistema operacional ou execute o contêiner do Docker mais recente. Ou, se tiver o Node.js e o npm mais recentes instalados, atualize-os digitando o seguinte (em distribuições Linux, talvez você precise usar **sudo**).

```
npm update -g azure-cli
```

## <a name="enable-tab-completion"></a>Ativar o recurso auto-completar com TAB
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


## <a name="next-steps"></a>Próximas etapas
* [Conecte-se da CLI à sua assinatura do Azure](xplat-cli-connect.md) para criar e gerenciar recursos do Azure.
* Para saber mais sobre a CLI do Azure, baixar o código-fonte, relatar problemas ou colaborar com o projeto, visite o [Repositório GitHub para a CLI do Azure](https://github.com/azure/azure-xplat-cli).
* Se tiver dúvidas quanto ao uso da CLI do Azure ou do Azure, visite os [Fóruns do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).
* Se desejar, você também pode experimentar a [Visualização do Azure CLI 2.0](https://github.com/azure/azure-cli) baseada em Python.

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md



<!--HONumber=Oct16_HO2-->


