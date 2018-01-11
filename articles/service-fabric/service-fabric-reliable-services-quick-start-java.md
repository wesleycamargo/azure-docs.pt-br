---
title: "Criar seu primeiro microsserviço confiável do Azure em Java | Microsoft Docs"
description: "Introdução à criação de um aplicativo do Service Fabric do Microsoft Azure com serviços com e sem estado."
services: service-fabric
documentationcenter: java
author: suhuruli
manager: timlt
editor: 
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: ab675207094bc8ee317573192c33c20039780fe2
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2017
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
Comece criando um aplicativo de Service Fabric. O SDK do Service Fabric para Linux inclui um gerador Yeoman para fornecer o scaffolding de um aplicativo de Service Fabric com um serviço sem estado. Comece executando o seguinte comando Yeoman:

```bash
$ yo azuresfjava
```

Siga as instruções para criar um **Serviço Confiável Sem Estado**. Para este tutorial, nomeie o aplicativo "HelloWorldApplication" e o serviço "HelloWorld". O resultado inclui os diretórios para `HelloWorldApplication` e `HelloWorld`.

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
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="implement-the-service"></a>Implementar o serviço

Abra **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Essa classe define o tipo de serviço e pode executar qualquer código. A API de serviço fornece dois pontos de entrada para seu código:

* Um método de ponto de entrada em aberto chamado `runAsync()`, em que você pode começar a executar qualquer carga de trabalho, incluindo cargas de trabalho de computação de longa duração.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
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
A plataforma chama esse método quando uma instância de um serviço é estabelecida e está pronta para execução. Para um serviço sem estado, isso simplesmente significa quando a instância do serviço é aberta. Um token de cancelamento é fornecido para coordenar quando sua instância de serviço deve ser fechada. No Service Fabric, esse ciclo de abertura/fechamento de uma instância de serviço pode ocorrer várias vezes durante a vida útil do serviço como um todo. Isso pode ocorrer por vários motivos, incluindo:

* O sistema move as instâncias de serviço para balanceamento de recursos.
* Ocorrem falhas no código.
* O aplicativo ou sistema é atualizado.
* O hardware subjacente sofre uma interrupção.

Essa orquestração é gerenciada pelo Service Fabric para manter o serviço altamente disponível e devidamente balanceado.

`runAsync()` não deve bloquear sincronicamente. A implementação de runAsync deve retornar um CompletableFuture para permitir que o tempo de execução continue. Se sua carga de trabalho precisar implementar uma tarefa demorada que deve ser executada dentro do CompletableFuture.

#### <a name="cancellation"></a>Cancelamento
O cancelamento da sua carga de trabalho é um esforço cooperativo orquestrado pelo token de cancelamento fornecido. O sistema aguarda o encerramento da tarefa (por conclusão bem-sucedida, cancelamento ou falha) antes de prosseguir. É importante honrar o token de cancelamento, concluir qualquer trabalho e sair do `runAsync()` o mais rapidamente possível quando o sistema solicita o cancelamento. O exemplo a seguir demonstra como processar um evento de cancelamento:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

    // TODO: Replace the following sample code with your own logic
    // or remove this runAsync override if it's not needed in your service.

    return CompletableFuture.runAsync(() -> {
        long iterations = 0;
        while(true)
        {
        cancellationToken.throwIfCancellationRequested();
        logger.log(Level.INFO, "Working-{0}", ++iterations);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex){}
        }
    });
}
```

Neste exemplo de serviço sem estado, a contagem é armazenada em uma variável local. Mas como esse é um serviço sem estado, o valor que é armazenado existe apenas para o ciclo de vida atual da sua instância de serviço. Quando o serviço se move ou é reiniciado, o valor é perdido.

## <a name="create-a-stateful-service"></a>Criar um serviço com estado
O Service Fabric introduz um novo tipo de serviço que é com estado. Um serviço com estado pode manter o estado de maneira confiável dentro do próprio serviço, localizado em conjunto com o código que o está usando. O estado é altamente disponibilizado pelo Service Fabric sem a necessidade de persistir o estado em um repositório externo.

Para converter o valor de contador de sem estado para altamente disponível e persistente, mesmo quando o serviço for movido ou reiniciado, você precisa de um serviço com estado.

No mesmo diretório que o aplicativo HelloWorld, você pode adicionar um novo serviço executando o comando `yo azuresfjava:AddService`. Escolha o "Serviço com Estado Confiável" para a sua estrutura e nomeie o serviço como "HelloWorldStateful". 

Seu aplicativo agora deve ter dois serviços: o serviço sem estado HelloWorld e o serviço com estado HelloWorldStateful.

Um serviço com estado tem os mesmos pontos de entrada que um serviço sem estado. A principal diferença é a disponibilidade de um provedor de estado que pode armazenar estados de maneira confiável. O Service Fabric é fornecido com uma implementação de provedor de estado chamada Coleções Confiáveis, que permite que você crie estruturas de dados replicados por meio do Gerenciador de Estado Confiável. Por padrão, um serviço confiável com estado usa esse provedor de estado.

Abra HelloWorldStateful.java em **HelloWorldStateful -> src**, que contém o método RunAsync a seguir:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    Transaction tx = stateManager.createTransaction();
    return this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap").thenCompose((map) -> {
        return map.computeAsync(tx, "counter", (k, v) -> {
            if (v == null)
                return 1L;
            else
                return ++v;
            }, Duration.ofSeconds(4), cancellationToken)
                .thenCompose((r) -> tx.commitAsync())
                .whenComplete((r, e) -> {
            try {
                tx.close();
            } catch (Exception e) {
                logger.log(Level.SEVERE, e.getMessage());
            }
        });
    });
}
```

