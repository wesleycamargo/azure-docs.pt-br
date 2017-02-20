---
title: Reliable Actors no Service Fabric | Microsoft Docs
description: "Descreve como os Reliable Actors são dispostos em camadas nos Reliable Services e como eles usam os recursos da plataforma do Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: c2e8f2d2f26bb93af5c2858242392b3dc207beb4
ms.openlocfilehash: 2a3a5647f506d61e853f465f241d66fbe8c4ced0


---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Como Reliable Actors usam a plataforma do Service Fabric
Este artigo explica sobre o funcionamento dos Reliable Actors na plataforma do Service Fabric. Os Reliable Actors são executados em uma estrutura hospedada em uma implementação de um Reliable Service com estado chamado *Serviço de Ator*. O Serviço de Ator contém todos os componentes necessários para gerenciar o ciclo de vida e a expedição de mensagens para seus atores:

* O Tempo de Execução de Ator gerencia o ciclo de vida, a coleta de lixo e impõe o acesso single-threaded.
* Um ouvinte de comunicação remota do serviço de ator aceita chamadas de acesso remoto aos atores e as envia a um dispatcher para encaminhamento à instância de ator apropriada.
* O Provedor de Estado de Ator encapsula provedores de estado (como o provedor de estado das Coleções Confiáveis) e fornece um adaptador para o gerenciamento de estado de ator.

Juntos, esses componentes formam a estrutura do Reliable Actor. 

