<properties
   pageTitle="Criar um front-end da Web para seu aplicativo | Microsoft Azure"
   description="Exponha seu aplicativo Service Fabric na Web usando projeto de API Web do ASP.NET 5 e comunicação entre serviços via ServiceProxy."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/21/2015"
   ms.author="seanmck"/>


# Criar um serviço Web front-end para seu aplicativo

Por padrão, os serviços do Service Fabric não fornecem uma interface pública para a Web. Para expor a funcionalidade do aplicativo para clientes HTTP, você precisará criar um projeto Web para atuar como ponto de entrada e se comunicar dali com seus serviços individuais.

Neste tutorial, vamos percorrer a adição de um front-end de API Web do ASP.NET 5 para um aplicativo que já inclui um Reliable Service baseado em um modelo de projeto de serviço com estado. Se você ainda não o fez, considere conferir [Criando seu primeiro aplicativo no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md) antes de iniciar este tutorial.


## Adicionar um serviço ASP.NET 5 ao seu aplicativo

O ASP.NET 5 é uma estrutura de desenvolvimento Web leve entre plataformas, permitindo a criação de interface do usuário Web e APIs Web modernas. Vamos adicionar um projeto de API Web do ASP.NET a nosso aplicativo existente.

1. No Explorador de Soluções, clique com o botão direito do mouse em **Serviços** no projeto de aplicativo e escolha **Adicionar Fabric Service**.

	![Adicionando um novo serviço a um aplicativo existente][vs-add-new-service]

2. Na caixa de diálogo do novo serviço, escolha **ASP.NET 5** e dê um nome a ele.

	![Escolhendo um serviço Web ASP.NET no diálogo Novo serviço][vs-new-service-dialog]

3. A próxima caixa de diálogo fornece um conjunto de modelos de projeto do ASP.NET 5. Observe que esses são os mesmos modelos que você veria se criasse um projeto ASP.NET 5 fora de um aplicativo Service Fabric. Para este tutorial, escolheremos a **API Web**, mas você pode aplicar os mesmos conceitos para criar um aplicativo Web completo.

	![Escolhendo o tipo de projeto do ASP.NET][vs-new-aspnet-project-dialog]

    Depois de criar seu projeto de API Web, você terá dois serviços em seu aplicativo. Enquanto você continua a criar seu aplicativo, irá adicionar mais serviços exatamente da mesma forma e cada um poderá ser controlado e atualizado independentemente.


## Executar o aplicativo

Para obter uma ideia do que fizemos, vamos implantar o novo aplicativo e examinar o comportamento padrão fornecido pelo modelo de API Web do ASP.NET 5.

1. Pressione F5 no Visual Studio para depurar o aplicativo.

2. Quando a implantação for concluída, o Visual Studio iniciará o navegador na raiz do serviço API Web do ASP.NET, algo como http://localhost:33003 (o número da porta é atribuído aleatoriamente e pode ser diferente em seu computador). O modelo de API Web do ASP.NET 5 não fornece comportamento padrão para a raiz, então você obterá um erro no navegador.

3. Adicione `/api/values` ao local no navegador. Isso chamará o método `Get` em ValuesController no modelo da API Web e retornará a resposta padrão fornecida pelo modelo, contendo duas cadeias de caracteres JSON da matriz:

    ![Valores padrão retornados do modelo de API Web do ASP.NET 5][browser-aspnet-template-values]

    Ao final do tutorial, teremos substituído esses valores padrão com o valor mais recente do contador de nosso serviço com estado.


## Conectar os serviços

O Service Fabric fornece total flexibilidade na comunicação com Reliable Services. Em um único aplicativo, você pode ter os serviços que podem ser acessados por meio de TCP, outros por meio de uma API REST de HTTP e ainda outros por meio de soquetes da Web. Para saber mais sobre as opções disponíveis e as compensações envolvidas, confira [Comunicando com os Serviços](service-fabric-connect-and-communicate-with-services.md). Neste tutorial, seguiremos uma das abordagens mais simples e usaremos as classes `ServiceProxy`/`ServiceCommunicationListener` fornecidas no SDK.

