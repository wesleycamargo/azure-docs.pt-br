<properties
   pageTitle="Visão geral do modelo de comunicação de serviço"
   description="Este artigo descreve os conceitos básicos do modelo de comunicação com suporte da API dos Serviços Confiáveis."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="04/13/2015"
   ms.author="bharatn@microsoft.com"/>

# Modelo de comunicação de serviço

O modelo de programação Serviços Confiáveis permite que os autores de serviço especifiquem o mecanismo de comunicação que desejam usar para expor seus pontos de extremidade de serviço, além de fornecer abstrações que os clientes podem usar para descobrir e se comunicar com o ponto de extremidade de serviço.

## Configurando a pilha de comunicação de serviço

A API de Serviços Confiáveis permite aos autores de serviço conectar a pilha de comunicação de sua escolha no serviço implementando o método a seguir em seu serviço.

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}

```

A interface `ICommunicationListener` define a interface que deve ser implementada pelo ouvinte de comunicação de um serviço.

```csharp

public interface ICommunicationListener
{
    void Initialize(ServiceInitializationParameters serviceInitializationParameters);

    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```
Os pontos de extremidade necessários para o serviço são descritos no [manifesto do serviço](service-fabric-application-model.md), na seção Pontos de extremidade.

```xml

<Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http" Type="Input" />
    <Endpoints>
</Resources>

```

O ouvinte de comunicação pode acessar os recursos do ponto de extremidade alocados para ele no `CodePackageActivationContext`, no `ServiceInitializationParameters`, e começar a escutar solicitações quando ele é aberto.

```csharp

var codePackageActivationContext = this.serviceInitializationParameters.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE]Os recursos dos pontos de extremidade são comuns a todo o pacote de serviço e são alocados pela Malha do Serviço quando o pacote de serviço é ativado. (Confira o [Modelo de serviço da Malha do Serviço](../service-fabric-service-model.md) para obter mais detalhes). Assim, todas as réplicas hospedadas no mesmo ServiceHost compartilham a mesma porta. Isso significa que o ouvinte de comunicação deve oferecer suporte ao compartilhamento de porta. A maneira recomendada de fazer isso é que o ouvinte de comunicação use a ID da partição e a ID de réplica/instância ao gerar o endereço de escuta.

```csharp

var replicaOrInstanceId = 0;
var parameters = this.serviceInitializationParameters as StatelessServiceInitializationParameters;
if (parameters != null)
{
  replicaOrInstanceId = parameters.InstanceId;
}
else
{
  replicaOrInstanceId = ((StatefulServiceInitializationParameters) this.serviceInitializationParameters).ReplicaId;
}

var nodeContext = FabricRuntime.GetNodeContext();

var listenAddress = new Uri(
    string.Format(
        CultureInfo.InvariantCulture,
        "{0}://{1}:{2}/{5}/{3}-{4}",
        scheme,
        nodeContext.IPAddressOrFQDN,
        port,
        this.serviceInitializationParameters.PartitionId,
        replicaOrInstanceId,
        Guid.NewGuid().ToString()));

```

## Comunicação do cliente com o serviço
A API de Serviços Confiáveis fornece as abstrações a seguir, que facilitam a criação de clientes para comunicação com serviços.

## ServicePartitionResolver
A classe ServicePartitionResolver ajuda o cliente a determinar o ponto de extremidade de um serviço da Malha do Serviço no tempo de execução.

> [AZURE.TIP]O processo de determinar o ponto de extremidade de um serviço é chamado de Resolução do Ponto de Extremidade de Serviço na terminologia da Malha do Serviço.

```csharp

public delegate FabricClient CreateFabricClientDelegate();

// ServicePartitionResolver methods

public ServicePartitionResolver(CreateFabricClientDelegate createFabricClient);

Task<ResolvedServicePartition> ResolveAsync(Uri serviceName,
    long partitionKey,
    CancellationToken cancellationToken);

Task<ResolvedServicePartition> ResolveAsync(ResolvedServicePartition previousRsp,
    CancellationToken cancellationToken);


```
> [AZURE.NOTE]FabricClient é o objeto usado para se comunicar com o cluster da Malha do Serviço em várias operações de gerenciamento no cluster da Malha do Serviço.

Normalmente, o código do cliente não precisa trabalhar com `ServicePartitionResolver` diretamente. Ele é criado e passado para outras classes auxiliares na API de Serviços Confiáveis, que usam o resolvedor internamente e ajudam o cliente a se comunicar com o serviço.

## CommunicationClientFactory
`ICommunicationClientFactory` define a interface base implementada por uma fábrica de cliente de comunicação que produz clientes que podem se comunicar com um serviço ServiceFabric. A implementação de CommunicationClientFactory dependerá da pilha de comunicação usada pelo serviço da Malha do Serviço com o qual o cliente deseja se comunicar. A API dos Serviços Confiáveis fornece um CommunicationClientFactoryBase<TCommunicationClient> que fornece uma implementação básica dessa interface `ICommunicationClientFactory` e executa tarefas que são comuns a todas as pilhas de comunicação (como usar um `ServicePartitionResolver` para determinar o ponto de extremidade de serviço). Os clientes geralmente implementam a classe abstrata CommunicationClientFactoryBase para lidar com lógica específica da pilha de comunicação.

```csharp

protected CommunicationClientFactoryBase(
    ServicePartitionResolver servicePartitionResolver = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null);


public class MyCommunicationClient : ICommunicationClient
{
    public MyCommunicationClient(MyCommunicationChannel communicationChannel)
    {
      this.CommunicationChannel = communicationChannel;
    }
    public MyCommunicationChannel CommunicationChannel { get; private set; }
    public ResolvedServicePartition ResolvedServicePartition;

}

public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient1 client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(ResolvedServiceEndpoint endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(ResolvedServiceEndpoint endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

## ServicePartitionClient
`ServicePartitionClient` usa CommunicationClientFactory (e internamente o ServicePartitionResolver) e ajuda na comunicação com o serviço ao lidar com repetições para exceções transitórias da Malha do Serviço e comunicação comum.

```csharp

public ServicePartitionClient(
    ICommunicationClientFactory<TCommunicationClient> factory,
    Uri serviceName,
    long partitionKey);

public async Task<TResult> InvokeWithRetryAsync<TResult>(
    Func<TCommunicationClient, Task<TResult>> func,
    CancellationToken cancellationToken,
    params Type[] doNotRetryExceptionTypes)

```

Um padrão de uso típico seria assim:

```csharp

public MyCommunicationClientFactory myCommunicationClientFactory;
public Uri myServiceUri;

... other client code ..

// Call the service corresponding to the partitionKey myKey.

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myKey);

  var result = await myServicePartitionClient.InvokeWithRetryAsync(
      client =>
      {
        // Communicate with the service using the client.
        throw new NotImplementedException();
      },
      CancellationToken.None);


... other client code ...

```

## Próximas etapas
* [Pilha de comunicação padrão fornecida pela Estrutura de Serviços Confiáveis](service-fabric-reliable-services-communication-default.md)

* [Pilha de comunicação baseada no WCF fornecida pela Estrutura de Serviços Confiáveis](service-fabric-reliable-services-communication-wcf.md)

* [Escrever um serviço usando a API dos Serviços Confiáveis que usa a pilha de comunicação WebAPI](service-fabric-reliable-services-communication-webapi.md)
 

<!---HONumber=July15_HO2-->