## <a name="service-layering"></a>Disposição em camadas de serviço
Como o próprio Serviço de Ator é um Reliable Service, todos os conceitos de [modelo de aplicativo](service-fabric-application-model.md), ciclo de vida, [empacotamento](service-fabric-application-model.md#package-an-application), [implantação](service-fabric-deploy-remove-applications.md), atualização e dimensionamento dos Reliable Services aplicam-se da mesma maneira aos serviços de Ator. 

![Disposição em camadas do Serviço de Ator][1]

O diagrama acima mostra a relação entre as estruturas do aplicativo do Service Fabric e o código do usuário. Os elementos em azul representam a estrutura do aplicativo dos Reliable Services, os elementos em laranja representam a estrutura do Reliable Actor e os elementos em verde representam o código do usuário. 

Nos Reliable Services, o serviço herda a classe `StatefulService`, que é derivada de `StatefulServiceBase`. (ou `StatelessService` para os serviços sem estado). Nos Reliable Actors, é utilizado o Serviço de Ator, que é uma implementação diferente da classe `StatefulServiceBase` que implementa o padrão de ator no qual seus atores são executados. Como o próprio Serviço de Ator é apenas uma implementação de `StatefulServiceBase`, é possível escrever seu próprio serviço que deriva de `ActorService` e implementar recursos no nível de serviço da mesma forma que você faria ao herdar `StatefulService`, tais como:

* Backup e restauração de serviço.
* Funcionalidade compartilhada para todos os Atores, por exemplo, um disjuntor.
* Chamadas de procedimento de comunicação remota no próprio serviço de ator, bem como em cada ator individual. 

### <a name="using-the-actor-service"></a>Usando o Serviço de Ator
As instâncias de ator têm acesso ao Serviço de Ator no qual estão sendo executadas. Por meio do Serviço de Ator, as instâncias de ator podem obter de forma programática o Contexto de Serviço que tem a ID da partição, o nome do serviço, o nome do aplicativo e outras informações específicas da plataforma do Service Fabric:

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```

Como todos os Reliable Services, o Serviço de Ator deve ser registrado com um tipo de serviço no tempo de execução do Service Fabric. Para que o Serviço de Ator seja executado em suas instâncias de ator, o tipo de ator também deve ser registrado no Serviço de Ator. O método de registro `ActorRuntime` realiza esse trabalho para os atores. No caso mais simples, é possível apenas registrar o tipo de ator e o Serviço de Ator com as configurações padrão será usado implicitamente:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```  

Como alternativa, é possível usar um lambda fornecido pelo método de registro para que você possa construir o Serviço de Ator por conta própria. Isso permite configurar o Serviço de Ator, bem como construir explicitamente as instâncias de ator, nas quais é possível injetar dependências no ator por meio de seu construtor:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

### <a name="actor-service-methods"></a>Métodos do Serviço de Ator
O Serviço de Ator implementa `IActorService`, que, por sua vez, implementa `IService`. Essa é a interface usada pela comunicação remota dos Reliable Services, que possibilita chamadas de procedimento remoto em métodos de serviço. Ela contém métodos no nível de serviço que podem ser chamados remotamente por meio da comunicação remota do serviço.

#### <a name="enumerating-actors"></a>Enumerando os atores
O Serviço de Ator permite que um cliente enumere metadados sobre os atores que estão sendo hospedados pelo serviço. Como o Serviço de Ator é um serviço com estado particionado, a enumeração é realizada por partição. Como cada partição pode conter um grande número de atores, a enumeração é retornada como um conjunto de resultados paginados. É executado um loop das páginas até que todas as páginas sejam lidas. O seguinte exemplo mostra como criar uma lista de todos os atores ativos em uma partição de um serviço de ator:

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

#### <a name="deleting-actors"></a>Excluindo atores
O Serviço de Ator também fornece uma função para a exclusão de atores:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```

Para obter mais informações sobre como excluir atores e seu estado, consulte a [documentação de ciclo de vida do ator](service-fabric-reliable-actors-lifecycle.md).

### <a name="custom-actor-service"></a>Serviço de Ator personalizado
Usando o lambda do registro do ator, também é possível registrar seu próprio serviço de ator personalizado que deriva de `ActorService` , no qual é possível implementar sua própria funcionalidade no nível de serviço. Isso é feito escrevendo uma classe de serviço que herda `ActorService`. Um serviço de ator personalizado herda toda a funcionalidade de tempo de execução de ator de `ActorService` e pode ser usado para implementar seus próprios métodos de serviço.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```


#### <a name="implementing-actor-back-up-and-restore"></a>Implementando o backup e a restauração de ator
 No seguinte exemplo, o serviço de ator personalizado expõe um método para fazer backup dos dados do ator, aproveitando o ouvinte de comunicação remota já presente em `ActorService`:

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```

Neste exemplo, `IMyActorService` é um contrato de comunicação remota que implementa `IService` e que, em seguida, é implementado por `MyActorService`. Ao adicionar esse contrato de comunicação remota, os métodos em `IMyActorService` agora também estão disponíveis para um cliente, por meio da criação de um proxy de comunicação remota usando `ActorServiceProxy`:

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```


## <a name="application-model"></a>Modelo de aplicativo
Os serviços de ator são Reliable Services; portanto, o modelo do aplicativo é o mesmo. No entanto, as ferramentas de build da estrutura de ator geram grande parte dos arquivos de modelo do aplicativo para você.

### <a name="service-manifest"></a>Manifesto do serviço
O conteúdo do ServiceManifest.xml do serviço de ator é gerado automaticamente pelas ferramentas de build da estrutura de ator. Isso inclui:

* O tipo de serviço de ator. O nome do tipo é gerado de acordo com o nome do projeto de ator. Com base no atributo de persistência do ator, o sinalizador HasPersistedState também é definido de acordo.
* Pacote de códigos.
* Pacote de configuração.
* Recursos e pontos de extremidade

### <a name="application-manifest"></a>Manifesto do aplicativo
As ferramentas de build da estrutura de ator criam automaticamente uma definição de serviço padrão para o serviço de ator. As propriedades de serviço padrão são populadas com as ferramentas de build:

* A contagem de conjuntos de réplicas é determinada pelo atributo de persistência no ator. Sempre que o atributo de persistência no ator é alterado, a contagem de conjuntos de réplicas na definição de serviço padrão é redefinida de acordo.
* O esquema de partição e o intervalo são definidos como Int64 Uniforme com o intervalo de chaves Int64 completo.

## <a name="service-fabric-partition-concepts-for-actors"></a>Conceitos de partição de Malha do Serviço para atores
Serviços de ator são serviços com estado particionados. Cada partição de um serviço de ator contém um conjunto de atores. As partições de serviço são distribuídas automaticamente em vários nós no Service Fabric. Dessa forma, as instâncias de ator são distribuídas como resultado disso.

![Particionamento e distribuição de ator][5]

Os Reliable Services podem ser criados com esquemas de partição diferentes e intervalos de chaves de partição. O Serviço de Ator usa o esquema de particionamento Int64 com o intervalo de chaves Int64 completo para mapear os atores para as partições. 

### <a name="actor-id"></a>ID de ator
Cada ator criado no serviço tem uma ID exclusiva associada, representada pela classe `ActorId` . A `ActorId` é um valor de ID opaco que pode ser usado para a distribuição uniforme de atores nas partições de serviço por meio da geração de IDs aleatórias:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```

Cada `ActorId` é codificado em hash para um Int64, motivo pelo qual o serviço de ator deve usar um esquema de particionamento Int64 com o intervalo de chaves Int64 completo. No entanto, valores de ID personalizados podem ser usados para uma `ActorID`, incluindo GUIDs, cadeias de caracteres e Int64s. 

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```

Ao usar GUIDs e cadeias de caracteres, os valores são codificados em hash para um Int64. No entanto, ao fornecer explicitamente um Int64 para uma `ActorId`, o Int64 será mapeado diretamente para uma partição sem hash adicional. Isso pode ser usado para controlar em qual partição os atores são colocados.

## <a name="next-steps"></a>Próximas etapas
* [Gerenciamento de estado do ator](service-fabric-reliable-actors-state-management.md)
* [Ciclo de vida do ator e coleta de lixo](service-fabric-reliable-actors-lifecycle.md)
* [Documentação de referência da API dos Atores](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Exemplo de código](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png



<!--HONumber=Feb17_HO2-->


