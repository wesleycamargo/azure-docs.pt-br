<properties
   pageTitle="Introdução ao Docker e Redigir em máquinas virtuais do Azure"
   description="Introdução rápida ao trabalho com o Redigir e o Docker no Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="05/07/2015"
   ms.author="danlep"/>

# Introdução ao Docker e Redigir em uma máquina virtual do Azure


Você pode começar a usar rapidamente o [Redigir](http://github.com/docker/compose) (o sucessor de *Fig*) para definir e executar aplicativos com Docker em uma máquina virtual Linux no Azure. Com o Redigir, você define um aplicativo de vários contêineres em um único arquivo e acelera seu aplicativo em um único comando que faz tudo para executá-lo na VM.




## Criar uma VM do Azure com a extensão de VM do Docker e instalar o Redigir

Você pode usar vários procedimentos do Azure e imagens disponíveis no Azure Markeplace para criar uma VM do Azure e instalar o Docker e o Redigir nele. Por exemplo, confira [Usando a extensão de VM do Docker da Interface de Linha de Comando do Azure](virtual-machines-docker-with-xplat-cli) para um procedimento rápido para criar uma VM Ubuntu com a extensão de VM do Docker usando a interface de linha de comando do Azure para Mac, Linux e Windows (a CLI do Azure). O exemplo nesse artigo usa a CLI no modo de Gerenciamento de Serviço (**asm**).


Depois que a VM do Ubuntu estiver em execução com o Docker, conecte-se a ela usando SSH e instale o [Redigir](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md) executando dois comandos:

```
curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]Se receber um erro de "Permissão negada", seu diretório /usr/local/bin provavelmente não é gravável e você precisará instalar o Redigir como superusuário. Execute `sudo -i`, em seguida, os dois comandos acima e depois `exit`.

Para testar sua instalação do Redigir, execute

```
docker-compose --version
```

Você verá uma saída semelhante a ```
docker-compose 1.2.0
```


## Criar um arquivo de configuração docker-compose.yml

O Redigir usa um arquivo de configuração de texto chamado `docker-compose.yml` para definir os contêineres que serão executados na VM. O arquivo especifica a imagem a ser executada em cada contêiner (ou pode ser uma compilação de um Dockerfile), as variáveis de ambiente e as dependências, as portas, os links entre contêineres e assim por diante. Para obter detalhes sobre a sintaxe de arquivo yml, confira [referência do docker-compose.yml](http://docs.docker.com/compose/yml/).

Crie um diretório de trabalho na sua VM e use seu editor de texto favorito para criar `docker-compose.yml`. Para testar um exemplo simples, copie o texto a seguir no arquivo. Essa configuração instala o WordPress (o sistema de blog e gerenciamento de conteúdo de software livre) e um banco de dados SQL MariaDB de back-end vinculado usando imagens do [Registro do DockerHub](https://registry.hub.docker.com/_/wordpress/).

 ``` wordpress: image: wordpress links: - db:mysql ports: - 8080:80

db: image: mariadb environment: MYSQL_ROOT_PASSWORD: <your password>

```

## Start the containers with Compose

In your working directory simply run

```
docker-compose up -d

```

to start the Docker containers specified in `docker-compose.yml`. You'll see output similar to:

```
Creating wordpress_db_1... Creating wordpress_wordpress_1... ```

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

Agora você pode se conectar ao WordPress diretamente na VM, navegando para `http://localhost:8080`. Se você quiser conectar-se à VM pela Internet, primeiro configure um ponto de extremidade HTTP na VM que mapeie a porta pública 80 para a porta privada 8080. Por exemplo, execute o seguinte comando da CLI do Azure:

```
azure vm endpoint create <machine-name> 80 8080

```

Agora você deve ver a tela inicial do WordPress, onde pode concluir a instalação.

![Tela inicial do WordPress][wordpress_start]




## Próximas etapas

* Faça o check-out da [Referência de comandos do Redigir](http://docs.docker.com/compose/cli/) e do [guia do usuário](http://docs.docker.com/compose/) para obter mais exemplos de criação e implantação de aplicativos de vários contêineres.
* Tente integrar o Redigir do Docker com um cluster [Docker Swarm](virtual-machines-docker-swarm.md). Consulte [Integração do Redigir do Docker/Swarm](https://github.com/docker/compose/blob/master/SWARM.md) para cenários.
* Use um modelo do Gerenciador de Recursos do Azure, seu ou da do [comunidade](http://azure.microsoft.com/documentation/templates/), para implantar uma VM do Azure com Docker e um aplicativo configurado com o Redigir. Por exemplo, a [VM do Ubuntu com o Docker e o modelo de três contêineres](http://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/) ajudam a implantar rapidamente três serviços com base nas imagens no [Registro do DockerHub](https://registry.hub.docker.com/).

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-docker-compose-quickstart/WordPress.png
 

<!---HONumber=July15_HO4-->