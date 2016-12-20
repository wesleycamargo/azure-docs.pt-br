---
title: "Introdução aos Reliable Actors do Service Fabric | Microsoft Docs"
description: "Este tutorial orienta você pelas etapas de criação, depuração e implantação de um serviço simples baseado em ator usando os Reliable Actors do Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d31dc8ab-9760-4619-a641-facb8324c759
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2016
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3e349d0c76078889b6e41340ee8bb2f599819ba3


---
# <a name="getting-started-with-reliable-actors"></a>Introdução aos Reliable Actors
> [!div class="op_single_selector"]
> * [C# em Windows](service-fabric-reliable-actors-get-started.md)
> * [Java no Linux](service-fabric-reliable-actors-get-started-java.md)
> 
> 

Este artigo explica os conceitos básicos dos Reliable Actors do Azure Service Fabric e orienta você durante a criação e a implantação de um aplicativo Reliable Actor simples em Java.

## <a name="installation-and-setup"></a>Instalação e configuração
Antes de começar, verifique se há um ambiente de desenvolvimento do Service Fabric configurado no seu computador.
Se precisar configurá-lo, vá para o [guia de introdução ao Mac](service-fabric-get-started-mac.md) ou o [guia de introdução ao Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Conceitos básicos
Para começar a usar os Reliable Actors, você só precisa entender alguns conceitos básicos:

* **Serviço do ator**. Os Reliable Actors são empacotados em Reliable Services que podem ser implantados na infraestrutura do Service Fabric. As instâncias de ator são ativadas em uma instância de serviço nomeado.
* **Registro do ator**. Como ocorre com Reliable Services, um serviço de Reliable Actor precisa ser registrado com o tempo de execução do Service Fabric. Além disso, o tipo de ator precisa ser registrado com o tempo de execução do ator.
* **Interface do Ator**. A interface do ator é usada para definir uma interface pública fortemente tipada de um ator. Na terminologia do modelo de Reliable Actor, a interface do ator define os tipos de mensagem que o ator pode entender e processar. A interface do ator é usada por outros atores ou aplicativos cliente para “enviar” (de modo assíncrono) mensagens ao ator. Os Reliable Actors pode implantar várias interfaces.
* **Classe ActorProxy**. A classe ActorProxy é usada por aplicativos cliente para invocar os métodos expostos por meio de suas interfaces de ator. A classe ActorProxy apresenta duas funcionalidades importantes:
  
  * Resolução do nome: ela consegue localizar o ator no cluster (localizar o nó do cluster no qual ele está hospedado).
  * Tratamento de falhas: ela pode repetir as invocações do método e resolver novamente o local do ator, por exemplo, depois que uma falha exige que ele seja relocado para outro nó no cluster.

Vale a pena mencionar as seguintes regras que pertencem às interfaces de ator:

* Métodos da interface de ator não podem ser sobrecarregados.
* Métodos da interface de ator não podem ter parâmetros de saída, de referência e opcionais.
* Não há suporte para interfaces genéricas.

## <a name="create-an-actor-service"></a>Criar um serviço de ator
Comece criando um novo aplicativo de Service Fabric. O SDK do Service Fabric para Linux inclui um gerador Yeoman para fornecer o scaffolding de um aplicativo de Service Fabric com um serviço sem estado. Comece executando o seguinte comando Yeoman:

```bash
$ yo azuresfjava
```

Siga as instruções para criar um **Serviço de Ator Confiável**. Para este tutorial, nomeie o aplicativo "HelloWorldActorApplication" e o ator "HelloWorldActor". O scaffolding a seguir será criado:

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="reliable-actors-basic-building-blocks"></a>Blocos de construção básicos de Reliable Actors
Os conceitos básicos descritos anteriormente são convertidos em blocos de construção básicos de um serviço Reliable Actor.

### <a name="actor-interface"></a>Interface do ator
Contém a definição da interface para o ator. Essa interface define o contrato de ator que é compartilhado com a implementação do ator e os clientes chamando o ator, assim, geralmente faz sentido defini-lo em um local separado da implementação do ator e pode ser compartilhado por vários outros serviços ou aplicativos cliente.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Serviço de ator
Contém a implementação do ator e o código de registro do ator. A classe de ator implementa a interface do ator. É o local em que o ator faz seu trabalho.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActor.HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends ReliableActor implements HelloWorldActor {
    Logger logger = Logger.getLogger(this.getClass().getName());

    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Registro do ator
O serviço de ator deve ser registrado com um tipo de serviço no tempo de execução do Service Fabric. Para que o Serviço de Ator seja executado em suas instâncias de ator, o tipo de ator também deve ser registrado no Serviço de Ator. O método de registro `ActorRuntime` realiza esse trabalho para os atores.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {

    public static void main(String[] args) throws Exception {

        try {
            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new ActorServiceImpl(context, actorType, ()-> new HelloWorldActorImpl()), Duration.ofSeconds(10));

            Thread.sleep(Long.MAX_VALUE);

        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
    }
}
```

### <a name="test-client"></a>Cliente de teste
Este é um aplicativo de cliente de teste simples pode ser executado separadamente do aplicativo do Service Fabric para testar o serviço de ator. Este é um exemplo de local em que o ActorProxy pode ser usado para ativar e comunicar-se com instâncias de ator. Ele não é implantado com seu serviço.

### <a name="the-application"></a>O aplicativo
Por fim, o aplicativo empacota junto o serviço de ator e quaisquer outros serviços que você possa adicionar no futuro para implantação. Ele contém o *ApplicationManifest.xml* e espaços reservados para o pacote de serviço do ator.

## <a name="run-the-application"></a>Executar o aplicativo
O scaffolding Yeoman inclui um script gradle para compilar o aplicativo e os scripts bash para implantar e cancelar a implantação do aplicativo. Para executar o aplicativo, primeiro compile o aplicativo com gradle:

```bash
$ gradle
```

Isso produzirá um pacote de aplicativo do Service Fabric que poderá ser implantado usando a CLI do Service Fabric do Azure. O script install.sh contém os comandos da CLI do Azure necessários para implantar o pacote de aplicativos. Basta executar o script install.sh para implantar:

```bask
$ ./install.sh
```



<!--HONumber=Nov16_HO3-->


