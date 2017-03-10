---
title: Usar o Docker Compose em uma VM Linux no Azure | Microsoft Docs
description: "Como usar o Docker e o Compose em máquinas virtuais Linux com a CLI do Azure"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 0240ecede4afc937972f81ca8536b3b05e905deb
ms.openlocfilehash: 8389b743dec50e3de0e13e023ef43e4f706eb477
ms.lasthandoff: 03/01/2017


---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Introdução ao Docker e Compose para definir e executar um aplicativo de vários contêineres no Azure
Com o [Compose](http://github.com/docker/compose), você usa um arquivo de texto simples para definir um aplicativo que consiste em vários contêineres do Docker. Em seguida, você acelera seu aplicativo com um único comando que faz tudo que é necessário para implantar o ambiente definido. Por exemplo, este artigo mostra como configurar rapidamente um blog WordPress com um banco de dados SQL MariaDB de back-end em uma VM Ubuntu. Você também pode usar o Redigir para configurar aplicativos mais complexos.

## <a name="step-1-set-up-a-linux-vm-as-a-docker-host"></a>Etapa 1: Configurar uma VM do Linux como um host do Docker
Você pode usar vários procedimentos do Azure e imagens disponíveis ou modelos do Resource Manager no Azure Marketplace para criar uma VM do Linux e configurá-la como um host do Docker. Por exemplo, veja [Uso da extensão de VM do Docker para implantar seu ambiente](virtual-machines-linux-dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para criar rapidamente uma VM do Ubuntu com a extensão de VM do Docker do Azure usando um [modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Quando você usa a extensão da VM do Docker, sua VM é configurada automaticamente como um host do Docker e o Redigir já está instalado. Você pode criar uma VM e usar a extensão de VM do Docker usando uma das seguintes versões da CLI:

- [CLI 2.0 do Azure](#azure-cli-20) – nossa última geração de CLI para o modelo de implantação de gerenciamento de recursos
- [CLI do Azure 1.0](#azure-cli-10) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos

### <a name="azure-cli-20"></a>CLI 2.0 do Azure
Instale a [CLI 2.0 do Azure](/cli/azure/install-az-cli2) mais recente e faça logon em uma conta do Azure usando [az login](/cli/azure/#login).

Primeiro, crie um grupo de recursos para seu ambiente do Docker com [az group create](/cli/azure/group#create). O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `West US`:

```azurecli
az group create --name myResourceGroup --location westus
```

Em seguida, implante uma VM com [az group deployment create](/cli/azure/group/deployment#create), o que inclui a extensão da VM do Docker do Azure a partir [deste modelo do Azure Resource Manager no Github](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Forneça seus próprios valores para `newStorageAccountName`, `adminUsername`, `adminPassword` e `dnsNameForPublicIP`:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"newStorageAccountName": {"value": "mystorageaccount"},
    "adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "dnsNameForPublicIP": {"value": "mypublicdns"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Leva alguns minutos para a conclusão da implantação. Após a conclusão da implantação, [vá para a próxima etapa](#step-2-verify-that-compose-is-installed) a fim de usar SSH em sua VM. 

Como opção, para, em vez disso, retornar o controle ao prompt e permitir que a implantação continue em segundo plano, adicione o sinalizador `--no-wait` ao comando anterior. Esse processo permite que você execute outro trabalho na CLI enquanto a implantação continua por alguns minutos. Você pode exibir detalhes sobre o status de host do Docker usando o comando [az vm show](/cli/azure/vm#show). O exemplo a seguir verifica o status da VM denominada `myDockerVM` (o nome padrão do modelo – não altere esse nome) no grupo de recursos denominado `myResourceGroup`:

```azurecli
az vm show --resource-group myResourceGroup --name myDockerVM \
  --query [provisioningState] --output tsv
```

Quando esse comando retornar `Succeeded`, a implantação terá sido concluída e você poderá usar SSH na VM na etapa a seguir.

### <a name="azure-cli-10"></a>CLI 1.0 do Azure
Instale a versão mais recente da [CLI do Azure 1.0](../xplat-cli-install.md) e faça logon em uma conta do Azure. Certifique-se de que você esteja usando o modo Resource Manager para criar a VM (`azure config mode arm`).

O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `West US` e implanta uma VM com a extensão de VM do Docker do Azure. Um [modelo do Azure Resource Manager do Github](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu) é usado para implantar o ambiente:

```azurecli
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

A CLI do Azure retorna você ao prompt apenas após alguns segundos, mas o host do Docker ainda está sendo criado e configurado. Leva alguns minutos para a conclusão da implantação. Você pode exibir detalhes sobre o status de host de Docker usando o comando `azure vm show`. O exemplo a seguir verifica o status da VM denominada `myDockerVM` (o nome padrão do modelo – não altere esse nome) no grupo de recursos denominado `myResourceGroup`. Insira o nome do grupo de recursos que você criou na etapa anterior:

```azurecli
azure vm show --resource-group myResourceGroup --name myDockerVM
```

## <a name="step-2-verify-that-compose-is-installed"></a>Etapa 2: Verificar se o Compose está instalado
Depois que a implantação for concluída, faça a conexão SSH com seu novo host do Docker usando o nome DNS que você forneceu durante a implantação. Você pode usar `azure vm show -g myResourceGroup -n myDockerVM` (CLI 1.0 do Azure) ou `az vm show -g myResourceGroup -n myDockerVM -d --query [fqdns] -o tsv` (CLI 2.0 do Azure) para exibir detalhes de sua VM, incluindo o nome DNS.

Para verificar se o Redigir está instalado na VM, execute o seguinte comando:

```bash
docker-compose --version
```

Você verá uma saída semelhante a `docker-compose 1.6.2, build 4d72027`.

> [!TIP]
> Se você usou outro método para criar um host do Docker e precisa instalar o Redigir por conta própria, veja a [documentação do Redigir](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="step-3-create-a-docker-composeyml-configuration-file"></a>Etapa 3: Criar um arquivo de configuração docker-compose.yml
Em seguida, você criará um arquivo `docker-compose.yml` , que é apenas um arquivo de configuração de texto, para definir os contêineres de Docker a serem executados na VM. O arquivo especifica a imagem a ser executada em cada contêiner (ou pode ser um build de um Dockerfile), as variáveis de ambiente e as dependências necessárias, as portas e os links entre contêineres. Para obter detalhes sobre a sintaxe do arquivo yml, veja a [referência de arquivo do Redigir](http://docs.docker.com/compose/yml/).

Crie o arquivo `docker-compose.yml` conforme descrito a seguir:

```bash
touch docker-compose.yml
```

Use seu editor de texto favorito para adicionar alguns dados ao arquivo. O exemplo a seguir usa o editor `vi`:

```bash
vi docker-compose.yml
```

Cole o exemplo a seguir em seu arquivo de texto. Essa configuração usa imagens do [Registro do DockerHub](https://registry.hub.docker.com/_/wordpress/) para instalar o WordPress (o sistema de blog e gerenciamento de conteúdo de software livre) e um banco de dados SQL MariaDB de back-end vinculado. Insira seu próprio `MYSQL_ROOT_PASSWORD` da seguinte maneira:

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

## <a name="step-4-start-the-containers-with-compose"></a>Etapa 4: iniciar os contêineres com a Redação
No mesmo diretório que seu arquivo `docker-compose.yml`, execute o comando a seguir (dependendo do seu ambiente, talvez seja necessário executar `docker-compose` usando `sudo`):

```bash
docker-compose up -d
```

Esse comando inicia os contêineres do Docker especificados em `docker-compose.yml`. Leva um ou dois minutos para concluir esta etapa. Você verá uma saída semelhante ao seguinte exemplo:

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> Não se esqueça de usar a opção **-d** na inicialização para que os contêineres sejam executados continuamente em segundo plano.


Para verificar se os contêineres estão ativos, digite `docker-compose ps`. Você deve ver algo como:

```bash
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

Agora você pode conectar o WordPress diretamente na VM na porta 80. Abra um navegador da Web e digite o nome DNS da VM (como `http://myresourcegroup.westus.cloudapp.azure.com`). Agora você deve ver a tela inicial do WordPress, na qual você pode concluir a instalação e começar a usar o aplicativo.

![Tela inicial do WordPress][wordpress_start]

## <a name="next-steps"></a>Próximas etapas
* Vá para o [Guia do usuário da extensão da VM do Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md) para obter mais opções para configurar o Docker e o Redigir em sua VM do Docker. Por exemplo, uma opção é colocar o arquivo yml do Compose (convertido em JSON) diretamente na configuração da extensão da VM do Docker.
* Confira a [referência da linha de comando do Compose](http://docs.docker.com/compose/reference/) e o [guia do usuário](http://docs.docker.com/compose/) para obter mais exemplos de criação e implantação de aplicativos com vários contêineres.
* Use um modelo do Gerenciador de Recursos do Azure, seu ou da do [comunidade](https://azure.microsoft.com/documentation/templates/), para implantar uma VM do Azure com Docker e um aplicativo configurado com o Redigir. Por exemplo, o modelo [Implantar um blog WordPress com Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) usa Docker e Redigir para implantar rapidamente o WordPress com um back-end do MySQL em uma VM do Ubuntu.
* Tente integrar o Redigir do Docker com um cluster [Docker Swarm](virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) . Veja [Using Compose with Swarm (Uso do Redigir com o Swarm)](https://docs.docker.com/compose/swarm/) para obter os cenários.

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png

