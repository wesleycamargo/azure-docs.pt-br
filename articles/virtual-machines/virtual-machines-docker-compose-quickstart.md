<properties
   pageTitle="Docker e Redigir em uma máquina virtual | Microsoft Azure"
   description="Introdução rápida ao trabalho com o Redigir e o Docker em máquinas virtuais do Azure."
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="11/16/2015"
   ms.author="danlep"/>

# Introdução ao Docker e Redigir para criar um aplicativo de contêiner múltiplos em uma máquina virtual do Azure

Este artigo mostra como começar a usar Docker e o [Redigir](http://github.com/docker/compose) para definir e executar um aplicativo complexo em uma máquina virtual Linux no Azure. Com o Redigir (o sucessor do *Fig*), use um arquivo de texto simples para definir um aplicativo que consiste em vários contêineres do Docker. Em seguida, você acelera seu aplicativo com um único comando que faz tudo que é necessário para executá-lo na VM. Por exemplo, este artigo mostra como configurar rapidamente um blog WordPress com um banco de dados SQL MariaDB de back-end, mas você também pode usar o Redigir para configurar aplicativos mais complexos.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](https://azure.microsoft.com/documentation/templates/docker-wordpress-mysql/).


Se você não estiver familiarizado com o Docker e contêineres, consulte o [Quadro de comunicações de nível elevado do Docker](http://azure.microsoft.com/documentation/videos/docker-high-level-whiteboard/).

## Etapa 1: Configurar uma VM do Linux como um host do Docker

Você pode usar vários procedimentos do Azure e imagens disponíveis no Azure Markeplace para criar uma VM do Linux e configurá-la como um host do Docker. Por exemplo, veja [Usando a Extensão de VM do Docker da Interface de Linha de Comando do Azure](virtual-machines-docker-with-xplat-cli.md) para obter um procedimento rápido para criar uma VM do Ubuntu com a extensão de VM do Docker. Quando você usa a extensão de VM do Docker, sua VM é definida automaticamente como um host do Docker. O exemplo neste artigo mostra como usar a [interface de linha de comando do Azure para Mac, Linux e Windows](../xplat-cli-install.md) (a CLI do Azure) no modo de Gerenciamento de Serviços para criar a VM.

## Etapa 2: Instalar o Redigir

Após a VM Linux estar em execução com o Docker, conecte-se a ela do seu computador cliente usando SSH. Se for necessário, instale o [Redigir](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md) executando os dois comandos a seguir.

>[AZURE.TIP]Se você usou a extensão Docker VM para criar sua VM, o Redigir já está instalado para você. Ignore esses comandos e vá para a etapa 3. Você só precisa instalar o Redigir caso você mesmo tenha instalado o Docker na VM.

```
$ curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

$ chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]Se receber um erro de "Permissão negada", seu diretório /usr/local/bin na VM provavelmente não é gravável e você precisará instalar o Redigir como superusuário. Execute `sudo -i`, em seguida, os dois comandos acima e depois `exit`.

Para testar sua instalação do Redigir, execute o comando a seguir.

```
$ docker-compose --version
```

Você verá uma saída semelhante a
```
docker-compose 1.3.2
```


## Etapa 3: Criar um arquivo de configuração docker-compose.yml

Em seguida, você criará um arquivo `docker-compose.yml`, que é apenas um arquivo de configuração de texto, para definir os contêineres de Docker a serem executados na VM. O arquivo especifica a imagem a ser executada em cada contêiner (ou pode ser uma compilação de um Dockerfile), as variáveis de ambiente e as dependências, as portas, os links entre contêineres e assim por diante. Para obter detalhes sobre a sintaxe de arquivo yml, confira [referência do docker-compose.yml](http://docs.docker.com/compose/yml/).

Crie um diretório de trabalho na sua VM e use seu editor de texto favorito para criar `docker-compose.yml`. Para testar um exemplo simples, copie o texto a seguir no arquivo. Essa configuração usa imagens do [Registro do DockerHub](https://registry.hub.docker.com/_/wordpress/) para instalar o WordPress (o sistema de blog e gerenciamento de conteúdo de software livre) e um banco de dados MariaDB de back-end vinculado.

 ```
 wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 8080:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>

```

## Step 4: Start the containers with Compose

In the working directory on your VM, simply run the following command.

```
$ docker-compose up -d

```

This starts the Docker containers specified in `docker-compose.yml`. You'll see output similar to:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
```

>[AZURE.NOTE]Não se esqueça de usar a opção **-d** na inicialização para que os contêineres sejam executados continuamente em segundo plano.

Para verificar se os contêineres estão ativos, digite `docker-compose ps`. Você deve ver algo como:

```
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:8080->80
ess_1              apache2-for ...                       /tcp
```

Agora você pode se conectar ao WordPress diretamente na VM, navegando para `http://localhost:8080`. Se você quiser conectar-se à VM pela Internet, primeiro configure um ponto de extremidade HTTP na VM que mapeie a porta pública 80 para a porta privada 8080. Por exemplo, em uma implantação de Gerenciamento de Serviços do Azure, execute o seguinte comando CLI do Azure:

```
$ azure vm endpoint create <machine-name> 80 8080

```

Agora você deve ver a tela inicial do WordPress, na qual você pode concluir a instalação e começar a usar o aplicativo.

![Tela inicial do WordPress][wordpress_start]


## Próximas etapas

* Confira a [Referência a CLI do Redigir](http://docs.docker.com/compose/reference/) e o [guia do usuário](http://docs.docker.com/compose/) para obter mais exemplos de criação e implantação de aplicativos de vários contêineres.
* Use um modelo do Gerenciador de Recursos do Azure, seu ou da do [comunidade](http://azure.microsoft.com/documentation/templates/), para implantar uma VM do Azure com Docker e um aplicativo configurado com o Redigir. Por exemplo, o modelo [Implantar um blog WordPress com Docker](https://azure.microsoft.com/documentation/templates/docker-wordpress-mysql/) usa Docker e Redigir para implantar rapidamente o WordPress com um back-end do MySQL em uma VM do Ubuntu.
* Tente integrar o Redigir do Docker com um cluster [Docker Swarm](virtual-machines-docker-swarm.md). Consulte
[Integração do Redigir do Docker/Swarm](https://github.com/docker/compose/blob/master/SWARM.md) para cenários.

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-docker-compose-quickstart/WordPress.png

<!---HONumber=Nov15_HO4-->