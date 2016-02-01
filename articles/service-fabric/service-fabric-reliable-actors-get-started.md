<properties
   pageTitle="Introdução ao Reliable Actors | Microsoft Azure"
   description="Este tutorial orienta você pelas etapas de criação, depuração e implantação de um serviço canônico HelloWorld usando os Reliable Actors da Malha de Serviços."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Reliable Actors: o cenário canônico HelloWorld passo a passo
Este artigo explica os conceitos básicos dos Reliable Actors do Service Fabric e orienta você durante a criação, a depuração e a implantação de um aplicativo simples HelloWorld no Visual Studio.

## Instalação e configuração
Antes de começar, verifique se há um ambiente de desenvolvimento do Service Fabric configurado no seu computador. Se você precisa configurá-lo, confira as instruções detalhadas em [Como configurar o ambiente de desenvolvimento](service-fabric-get-started.md).

## Conceitos básicos
Para começar a usar os Reliable Actors, você só precisa entender quatro conceitos básicos:

* **Serviço do ator**. Os Reliable Actors são empacotados em Serviços que podem ser implantados na infraestrutura do Service Fabric. Um serviço pode hospedar um ou mais atores. Examinaremos mais detalhadamente as vantagens e desvantagens de ter um ator ou vários atores por serviço abaixo. Por enquanto, vamos supor que precisamos implementar apenas um ator.
* **Interface do Ator**. A interface do ator é usada para definir a interface pública de um ator. Na terminologia do modelo de Reliable Actor, a interface do ator define os tipos de mensagem que o ator pode entender e processar. A interface do ator é usada por outros atores ou aplicativos cliente para “enviar” (de modo assíncrono) mensagens ao ator. Os Reliable Actors pode implantar várias interfaces. Como veremos, um ator de HelloWorld pode implantar a interface IHelloWorld, mas também pode implantar uma interface ILogging que define mensagens e/ou funcionalidades diferentes.
* **Registro do ator**. No serviço Reliable Actors, o tipo de ator precisa ser registrado. Dessa forma, o Service Fabric está ciente do novo tipo e pode usá-lo para criar novos atores.
* **Classe ActorProxy**. A classe ActorProxy é usada para associar um Ator e invocar os métodos expostos por meio de suas interfaces. A classe ActorProxy apresenta duas funcionalidades importantes:
	* Ela resolve nomes. Ela consegue localizar o ator no cluster (localizar o nó do cluster no qual ele está hospedado).
	* Ela lida com falhas. Ela pode repetir as invocações do método e determinar novamente o local do ator, por exemplo, depois que uma falha exige que ele seja relocado para outro nó no cluster.

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

* **O projeto Aplicativo (HelloWorldApplication)**. Esse é o projeto que empacota todos os serviços juntos para implantação. Ele contém os scripts do PowerShell e o **ApplicationManifest.xml** para gerenciamento do aplicativo.

* **O projeto de interface (HelloWorld.Interfaces)**. Esse é o projeto que contém a definição de interface para o ator. No projeto HelloWorld.Interfaces, você pode definir as interfaces que serão usadas pelos atores na solução.

```csharp

namespace MyActor.Interfaces
{
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;

    public interface IMyActor : IActor
    {
        Task<string> HelloWorld();
    }
}

```

* **O projeto de serviço (HelloWorld)**. Esse é o projeto usado para definir o serviço da Malha do Serviço que hospedará o ator. Ele contém parte de um código clichê que não precisa ser editado na maioria dos casos (ServiceHost.cs), bem como a implantação do ator. A implantação do ator envolve a implantação de uma classe que deriva de um tipo básico (Ator). Ele também implanta as interfaces que são definidas no projeto HelloWorld.Interfaces.

```csharp

namespace MyActor
{
    using System;
    using System.Threading.Tasks;
    using Interfaces;
    using Microsoft.ServiceFabric.Actors;

    internal class MyActor : StatelessActor, IMyActor
    {
        public Task<string> HelloWorld()
        {
            throw new NotImplementedException();
        }
    }
}

```

O projeto de serviço Reliable Actors contém o código para criar um serviço do Service Fabric. Na definição do serviço, os tipos de ator são registrados, para que possam ser usados ao instanciar novos atores.

```csharp

namespace MyActor
{
    using System;
    using System.Fabric;
    using System.Threading;
    using Microsoft.ServiceFabric.Actors;

    internal static class Program
    {
        private static void Main()
        {
            try
            {
                using (FabricRuntime fabricRuntime = FabricRuntime.Create())
                {
                    fabricRuntime.RegisterActor<MyActor>();

                    Thread.Sleep(Timeout.Infinite);  // Prevents this host process from terminating so services keeps running.
                }
            }
            catch (Exception e)
            {
                ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}

```

Se você iniciar um novo projeto no Visual Studio e tiver apenas uma definição de ator, o registro será incluído por padrão no código gerado pelo Visual Studio. Se você definir outros atores no serviço, será preciso adicionar o registro do ator usando:

```csharp

fabricRuntime.RegisterActor<MyActor>();


```

## Depurando

As ferramentas para Visual Studio do Service Fabric dão suporte à depuração no computador local. Você pode iniciar uma sessão de depuração pressionando a tecla F5. O Visual Studio cria (se necessário) pacotes. Ele também implanta o aplicativo no cluster do Service Fabric local e anexa o depurador. A experiência é semelhante à depuração de um aplicativo ASP.NET.

Durante o processo de implantação, você pode ver o andamento na janela **Saída**.

![Janela de saída de depuração do Service Fabric][3]


## Próximas etapas

- [Introdução aos Reliable Actors do Service Fabric](service-fabric-reliable-actors-introduction.md)
- [Documentação de referência sobre as APIs de Atores](https://msdn.microsoft.com/library/azure/dn971626.aspx)
- [Exemplo de código](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG

<!---HONumber=AcomDC_0121_2016-->