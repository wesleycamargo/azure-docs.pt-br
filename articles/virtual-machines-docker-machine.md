<properties
   pageTitle="Como usar a máquina de suporte com o Azure"
   description="Mostra como colocar em funcionamento o Azure com Máquina de Suporte no Ubuntu."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="05/25/2015"
   ms.author="rasquill"/>

# Como usar a máquina de suporte com o Azure

Este tópico descreve como usar o [Docker](https://www.docker.com/) com [máquina](https://github.com/docker/machine) e a [CLI do Azure](https://github.com/Azure/azure-xplat-cli) para criar uma Máquina Virtual do Azure para gerenciar rápida e facilmente contêineres Linux de um computador executando o Ubuntu. Para demonstrar, o tutorial mostra como implantar a  [imagem de Hub Docker busybox](https://registry.hub.docker.com/_/busybox/) e também a [imagem de Hub do Docker nginx](https://registry.hub.docker.com/_/nginx/) e configurar o contêiner para rotear solicitações da Web para o contêiner nginx. (A documentação da **máquina** docker descreve como modificar essas instruções para outras plataformas.)

Há alguns pré-requisitos para concluir este tutorial. Você precisará instalar o seguinte:

1. [npm](https://docs.npmjs.com/) e [Node.js](http://nodejs.org/)
2. [A CLI do Azure](https://github.com/Azure/azure-xplat-cli)
3. Um [cliente Docker](https://docs.docker.com/installation/)

Se você instalar esses itens nessa ordem, o computador Ubuntu estará pronto para o tutorial. (Esse tutorial deve ser praticamente o mesmo para qualquer outra distribuição baseada em dpkg como o Debian. Fale nos comentários se você descobrir etapas ou requisitos extras.)

## Obtenha a máquina docker-- ou crie-a

A maneira mais rápida de começar a usar a **máquina docker** é baixar a versão adequada diretamente pelo [compartilhamento de versão](https://github.com/docker/machine/releases). O computador cliente neste tutorial estava executando o Ubuntu em um computador x64, portanto, a imagem **docker-machine_linux-amd64** é usada.

Você também pode criar sua **máquina docker** por conta própria seguindo as etapas para [contribuir para a máquina](https://github.com/docker/machine#contributing). Você deve estar pronto para fazer o download de até 1 GB ou mais para executar a compilação. Mas fazendo isso, você pode personalizar sua experiência exatamente da maneira desejada.

> [AZURE.NOTE]Você também pode criar um [link simbólico](http://en.wikipedia.org/wiki/Symbolic_link) para sua versão de plataforma, mas este tutorial usa o binário diretamente para demonstrar claramente o comportamento. O resultado é que, em vez de comandos como `docker-machine env`, como mostra a documentação do **docker-machine**, este tutorial usa `docker-machine_linux-amd64 env`. É sua escolha criar um symlink ou apenas usar o nome binário diretamente, mas se você alterar o nome que está usando, lembre-se de modificar o nome nas instruções abaixo.

<br />

>  Dependendo do método escolhido, você deve chamar no binário diretamente na linha de comando ou colocar o binário no caminho como **/usr/local/bin**. Lembre-se de verificar se ele está marcado como executável digitando `chmod +x` &lt;*`binaryName`*&gt; onde &lt;*`binaryName`*&gt; é o nome do executável da máquina Docker. Este tutorial usa **docker-machine_linux-amd64**.

## Crie os arquivos de certificado e chave para o docker, o computador e o Azure

Agora, você deve criar os arquivos de certificado e chave que o Azure precisa para confirmar sua identidade e permissões, bem como aquelas que a **máquina docker** precisa para se comunicar com sua Máquina Virtual do Azure para criar e gerenciar contêineres remotamente. Se você já tiver esses arquivos em um diretório, talvez para uso com docker, você pode reutilizá-los. No entanto, a melhor prática para teste da **máquina docker** seria criá-los em um diretório separado e apontar uma máquina docker.

> [AZURE.NOTE]Se acabar tentando a **máquina docker** repetidamente, não se esqueça de reutilizar o mesmo arquivo de certificado e chave. A **máquina docker** cria um conjunto de certificados cliente também – tudo o que ela cria pode ser examinado em `~/.docker/machine`. Se você mover esses certificados para outro computador, também precisará mover as pastas de certificado da **máquina docker**. Isso faz diferença se você pretende usar a **máquina docker** em outra plataforma, por exemplo, apenas para ver como tudo funciona.

Se você tiver experiência com distribuições do Linux, é possível já ter esses arquivos disponíveis para uso no computador em um local específico e a [documentação HTTPS do Docker também explica essas etapas](https://docs.docker.com/articles/https/). No entanto, esta é a forma mais simples desta etapa.

1. Crie uma pasta local e na linha de comando, navegue até essa pasta e digite:

		openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
		openssl pkcs12 -export -out mycert.pfx -in mycert.pem -name "My Certificate"

	Prepare-se aqui para inserir a senha de exportação do seu certificado e capturá-lo para uso futuro. Em seguida, digite:

		openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

2. Carregar arquivo .cer do certificado no Azure. No [Portal Azure](https://manage.windowsazure.com), clique em **Configurações** na parte inferior esquerda da área de serviço (mostrada abaixo)

	![][portalsettingsitem]

	e clique em **Certificados de Gerenciamento**:

	![][managementcertificatesitem]

	e clique em **Carregar** (na parte inferior da página)![][uploaditem] para carregar o arquivo **mycert.cer** criado na etapa anterior.

3. No mesmo painel **Configurações** do portal, clique em **Assinaturas** e capture a ID de assinatura para usar ao criar sua VM, porque você a usará na próxima etapa. (Você também pode localizar a id da assinatura na linha de comando usando o comando da CLI do Azure `azure account list`, que exibe a id da assinatura para cada assinatura que você tem na conta.)

4. Crie uma VM do host docker no Azure usando o comando **docker-machine create**. O comando requer a ID da assinatura capturada na etapa anterior e o caminho para o arquivo **.pem** criado na etapa 1. Este tópico usa o "nome do computador" como o Serviço de Nuvem do Azure (e sua VM), mas você deve substituí-lo por sua própria escolha e lembrar de usar o nome do seu serviço de nuvem em qualquer etapa que exige o nome da máquina virtual. (Lembre-se de que neste exemplo estamos usando o nome binário completo e não um symlink da **máquina docker**.)

		docker-machine_linux-amd64 create \
	    -d azure \
	    --azure-subscription-id="<subscription ID acquired above>" \
	    --azure-subscription-cert="mycert.pem" \
	    machine-name

	Como as duas primeiras etapas exigem a criação de uma nova VM e o carregamento do agente do Linux Azure, bem como a atualização de novas VMs, você verá algo semelhante ao seguinte.

		INFO[0001] Creating Azure machine...
	    INFO[0049] Waiting for SSH...
	    modprobe: FATAL: Module aufs not found.
	    + sudo -E sh -c sleep 3; apt-get update
	    + sudo -E sh -c sleep 3; apt-get install -y -q linux-image-extra-3.13.0-36-generic
	    E: Unable to correct problems, you have held broken packages.
	    modprobe: FATAL: Module aufs not found.
	    Warning: tried to install linux-image-extra-3.13.0-36-generic (for AUFS)
	     but we still have no AUFS.  Docker may not work. Proceeding anyways!
	    + sleep 10
	    + [ https://get.docker.com/ = https://get.docker.com/ ]
	    + sudo -E sh -c apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
	    gpg: requesting key A88D21E9 from hkp server keyserver.ubuntu.com
	    gpg: key A88D21E9: public key "Docker Release Tool (releasedocker) <docker@dotcloud.com>" imported
	    gpg: Total number processed: 1
	    gpg:               imported: 1  (RSA: 1)
	    + sudo -E sh -c echo deb https://get.docker.com/ubuntu docker main > /etc/apt/sources.list.d/docker.list
	    + sudo -E sh -c sleep 3; apt-get update; apt-get install -y -q lxc-docker
	    + sudo -E sh -c docker version
	    INFO[0368] "machine-name" has been created and is now the active machine.
	    INFO[0368] To point your Docker client at it, run this in your shell: $(docker-machine_linux-amd64 env machine-name)

    > [AZURE.NOTE]Como uma VM está sendo criada, pode levar alguns minutos para ela estar em um estado pronto. Enquanto você aguarda, é possível verificar o estado do seu novo host Docker digitando `azure vm list` usando a CLI do Azure até ver suas VMs com o status **ReadyRole**.

5. Defina as variáveis de ambiente de máquina e docker para a sessão de terminal. A última linha de comentário sugere que você execute imediatamente o comando **env** para exportar as variáveis de ambiente necessárias para usar o client docker diretamente com um computador específico.

		$(docker-machine_linux-amd64 env machine-name)

	Quando você fizer isso, não é necessário nenhum comando especial para usar o client docker local para conectar-se à VM que a **máquina** Docker criou.

	    $ docker info
	    Containers: 0
	    Images: 0
	    Storage Driver: devicemapper
	     Pool Name: docker-8:1-131736-pool
	     Pool Blocksize: 65.54 kB
	     Backing Filesystem: extfs
	     Data file: /dev/loop0
	     Metadata file: /dev/loop1
	     Data Space Used: 305.7 MB
	     Data Space Total: 107.4 GB
	     Metadata Space Used: 729.1 kB
	     Metadata Space Total: 2.147 GB
	     Udev Sync Supported: false
	     Data loop file: /var/lib/docker/devicemapper/devicemapper/data
	     Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
	     Library Version: 1.02.82-git (2013-10-04)
	    Execution Driver: native-0.2
	    Kernel Version: 3.13.0-36-generic
	    Operating System: Ubuntu 14.04.1 LTS
	    CPUs: 1
	    Total Memory: 1.639 GiB
	    Name: machine-name
	    ID: W3FZ:BCZW:UX24:GDSV:FR4N:N3JW:XOC2:RI56:IWQX:LRTZ:3G4P:6KJK
	    WARNING: No swap limit support

> [AZURE.NOTE]Este tutorial mostra a **máquina docker** criar uma máquina virtual. No entanto, você pode repetir as etapas para criar quantas máquinas quiser. Se você fizer isso, a melhor maneira de alternar entre máquinas virtuais com o docker é usar o comando **env** em linha para definir as variáveis de ambiente do **docker** para cada comando individual. Por exemplo, para usar as **informações do docker** com uma máquina virtual diferente, você pode digitar `docker $(docker-machine env <VM name>) info` e o comando **env** preenche as informações de conexão do docker para usar com essa VM.

## Pronto. Vamos executar alguns aplicativos remotamente usando imagens do Hub Docker e o docker.

Agora você pode usar o docker da forma normal para criar um aplicativo no contêiner. É mais fácil demonstrar o [busybox](https://registry.hub.docker.com/_/busybox/):

	    $  docker run busybox echo hello world
	    Unable to find image 'busybox:latest' locally
	    511136ea3c5a: Pull complete
	    df7546f9f060: Pull complete
	    ea13149945cb: Pull complete
	    4986bf8c1536: Pull complete
	    busybox:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
	    Status: Downloaded newer image for busybox:latest
	    hello world

No entanto, você talvez queira criar um aplicativo que possa ver imediatamente na internet, como o [nginx](https://registry.hub.docker.com/_/nginx/) pelo [Hub do Docker](https://registry.hub.docker.com/).

> [AZURE.NOTE]Lembre-se de usar a opção **-P** para que o **docker** atribua portas aleatórias para a imagem e **-d** para garantir que o contêiner seja executado em segundo plano continuamente. (Se você esquecer, iniciará o nginx e ele será imediatamente encerrado. Não se esqueça!)

	$ docker run --name machinenginx -P -d nginx
    Unable to find image 'nginx:latest' locally
    30d39e59ffe2: Pull complete
    c90d655b99b2: Pull complete
    d9ee0b8eeda7: Pull complete
    3225d58a895a: Pull complete
    224fea58b6cc: Pull complete
    ef9d79968cc6: Pull complete
    f22d05624ebc: Pull complete
    117696d1464e: Pull complete
    2ebe3e67fb76: Pull complete
    ad82b43d6595: Pull complete
    e90c322c3a1c: Pull complete
    4b5657a3d162: Pull complete
    511136ea3c5a: Already exists
    nginx:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for nginx:latest
    5883e2ff55a4ba0aa55c5c9179cebb590ad86539ea1d4d367d83dc98a4976848

Para vê-lo pela internet, crie um ponto de extremidade público na porta 80 para a VM do Azure e mapeie essa porta para a porta do contêiner nginx. Primeiro, use `docker ps -a` para localizar o contêiner e encontrar quais portas o **docker** atribuiu para a porta 80 do contêiner. (As informações exibidas abaixo são editadas para mostrar apenas as informações de porta. Você verá mais.)

	$ docker ps -a
    IMAGE               PORTS
    nginx:latest        0.0.0.0:49153->80/tcp, 0.0.0.0:49154->443/tcp
    busybox:latest

Podemos ver que o docker atribuiu a porta 80 do contêiner a porta 49153 da VM. Agora podemos usar a CLI do Azure para mapear a porta pública 80 do Serviço de Nuvem para a porta 49153 na VM. O Docker garante que o tráfego tcp de entrada na porta 49153 da VM seja roteado para o contêiner nginx.

	$ azure vm endpoint create machine-name 80 49153
    info:    Executing command vm endpoint create
    + Getting virtual machines
    + Reading network configuration
    + Updating network configuration
    info:    vm endpoint create command OK

Abra seu navegador favorito e dê uma olhada.

![][nginx]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
Vá para o [guia do usuário Docker](https://docs.docker.com/userguide/) e crie alguns aplicativos no Microsoft Azure. Ou vá brincar com o [**docker** e o swarm](https://github.com/docker/swarm) no Azure](virtual-machines-docker-swarm) e veja como o swarm pode ser usado com o docker e o Azure.

<!--Image references-->
[nginx]: ./media/virtual-machines-docker-machine/nginxondocker.png
[portalsettingsitem]: ./media/virtual-machines-docker-machine/portalsettingsitem.png
[managementcertificatesitem]: ./media/virtual-machines-docker-machine/managementcertificatesitem.png
[uploaditem]: ./media/virtual-machines-docker-machine/uploaditem.png

<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!---HONumber=58-->