---
title: Criar um aplicativo .NET para o Service Fabric | Microsoft Docs
description: "Saiba como criar um aplicativo com um front-end do ASP.NET Core e um serviço confiável de back-end com estado e implantar o aplicativo em um cluster."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: ryanwi, mikhegn
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 71d910bc0e459528805521ba991e5291396a3b8d
ms.contentlocale: pt-br
ms.lasthandoff: 08/10/2017

---

# <a name="create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Criar e implantar um aplicativo com um serviço de front-end de API Web do ASP.NET Core e um serviço de back-end com estado
Este tutorial faz parte de uma série e mostra como criar um aplicativo do Azure Service Fabric com um front-end da API Web do ASP.NET Core e um serviço de back-end com estado para armazenar seus dados. 

![Diagrama de aplicativo](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Na primeira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Criar um serviço de API Web do ASP.NET Core como um serviço confiável
> * Criar um serviço confiável com estado
> * Implementar a comunicação remota do serviço e o uso de um proxy de serviço

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * Criar um aplicativo do Service Fabric .NET
> * [Implantar o aplicativo em um cluster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Configurar CI/CD usando o Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Instale o Visual Studio 2017](https://www.visualstudio.com/) e instale as cargas de trabalho de **desenvolvimento do Azure** e de **desenvolvimento para a Web e ASP.NET**.
- [Instalar o SDK do Service Fabric](service-fabric-get-started.md)

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>Criar um serviço de API Web ASP.NET como um serviço confiável
O ASP.NET Core é uma estrutura de desenvolvimento Web leve entre plataformas que permite a criação de uma interface do usuário Web e APIs Web modernas. Para obter uma compreensão completa de como o ASP.NET Core se integra ao Service Fabric, é altamente recomendável ler o artigo [ASP.NET Core nos Reliable Services do Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md). Por hora você pode seguir este tutorial para começar rapidamente. Para saber mais sobre o ASP.NET Core, confira a [Documentação do ASP.NET Core](https://docs.microsoft.com/aspnet/core/).

> [!NOTE]
> Este tutorial se baseia nas [Ferramentas do ASP.NET Core para Visual Studio 2017](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/start-mvc). As ferramentas do .NET Core para Visual Studio 2015 não estão mais sendo atualizadas.

1. Inicie o Visual Studio como um **administrador**.

2. Crie um projeto com **Arquivo**->**Novo**->**Projeto**

3. Na caixa de diálogo **Novo Projeto**, escolha **Nuvem > Aplicativo do Service Fabric**.

4. Nomeie o aplicativo **MyApplication** e pressione **OK**.

   ![Caixa de diálogo Novo projeto no Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. Na página **Novo Serviço do Service Fabric** escolha **ASP.NET Core sem estado** e dê o nome **MyWebAPIFrontEnd** ao seu serviço.
   
   ![Escolhendo um serviço Web ASP.NET no diálogo Novo serviço](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. A próxima página fornece um conjunto de modelos de projeto do ASP.NET Core. Neste tutorial, escolhemos **API Web**. No entanto, você pode aplicar os mesmos conceitos para compilar um aplicativo Web completo.
   
   ![Escolhendo o tipo de projeto do ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   O Visual Studio cria um aplicativo e um projeto de serviço e os exibe no Gerenciador de Soluções.

   ![Gerenciador de Soluções após a criação de aplicativo com serviço da API Web do ASP.NET Core]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="deploy-and-debug-the-application-locally"></a>Implantar e depurar o aplicativo localmente
Agora você pode avançar e depurar o aplicativo e observar o comportamento padrão que o modelo de API Web do ASP.NET Core fornece.

Pressione `F5` no Visual Studio a fim de implantar o aplicativo para depuração. O `F5` falhará se você não abriu anteriormente o Visual Studio como **administrador**.

> [!NOTE]
> Na primeira vez em que você executar e implantar o aplicativo localmente, o Visual Studio criará um cluster local para depuração, o que pode levar algum tempo. O status de criação do cluster é exibido na janela de saída do Visual Studio.

Quando o cluster estiver pronto, você receberá uma notificação do aplicativo Gerenciador de Cluster local do Service Fabric na bandeja do sistema.

1. Para depurar o aplicativo, pressione F5 no Visual Studio.
2. Quando a implantação for concluída, o Visual Studio iniciará o navegador na raiz do serviço de API Web ASP.NET. O modelo da API Web do ASP.NET Core não fornece o comportamento padrão para a raiz, então você receberá um erro no navegador.
3. Adicione `/api/values` à URL no navegador. Essa solicitação invocará o método `Get` no ValuesController no modelo da API Web. Ele retorna a resposta padrão fornecida pelo modelo, uma matriz JSON que contém duas cadeias de caracteres:
   
![Valores padrão retornados do modelo de API Web do ASP.NET Core](./media/service-fabric-tutorial-create-dotnet-app/browser-aspnet-template-values.png)

> [!NOTE]
> Para alterar o comportamento padrão do Visual Studio 2017 ao depurar um aplicativo, você pode alterar as propriedades do projeto de aplicativo do Service Fabric **MyApplication**.
> Para este tutorial, definir o Modo de Depuração do Aplicativo como **Atualizar Aplicativo** e adicionar **'/api/values'** à propriedade URL do aplicativo otimizará a experiência de depuração.
> 

Para interromper a depuração do aplicativo, volte para o Visual Studio e pressione **Shift + F5**.

### <a name="understanding-the-service-fabric-application-and-service"></a>Noções básicas sobre o aplicativo e o serviço do Service Fabric
Sua solução do Visual Studio agora contém dois projetos.
1. O projeto de aplicativo do Service Fabric – **MyApplication**
    - Esse projeto não contém nenhum código diretamente. Em vez disso, ele faz referência a um conjunto de projetos de serviço. Além disso, ele contém outros tipos de conteúdo para especificar como o aplicativo é composto e implantado.
2. O projeto de serviço – **MyWebAPIFrontEnd**
    - Este projeto é o seu projeto de API Web do ASP.NET Core, que contém o código e a configuração do serviço. Ao examinar o arquivo de código ValuesController.cs na pasta Controllers, você observa que ele é um controlador de API Web do ASP.NET Core regular. Não há requisitos específicos para o código que você escreve como parte dos controladores, ao executar uma API Web do ASP.NET Core como um serviço confiável no Service Fabric.

Para obter mais informações sobre o modelo de aplicativo no Service Fabric, consulte [Modelar um aplicativo no Service Fabric](service-fabric-application-model.md).

Para obter mais informações sobre o conteúdo do projeto de serviço, confira [Introdução aos Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Adicionar um serviço de back-end com estado ao seu aplicativo
Agora que temos um serviço de API Web ASP.NET em execução em nosso aplicativo, vamos prosseguir e adicionar um serviço confiável com estado para armazenar alguns dados em nosso aplicativo.

O Service Fabric permite que você armazene seus dados de forma consistente e confiável diretamente em seu serviço usando as coleções confiáveis. As coleções confiáveis são um conjunto simples de classes de coleção confiáveis altamente disponíveis que são familiares a todos aqueles que já usaram coleções de C#.

Neste tutorial, você cria um serviço que armazena um valor de contador em uma coleção confiável.

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **Serviços** no projeto de aplicativo e escolha **Adicionar > Novo Serviço do Fabric Service**.
   
    ![Adicionando um novo serviço a um aplicativo existente](./media/service-fabric-tutorial-create-dotnet-app/vs-add-new-service.png)

2. Na caixa de diálogo **Novo Serviço do Service Fabric**, escolha **Serviço com Estado**, nomeie o serviço como **MyStatefulService** e pressione **OK**.

    ![Caixa de diálogo Novo serviço no Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    Depois de criar seu projeto de serviço, você terá dois serviços em seu aplicativo. Conforme continua a criar o aplicativo, você pode adicionar mais serviços da mesma forma. Cada um pode seu próprio controle de versão e ser atualizado de forma independente.

### <a name="deploy-and-debug-the-application-locally"></a>Implantar e depurar o aplicativo localmente
Com o novo serviço adicionado ao aplicativo, vamos depurar o aplicativo completo e observar o comportamento padrão do novo serviço.

Para depurar o aplicativo, pressione F5 no Visual Studio.

> [!NOTE]
> Se optar por usar **Atualizar Aplicativo** como o Modo de Depuração do Aplicativo, você precisará conceder acesso ao cluster do Service Fabric local para a pasta de saída de build do seu aplicativo.
> 

Ambos os serviços em seu aplicativo são compilados, implantados e mencionados no cluster local do Service Fabric. Assim que os serviços forem iniciados, o Visual Studio ainda iniciará seu navegador, mas automaticamente também mostrará o **Visualizador de eventos de diagnóstico**, no qual você poderá ver a saída de rastreamento de seus serviços.
   
![Visualizador de eventos de diagnóstico](./media/service-fabric-tutorial-create-dotnet-app/diagnostic-events-viewer.png)

O visualizador de Eventos de Diagnóstico mostra mensagens de rastreamento de todos os serviços que fazem parte da solução do Visual Studio que está sendo depurada. Ao pausar o visualizador de Eventos de Diagnóstico, você poderá expandir uma das mensagens do serviço, a fim de inspecionar suas propriedades. Fazendo isso, você poderá ver qual serviço do cluster emitiu a mensagem, qual nó aquela instância de serviço está executando no momento e outras informações.

![Visualizador de eventos de diagnóstico](./media/service-fabric-tutorial-create-dotnet-app/expanded-diagnostics-viewer.png)

Você percebe que as mensagens de serviço são provenientes do serviço com estado que nós criamos, pois o **serviceTypeName** é **MyStatefulServiceType**. Você também pode ver que ele está enviando mensagens com o texto "O contador atual é....". Essa mensagem está sendo emitida pela linha de código realçada no método `RunAsync` do **MyStatefulService.cs**, que você pode ver na captura de tela a seguir.

![Código de mensagem de serviço](./media/service-fabric-tutorial-create-dotnet-app/service-message-code.png)

Para obter mais informações sobre a emissão de informações de diagnóstico de seus serviços e aplicativos, consulte [Monitoramento e diagnóstico no Azure Service Fabric](service-fabric-diagnostics-overview.md).

Para interromper a depuração do aplicativo, volte para o Visual Studio e pressione **Shift + F5**.

### <a name="understanding-the-mystatefulservice-code"></a>Noções básicas sobre o código MyStatefulService
Para entender como ele está usando um dicionário confiável para armazenar os dados no cluster, vamos passar algum tempo examinando o código do serviço **MyStatefulService**.

Há cinco linhas de código no serviço que estão relacionadas com a criação, atualização e leitura do dicionário confiável.

![Código do dicionário confiável](./media/service-fabric-tutorial-create-dotnet-app/reliable-dictionary-code.png)

1. Sempre que o método `RunAsync` do serviço estiver sendo executado (o que acontece quando o serviço é iniciado), esta linha de código obtém ou adiciona um dicionário confiável com o nome `myDictionary` ao serviço.
2. Toda a interação com valores em um dicionário confiável exige uma transação, essa instrução using que está sendo inserida nesta linha de código cria essa transação.
3. Essa linha de código obtém o valor associado com a chave especificada na chamada de método, por exemplo, `Counter`.
4. Essa linha de código atualiza o valor associado à chave `Counter`, aumentando o valor.
5. Esta chamada de método confirma a transação e retorna assim que o valor atualizado for armazenado em um quorum de nós no cluster.

Para obter mais informações detalhadas sobre dicionários confiáveis e coleções confiáveis, consulte [Introdução às Coleções confiáveis em serviços com estado do Azure Service Fabric](service-fabric-reliable-services-reliable-collections.md).

## <a name="connect-the-services"></a>Conectar os serviços
Na próxima etapa vamos conectar os dois serviços e fazer com que API Web do front-end retorne o valor atual do dicionário confiável de serviços de back-end.

O Service Fabric fornece total flexibilidade na comunicação com Reliable Services. Em um único aplicativo, você pode ter serviços que sejam acessíveis por meio de TCP. Outros serviços que podem ser acessados por meio de uma API REST de HTTP e ainda outros serviços podem estar acessíveis por meio de soquetes da Web. Para saber mais sobre as opções disponíveis e as compensações envolvidas, confira [Comunicação com os serviços](service-fabric-connect-and-communicate-with-services.md).

Neste tutorial, estamos usando a [Comunicação remota do serviço com Reliable Services](service-fabric-reliable-services-communication-remoting.md).

Na abordagem da comunicação remota do serviço (modelada em chamadas de procedimento remoto ou RPCs), você define uma interface para atuar como o contrato público do serviço. Em seguida, você usa essa interface para gerar uma classe proxy para interação com o serviço.

### <a name="create-the-remoting-interface"></a>Criar a interface de comunicação remota
Vamos começar criando a interface para atuar como o contrato entre os dois serviços. A interface deve ser referenciada por todos os serviços que a usam, portanto nós a criamos em um projeto separado de Biblioteca de classes.

1. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e escolha **Adicionar** > **Novo Projeto**.
2. Escolha a entrada do **Visual C#** no painel de navegação esquerdo e selecione o modelo **Biblioteca de Classes (.NET Framework)**. Verifique se a versão do .NET Framework está definida como **4.5.2** ou superior.
   
    ![Criando um projeto de interface para o serviço com estado](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-project.png)

3. Nomeie a Biblioteca de classes como **MyStatefulService.Interface** e clique em **OK**.

4. Clique com o botão direito do mouse no Gerenciador de Soluções e escolha **Gerenciar Pacotes do NuGet para a Solução**.

5. Escolha **Procurar** e pesquise **Microsoft.ServiceFabric.Services.Remoting**. Opte por instalá-lo nos três projetos de serviço na solução: 
   
    ![Adicionar o pacote NuGet de serviços](./media/service-fabric-tutorial-create-dotnet-app/add-nuget.png)

6. Na biblioteca de classes, crie uma interface com um único método, `GetCountAsync` e estenda a interface por meio do `Microsoft.ServiceFabric.Services.Remoting.IService`. A interface de comunicação remota deve derivar dessa interface para indicar que ela é uma interface da Comunicação Remota do Serviço. 
   
    ```c#
    using Microsoft.ServiceFabric.Services.Remoting;  
    using System.Threading.Tasks;
    ...
    namespace MyStatefulService.Interface
    {           
        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```
7. Clique com botão direito do mouse no projeto **MyStatefulService.Interface** no Gerenciador de Soluções e selecione **Propriedades**. Selecione a guia **Compilar** e, em seguida, selecione o valor **x64** na lista suspensa **Destino de plataforma**. 

8. Salve todas as alterações.

### <a name="implement-the-interface-in-your-stateful-service"></a>Implemente a interface em seu serviço com estado
Agora que definimos a interface, precisamos implementá-la no serviço com estado.

1. Adicione uma referência ao projeto de biblioteca de classes que contém a interface do projeto **MyStatefulService**.
   
    ![Adicionando uma referência ao projeto de biblioteca de classes ao serviço com estado](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-reference.png)

    ![Adicionando uma referência ao projeto de biblioteca de classes ao serviço com estado](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-reference-2.png)

2. Abra o arquivo `MyStatefulService.cs` e estenda-o para implementar a interface `ICounter` que nós criamos.
   
    ```c#
    using MyStatefulService.Interface;
    ...
   
    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. Agora, implemente o método único definido na interface `ICounter`, `GetCountAsync`.
   
    ```c#
    public async Task<long> GetCountAsync()
    {
        var myDictionary =
          await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
   
        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```

    - Esse método retorna o valor armazenado da chave `Counter` no dicionário confiável denominado `myDictionary`. 

### <a name="expose-the-stateful-service-using-service-remoting"></a>Expor o serviço com estado usando a comunicação remota do serviço
Com a interface `ICounter` implementada, a etapa final é abrir o ponto de extremidade da comunicação remota do serviço. Para serviços com estado, o Service Fabric fornece um método substituível chamado `CreateServiceReplicaListeners`. Com esse método, você pode especificar um ou mais ouvintes de comunicação, com base no tipo de comunicação que você deseja habilitar para o serviço.

Nesse caso, substituímos o método `CreateServiceReplicaListeners` existente e fornecemos uma instância do `ServiceRemotingListener`, que cria um ponto de extremidade RPC que pode ser chamado por clientes por meio do `ServiceProxy`.  

Altere o método **CreateServiceReplicaListeners** no arquivo **MyStatefulService.cs** e, em seguida, adicione uma instrução using ao namespace `Microsoft.ServiceFabric.Services.Remoting.Runtime`.

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```

Agora, nosso serviço com estado está pronto para receber o tráfego de outros serviços na RPC, usando a Comunicação remota do serviço.

### <a name="call-the-stateful-back-end-service-from-the-front-end-service"></a>Chamar o serviço de back-end com estado do serviço de front-end
Agora que o nosso serviço de back-end expôs uma interface, tudo o que falta é adicionar o código para se comunicar com ele por meio do serviço de API Web ASP.NET. Para comunicar-se usando a Comunicação remota do serviço, estamos usando um Proxy de serviço do nosso Controlador de valores.

1. No Gerenciador de Soluções, expanda o **MyWebAPIFrontEnd**, clique com o botão direito do mouse em **Dependências** e selecione **Adicionar Referência**.  Selecione **MyStatefulService.Interface** e clique em **OK**.
   
2. Na pasta **Controladores**, abra o arquivo `ValuesController.cs`. Adicione as seguintes instruções using ao arquivo:

    ```c#
    using MyStatefulService.Interface;
    using Microsoft.ServiceFabric.Services.Client;
    using Microsoft.ServiceFabric.Services.Remoting.Client;
    ```
    
3. O método `Get` atualmente retorna somente uma matriz de cadeia de caracteres embutida em código "value1" e "value2", que coincide com o que vimos anteriormente no navegador. Substitua essa implementação pelo seguinte código:
   
    ```
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));
   
        long count = await counter.GetCountAsync();
   
        return new string[] { count.ToString() };
    }
    ```
 
    A primeira linha de código nesse método cria o objeto ServiceProxy para o serviço com estado usando a interface ICounter. Ao criar um ServiceProxy, você precisa fornecer dois tipos de informações: uma ID de partição e o nome do serviço.
   
    Os serviços com estado podem ser particionados para serem dimensionados por meio do sombreamento dos dados em diferentes buckets, com base em uma chave como uma ID do cliente ou código postal. Em nosso aplicativo comum, o serviço com estado tem somente uma partição, portanto, a chave não importa. Qualquer chave que você fornece leva à mesma partição. Confira [Como particionar Reliable Services do Service Fabric](service-fabric-concepts-partitioning.md)para saber mais sobre como particionar seu serviço.
   
    O nome do serviço é um URI de malha:/&lt;application_name&gt;/&lt;service_name&gt;.
   
    Com esses dois tipos de informação, o Service Fabric pode identificar exclusivamente o computador para o qual as solicitações devem ser enviadas. A classe `ServiceProxy` também cuidará da situação no caso de a máquina que hospeda a partição do serviço com estado falhar e outra máquina tiver que ser promovida em seu lugar. Essa abstração simplifica a escrita de código do cliente para lidar com outros serviços.
   
    Assim que tivermos o proxy, invocaremos o método `GetCountAsync` e retornaremos seu resultado.

4. Pressione F5 novamente para executar o aplicativo modificado. Como antes, o Visual Studio inicia o navegador automaticamente na raiz do projeto Web. Adicione o caminho "api/values" e você deve ver o valor atual do contador retornado.
   
    ![O valor do contador com estado exibido no navegador](./media/service-fabric-tutorial-create-dotnet-app/browser-aspnet-counter-value.png)
   
    Atualize o navegador periodicamente para ver o valor do contador de atualização.

Para interromper a depuração do aplicativo, volte para o Visual Studio e pressione **Shift + F5**.

## <a name="next-steps"></a>Próximas etapas
Nesta parte do tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um serviço de API Web do ASP.NET Core como um serviço confiável
> * Criar um serviço confiável com estado
> * Implementar a comunicação remota do serviço e o uso de um proxy de serviço

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Implantar o aplicativo no Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
