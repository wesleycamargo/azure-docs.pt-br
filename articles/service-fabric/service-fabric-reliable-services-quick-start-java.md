---
title: "Introdução aos Reliable Services | Microsoft Docs"
description: "Introdução à criação de um aplicativo do Service Fabric do Microsoft Azure com serviços com e sem estado."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 4450ad62a9b05ac4c963ae3271590f9431b782ed
ms.openlocfilehash: 2a2378dbeb5e7994039291deffd35cb04bf8057c


---
# <a name="get-started-with-reliable-services"></a>Introdução aos Reliable Services
> [!div class="op_single_selector"]
> * [C# em Windows](service-fabric-reliable-services-quick-start.md)
> * [Java no Linux](service-fabric-reliable-services-quick-start-java.md)
> 
> 

Este artigo explica os conceitos básicos dos Reliable Services do Azure Service Fabric e o orienta você durante a criação e a implantação de um aplicativo Reliable Service simples escrito em Java. Este vídeo da Microsoft Virtual Academy mostra como criar um serviço confiável sem estado: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-reliable-services-quick-start-java/ReliableServicesJavaVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="installation-and-setup"></a>Instalação e configuração
Antes de começar, verifique se há um ambiente de desenvolvimento do Service Fabric configurado no seu computador.
Se precisar configurá-lo, vá para o [guia de introdução ao Mac](service-fabric-get-started-mac.md) ou o [guia de introdução ao Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Conceitos básicos
Para começar a usar os Reliable Services, você só precisa entender alguns conceitos básicos:

* **Tipo de serviço**: esta é sua implementação de serviço. Ele é definido pela classe que você escreve que estende `StatelessService` e qualquer outro código ou dependências usadas nele, juntamente com um nome e um número de versão.
* **Instância de serviço nomeada**: para executar seu serviço, criar instâncias nomeadas do tipo de serviço, bem como criar instâncias de objeto de um tipo de classe. Instâncias de serviço são, na verdade, as instâncias de objeto de sua classe de serviço que você escreve. 
* **Host de serviço**: as instâncias de serviço nomeado que você cria precisam executar dentro de um host. O host de serviço é apenas um processo em que instâncias do serviço podem ser executadas.
* **Registro de serviço**: o registro reúne tudo. O tipo de serviço deve ser registrado com o tempo de execução do Service Fabric em um host de serviço para permitir que o Service Fabric crie instâncias para executar.  

## <a name="create-a-stateless-service"></a>Criar um serviço sem estado
Comece criando um novo aplicativo de Service Fabric. O SDK do Service Fabric para Linux inclui um gerador Yeoman para fornecer o scaffolding de um aplicativo de Service Fabric com um serviço sem estado. Comece executando o seguinte comando Yeoman:

```bash
$ yo azuresfjava
```

Siga as instruções para criar um **Serviço Confiável Sem Estado**. Para este tutorial, nomeie o aplicativo "HelloWorldApplication" e o serviço "HelloWorld". O resultado incluirá os diretórios para `HelloWorldApplication` e `HelloWorld`.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="implement-the-service"></a>Implementar o serviço
Abra **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Essa classe define o tipo de serviço e pode executar qualquer código. A API de serviço fornece dois pontos de entrada para seu código:

* Um método de ponto de entrada em aberto chamado `runAsync()`, em que você pode começar a executar qualquer carga de trabalho, incluindo cargas de trabalho de computação de longa duração.

```java
@Override
protected CompletableFuture<?> runAsync() {
    ...
}
```

* Um ponto de entrada de comunicação no qual você pode conectar a pilha de comunicação de sua escolha. É onde você pode começar a receber solicitações de usuários e outros serviços.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Neste tutorial, enfatizaremos o método de ponto de entrada `runAsync()` . É aqui que você pode começar imediatamente a executar seu código.

### <a name="runasync"></a>RunAsync
A plataforma chama esse método quando uma instância de um serviço é estabelecida e está pronta para execução. O ciclo de abertura/fechamento de uma instância de serviço pode ocorrer várias vezes durante o tempo de vida do serviço como um todo. Isso pode ocorrer por vários motivos, incluindo:

* O sistema move as instâncias de serviço para balanceamento de recursos.
* Ocorrem falhas no código.
* O aplicativo ou sistema é atualizado.
* O hardware subjacente sofre uma interrupção.

Essa orquestração é gerenciada pelo Service Fabric para manter o serviço altamente disponível e devidamente balanceado.

#### <a name="cancellation"></a>Cancelamento
É essencial que seu código no `runAsync()` possa interromper a execução quando notificado pelo Service Fabric. O `CompletableFuture` retornado de `runAsync()` é cancelado quando o Service Fabric requer que seu serviço interrompa a execução. O exemplo a seguir demonstra como processar um evento de cancelamento: 

```java
    @Override
    protected CompletableFuture<?> runAsync() {

        CompletableFuture<?> completableFuture = new CompletableFuture<>();
        ExecutorService service = Executors.newFixedThreadPool(1);

        Future<?> userTask = service.submit(() -> {
            while (!Thread.currentThread().isInterrupted()) {
                try
                {
                   logger.log(Level.INFO, this.context().serviceName().toString());
                   Thread.sleep(1000);
                }
                catch (InterruptedException ex)
                {
                    logger.log(Level.INFO, this.context().serviceName().toString() + " interrupted. Exiting");
                    return;
                }
            }
         });

        completableFuture.handle((r, ex) -> {
            if (ex instanceof CancellationException) {
                userTask.cancel(true);
                service.shutdown();
            }
            return null;
        });

        return completableFuture;
   }
``` 

### <a name="service-registration"></a>Registro do serviço
Os tipos de serviço devem ser registrados com o tempo de execução do Service Fabric. O tipo de serviço é definido na `ServiceManifest.xml` e sua classe de serviço que implementa `StatelessService`. O registro de serviço é executado no ponto de entrada principal do processo. Neste exemplo, o ponto de entrada principal do processo é `HelloWorldServiceHost.java`:

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    } 
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration: {0}", ex.toString());
        throw ex;
    }
}
```

## <a name="run-the-application"></a>Executar o aplicativo
O scaffolding Yeoman inclui um script gradle para compilar o aplicativo e os scripts bash para implantar e cancelar a implantação do aplicativo. Para executar o aplicativo, primeiro compile o aplicativo com gradle:

```bash
$ gradle
```

Isso produzirá um pacote de aplicativo do Service Fabric que poderá ser implantado usando a CLI do Service Fabric do Azure. O script install.sh contém os comandos da CLI do Azure necessários para implantar o pacote de aplicativos. Basta executar o script install.sh para implantar:

```bask
$ ./install.sh
```



<!--HONumber=Dec16_HO2-->


