<properties
   pageTitle="Introdução aos Atores Confiáveis | Microsoft Azure"
   description="Este tutorial orienta você pelas etapas de criação, depuração e implantação de um serviço canônico HelloWorld usando os Atores Confiáveis da Malha de Serviços."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/10/2015"
   ms.author="claudioc"/>

# Atores Confiáveis: o cenário canônico HelloWorld passo a passo
Este artigo explica os conceitos básicos dos Atores Confiáveis da Malha de Serviços e orienta você durante a criação, depuração e implantação de um aplicativo simples HelloWorld no Visual Studio.

## Instalação e configuração
Antes de começar, verifique se há um ambiente de desenvolvimento da Malha do Serviço configurado no seu computador. Instruções detalhadas sobre como configurar o ambiente de desenvolvimento podem ser encontradas [aqui](service-fabric-get-started.md).

## Conceitos básicos
Para começar a usar os Atores Confiáveis, você só precisa entender quatro conceitos básicos:

* **Serviço do Ator**. Os Atores Confiáveis são empacotados em Serviços que podem ser implantados na infraestrutura da Malha do Serviço. Um serviço pode hospedar um ou mais atores. Posteriormente, examinaremos mais detalhadamente as vantagens e desvantagens de ter um ator ou vários atores por serviço. Por enquanto, vamos supor que precisamos implementar apenas um ator.
* **Interface do Ator**. A interface do ator é usada para definir a interface pública de um ator. Na terminologia do modelo de ator, ela define o tipo de mensagem que o ator é capaz de entender no processo. A interface do ator é usada por outros atores ou aplicativos cliente para ‘enviar’ (de modo assíncrono) mensagens ao ator. Os Atores Confiáveis podem implementar várias interfaces, como veremos, um Ator de HelloWorld pode implementar a interface IHelloWorld, mas também uma interface ILogging que define diferentes mensagens/funcionalidades.
* **Registro do Ator**. No Serviço do Ator, o Tipo de Ator precisa ser registrado para que a Malha do Serviço esteja ciente do novo tipo e possa usá-lo para criar novos atores.
* **Classe ActorProxy**. A classe ActorProxy é usada para associar um Ator e invocar os métodos expostos por meio de suas interfaces. A classe ActorProxy apresenta duas funcionalidades importantes:
	* Resolução de nome: é capaz de localizar o Ator no cluster (encontrar em qual nó do cluster ele está hospedado).
	* Tratamento de falhas: pode repetir as invocações do método e determinar novamente o local do Ator, por exemplo, depois que uma falha exige que o ator seja relocado para outro nó no cluster.

## Criar um novo projeto no Visual Studio
Depois de instalar as Ferramentas para Visual Studio da Malha do Serviço, você pode criar novos tipos de projeto. Os novos tipos de projeto estão na categoria ‘Nuvem’ da caixa de diálogo Novo Projeto


![Ferramentas de Malha de Serviço para VS - Novo projeto][1]

Na próxima caixa de diálogo, você pode escolher o tipo de projeto que deseja criar.

![Modelos de projeto de Malha de Serviço][5]

Para o projeto HelloWorld, vamos usar o Serviço do Ator da Malha do Serviço.

Depois que a solução é criada, você deve ver a seguinte estrutura:

![Estrutura de projeto de Malha de Serviço][2]

## Blocos de construção básicos de Atores Confiáveis

Uma solução comum de Atores Confiáveis é composta por 3 projetos:

* O projeto Aplicativo (HelloWorldApplication). Esse é o projeto que empacota todos os serviços juntos para implantação. Ele contém os scripts do PowerShell e o ApplicationManifest.xml para gerenciamento do aplicativo.

* O projeto Interface (HelloWorld.Interfaces). Esse é o projeto que contém a definição de interface para o ator. No projeto Interfaces, você pode definir as interfaces que serão usadas pelos atores na solução.

```csharp

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Actors;

namespace HelloWorld.Interfaces
{
    public interface IHelloWorld : IActor
    {
        Task<string> SayHello(string greeting);
    }
}

```

* O projeto Serviço (HelloWorld). Esse é o projeto usado para definir o serviço da Malha do Serviço que hospedará o ator. Ele contém parte de um código clichê que não precisa ser editado na maioria dos casos (ServiceHost.cs) e a implementação do Ator. A implementação do ator envolve a implementação de uma classe que deriva de um tipo básico (Ator) e implementa as interfaces definidas no projeto .Interfaces.

```csharp

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using HelloWorld.Interfaces;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Actors;

namespace HelloWorld
{
    public class HelloWorld : Actor, IHelloWorld
    {
        public Task<string> SayHello(string greeting)
        {
            return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
        }
    }
}

```

O projeto Serviço do Ator contém o código para criar um serviço da Malha do Serviço, na definição do serviço, e tipos de Ator são registrados para que possam ser usados para criar instâncias de novos atores.

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterActor(typeof(HelloWorld));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e);
            throw;
        }
    }
}  

```

Se você iniciar um novo projeto no Visual Studio e tiver apenas uma definição de Ator, o registro será incluído por padrão no código gerado pelo Visual Studio. Se você definir outros atores no serviço, será preciso adicionar o registro do Ator usando:

```csharp

fabricRuntime.RegisterActor(typeof(MyNewActor));


```

## Depurando

As ferramentas para Visual Studio da Malha do Serviço oferecem suporte à depuração no computador local. Você pode iniciar uma sessão de depuração pressionando F5. O Visual Studio cria (se necessário), empacota e implanta o aplicativo no cluster local da Malha do Serviço e anexa o depurador. A experiência é semelhante à depuração de um aplicativo ASP.NET. Durante o processo de implantação, você pode ver o andamento na janela Saída.

![Janela de saída de Depuração de Malha de Serviço][3]

## Implantação do aplicativo
No Visual Studio, você também pode empacotar e implantar o aplicativo no cluster local sem precisar iniciar o depurador, bastando selecionar o projeto Aplicativo da Malha do Serviço e clicar nele com o botão direito do mouse.

![Malha de Serviço - projetar menu para a implantação e empacotamento de aplicativos][4]

* **Implantar**: empacota o aplicativo e inicia o processo de implantação
* **Remover Implantação**: pode ser usada para remover um aplicativo do cluster local
* **Empacotar**: empacota o aplicativo. Essa ação pode ser útil para preparar o aplicativo a ser implantado em um cluster diferente, por exemplo, no Azure.

## Próximas etapas

[Introdução aos atores de Malha de Serviço](service-fabric-reliable-actors-introduction.md)[Documentação de referência de APIs de atores](https://msdn.microsoft.com/library/azure/dn971626.aspx)[Código de exemplo](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
 

<!---HONumber=July15_HO4-->