Na abordagem `ServiceProxy` (modelada sobre chamadas de procedimento remoto ou RPC), você define uma interface para atuar como contrato público do serviço e usa essa interface para gerar uma classe proxy para interagir com o serviço.


### Criando a interface

Vamos começar criando a interface para atuar como o contrato entre o serviço com estado e seus clientes, incluindo o projeto ASP.NET 5.

1. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e escolha **Adicionar > Novo Projeto**.

2. Escolha a entrada do Visual C# no painel de navegação esquerdo e selecione o modelo **Biblioteca de Classe**. Verifique se a versão do .NET Framework está definida como 4.5.1.

    ![Criando um projeto de interface para o serviço com estado][vs-add-class-library-project]

3. Para que uma interface possa ser usada por `ServiceProxy`, ela deve derivar da interface IService, que está incluída em um dos pacotes NuGet do Service Fabric. Para adicionar o pacote, clique com o botão direito do mouse no novo projeto de biblioteca de classe e escolha **Gerenciar Pacotes NuGet**.

4. Verifique se a caixa de seleção **Incluir Pré-lançamento** está marcada, procure o pacote **Microsoft.ServiceFabric.Services** e instale-o.

    ![Adicionar o pacote NuGet de serviços][vs-services-nuget-package]

5. Na biblioteca de classe, crie uma interface com um método único, `GetCountAsync`, e estenda a interface do IService.

    ```c#
    namespace MyStatefulService.Interfaces
    {
        using Microsoft.ServiceFabric.Services.Remoting;

        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```


### Implemente a interface em seu serviço com estado

Agora que definimos a interface, precisamos implementá-la em nosso serviço com estado.

1. Em seu serviço com estado, adicione uma referência ao projeto de biblioteca de classe que contém a interface.

    ![Adicionando uma referência ao projeto de biblioteca de classes ao serviço com estado][vs-add-class-library-reference]

