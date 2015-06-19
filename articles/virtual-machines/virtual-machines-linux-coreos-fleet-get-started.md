<properties 
	pageTitle="Introdução ao Fleet no CoreOS no Azure" 
	description="Fornece exemplos básicos do uso de fleet e Docker em uma máquina virtual de CoreOS Linux no Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"
	editor="madhana"/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure-services" 
	ms.date="03/17/2015" 
	ms.author="danlep"/>


<!--The next line, with one pound sign at the beginning, is the page title-->
# Introdução ao Fleet no CoreOS no Azure

Este artigo oferece dois exemplos rápidos de uso do [fleet](https://github.com/coreos/fleet) e do [Docker](https://www.docker.com/) para executar aplicativos em um cluster de máquinas virtuais do [CoreOS].

Para usar esses exemplos, primeiro configure um cluster CoreOS de três nós como descrito em [Como usar o CoreOS no Azure]. Feito isso, você entenderá os elementos básicos das implantações de CoreOS e terá um cluster e um computador cliente funcionais. Vamos usar exatamente o mesmo nome de cluster nestes exemplos. Além disso, estes exemplos supõem que você está usando o host local do Linux para executar seus comandos **fleet**.




## <a id='simple'>Exemplo 1: Hello World com Docker</a>

Veja um aplicativo "Hello World" simples que é executado em um único contêiner do Docker. Ele usa a [imagem do Docker Hub do busybox].

No computador cliente do Linux, use seu editor de texto favorito para criar o seguinte arquivo de unidade **systemd** e chame-o de `helloworld.service`. (Para mais detalhes sobre a sintaxe, confira [Arquivos de unidade]).

```
[Unit]
Description=HelloWorld
After=docker.service
Requires=docker.service

[Service]

TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill busybox1
ExecStartPre=-/usr/bin/docker rm busybox1
ExecStartPre=/usr/bin/docker pull busybox
ExecStart=/usr/bin/docker run --name busybox1 busybox /bin/sh -c "while true; do echo Hello World; sleep 1; done"
ExecStop=/usr/bin/docker stop busybox1

```

Agora conecte-se ao cluster CoreOS e inicie a unidade que esteja executando o seguinte comando **fleetctl**. O resultado mostra que a unidade foi iniciada e onde está localizada.


```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start helloworld.service


Unit helloworld.service launched on 62f0f66e.../100.79.86.62
```

>[AZURE.NOTE]Para executar seus comandos remotos **fleetctl** sem o parâmetro **--tunnel**, defina opcionalmente a variável de ambiente FLEETCTL_TUNNEL para encapsular as solicitações. Por exemplo: `export FLEETCTL_TUNNEL=coreos-cluster.cloudapp.net:22`.


Você pode se conectar ao contêiner para ver o resultado do serviço:

```
fleetctl --tunnel coreos-cloudapp.cluster.net:22 journal helloworld.service

Mar 04 21:29:26 node-1 docker[57876]: Hello World
Mar 04 21:29:27 node-1 docker[57876]: Hello World
Mar 04 21:29:28 node-1 docker[57876]: Hello World
Mar 04 21:29:29 node-1 docker[57876]: Hello World
Mar 04 21:29:30 node-1 docker[57876]: Hello World
Mar 04 21:29:31 node-1 docker[57876]: Hello World
Mar 04 21:29:32 node-1 docker[57876]: Hello World
Mar 04 21:29:33 node-1 docker[57876]: Hello World
Mar 04 21:29:34 node-1 docker[57876]: Hello World
Mar 04 21:29:35 node-1 docker[57876]: Hello World
```

Para limpar, parar e descarregar a unidade.

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop helloworld.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload helloworld.service
```


## <a id='highavail'>Exemplo 2: Servidor Apache altamente disponível</a>

Uma vantagem de usar CoreOS, Docker e **fleet** é a facilidade para executar os serviços de maneira altamente disponível. Neste exemplo, você implantará um serviço composto por três contêineres idênticos executando o servidor Web Apache. Os contêineres serão executados nas três VMs no cluster. Este exemplo é semelhante àquele em [Iniciando contêineres com fleet] e usa a [imagem do Docker Hub do CoreOS Apache].

>[AZURE.NOTE]Para executar o servidor Apache altamente disponível, você precisará configurar um ponto de extremidade HTTP com balanceamento de carga nas máquinas virtuais (porta pública 80, porta privada 80). Você pode fazer isso depois de criar o cluster CoreOS, usando o Portal de Gerenciamento do Azure ou o comando **azure vm endpoint**. Confira [Configurar um conjunto com balanceamento de carga] para saber mais.

No computador cliente, use seu editor de texto favorito para criar um modelo de arquivo de unidade **systemd** chamado apache@.service. Você usará esse modelo para iniciar três instâncias separadas, chamadas apache@1.service, apache@2.service e apache@3.service:

```
[Unit]
Description=High Availability Apache
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill apache1
ExecStartPre=-/usr/bin/docker rm apache1
ExecStartPre=/usr/bin/docker pull coreos/apache
ExecStart=/usr/bin/docker run -rm --name apache1 -p 80:80 coreos/apache /usr/sbin/apache2ctl -D FOREGROUND
ExecStop=/usr/bin/docker stop apache1

[X-Fleet]
X-Conflicts=apache@*.service
```

>[AZURE.NOTE]O atributo `X-Conflicts` informa ao CoreOS que apenas uma instância desse contêiner pode ser executada em um determinado host CoreOS. Para obter detalhes, confira [Arquivos de unidade].

Agora inicie as instâncias de unidade no cluster CoreOS. Você deve ver que elas estão em execução em três computadores diferentes:

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start apache@{1,2,3}.service

unit apache@3.service launched on 00c927e4.../100.79.62.16
unit apache@1.service launched on 62f0f66e.../100.79.86.62
unit apache@2.service launched on df85f2d1.../100.78.126.15

```
Para acessar o servidor Apache em execução em uma das unidades, envie uma solicitação simples para o serviço em nuvem que hospeda o cluster CoreOS.

`curl http://coreos-cluster.cloudapp.net`

Você verá um texto padrão do servidor Apache semelhante a:

```
<htm\l><body><h1>It works!</h1>
<p>This is the default web page for this server.</p>
<p>The web server software is running but no content has been added, yet.</p>
</body></html>
```

Você pode tentar desligar uma ou mais máquinas virtuais no cluster para verificar se o serviço do Apache continuará a ser executado.

Quando terminar, pare e descarregue as unidades.

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop apache@{1,2,3}.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload apache@{1,2,3}.service

```

## Próximas etapas

Você pode tentar algo mais com o cluster CoreOS de três nós no Azure. Explore como criar clusters mais complexos e como usar o Docker e criar aplicativos mais interessantes. Para isso, leia o [Tutorial de Tim Park sobre o CoreOS], o [Tutorial de Patrick Chanezon sobre o CoreOS Tutorial], a documentação do [Docker] e a [Visão geral do CoreOS].

Confira também [Computação de software livre e do Linux no Azure] para saber mais sobre como usar ambientes de software livre em VMs Linux no Azure.

<!--Link references-->
[Azure Cross-Platform Interface (xplat-cli)]: ../xplat-cli.md
[CoreOS]: https://coreos.com/
[Visão geral do CoreOS]: https://coreos.com/using-coreos/
[CoreOS with Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[Tutorial de Tim Park sobre o CoreOS]: https://github.com/timfpark/coreos-azure
[Tutorial de Patrick Chanezon sobre o CoreOS Tutorial]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[Como usar o CoreOS no Azure]: virtual-machines-linux-coreos-how-to.md
[Configurar um conjunto com balanceamento de carga]: http://msdn.microsoft.com/library/azure/dn655055.aspx
[Iniciando contêineres com fleet]: https://coreos.com/docs/launching-containers/launching/launching-containers-fleet/
[Arquivos de unidade]: https://coreos.com/docs/launching-containers/launching/fleet-unit-files/
[imagem do Docker Hub do busybox]: https://registry.hub.docker.com/_/busybox/
[imagem do Docker Hub do CoreOS Apache]: https://registry.hub.docker.com/u/coreos/apache/
[Computação de software livre e do Linux no Azure]: virtual-machines-linux-opensource.md
<!--HONumber=54--> 