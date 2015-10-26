<properties
   pageTitle="Introdução aos Serviços Confiáveis da Malha de Serviços do Microsoft Azure"
   description="Como criar um aplicativo de Malha de Serviços com serviços com e sem estado."
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
   ms.date="07/23/2015"
   ms.author="vturecek"/>

# Introdução aos Serviços Confiáveis da Malha de Serviços do Microsoft Azure

Um aplicativo da Malha de Serviços contém um ou mais serviços que executam seu código. Este tutorial explica as etapas de criação de aplicativos da Malha de Serviços “Olá Mundo” com e sem estado usando o modelo de programação de [*Serviços Confiáveis*](../Service-Fabric/service-fabric-reliable-services-introduction.md).

Um serviço sem estado é o tipo de serviço mais comum em aplicativos em nuvem atualmente. O serviço é considerado sem estado porque o serviço em si não contém dados que precisam ser armazenados de forma confiável ou que precisam ser altamente disponíveis - em outras palavras, se uma instância de um serviço sem estado é desligada, todo o seu estado interno é perdido. Nesses tipos de serviços, o estado deve ser preservado em um repositório externo, como Tabelas do Azure ou um banco de dados SQL, para se tornar altamente disponível e confiável.

A Malha de Serviços introduz um novo tipo de serviço com estado: um serviço que pode manter o estado de forma confiável dentro de si mesmo, localizado junto com o código que o está usando. A Malha de Serviços torna o estado altamente disponível sem a necessidade de manter o estado em um repositório externo.

Neste tutorial, você implementará um serviço sem estado e um serviço com estado que mantém um contador interno. No serviço sem estado, o valor do contador é perdido quando o serviço é reiniciado ou movido. No entanto, no serviço com estado, a Malha de Serviços torna o estado do contador confiável para que, se a execução do serviço for interrompida por qualquer motivo no meio de contagem, seja possível retomá-la de onde parou.

## Criar um serviço sem estado

Vamos começar com um serviço sem estado.

Inicie o Visual Studio 2015 RC como **Administrador** e crie um novo projeto de **Aplicativo da Malha de Serviços** chamado *HelloWorld*:

