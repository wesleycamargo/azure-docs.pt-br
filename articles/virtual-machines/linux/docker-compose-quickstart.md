---
title: Usar o Docker Compose em uma VM Linux no Azure | Microsoft Docs
description: "Como usar o Docker e o Compose em máquinas virtuais Linux com a CLI do Azure"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: 9f8c9a32be9b889ced4fdc7065acd09e6700afd5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Introdução ao Docker e Compose para definir e executar um aplicativo de vários contêineres no Azure
Com o [Compose](http://github.com/docker/compose), você usa um arquivo de texto simples para definir um aplicativo que consiste em vários contêineres do Docker. Em seguida, você acelera seu aplicativo com um único comando que faz tudo que é necessário para implantar o ambiente definido. Por exemplo, este artigo mostra como configurar rapidamente um blog WordPress com um banco de dados SQL MariaDB de back-end em uma VM Ubuntu. Você também pode usar o Redigir para configurar aplicativos mais complexos.


## <a name="set-up-a-linux-vm-as-a-docker-host"></a>Configurar uma VM Linux como um host do Docker
Você pode usar vários procedimentos do Azure e imagens disponíveis ou modelos do Resource Manager no Azure Marketplace para criar uma VM do Linux e configurá-la como um host do Docker. Por exemplo, veja [Uso da extensão de VM do Docker para implantar seu ambiente](dockerextension.md) para criar rapidamente uma VM do Ubuntu com a extensão de VM do Docker do Azure usando um [modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Quando você usa a extensão da VM do Docker, sua VM é configurada automaticamente como um host do Docker e o Redigir já está instalado.


### <a name="create-docker-host-with-azure-cli-20"></a>Criar host do Docker com a CLI do Azure 2.0
Instale a [CLI 2.0 do Azure](/cli/azure/install-az-cli2) mais recente e faça logon em uma conta do Azure usando [az login](/cli/azure/#az_login).

Primeiro, crie um grupo de recursos para seu ambiente do Docker com [az group create](/cli/azure/group#az_group_create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Em seguida, implante uma VM com [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create), o que inclui a extensão da VM do Docker do Azure [deste modelo do Azure Resource Manager no GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Mediante solicitação, forneça seus próprios valores exclusivos para *newStorageAccountName*, *adminUsername*, *adminPassword* e *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Leva alguns minutos para a conclusão da implantação.


## <a name="verify-that-compose-is-installed"></a>Verificar se o Compose está instalado
Para exibir detalhes de sua VM, incluindo o nome DNS, use [az vm show](/cli/azure/vm#az_vm_show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

SSH para seu novo host do Docker. Fornece seu próprio nome de usuário e nome DNS das etapas anteriores:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Para verificar se o Redigir está instalado na VM, execute o seguinte comando:

```bash
docker-compose --version
```

Você ver uma saída semelhante a *docker-compose 1.6.2, build 4d72027*.

> [!TIP]
> Se você usou outro método para criar um host do Docker e precisa instalar o Redigir por conta própria, veja a [documentação do Redigir](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="create-a-docker-composeyml-configuration-file"></a>Criar um arquivo de configuração docker-compose.yml
Em seguida, você criará um arquivo `docker-compose.yml` , que é apenas um arquivo de configuração de texto, para definir os contêineres de Docker a serem executados na VM. O arquivo especifica a imagem a ser executada em cada contêiner (ou pode ser um build de um Dockerfile), as variáveis de ambiente e as dependências necessárias, as portas e os links entre contêineres. Para obter detalhes sobre a sintaxe do arquivo yml, veja a [referência de arquivo do Redigir](https://docs.docker.com/compose/compose-file/).

Crie um arquivo *docker-compose.yml*. Use seu editor de texto favorito para adicionar alguns dados ao arquivo. O exemplo a seguir cria o arquivo com um prompt para `sensible-editor` escolher um editor que você deseja usar:

```bash
sensible-editor docker-compose.yml
```

Cole o exemplo a seguir em seu arquivo do Docker Compose. Essa configuração usa imagens do [Registro do DockerHub](https://registry.hub.docker.com/_/wordpress/) para instalar o WordPress (o sistema de blog e gerenciamento de conteúdo de software livre) e um banco de dados SQL MariaDB de back-end vinculado. Insira seu próprio *MYSQL_ROOT_PASSWORD* da seguinte maneira:

```sh
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>Iniciar os contêineres com o Compose
No mesmo diretório que seu arquivo *docker-compose.yml*, execute o comando a seguir (dependendo do seu ambiente, talvez seja necessário executar `docker-compose` usando `sudo`):

```bash
docker-compose up -d
```

Esse comando inicia os contêineres do Docker especificados em *docker-compose.yml*. Leva um ou dois minutos para concluir esta etapa. Você verá uma saída semelhante ao seguinte exemplo:

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> Não se esqueça de usar a opção **-d** na inicialização para que os contêineres sejam executados continuamente em segundo plano.


Para verificar se os contêineres estão ativos, digite `docker-compose ps`. Você deve ver algo como:

```bash
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Agora você pode conectar o WordPress diretamente na VM na porta 80. Abra um navegador da Web e digite o nome DNS da VM (como `http://mypublicdns.eastus.cloudapp.azure.com`). Agora você deve ver a tela inicial do WordPress, na qual você pode concluir a instalação e começar a usar o aplicativo.

![Tela inicial do WordPress][wordpress_start]

## <a name="next-steps"></a>Próximas etapas
* Vá para o [Guia do usuário da extensão da VM do Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md) para obter mais opções para configurar o Docker e o Redigir em sua VM do Docker. Por exemplo, uma opção é colocar o arquivo yml do Compose (convertido em JSON) diretamente na configuração da extensão da VM do Docker.
* Confira a [referência da linha de comando do Compose](http://docs.docker.com/compose/reference/) e o [guia do usuário](http://docs.docker.com/compose/) para obter mais exemplos de criação e implantação de aplicativos com vários contêineres.
* Use um modelo do Gerenciador de Recursos do Azure, seu ou da do [comunidade](https://azure.microsoft.com/documentation/templates/), para implantar uma VM do Azure com Docker e um aplicativo configurado com o Redigir. Por exemplo, o modelo [Implantar um blog WordPress com Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) usa Docker e Redigir para implantar rapidamente o WordPress com um back-end do MySQL em uma VM do Ubuntu.
* Tente integrar o Docker Compose com um cluster do Docker Swarm. Veja [Using Compose with Swarm (Uso do Redigir com o Swarm)](https://docs.docker.com/compose/swarm/) para obter os cenários.

<!--Image references-->

[wordpress_start]: media/docker-compose-quickstart/WordPress.png
