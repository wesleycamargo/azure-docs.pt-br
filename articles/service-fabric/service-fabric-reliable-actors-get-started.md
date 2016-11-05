---
title: Introdução aos Reliable Actors do Service Fabric | Microsoft Docs
description: Este tutorial orienta você pelas etapas de criação, depuração e implantação de um serviço simples baseado em ator usando os Reliable Actors do Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/06/2016
ms.author: vturecek

---
# Introdução aos Reliable Actors
Este artigo explica os conceitos básicos dos Reliable Actors do Service Fabric e orienta você durante a criação, a depuração e a implantação de um aplicativo Reliable Actor simples no Visual Studio.

## Instalação e configuração
Antes de começar, verifique se há um ambiente de desenvolvimento do Service Fabric configurado no seu computador. Se você precisa configurá-lo, confira as instruções detalhadas em [Como configurar o ambiente de desenvolvimento](service-fabric-get-started.md).

## Conceitos básicos
Para começar a usar os Reliable Actors, você só precisa entender quatro conceitos básicos:

* **Serviço do ator**. Os Reliable Actors são empacotados em Reliable Services que podem ser implantados na infraestrutura do Service Fabric. Um serviço pode hospedar um ou mais atores. Examinaremos mais detalhadamente as vantagens e desvantagens de ter um ator ou vários atores por serviço abaixo. Por enquanto, vamos supor que precisamos implementar apenas um ator.
* **Interface do Ator**. A interface do ator é usada para definir uma interface pública fortemente tipada de um ator. Na terminologia do modelo de Reliable Actor, a interface do ator define os tipos de mensagem que o ator pode entender e processar. A interface do ator é usada por outros atores ou aplicativos cliente para “enviar” (de modo assíncrono) mensagens ao ator. Os Reliable Actors pode implantar várias interfaces. Como veremos, um ator de HelloWorld pode implantar a interface IHelloWorld, mas também pode implantar uma interface ILogging que define mensagens e/ou funcionalidades diferentes.
* **Registro do ator**. No serviço Reliable Actors, o tipo de ator precisa ser registrado. Dessa forma, o Service Fabric está ciente do novo tipo e pode usá-lo para criar novos atores.
* **Classe ActorProxy**. A classe ActorProxy é usada por aplicativos cliente para invocar os métodos expostos por meio de suas interfaces. A classe ActorProxy apresenta duas funcionalidades importantes:
  * Ela resolve nomes. Ela consegue localizar o ator no cluster (localizar o nó do cluster no qual ele está hospedado).
  * Ela lida com falhas. Ela pode repetir as invocações do método e determinar novamente o local do ator, por exemplo, depois que uma falha exige que ele seja relocado para outro nó no cluster.

Vale a pena mencionar as seguintes regras que pertencem às interfaces de ator:

* Métodos da interface de ator não podem ser sobrecarregados.
* Métodos da interface de ator não podem ter parâmetros de saída, de referência e opcionais.
* Não há suporte para interfaces genéricas.

## Criar um novo projeto no Visual Studio
Depois de instalar as ferramentas do Service Fabric para Visual Studio, você poderá criar novos tipos de projeto. Os novos tipos de projeto estão na categoria **Nuvem** da caixa de diálogo **Novo Projeto**.

![Ferramentas do Service Fabric para Visual Studio – novo projeto][1]

Na próxima caixa de diálogo, você pode escolher o tipo de projeto que deseja criar.

![Modelos de projeto do Service Fabric][5]

Para o projeto HelloWorld, vamos usar o serviço Reliable Actors do Service Fabric.

Depois de criar a solução, você verá a seguinte estrutura:

![Estrutura de projeto do Service Fabric][2]

## Blocos de construção básicos de Reliable Actors
Uma solução comum do Reliable Actors é composta por três projetos:

* **O projeto do aplicativo (MyActorApplication)**. Esse é o projeto que empacota todos os serviços juntos para implantação. Ele contém os scripts do PowerShell e o *ApplicationManifest.xml* para gerenciamento do aplicativo.
* **O projeto da interface (MyActor.Interfaces)**. Esse é o projeto que contém a definição de interface para o ator. No projeto MyActor.Interfaces, você pode definir as interfaces que serão usadas pelos atores na solução. As interfaces de ator podem ser definidas em qualquer projeto com qualquer nome, mas a interface define o contrato de ator compartilhado pela implementação do ator e os clientes que chamam o ator e, portanto, geralmente faz sentido defini-lo em um assembly separado da implementação do ator e que possa ser compartilhado por vários outros projetos.

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **O projeto do serviço de ator (MyActor)**. Esse é o projeto usado para definir o serviço da Malha do Serviço que hospedará o ator. Ele contém a implementação do ator. Uma implementação do ator é uma classe derivada do tipo base `Actor` e implementa as interfaces definidas no projeto MyActor.Interfaces.

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class MyActor : Actor, IMyActor
{
    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

O serviço de ator deve ser registrado com um tipo de serviço no tempo de execução do Service Fabric. Para que o Serviço de Ator seja executado em suas instâncias de ator, o tipo de ator também deve ser registrado no Serviço de Ator. O método de registro `ActorRuntime` realiza esse trabalho para os atores.

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Se você iniciar um novo projeto no Visual Studio e tiver apenas uma definição de ator, o registro será incluído por padrão no código gerado pelo Visual Studio. Se você definir outros atores no serviço, será preciso adicionar o registro do ator usando:

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [!TIP]
> O tempo de execução dos Atores do Service Fabric emitem alguns [eventos e contadores de desempenho relacionados aos métodos de ator](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Eles são úteis para diagnóstico e monitoramento de desempenho.
> 
> 

## Depurando
As ferramentas para Visual Studio do Service Fabric dão suporte à depuração no computador local. Você pode iniciar uma sessão de depuração pressionando a tecla F5. O Visual Studio cria (se necessário) pacotes. Ele também implanta o aplicativo no cluster do Service Fabric local e anexa o depurador.

Durante o processo de implantação, você poderá ver o andamento na janela **Saída**.

![Janela de saída de depuração do Service Fabric][3]

## Próximas etapas
* [Como os Reliable Actors usam a plataforma do Service Fabric](service-fabric-reliable-actors-platform.md)
* [Gerenciamento de estado do ator](service-fabric-reliable-actors-state-management.md)
* [Ciclo de vida do ator e coleta de lixo](service-fabric-reliable-actors-lifecycle.md)
* [Documentação de referência da API do Ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Exemplo de código](https://github.com/Azure/servicefabric-samples)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG

<!---HONumber=AcomDC_0713_2016-->