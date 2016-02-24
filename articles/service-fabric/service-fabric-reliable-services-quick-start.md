<properties
   pageTitle="Introdução aos Reliable Services | Microsoft Azure"
   description="Introdução à criação de um aplicativo do Service Fabric do Microsoft Azure com serviços com e sem estado."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/15/2015"
   ms.author="vturecek"/>

# Introdução aos Reliable Services do Service Fabric

Um aplicativo do Service Fabric do Azure contém um ou mais serviços que executam seu código. Este guia mostra como criar aplicativos do Service Fabric sem e com estado usando os [Reliable Services](service-fabric-reliable-services-introduction.md).

## Criar um serviço sem estado

Um serviço sem estado é um tipo de serviço que atualmente está na norma dos aplicativos em nuvem. Ele é considerado sem estado porque o serviço em si não contém dados que precisam ser armazenados de modo confiável nem altamente disponibilizados. Se uma instância de um serviço sem estado for desligada, todo seu estado interno será perdido. Nesse tipo de serviço, o estado deve ser mantido em um repositório externo, como em Tabelas do Azure ou um banco de dados SQL, para que ele se torne altamente disponível e confiável.

Inicie o Visual Studio 2015 RC como administrador e crie um novo projeto de aplicativo do Service Fabric chamado *HelloWorld*:

![Usar a caixa de diálogo Novo Projeto para criar um novo aplicativo do Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Em seguida, crie um projeto de serviço sem estado chamado *HelloWorldStateless*:

![Na segunda caixa de diálogo, criar um projeto de serviço sem estado](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Agora, sua solução contém dois projetos:

 - *HelloWorld*. Esse é o projeto de *aplicativo* que contém seus *serviços*. Ele também contém o manifesto do aplicativo que descreve o aplicativo, bem como diversos scripts do PowerShell que ajudam a implantar o aplicativo.
 - *HelloWorldStateless*. Esse é o projeto de serviço. Ele contém a implementação do serviço sem estado.


## Implementar o serviço

Abra o arquivo **HelloWorldStateless.cs** no projeto de serviço. No Service Fabric, um serviço pode executar qualquer lógica de negócios. A API de serviço fornece dois pontos de entrada para seu código:

 - Um método de ponto de entrada ilimitado chamado *RunAsync*, em que você pode começar a executar qualquer carga de trabalho. Isso inclui cargas de trabalho de computação de execução longa.

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

 - Um ponto de entrada de comunicação em que você pode conectar a pilha de comunicação de sua escolha, como API Web ASP.NET. É onde você pode começar a receber solicitações de usuários e outros serviços.

```C#
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

Neste tutorial, enfatizaremos o método de ponto de entrada `RunAsync()`. É aqui que você pode começar imediatamente a executar seu código. O modelo de projeto inclui um exemplo de implementação de `RunAsync()` que incrementa uma contagem progressiva.

> [AZURE.NOTE]Para obter detalhes sobre como trabalhar com uma pilha de comunicação, confira [Serviços de API do Service Fabric com auto-hospedagem do OWIN](service-fabric-reliable-services-communication-webapi.md)


### RunAsync

```C#
protected override async Task RunAsync(CancellationToken cancelServiceInstance)
{
    // TODO: Replace the following sample code with your own logic.

    int iterations = 0;
    // This service instance continues processing until the instance is terminated.
    while (!cancelServiceInstance.IsCancellationRequested)
    {

        // Log what the service is doing
        ServiceEventSource.Current.ServiceMessage(this, "Working-{0}", iterations++);

        // Pause for 1 second before continue processing.
        await Task.Delay(TimeSpan.FromSeconds(1), cancelServiceInstance);
    }
}
```

A plataforma chama esse método quando uma instância de um serviço é estabelecida e está pronta para execução. Para um serviço sem estado, isso simplesmente significa quando a instância do serviço é aberta. Um token de cancelamento é fornecido para coordenar quando sua instância de serviço deve ser fechada. No Service Fabric, esse ciclo de abertura/fechamento de uma instância de serviço pode ocorrer várias vezes durante a vida útil do serviço como um todo. Isso pode ocorrer por vários motivos, incluindo:

- O sistema move as instâncias de serviço para balanceamento de recursos.
- Ocorrem falhas no código.
- O aplicativo ou sistema é atualizado.
- O hardware subjacente sofre uma interrupção.

Essa orquestração é gerenciada pelo sistema a fim de manter o serviço altamente disponível e devidamente balanceado.

`RunAsync()` é executado em sua própria tarefa. Observe que, no trecho de código acima, pulamos diretamente para um loop *while*. Não é necessário agendar uma tarefa separada para sua carga de trabalho. O cancelamento da sua carga de trabalho é um esforço cooperativo orquestrado pelo token de cancelamento fornecido. O sistema aguardará o encerramento da tarefa (por conclusão bem-sucedida, cancelamento ou falha) antes de prosseguir. É importante honrar o token de cancelamento, concluir qualquer trabalho e sair do `RunAsync()` o mais rapidamente possível quando o sistema solicita o cancelamento.

Neste exemplo de serviço sem estado, a contagem é armazenada em uma variável local. Mas como esse é um serviço sem estado, o valor que é armazenado existe apenas para o ciclo de vida atual da sua instância de serviço. Quando o serviço se move ou é reiniciado, o valor é perdido.

## Criar um serviço com estado

O Service Fabric introduz um novo tipo de serviço que é com estado. Um serviço com estado pode manter o estado de maneira confiável dentro do próprio serviço, localizado em conjunto com o código que o está usando. O estado é altamente disponibilizado pelo Service Fabric sem a necessidade de persistir o estado em um repositório externo.

Para converter o valor de contador de sem estado para altamente disponível e persistente, mesmo quando o serviço for movido ou reiniciado, você precisa de um serviço com estado.

No mesmo aplicativo *HelloWorld*, é possível adicionar um novo serviço clicando com o botão direito duas vezes no projeto de aplicativo e selecionando **Adicionar Service Fabric**.

![Adicione um serviço ao aplicativo da Malha de Serviços](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Selecione **Serviço com Estado** e dê o nome de *HelloWorldStateful*. Clique em **OK**.

![Usar a caixa de diálogo Novo Projeto para criar um novo serviço com estado do Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Seu aplicativo agora deve ter dois serviços: o serviço sem estado *HelloWorld* e o serviço com estado *HelloWorldStateful*.

Abra **HelloWorldStateful.cs** em *HelloWorldStateful*, que contém o método RunAsync a seguir:

```C#
protected override async Task RunAsync(CancellationToken cancelServicePartitionReplica)
{
    // TODO: Replace the following sample code with your own logic.

    // Gets (or creates) a replicated dictionary called "myDictionary" in this partition.
    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    // This partition's replica continues processing until the replica is terminated.
    while (!cancelServicePartitionReplica.IsCancellationRequested)
    {

        // Create a transaction to perform operations on data within this partition's replica.
        using (var tx = this.StateManager.CreateTransaction())
        {

            // Try to read a value from the dictionary whose key is "Counter-1".
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

            // Log whether the value existed or not.
            ServiceEventSource.Current.ServiceMessage(this, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            // If the "Counter-1" key doesn't exist, set its value to 0
            // else add 1 to its current value.
            await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

            // Committing the transaction serializes the changes and writes them to this partition's secondary replicas.
            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is sent to this partition's secondary replicas.
            await tx.CommitAsync();
        }

        // Pause for one second before continuing processing.
        await Task.Delay(TimeSpan.FromSeconds(1), cancelServicePartitionReplica);
    }
}
```

### RunAsync

Um serviço com estado tem os mesmos pontos de entrada que um serviço sem estado. A principal diferença é a disponibilidade das Reliable Collections e o gerenciador de estado. `RunAsync()` opera de forma semelhante em serviços com e sem estado. No entanto, em um serviço com estado, a plataforma executa trabalho adicional em seu nome antes de executar `RunAsync()`. Esse trabalho pode incluir garantir que o gerenciador de estado e as Reliable Collections estejam prontos para uso.

### Reliable Collections e gerenciador de estado

```C#
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

*IReliableDictionary* é uma implementação de dicionário que você pode usar para armazenar de forma confiável o estado no serviço. Isso faz parte das [Reliable Collections](service-fabric-reliable-services-reliable-collections.md) internas do Service Fabric. Com o Service Fabric e as Reliable Collections, você agora pode armazenar dados diretamente em seu serviço sem a necessidade de um repositório persistente externo. Essa abordagem torna os dados altamente disponíveis. O Service Fabric consegue isso criando e gerenciando várias *réplicas* do seu serviço para você. Ele também fornece uma API que abstrai as complexidades de gerenciar essas réplicas e as respectivas transições de estado.

As Reliable Collections podem armazenar qualquer tipo .NET, incluindo tipos personalizados, com algumas limitações:

 - O Service Fabric torna seu estado altamente disponível *replicando* o estado entre nós e armazenando-o no disco local. Isso significa que tudo que é armazenado nas Reliable Collections deve ser *serializável*. Por padrão, as Reliable Collections usam [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) para serialização, de modo que é importante assegurar que seus tipos tenham [suporte do Serializador de Contrato de Dados](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) quando você usar o serializador padrão.

 - Os objetos são replicados para alta disponibilidade quando você confirma uma transação nas Reliable Collections. Objetos armazenados nas Reliable Collections são mantidos na memória local do serviço. Isso significa que você tem uma referência local para o objeto.

    É importante que você não modifique instâncias locais desses objetos sem executar uma operação de atualização na coleção confiável em uma transação. Isso porque essas mudanças não serão replicadas automaticamente.

O gerenciador de estado gerencia as Reliable Collections para você. Basta solicitar ao gerenciador de estado uma coleção confiável por nome a qualquer momento e em qualquer lugar no seu serviço. O gerenciador de estado assegura que você obtenha uma referência de volta. Não é recomendável salvar referências nas instâncias de coleção confiável em propriedades ou variáveis de membro de classe. É preciso tomar muito cuidado para garantir que a referência seja definida para uma instância o tempo todo no ciclo de vida do serviço. O gerenciador de estado faz esse trabalho para você, sendo otimizado para repetir visitas.

### Operações transacionais e assíncronas

```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

As Reliable Collections têm muitas das mesmas operações que suas correspondentes `System.Collections.Generic` e `System.Collections.Concurrent`, incluindo o LINQ. No entanto, as operações em Reliable Collections são assíncronas. Isso porque as operações de gravação com Reliable Collections são *replicadas*. Para alta disponibilidade, essas operações são enviadas a outras réplicas do serviço em nós diferentes.

Elas também oferecem suporte a operações *transacionais*, para que você possa manter o estado consistente entre várias Reliable Collections. Por exemplo, você pode remover um item de trabalho de uma fila confiável, executar uma operação nele e salvar o resultado em um dicionário confiável, tudo em uma única transação. Trata-se de uma operação atômica garante que toda a operação seja bem-sucedida, e não apenas uma parte. Se ocorrer um erro depois de remover o item da fila, mas antes de salvar o resultado, toda a transação será revertida e o item permanecerá na fila para processamento.

## Executar o aplicativo

Agora retornamos ao aplicativo *HelloWorld*. Agora você pode criar e implantar seus serviços. Quando você pressionar **F5**, seu aplicativo será criado e implantado no cluster local.

Depois que os serviços começaram a ser executados, você poderá exibir os eventos ETW (Rastreamento de Eventos para Windows) gerados em uma janela **Eventos de Diagnóstico**. Observe que os eventos exibidos são dos serviços sem e com estado do aplicativo. Você pode pausar o fluxo clicando no botão **Pausar**. Assim, você pode examinar os detalhes de uma mensagem expandindo-a.

>[AZURE.NOTE]Antes de executar o aplicativo, verifique a existência de um cluster de desenvolvimento local em execução. Confira o [guia de introdução](service-fabric-get-started.md) para obter informações sobre como configurar o ambiente local.

![Exibir Eventos de Diagnóstico no Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)


## Próximas etapas

[Depurar seu aplicativo do Service Fabric usando o Visual Studio](service-fabric-debugging-your-application.md)

[Introdução aos serviços de API Web do Service Fabric com auto-hospedagem OWIN](service-fabric-reliable-services-communication-webapi.md)

[Saiba mais sobre as Reliable Collections](service-fabric-reliable-services-reliable-collections.md)

[Implantar um aplicativo](service-fabric-deploy-remove-applications.md)

[Atualização de aplicativo](service-fabric-application-upgrade.md)

[Referência do desenvolvedor para Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

<!---HONumber=AcomDC_0121_2016-->