2. Localize a classe que herda de `StatefulService`, como `MyStatefulService` e a estenda para implementar a interface `ICounter`.

    ```c#
    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. Implemente o método único definido na interface `ICounter`, `GetCountAsync`.

    ```c#
    public async Task<long> GetCountAsync()
    {
      var myDictionary =
        await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```


### Expor o serviço com estado usando o ServiceCommunicationListener

Com a interface `ICounter` implementada, a etapa final para habilitar o chamamento do serviço com estado por outros serviços é abrir um canal de comunicação. Para serviços com estado, o Service Fabric fornece um método substituível chamado `CreateServiceReplicaListeners` em que você pode especificar um ou mais ouvintes de comunicação com base no tipo de comunicação que deseja habilitar para seu serviço.

>[AZURE.NOTE]O método equivalente para abrir um canal de comunicação para serviços sem estado é chamado `CreateServiceInstanceListeners`.

Nesse caso, fornecemos um `ServiceCommunicationListener`, que cria um ponto de extremidade RPC que pode ser chamado por clientes usando o `ServiceProxy`.

```c#
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (initParams) =>
                new ServiceCommunicationListener<ICounter>(initParams, this))
    };
}
```


### Usar o ServiceProxy para interagir com o serviço

Nosso serviço com estado agora está pronto para receber o tráfego de outros serviços; portanto, falta somente adicionar o código para se comunicar com ele usando o serviço Web ASP.NET.

1. Em seu projeto ASP.NET, adicione uma referência à biblioteca de classe com a interface `ICounter`.

2. Adicione o pacote Microsoft.ServiceFabric.Services ao projeto do ASP.NET, exatamente como fez anteriormente para o projeto de biblioteca de classe. Isso fornecerá a classe `ServiceProxy`.

3. Na pasta de controladores, abra a classe `ValuesController`. Observe que o método `Get` atualmente retorna somente uma matriz de cadeia de caracteres codificados "value1" e "value2", que coincide com o que vimos anteriormente no navegador. Substitua essa implementação pelo seguinte código:

    ```c#
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(0, new Uri("fabric:/MyApp/MyStatefulService"));

        long count = await counter.GetCountAsync();

        return new string[] { count.ToString() };
    }
    ```

    A primeira linha de código é a principal. Para criar o proxy ICounter para o serviço com estado, você precisa fornecer duas informações: uma ID de partição e o nome do serviço.

    O particionamento permite que você dimensione serviços com estado ao dividir seu estado em diferentes recipientes com base em uma chave definida por você, como ID de cliente ou CEP. No nosso aplicativo comum, o serviço com estado tem somente uma partição, portanto, a chave não importa. Qualquer chave que você fornecer levará até a mesma partição. Confira [Como particionar Reliable Services do Service Fabric](service-fabric-concepts-partitioning) para saber mais sobre como particionar seu serviço.

    O nome do serviço é um URI da malha de formulário:/&lt;application\_name&gt;/&lt;service\_name&gt;.

    Com esses dois tipos de informação, o Service Fabric pode identificar exclusivamente o computador para o qual as solicitações devem ser enviadas. O `ServiceProxy` também cuidará da situação no caso de o computador que hospeda a nossa partição de serviço com estado falhar e outra máquina tiver que ser promovida para seu lugar. Essa abstração torna a escrita de código do cliente para lidar com outros serviço bem mais simples.

    Assim que tivermos o proxy, podemos simplesmente chamar o método `GetCountAsync` e retornar seu resultado.

4. Pressione F5 novamente para executar o aplicativo modificado. Como antes, o Visual Studio iniciará automaticamente o navegador na raiz do projeto Web. Adicione o caminho "api/values" e você deve ver o valor atual do contador retornado.

    ![O valor do contador com estado exibido no navegador][browser-aspnet-counter-value]

    Atualize o navegador periodicamente para ver o valor do contador de atualização.


## E os atores?

Este tutorial está focado em adicionar um front-end da Web que se comunique com um serviço com estado, mas você pode seguir um modelo muito semelhante ao conversar com atores. Na verdade, é um pouco mais simples.

Quando você cria um projeto de ator, o Visual Studio gera automaticamente um projeto de interface para você. Você pode usar essa interface para gerar um proxy de ator no projeto Web para se comunicar com o ator. O canal de comunicação é fornecido automaticamente para que você não precise fazer nada equivalente a estabelecer um `ServiceCommunicationListener` como feito para o serviço com estado neste tutorial.

## Executando serviços Web em um cluster local

Em geral, você pode implantar exatamente o mesmo aplicativo Service Fabric em um cluster com vários computadores implantados no cluster local e ter a certeza de que ele funcionará conforme o esperado, pois o cluster local é simplesmente uma configuração de cinco nós recolhidos em um único computador.

No entanto, quando se trata de serviços Web, há uma nuance essencial. Quando o cluster fica atrás de um balanceador de carga, como é o caso no Azure, você deve verificar se seus serviços Web estão implantados em todos os computadores, já que o balanceador de carga simplesmente fará round-robin com o tráfego entre as máquinas. Isso pode ser feito definindo `InstanceCount` para o serviço com o valor especial -1. Por outro lado, na execução local, é necessário garantir que apenas uma instância do serviço está sendo executada; caso contrário, você terá conflitos de vários processos escutando na mesma porta e no mesmo caminho. Em consequência, a contagem de instâncias de serviço Web deve ser definida como 1 para implantações locais.

Para saber como configurar valores diferentes para um ambiente diferente, confira [Gerenciar parâmetros de aplicativo para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

## Próximas etapas

- [Criar um cluster no Azure para implantar seu aplicativo na nuvem](service-fabric-cluster-creation-via-portal.md)
- [Saiba mais sobre a comunicação com os serviços](service-fabric-connect-and-communicate-with-services.md)
- [Saiba mais sobre como particionar serviços com estado](service-fabric-concepts-partitioning.md)

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png

<!---HONumber=AcomDC_1203_2015-->