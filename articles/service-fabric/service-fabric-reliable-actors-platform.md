<properties
   pageTitle="Reliable Actors no Service Fabric | Microsoft Azure"
   description="Descreve como os Reliable Actors usam os recursos da plataforma do Service Fabric que abordam os conceitos do ponto de vista dos desenvolvedores do ator."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/15/2016"
   ms.author="amanbha"/>

# Como Reliable Actors usam a plataforma do Service Fabric

Os atores usam o modelo de aplicativo do Service Fabric do Azure para gerenciar o ciclo de vida do aplicativo. Cada tipo de ator é mapeado para um [tipo de serviço](service-fabric-application-model.md#describe-a-service) do Service Fabric. O código de ator é [empacotado](service-fabric-application-model.md#package-an-application) como um aplicativo de Malha do serviço e [implantado](service-fabric-deploy-remove-applications.md#deploy-an-application) no cluster.

## Conceito do modelo de aplicativo de exemplo para atores

Vejamos o exemplo de um projeto de ator [criado usando o Visual Studio](service-fabric-reliable-actors-get-started.md), para ilustrar alguns dos conceitos acima.

O manifesto do aplicativo, o manifesto do serviço e o arquivo de configuração Settings.xml são incluídos no projeto para o serviço de ator quando a solução é criada no Visual Studio. Isso é mostrado na captura de tela abaixo.

![Projeto criado no Visual Studio][1]

O tipo de aplicativo e a versão do aplicativo no qual o ator é empacotado podem ser encontrados examinando-se o manifesto do aplicativo que está incluído no projeto para o serviço de ator. O trecho de código de um manifesto de aplicativo apresentado a seguir é um exemplo disso.

~~~
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                     ApplicationTypeName="VoiceMailBoxApplication"
                     ApplicationTypeVersion="1.0.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric">
~~~

O tipo de serviço que o tipo de ator mapeia pode ser encontrado examinando-se o manifesto do serviço que está incluído no projeto para o serviço de ator. O trecho de código de um manifesto do serviço apresentado a seguir é um exemplo disso.

~~~
<StatefulServiceType ServiceTypeName="VoiceMailBoxActorServiceType" HasPersistedState="true">
~~~

Quando o pacote de aplicativo é criado por meio do Visual Studio, os logs na janela de Saída de Compilação indicam o local do pacote de aplicativos. Por exemplo:

    -------- Package started: Project: VoiceMailBoxApplication, Configuration: Debug x64 ------
    VoiceMailBoxApplication -> C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug

Apresentamos a seguir uma lista parcial do local acima (listagem completa omitida para fins de brevidade).

    C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug>tree /f
    Folder PATH listing
    Volume serial number is 303F-6F91
    C:.
    │   ApplicationManifest.xml
    │
    ├───VoiceMailBoxActorServicePkg
    │   │   ServiceManifest.xml
    │   │
    │   ├───Code
    │   │   │   Microsoft.ServiceFabric.Actors.dll
    │   │   │       :
    │   │   │   Microsoft.ServiceFabric.Services.dll
    │   │   │   ServiceFabricServiceModel.dll
    │   │   │   System.Fabric.Common.Internal.dll
    │   │   │   System.Fabric.Common.Internal.Strings.dll
    │   │   │   VoiceMailBox.exe
    │   │   │   VoiceMailBox.exe.config
    │   │   │   VoiceMailBox.Interfaces.dll
    │   │   │
    │   │   └───pt-BR
    │   │           System.Fabric.Common.Internal.Strings.resources.dll
    │   │
    │   └───Config
    │           Settings.xml
    │
    └───VoicemailBoxWebServicePkg
        │   ServiceManifest.xml
        │
        └───Code
            │   Microsoft.Owin.dll
            │       :
            │   Microsoft.ServiceFabric.Services.dll
            │       :
            │   System.Fabric.Common.Internal.dll
            │   System.Fabric.Common.Internal.Strings.dll
            │       :
            │   VoiceMailBox.Interfaces.dll
            │   VoicemailBoxWebService.exe
            │   VoicemailBoxWebService.exe.config
            │
            └───pt-BR
                    System.Fabric.Common.Internal.Strings.resources.dll

A listagem acima mostra os assemblies que implementam o ator VoicemailBox sendo incluído no pacote de códigos dentro do pacote de serviços, no pacote de aplicativos.

O gerenciamento subsequente (isto é, atualizações e eventual exclusão) do aplicativo também é executado usando mecanismos de gerenciamento de aplicativo do Service Fabric. Para obter mais informações, confira os tópicos sobre o [modelo de aplicativo](service-fabric-application-model.md), [implantação e remoção de aplicativos](service-fabric-deploy-remove-applications.md), e [atualização de aplicativo](service-fabric-application-upgrade.md).

## Escalabilidade para serviços de ator
Os administradores de cluster podem criar um ou mais serviços de ator de cada tipo de serviço no cluster. Cada um desses serviços de ator pode ter uma ou mais partições (semelhantes a qualquer outro serviço da Malha do Serviço). A capacidade de criar vários serviços de um tipo de serviço (que é mapeado para um tipo de ator) e a capacidade de criar várias partições para um serviço permite que o aplicativo de ator seja dimensionado. Confira o artigo em [escalabilidade](service-fabric-concepts-scalability.md) para obter mais informações.

> [AZURE.NOTE] Serviços de ator sem estado precisam ter uma contagem de [instância](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services) de 1. Não há suporte para mais de uma instância de um serviço de ator sem estado em uma partição. Portanto, os serviços de ator sem estado têm a opção de aumentar a contagem de instâncias para obter escalabilidade. Eles devem usar as opções de escalabilidade descritas no [artigo sobre escalabilidade](service-fabric-concepts-scalability.md).

## Conceitos de partição de Malha do Serviço para atores
A ID de um ator é mapeada para uma partição de um serviço de ator. O ator é criado dentro da partição para a qual sua ID do ator está mapeada. Quando um ator é criado, o tempo de execução dos atores grava um [evento EventSource](service-fabric-reliable-actors-diagnostics.md#eventsource-events) que indica em qual partição o ator está criado. Abaixo está um exemplo desse evento que indica que um ator com ID `-5349766044453424161` foi criado na partição `b6afef61-be9a-4492-8358-8f473e5d2487` do serviço `fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService`, o aplicativo `fabric:/VoicemailBoxAdvancedApplication`.

    {
      "Timestamp": "2015-04-26T10:12:20.2485941-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -5349766044453424161, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: 0583c745-1bed-43b2-9545-29d7e3448156.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-5349766044453424161",
        "isStateful": "True",
        "replicaOrInstanceId": "130906628008120392",
        "partitionId": "b6afef61-be9a-4492-8358-8f473e5d2487",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

Outro ator com a ID `-4952641569324299627` foi criado em uma partição diferente (`5405d449-2da6-4d9a-ad75-0ec7d65d1a2a`) do mesmo serviço, conforme indicado pelo evento abaixo.

    {
      "Timestamp": "2015-04-26T15:06:56.93882-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -4952641569324299627, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: c146fe53-16d7-4d96-bac6-ef54613808ff.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-4952641569324299627",
        "isStateful": "True",
        "replicaOrInstanceId": "130745418574851853",
        "partitionId": "5405d449-2da6-4d9a-ad75-0ec7d65d1a2a",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

> [AZURE.NOTE] Alguns campos dos eventos acima são omitidos para fins de brevidade.

A ID de partição pode ser usada para obter outras informações sobre a partição. Por exemplo, a ferramenta [Gerenciador do Service Fabric](service-fabric-visualizing-your-cluster.md) pode ser usada para exibir informações sobre a partição e o serviço e o aplicativo ao qual ele pertence. A captura de tela abaixo mostra informações sobre a partição `5405d449-2da6-4d9a-ad75-0ec7d65d1a2a`, que continha o ator com ID `-4952641569324299627` no exemplo acima.

![Informações sobre uma partição no Gerenciador do Service Fabric][3]

Os atores podem obter, por meio de programação, a ID de partição, nome, nome do aplicativo e outras informações específicas da plataforma do Service Fabric por meio de `Host.ActivationContext` e os membros da classe base de `Host.StatelessServiceInitialization` ou `Host.StatefulServiceInitializationParameters` da qual o tipo de ator deriva. Apresentamos a seguir um exemplo do trecho de código.

```csharp
public void ActorMessage(StatefulActorBase actor, string message, params object[] args)
{
    if (this.IsEnabled())
    {
        string finalMessage = string.Format(message, args);
        ActorMessage(
            actor.GetType().ToString(),
            actor.Id.ToString(),
            actor.ActorService.ServiceInitializationParameters.CodePackageActivationContext.ApplicationTypeName,
            actor.ActorService.ServiceInitializationParameters.CodePackageActivationContext.ApplicationName,
            actor.ActorService.ServiceInitializationParameters.ServiceTypeName,
            actor.ActorService.ServiceInitializationParameters.ServiceName.ToString(),
            actor.ActorService.ServiceInitializationParameters.PartitionId,
            actor.ActorService.ServiceInitializationParameters.ReplicaId,
            FabricRuntime.GetNodeContext().NodeName,
            finalMessage);
    }
}
```

### Conceitos de partição de Malha do Serviço para atores sem monitoração de estado
Atores sem monitoração de estado são criados dentro de uma partição de um serviço sem monitoração de estado da Malha do Serviço. A ID do ator determina em qual partição o ator de é criado. A contagem de [instância](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services) para um serviço de ator sem estado deve ser de 1. Não há suporte para a alteração da contagem de instâncias para qualquer outro valor. Portanto, o ator é criado dentro da instância de serviço única dentro da partição.

> [AZURE.TIP] O tempo de execução de Atores da Malha emite alguns eventos de [ relacionados instâncias de ator sem monitoração de estado](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateless-actor-instances). Eles são úteis para diagnóstico e monitoramento de desempenho.

Quando um ator sem monitoração de estado é criado, o tempo de execução dos atores grava um [evento EventSource](service-fabric-reliable-actors-diagnostics.md#eventsource-events) que indica em qual partição e instância o ator está criado. Abaixo está um exemplo desse evento. Isso indica que um ator com a ID `abc` foi criado na instância `130745709600495974` da partição `8c828833-ccf1-4e21-b99d-03b14d4face3`, do serviço `fabric:/HelloWorldApplication/HelloWorldActorService`, aplicativo `fabric:/HelloWorldApplication`.

    {
      "Timestamp": "2015-04-26T18:17:46.1453113-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: HelloWorld.HelloWorld, actor ID: abc, stateful: False, replica/instance ID: 130,745,709,600,495,974, partition ID: 8c828833-ccf1-4e21-b99d-03b14d4face3.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "HelloWorld.HelloWorld",
        "actorId": "abc",
        "isStateful": "False",
        "replicaOrInstanceId": "130745709600495974",
        "partitionId": "8c828833-ccf1-4e21-b99d-03b14d4face3",
        "serviceName": "fabric:/HelloWorldApplication/HelloWorldActorService",
        "applicationName": "fabric:/HelloWorldApplication",
      }
    }

> [AZURE.NOTE] Alguns campos dos eventos acima são omitidos para fins de brevidade.

### Conceitos de partição de Malha do Serviço para atores com monitoração de estado
Atores com monitoração de estado são criados dentro de uma partição do serviço com monitoração de estado da Malha do Serviço. A ID do ator determina em qual partição o ator de é criado. Cada partição do serviço pode ter uma ou mais [réplicas](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services) que são colocadas em diferentes nós do cluster. Ter várias réplicas fornece confiabilidade para o estado de ator. O Gerenciador de Recursos do Azure otimiza o posicionamento baseado na falha disponível e nos domínios de atualização do cluster. Nunca são colocadas duas réplicas da mesma partição no mesmo nó. Os atores são sempre criados na réplica primária da partição à qual sua ID do ator é mapeada.

> [AZURE.TIP] O tempo de execução de Atores da Malha emite alguns eventos de [ relacionados réplicas de ator com monitoração de estado](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateful-actor-replicas). Eles são úteis para diagnóstico e monitoramento de desempenho.

Lembre-se de que no exemplo do [VoiceMailBoxActor apresentado anteriormente](#service-fabric-partition-concepts-for-actors), o ator com ID `-4952641569324299627` foi criado na partição `5405d449-2da6-4d9a-ad75-0ec7d65d1a2a`. O evento EventSource desse exemplo também indicou que o ator foi criado na réplica `130745418574851853` dessa partição. Essa era a réplica primária de partição no momento em que o ator foi criado. A captura de tela do Gerenciador da Malha do Serviço abaixo confirma isso.

![Réplica primária no Gerenciador do Service Fabric][4]

## Opções do provedor de estado de ator
Há alguns provedores de estado de ator padrão que estão incluídos no tempo de execução dos Atores. Para escolher um provedor de estado apropriado para um serviço de ator, é necessário entender como os provedores de estado usam os recursos da plataforma subjacentes da Malha de Serviço para tornar o estado de ator altamente disponível.

Por padrão, um ator com monitoração de estado usa o provedor de estado de ator de armazenamento com valor de chave. Esse provedor de estado é criado no armazenamento de chave-valor distribuído fornecido pela plataforma do Service Fabric. O estado é salvo permanentemente no disco local do nó que está hospedando a [réplica](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services) primária. O estado é também replicado e permanentemente salvo nos discos locais de nós que hospedam as réplicas secundárias. O salvamento do estado é concluído somente quando um quorum de réplicas confirma o estado em seus discos locais. O armazenamento de chave-valor tem recursos avançados para detectar inconsistências como falso progresso e corrigi-las automaticamente.

O tempo de execução dos Atores também inclui um `VolatileActorStateProvider`. Esse provedor de estado replica o estado para réplicas (cópias do estado), mas o estado permanece na memória em cada réplica. Se uma réplica falhar e voltar a funcionar, seu estado será recriado da outra réplica. No entanto, se todas as réplicas falharem simultaneamente, os dados de estado serão perdidos. Portanto, esse provedor de estado é adequado para aplicativos em que os dados possam resistir a falhas de algumas réplicas e possam sobreviver aos failovers planejados, como atualizações. Se todas as réplicas forem perdidas, os dados precisarão ser recriados usando mecanismos externos ao Service Fabric. Você pode configurar um ator com monitoração de estado para usar o provedor de estado de ator volátil, adicionando o atributo `VolatileActorStateProvider` à classe do ator ou um atributo de nível de assembly.

O trecho de código a seguir mostra como alterar todos os atores no assembly que não tenham um atributo de provedor de estado explícito para usar o `VolatileActorStateProvider`.

```csharp
[assembly:Microsoft.ServiceFabric.Actors.VolatileActorStateProvider]
```

O trecho de código a seguir mostra como alterar o provedor de estado para um tipo de ator específico, `VoicemailBox` neste caso, para ser `VolatileActorStateProvider`.

```csharp
[VolatileActorStateProvider]
public class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

Observe que a alteração do provedor de estado requer que o serviço de ator seja recriado. Os provedores de estado não podem ser alterados como parte da atualização do aplicativo.

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/manifests-in-vs-solution.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png

<!---HONumber=AcomDC_0316_2016-->
