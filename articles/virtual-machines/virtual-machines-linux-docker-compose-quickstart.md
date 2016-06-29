<properties
   pageTitle="Docker e Redigir em uma máquina virtual | Microsoft Azure"
   description="Introdução rápida ao trabalho com a Redação e o Docker em máquinas virtuais do Linux no Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/10/2016"
   ms.author="danlep"/>

# Introdução ao Docker e Redigir para definir e executar um aplicativo de contêiner múltiplos em uma máquina virtual do Azure

Comece a usar o Docker e a [Redação](http://github.com/docker/compose) para definir e executar um aplicativo complexo em uma máquina virtual do Linux no Azure. Com o Redigir (o sucessor do *Fig*), use um arquivo de texto simples para definir um aplicativo que consiste em vários contêineres do Docker. Em seguida, você acelera seu aplicativo com um único comando que faz tudo que é necessário para executá-lo na VM.

Por exemplo, este artigo mostra como configurar rapidamente um blog WordPress com um banco de dados SQL MariaDB de back-end em uma VM Ubuntu, mas você também pode usar o Compose para configurar aplicativos mais complexos.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


Se você não estiver familiarizado com o Docker e contêineres, consulte o [Quadro de comunicações de nível elevado do Docker](https://azure.microsoft.com/documentation/videos/docker-high-level-whiteboard/).

## Etapa 1: Configurar uma VM do Linux como um host do Docker

Você pode usar vários procedimentos do Azure e imagens disponíveis ou modelos do Gerenciador de Recursos no Azure Markeplace para criar uma VM do Linux e configurá-la como um host do Docker. Por exemplo, consulte [Usando a Extensão da VM do Docker para implantar seu ambiente](virtual-machines-linux-dockerextension.md) para ter um procedimento rápido para criar uma VM do Ubuntu com a extensão da VM do Docker do Azure usando um [modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Quando você usa a extensão da VM do Docker, sua VM é configurada automaticamente como um host do Docker e o Redigir já está instalado. O exemplo neste artigo mostra como usar a [interface de linha de comando do Azure para o Mac, Linux e Windows](../xplat-cli-install.md) (a CLI do Azure) no modo Gerenciamento de Recursos para criar a VM.

## Etapa 2: Verificar se o Compose está instalado

Após a VM Linux estar em execução com o Docker, conecte-se a ela do seu computador cliente usando SSH.

Para testar sua instalação do Compose na VM, execute o comando a seguir.

```
$ docker-compose --version
```

Você verá uma saída semelhante para `docker-compose 1.6.2, build 4d72027`.

>[AZURE.TIP] Se você usou outro método para criar um host do Docker e precisar instalar o Compose por conta própria, consulte a [documentação do Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## Etapa 3: Criar um arquivo de configuração docker-compose.yml

Em seguida, você criará um arquivo `docker-compose.yml`, que é apenas um arquivo de configuração de texto, para definir os contêineres de Docker a serem executados na VM. O arquivo especifica a imagem a ser executada em cada contêiner (ou pode ser uma compilação de um Dockerfile), as variáveis de ambiente e as dependências, as portas, os links entre contêineres e assim por diante. Para obter detalhes sobre a sintaxe do arquivo yml, consulte a [referência do arquivo Compose](http://docs.docker.com/compose/yml/).

Crie um diretório de trabalho na sua VM e use seu editor de texto favorito para criar `docker-compose.yml`. Para testar um exemplo simples para provar o conceito, copie o seguinte texto para o arquivo. Essa configuração usa imagens do [Registro do DockerHub](https://registry.hub.docker.com/_/wordpress/) para instalar o WordPress (o sistema de blog e gerenciamento de conteúdo de software livre) e um banco de dados SQL MariaDB de back-end vinculado.

 ```
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

## Etapa 4: iniciar os contêineres com a Redação

No diretório de trabalho de sua VM, basta executar o comando a seguir. (Dependendo de seu ambiente, talvez seja necessário executar `docker-compose` usando `sudo`.)

```
$ docker-compose up -d

```

Isso inicia os contêineres do Docker especificados em `docker-compose.yml`. Você verá uma saída semelhante a:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
```

>[AZURE.NOTE] Não se esqueça de usar a opção **-d** na inicialização para que os contêineres sejam executados continuamente em segundo plano.

Para verificar se os contêineres estão ativos, digite `docker-compose ps`. Você deve ver algo como:

```
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

Agora você pode conectar o WordPress diretamente na VM na porta 80. Se você usou um modelo do Gerenciador de Recursos para criar a VM, tente conectar o `http://<dnsname>.<region>.cloudapp.azure.com` ou se criou a VM usando o modelo de implantação clássico, tente conectar o `http://<cloudservicename>.cloudapp.net`. Agora você deve ver a tela inicial do WordPress, na qual você pode concluir a instalação e começar a usar o aplicativo.

![Tela inicial do WordPress][wordpress_start]


## Próximas etapas

* Vá para o [Guia do usuário da extensão da VM do Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md) para obter mais opções para configurar o Docker e o Compose em sua VM do Docker. Por exemplo, uma opção é colocar o arquivo yml do Compose (convertido em JSON) diretamente na configuração da extensão da VM do Docker.
* Confira a [referência da linha de comando do Compose](http://docs.docker.com/compose/reference/) e o [guia do usuário](http://docs.docker.com/compose/) para obter mais exemplos de criação e implantação de aplicativos com vários contêineres.
* Use um modelo do Gerenciador de Recursos do Azure, seu ou da do [comunidade](https://azure.microsoft.com/documentation/templates/), para implantar uma VM do Azure com Docker e um aplicativo configurado com o Redigir. Por exemplo, o modelo [Implantar um blog WordPress com Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) usa Docker e Redigir para implantar rapidamente o WordPress com um back-end do MySQL em uma VM do Ubuntu.
* Tente integrar o Redigir do Docker com um cluster [Docker Swarm](virtual-machines-linux-docker-swarm.md). Consulte [Usando o Compose com o Swarm](https://docs.docker.com/compose/swarm/) para ver os cenários.

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png

<!---HONumber=AcomDC_0615_2016-->