![Use a caixa de diálogo Novo Projeto para criar um Aplicativo da Malha de Serviços](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Em seguida, crie um projeto de **Serviço sem Estado** chamado *HelloWorldStateless*:

![Na segunda caixa de diálogo, crie um serviço sem estado](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Agora, sua solução contém 2 projetos:

 + **HelloWorld** Este é o projeto de *aplicativo* que contém seus *serviços*. Ele também contém o manifesto do aplicativo que descreve o aplicativo e diversos scripts do PowerShell que ajudam a implantar seu aplicativo.
 + **HelloWorldStateless** Esse é o projeto de serviço, que contém a implementação do serviço sem estado.


## Implementar o serviço

Abra o arquivo **HelloWorld.cs** no projeto de serviço. Na Malha de Serviços, um serviço pode executar qualquer lógica de negócios. A API de serviço fornece dois pontos de entrada para seu código:

 - Um método de ponto de entrada em aberto chamado *RunAsync*, em que você pode começar a executar qualquer carga de trabalho que deseje, como cargas de trabalho de computação de longa duração.

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

 - Um ponto de entrada de comunicação em que você pode conectar a pilha de comunicação de sua escolha, como API Web, em que pode começar a receber solicitações de usuários ou outros serviços.

```C#
protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}
```

Neste tutorial, o foco será o método de ponto de entrada `RunAsync()`, em que você pode começar a executar seu código imediatamente. O modelo de projeto inclui um exemplo de implementação de `RunAsync()` que incrementa uma contagem progressiva.

> [AZURE.NOTE]Para obter detalhes sobre como trabalhar com uma pilha de comunicação, consulte [Introdução aos serviços de API Web da Malha de Serviços com auto-hospedagem do OWIN](service-fabric-reliable-services-communication-webapi.md)


### RunAsync

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.

    int iterations = 0;
    while (!cancellationToken.IsCancellationRequested)
    {
        ServiceEventSource.Current.ServiceMessage(this, "Working-{0}", iterations++);
        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

A plataforma chama esse método quando uma instância do serviço é realizada e está pronta para execução. Para serviços sem estado, isso simplesmente significa quando a instância do serviço é aberta. Um token de cancelamento é fornecido para coordenar quando sua instância de serviço deve ser fechada. Na Malha de Serviços, esse ciclo de abertura-fechamento de uma instância de serviço pode ocorrer várias vezes durante a vida útil do serviço como um todo, por vários motivos, incluindo:

- O sistema pode percorrer as instâncias de seu serviço para obter o balanceamento de recursos.
- Ocorreram falhas em seu código.
- Durante as atualizações do aplicativo ou do sistema.
- Quando o hardware subjacente sofrer uma interrupção.

Essa orquestração é gerenciada pelo sistema a fim de manter seu serviço altamente disponível e devidamente balanceado.

`RunAsync()` é executado em sua própria **Tarefa**. Observe que no trecho de código acima aqui que vamos diretamente a um loop **while**; não há necessidade de agendar uma tarefa separada para sua carga de trabalho. O cancelamento da sua carga de trabalho é um esforço cooperativo orquestrado pelo token de cancelamento fornecido. O sistema esperará o encerramento de sua tarefa (por conclusão bem-sucedida, cancelamento ou falha) antes de prosseguir. É **importante** honrar o token de cancelamento, concluir qualquer trabalho e sair do `RunAsync()` o mais rápido possível quando o cancelamento for solicitado pelo sistema.

Neste exemplo de serviço sem estado, a contagem é armazenada em uma variável local. Mas, como esse é um serviço sem estado, o valor que será armazenado só existe para o ciclo de vida atual da instância do serviço na qual está. Quando o serviço se move ou é reiniciado, o valor é perdido.

## Criar um serviço com estado

Para converter o valor do contador de sem estado para altamente disponível e persistente, mesmo quando o serviço for movido ou reiniciado, precisamos de um serviço com estado.

No mesmo aplicativo **HelloWorld**, adicione um novo serviço clicando duas vezes no projeto de aplicativo e selecionando **Novo Serviço de Malha**.

![Adicione um serviço ao aplicativo da Malha de Serviços](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Selecione **Serviço com Estado da Malha de Serviços** e nomeie-o como "HelloWorldStateful". Clique em **Adicionar**.

![Use a caixa de diálogo Novo Projeto para criar um novo serviço com estado da Malha de Serviços](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Seu aplicativo agora deve ter dois serviços: o serviço sem estado *HelloWorld* e o serviço com estado *HelloWorldStateful*.

Abra **HelloWorldStateful.cs** na *HelloWorldStateful* que contém o seguinte método `RunAsync`:

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (!cancellationToken.IsCancellationRequested)
    {
        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");
            ServiceEventSource.Current.ServiceMessage(
                this,
                "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

### RunAsync

Um serviço com estado tem os mesmos pontos de entrada que um serviço sem estado. A principal diferença é a disponibilidade de *Coleções Confiáveis* e do *Gerenciador de Estado*. `RunAsync()` em um serviço com estado opera de maneira semelhante a um serviço sem estado, com a diferença de que, em um serviço com estado, a plataforma executa trabalho adicional por você antes de executar `RunAsync()`, como garantir que o *Gerenciador de Estado* e as *Coleções Confiáveis* estão prontos para uso.

### Coleções Confiáveis e Gerenciador de Estado

```C#
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

**IReliableDictionary** é uma implementação de dicionário que lhe permite armazenar de forma confiável o estado no serviço. Isso faz parte das [Coleções Confiáveis](service-fabric-reliable-services-reliable-collections.md) internas da Malha de Serviços. Com a Malha de Serviços e as Coleções Confiáveis, você agora pode armazenar dados diretamente em seu serviço, sem a necessidade de um repositório persistente externo, tornando seus dados altamente disponíveis. A Malha de Serviços faz isso criando e gerenciando várias *réplicas* do seu serviço para você, além de fornecer uma API que abstrai as complexidades de gerenciar essas réplicas e suas transições de estado.

As Coleções Confiáveis podem armazenar qualquer tipo .NET - incluindo seus tipos personalizados - com algumas limitações:

 1. A Malha de Serviços torna seu estado altamente disponível *replicando* estado entre nós e armazenando-os no disco local. Isso significa que tudo que é armazenado em uma Coleção Confiável deve ser *serializável*. Por padrão, as Coleções Confiáveis usam [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) para serialização, então é importante assegurar que seus tipos sejam [suportados pelo Serializador de Contrato de Dados](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) ao usar o serializador padrão.

 2. Os objetos são replicados para alta disponibilidade quando você confirma uma transação em uma Coleção Confiável. Objetos armazenados em Coleções Confiáveis são mantidos na memória local em seu serviço, o que significa que você tem uma referência local ao objeto.

    É importante que você não modifique instâncias locais desses objetos sem executar uma operação de atualização na Coleção Confiável em uma transação, pois essas alterações não serão replicadas automaticamente.

O *StateManager* cuida do gerenciamento de Coleções Confiáveis para você. Basta solicitar ao StateManager uma coleção confiável por nome a qualquer momento e em qualquer lugar no seu serviço e ele garante que você obterá uma referência. Não é recomendável salvar referências às instâncias de Coleção Confiável em propriedades ou variáveis membro de classe, pois cuidado especial deve ser tomado para garantir que a referência seja definida como uma instância durante todo o ciclo de vida do serviço. O StateManager faz esse trabalho para você, otimizado para visitas repetidas.

### Transacionais e assíncronas

```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

As Coleções Confiáveis têm muitas das mesmas operações que suas correspondentes `System.Collections.Generic` e `System.Collections.Concurrent`, incluindo o LINQ. No entanto, as operações em Coleções Confiáveis são assíncronas. Isso ocorre porque as operações de gravação com Coleções Confiáveis são *replicadas*, ou seja, a operação é enviada para outras réplicas do serviço em diferentes nós para alta disponibilidade.

Elas também dão suporte a operações *transacionais* a fim de manter o estado consistente entre várias Coleções Confiáveis. Por exemplo, você pode remover um item de trabalho de uma Fila Confiável, executar uma operação nele e salvar o resultado em um Dicionário Confiável, tudo em uma única transação. Isso é tratado como uma operação atômica, garantindo que toda a operação será bem-sucedida ou nenhuma parte dela será - se ocorrer um erro depois que você tiver removido o item da fila, mas antes de poder ter salvado o resultado, toda a transação será revertida e o item permanecerá na fila para processamento.

## Executar o aplicativo

Voltar ao aplicativo *HelloWorld*. Agora você pode criar e implantar seus serviços. Pressione **F5** e seu aplicativo será criado e implantado em seu cluster local.

Quando os serviços estão em execução, você pode ver os eventos ETW gerados em na janela **Diagnóstico de Eventos**. Observe que há eventos exibidos dos serviços sem e com estado do aplicativo. Você pode pausar o fluxo clicando no botão *Pausar* e examinar os detalhes da mensagem expandindo uma mensagem.

>[AZURE.NOTE]Antes de executar o aplicativo, verifique se que você tem um cluster de desenvolvimento local em execução. Confira o [Guia de Introdução](service-fabric-get-started.md) para configurar o seu ambiente local.

![Exibir eventos de diagnóstico no Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)


## Próximas etapas

[Depurando seu aplicativo da Malha de Serviços no Visual Studio](service-fabric-debugging-your-application.md)

[Introdução aos serviços de API Web da Malha de Serviços do Microsoft Azure com auto-hospedagem do OWIN](service-fabric-reliable-services-communication-webapi.md)

[Saiba mais sobre as Coleções Confiáveis](service-fabric-reliable-services-reliable-collections.md)

[Gerenciar um serviço da Malha de Serviços](service-fabric-manage-your-service-index.md)

[Referência do desenvolvedor para Serviços Confiáveis](https://msdn.microsoft.com/library/azure/dn706529.aspx)

<!---HONumber=Oct15_HO3-->