### <a name="runasync"></a>RunAsync
`RunAsync()` opera da mesma forma em serviços com e sem estado. No entanto, em um serviço com estado, a plataforma executa trabalho adicional em seu nome antes de executar `RunAsync()`. Esse trabalho pode incluir garantir que o Gerenciador de Estado Confiável e as Coleções Confiáveis estejam prontos para uso.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Coleções Confiáveis e Gerenciador de Estado Confiável
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/en-us/java/api/microsoft.servicefabric.data.collections._reliable_hash_map) é uma implementação de dicionário que você pode usar para armazenar o estado no serviço de forma confiável. Com o Service Fabric e os Hashmaps Confiáveis, você agora pode armazenar dados diretamente em seu serviço sem a necessidade de um repositório persistente externo. Os Hashmaps Confiáveis tornam os dados altamente disponíveis. O Service Fabric consegue isso criando e gerenciando várias *réplicas* do seu serviço para você. Ele também fornece uma API que abstrai as complexidades de gerenciar essas réplicas e as respectivas transições de estado.

As Coleções Confiáveis podem armazenar qualquer tipo Java, incluindo tipos personalizados, com algumas limitações:

* O Service Fabric torna seu estado altamente disponível *replicando* o estado entre nós, e os Hashmaps Confiáveis armazenam seus dados no disco local em cada réplica. Isso significa que tudo o que é armazenado nos Hashmaps Confiáveis deve ser *serializável*. 
* Os objetos são replicados para alta disponibilidade quando você confirma transações nos Hashmaps Confiáveis. Objetos armazenados nos Hashmaps confiáveis são mantidos na memória local do serviço. Isso significa que você tem uma referência local para o objeto.
  
   É importante que você não modifique instâncias locais desses objetos sem executar uma operação de atualização na coleção confiável em uma transação. Isso ocorre porque as mudanças para instâncias de objetos locais não serão replicadas automaticamente. Você deve inserir novamente o objeto de volta no dicionário ou usar um dos métodos *atualizar* do dicionário.

O Gerenciador de Estado Confiável gerencia os Hashmaps Confiáveis para você. Basta solicitar ao Gerenciador de Estado Confiável uma coleção confiável por nome a qualquer momento e em qualquer lugar no seu serviço. O Gerenciador de Estado Confiável assegura que você obtenha uma referência de volta. Não é recomendável salvar referências nas instâncias de coleção confiável em propriedades ou variáveis de membro de classe. É preciso tomar muito cuidado para garantir que a referência seja definida para uma instância o tempo todo no ciclo de vida do serviço. O Gerenciador de Estado Confiável faz esse trabalho para você e jé otimizado para repetir visitas.


### <a name="transactional-and-asynchronous-operations"></a>Operações transacionais e assíncronas
```java
return map.computeAsync(tx, "counter", (k, v) -> {
    if (v == null)
        return 1L;
    else
        return ++v;
    }, Duration.ofSeconds(4), cancellationToken)
        .thenCompose((r) -> tx.commitAsync())
        .whenComplete((r, e) -> {
    try {
        tx.close();
    } catch (Exception e) {
        logger.log(Level.SEVERE, e.getMessage());
    }
});
```

As operações nos Hashmaps Confiáveis são assíncronas. Isso ocorre porque as operações de gravação nas Coleções Confiáveis executam operações de E/S para replicar e manter os dados no disco.

As operações de Hashmap Confiável são *transacionais*, de modo que você pode manter o estado consistente entre vários Hashmaps Confiáveis e operações. Por exemplo, você pode obter um item de trabalho de um Dicionário Confiável, executar uma operação nele e salvar o resultado em outro Hashmap Confiável, tudo em uma única transação. Trata-se de uma operação atômica e ela garante que toda a operação seja bem-sucedida ou revertida. Se ocorrer um erro depois de remover o item da fila, mas antes de salvar o resultado, toda a transação será revertida e o item permanecerá na fila para processamento.


## <a name="run-the-application"></a>Executar o aplicativo

O scaffolding Yeoman inclui um script gradle para compilar o aplicativo e os scripts bash para implantar e remover o aplicativo. Para executar o aplicativo, primeiro compile o aplicativo com gradle:

```bash
$ gradle
```

Isso produz um pacote de aplicativos do Service Fabric que poderá ser implantado usando a CLI do Service Fabric.

### <a name="deploy-with-service-fabric-cli"></a>Implantar com a CLI do Service Fabric

O script install.sh contém os comandos da CLI do Service Fabric necessários para implantar o pacote de aplicativos. Execute o script install.sh para implantar o aplicativo.

```bash
$ ./install.sh
```

## <a name="next-steps"></a>Próximas etapas

* [Introdução à CLI do Service Fabric](service-fabric-cli.md)
