<properties
   pageTitle="Pilha de comunicação padrão fornecida pela Malha de Serviço"
   description="Este artigo descreve a pilha de comunicação padrão fornecida pela estrutura do Serviço Confiável para que serviços e clientes possam se comunicar."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="04/13/2015"
   ms.author="bharatn@microsoft.com"/>

# Pilha de comunicação padrão fornecida pela Estrutura de Serviços Confiáveis
Para autores de serviço que não estejam atrelados a uma implementação específica da pilha de comunicação (WebAPI, WCF etc), a estrutura oferece as peças de comunicação relativas ao Cliente e ao serviço que podem ser usadas para configurar a comunicação entre o Serviço e o Cliente.

> [AZURE.NOTE]Atualize o pacotes NuGet mais recentes para obter os recursos mencionados abaixo.

## Ouvinte de comunicação de serviço
O ouvinte de comunicação padrão para o serviço é implementado na classe `ServiceCommunicationListener`

```csharp

public class ServiceCommunicationListener<TServiceImplementation> : ICommunicationListener where TServiceImplementation : class
{
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType);
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType, string endpointResourceName);

    public void Abort();
    public Task CloseAsync(CancellationToken cancellationToken);
    public void Initialize(ServiceInitializationParameters serviceInitializationParameters);
    public Task<string> OpenAsync(CancellationToken cancellationToken);
}

```
Os métodos que o serviço implementa e deseja expor para seus clientes são definidos como métodos assíncronos em uma interface que herda da interface do `IService`. O serviço pode simplesmente instanciar o objeto do `ServiceCommunicationListener` e retorná-lo no método [`CreateCommunicationListener`](service-fabric-reliable-services-communication.md). Por exemplo, o código do serviço HelloWorld para configurar essa pilha de comunicação pode ser definido da seguinte maneira.

```csharp

[DataContract]
public class Message
{
    [DataMember]
    public string Content;
}

public interface IHelloWorld : IService
{
    Task<Message> GetGreeting();
}

public class HelloWorldService : StatelessService, IHelloWorld
{
    public const string ServiceTypeName = "HelloWorldUsingDefaultCommunicationType";

    private Message greeting = new Message() { Content = "Default greeting" };

    protected override ICommunicationListener CreateCommunicationListener()
    {
        return new ServiceCommunicationListener<HelloWorldService>(this);
    }

    public Task<Message> GetGreeting()
    {
        return Task.FromResult(this.greeting);
    }
}

```
> [AZURE.NOTE]Os argumentos e os tipos de retorno na interface de serviço, como a classe de mensagem acima, devem ser serializáveis pelo [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx) do .net.


## Desenvolvimento de clientes para se comunicarem com o ServiceCommunicationListener
Para que os clientes se comuniquem com serviços que usamo o `ServiceCommunicationListener`, a estrutura oferece uma classe `ServiceProxy`.

```csharp

public abstract class ServiceProxy : IServiceProxy
{
...

    public static TServiceInterface Create<TServiceInterface>(Uri serviceName);

...
}

```

Os clientes podem instanciar um objeto de proxy de serviço que implementa a Interface de Serviço correspondente e chama os métodos no objeto de proxy.

```csharp

var helloWorldClient = ServiceProxy.Create<IHelloWorld>(helloWorldServiceName);

var message = await helloWorldClient.GetGreeting();

Console.WriteLine("Greeting is {0}", message.Content);


```

>[AZURE.NOTE]A estrutura de comunicação trata de propagar as exceções geradas no serviço para o cliente. Dessa forma, a lógica de manipulação de exceção no cliente que usa o ServiceProxy pode tratar diretamente das exceções potencialmente geradas pelo serviço.
 

<!---HONumber=July15_HO4-->