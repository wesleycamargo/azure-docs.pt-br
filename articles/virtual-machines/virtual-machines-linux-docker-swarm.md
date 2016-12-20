---
title: "Introdução ao uso do docker com o swarm no Azure"
description: "Descreve como criar um grupo de VMs com a Extensão de VM Docker e usar o swarm para criar um cluster Docker."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: d529b1f5-864e-4163-9b34-b52d48ceedb1
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/04/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 5cf20d5cccba235984b74a780ba63b0b69f7a20a


---
# <a name="how-to-use-docker-with-swarm"></a>Como usar o docker com o swarm
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Para obter um modelo do Resource Manager para implantar o Docker Swarm, veja [aqui](https://azure.microsoft.com/documentation/templates/docker-swarm-cluster/).

Este tópico mostra uma forma muito simples de usar o [docker](https://www.docker.com/) com o [swarm](https://github.com/docker/swarm) para criar um cluster gerenciado por swarm no Azure. Ele cria quatro máquinas virtuais no Azure, uma que atuará como o Gerenciador de Swarm e três atuando como parte do cluster dos hosts do docker. Quando tiver terminado, você pode usar o swarm para visualizar o cluster e começar a usar o docker nele. Além disso, as chamadas da CLI do Azure neste tópico usam o modo de gerenciamento do serviço (asm). 

> [!NOTE]
> Este tópico usa o docker com swarm e a CLI do Azure *sem* a **docker-machine** para mostrar como as diferentes ferramentas funcionam juntas, mas permanecem independentes. O **docker-machine** tem opções de **-swarm** que permitem que você use o **docker-machine** para adicionar nós diretamente a um swarm. Para obter um exemplo, confira a documentação da [máquina docker](https://github.com/docker/machine) . Caso você tenha perdido a execução do **docker-machine** nas VMs do Azure, confira [Como usar o docker-machine com o Azure](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-docker-hosts-with-azure-virtual-machines"></a>Criar hosts do docker com máquinas virtuais do Azure
Este tópico cria quatro VMs, mas você pode usar qualquer número que desejar. Chame o seguinte com *&lt;senha&gt;* substituída pela senha escolhida.

    azure vm docker create swarm-master -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-1 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-2 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-3 -l "East US" -e 22 $imagename ops <password>

Quando tiver concluído, deve ser capaz de usar a **lista de vms do azure** para ver suas VMs do Azure:

    $ azure vm list | grep "swarm-[mn]"
    data:    swarm-master     ReadyRole           East US       swarm-master.cloudapp.net                               100.78.186.65
    data:    swarm-node-1     ReadyRole           East US       swarm-node-1.cloudapp.net                               100.66.72.126
    data:    swarm-node-2     ReadyRole           East US       swarm-node-2.cloudapp.net                               100.72.18.47  
    data:    swarm-node-3     ReadyRole           East US       swarm-node-3.cloudapp.net                               100.78.24.68  

## <a name="installing-swarm-on-the-swarm-master-vm"></a>Instalar o swarm na MV mestre do swarm
Este tópico usa o [modelo do contêiner de instalação pela documentação do swarm do docker](https://github.com/docker/swarm#1---docker-image) – mas você também pode usar o SSH para o **mestre swarm**. Nesse modelo, o **swarm** é baixado como um contêiner de docker no swarm em execução. Abaixo, podemos executar esta etapa *remotamente pelo laptop usando o docker* para se conectar à VM **swarm-master** e instrui-la para usar o comando de criação de ID do cluster, **swarm create**. A id de cluster é como o **swarm** descobre os membros do grupo swarm. (Você também pode clonar o repositório e compilá-lo por conta própria, que fornece controle total e habilitar a depuração.)

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm create
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    36731c17189fd8f450c395db8437befd

A última linha é a id do cluster; copie-a em algum lugar porque você a usará novamente quando unir as VMs do nó ao swarm mestre para criar o "swarm". Neste exemplo, a id do cluster é **36731c17189fd8f450c395db8437befd**.

> [!NOTE]
> Apenas para deixar claro, estamos usando nossa instalação de docker local para se conectar à VM do **swarm-master** no Azure e instruir o **swarm-master** a baixar, instalar e executar o comando **create**, que retorna a nossa ID de cluster, que posteriormente usamos para fins de descoberta.
> <!-- -->
> Para confirmar isso, execute `docker -H tcp://`*&lt;hostname&gt;* ` images` para listar os processos de contêiner no computador do **swarm-master** e em outro nó para comparação (como executamos o comando anterior do swarm com a opção **--rm**, o contêiner foi removido após concluir e, então, usar **docker ps -a** não retornará nada).:
> 
> 

        $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        swarm               latest              92d78d321ff2        11 days ago         7.19 MB
        $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        $
<P />

> Se você estiver familiarizado com o **docker**, saberá que outros nós não tem entradas porque nenhuma imagem foi baixada e executada.
> 
> 

## <a name="join-the-node-vms-to-our-docker-cluster"></a>Una as VMs do nó ao nosso cluster do docker
Para cada nó, liste as informações de ponto de extremidade usando a CLI do Azure. Faremos isso abaixo para o host do docker s **swarm-node-1** para obter a porta do docker do nó.

    $ azure vm endpoint list swarm-node-1
    info:    Executing command vm endpoint list
    + Getting virtual machines
    data:    Name    Protocol  Public Port  Private Port  Virtual IP      EnableDirectServerReturn  Load Balanced
    data:    ------  --------  -----------  ------------  --------------  ------------------------  -------------
    data:    docker  tcp       2376         2376          138.91.112.194  false                     No
    data:    ssh     tcp       22           22            138.91.112.194  false                     No
    info:    vm endpoint list command OK


Usando o **docker** e a opção `-H` para apontar o cliente docker na sua VM do nó, unir esse nó ao swarm que você está criando, transmitindo a ID do cluster e porta do docker do nó (o último usando **--addr**):

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 run -d swarm join --addr=138.91.112.194:2376 token://36731c17189fd8f450c395db8437befd
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    bbf88f61300bf876c6202d4cf886874b363cd7e2899345ac34dc8ab10c7ae924

Parece bom. Para confirmar que o **swarm** está sendo executado no **swarm-node-1**, digite:

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 ps -a
        CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
        bbf88f61300b        swarm:latest        "/swarm join --addr=   13 seconds ago      Up 12 seconds       2375/tcp            angry_mclean

Repita para todos os outros nós no cluster. Em nosso caso, podemos fazer isso para **swarm-node-2** e **swarm-node-3**.

## <a name="begin-managing-the-swarm-cluster"></a>Comece a gerenciar o cluster do swarm
    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run -d -p 2375:2375 swarm manage token://36731c17189fd8f450c395db8437befd
    d7e87c2c147ade438cb4b663bda0ee20981d4818770958f5d317d6aebdcaedd5

e você pode listar seus nós no cluster:

    ralph@local:~$ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm list token://73f8bc512e94195210fad6e9cd58986f
    54.149.104.203:2375
    54.187.164.89:2375
    92.222.76.190:2375

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
Vá executar as coisas em seu swarm. Para procurar inspiração, confira [https://github.com/docker/swarm/](https://github.com/docker/swarm/) ou assista a um [vídeo](https://www.youtube.com/watch?v=EC25ARhZ5bI).

<!-- links -->

[docker-machine-azure]: virtual-machines-linux-docker-machine.md




<!--HONumber=Nov16_HO3-->


