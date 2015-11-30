<properties
   pageTitle="Como relatar e verificar a integridade do Service Fabric| Microsoft Azure"
   description="Este artigo descreve como enviar relatórios de integridade de seu código de serviço e verificar a integridade do serviço usando as ferramentas de Monitoramento de Integridade fornecidas com o Azure Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/05/2015"
   ms.author="kunalds"/>


# Relatório e verificação da integridade do serviço
Quando seus serviços enfrentam problemas, a sua capacidade de reagir e de corrigir os incidentes e as interrupções resultantes depende da capacidade de detectar os problemas rapidamente. Ao relatar problemas e falhas ao Gerenciador de Integridade do Service Fabric a partir do código de seu serviço, é possível usar ferramentas padrão de Monitoramento de Integridade fornecidas pelo Service Fabric para verificar o status de integridade. Este documento fornecerá um exemplo de como adicionar um relatório de integridade a um serviço e mostrará como é possível verificar o status de integridade usando as ferramentas fornecidas pelo Service Fabric. Este artigo tem a intenção de ser uma breve introdução aos recursos de monitoramento de integridade no Service Fabric. Para saber mais, leia a série de artigos detalhados sobre integridade, começando com o link ao final deste documento.

## Pré-requisitos
Você deve ter o seguinte instalado: SDK do Service Fabric do * Visual Studio 2015 *

## Implantar um aplicativo e verificar sua integridade
Para implantar um aplicativo e verificar sua integridade, execute estas etapas:

1. Inicie o Visual Studio como um administrador.

2. Crie um projeto para um serviço com estado.

  ![Criar um aplicativo do Service Fabric com serviços com estado](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. Pressione F5 para executar o aplicativo em modo de depuração. O aplicativo será implantado no cluster local.

4. Quando o aplicativo estiver em execução, inicie o Gerenciador do Service Fabric clicando com o botão direito no aplicativo da bandeja do sistema do Gerenciador de Cluster Local e escolha Gerenciar Cluster Local no menu de contexto. ![Iniciar o Gerenciador do Service Fabric da bandeja do sistema]()

5. A integridade do aplicativo deve ser exibida de acordo com a imagem abaixo. Neste momento, o aplicativo deve ser íntegro, sem erros.

  ![Aplicativo de integridade no Gerenciador do Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. Você também pode verificar a integridade usando o Powershell. Você pode verificar a integridade de um aplicativo usando ```Get-ServiceFabricApplicationHealth```, e a integridade do serviço pode ser consultada por ```Get-ServiceFabricServiceHealth```. O relatório de integridade para o mesmo aplicativo no Powershell tem esta aparência. ![Aplicativo de integridade no Powershell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## Adicionar eventos de integridade personalizados ao código de seu serviço
Os modelos de projeto do Visual Studio da Malha do Serviço contêm amostra de código. As etapas a seguir mostram como você pode relatar eventos de integridade personalizados a partir do código de seu serviço. Esses relatórios serão exibidos automaticamente nas ferramentas padrão para Monitoramento de Integridade fornecidas pelo Service Fabric, como o Gerenciador do Service Fabric, a exibição de integridade do Portal do Azure e o Powershell.

1. Abra novamente o aplicativo que você criou anteriormente no Visual Studio ou crie um novo aplicativo com um serviço com estado a partir dos modelos de VS.
2. Abra o arquivo **Stateful1.cs**. Localize a declaração para `var myDictionary` e adicione o código abaixo logo após a declaração `var myDictionary`. O objeto `fabricClient` criado aqui será usado posteriormente para relatar a integridade.

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    Adicione também este namespace ao arquivo **Stateful1.cs**.

    ```csharp
    using System.Fabric;
    ```

4. Em seguida, procure a chamada `myDictionary.TryGetValueAsync` no método *RunAsync*. Você pode ver que isso retorna um `result` que mantém o valor atual do contador, pois a lógica principal deste aplicativo é manter uma contagem em execução. Se esse fosse um aplicativo real, e a falta de resultado representasse uma falha, seria necessário relatar isso ao Gerenciador de Integridade.
5. Para relatar um evento de integridade para a falta de resultado representando uma falha, adicione o código a seguir após a chamada `myDictionary.TryGetValueAsync`. Reportamos o evento como um `StatefulServiceReplicaHealthReport`, uma vez que isso está sendo reportado de um serviço com estado. O PartitionId e ReplicaId passados para o evento de relatório ajudarão a identificar a origem deste relatório quando você vê-lo em uma das ferramentas de Monitoramento de Integridade, uma vez que um serviço com estado implantado pode ter várias partições e cada partição pode ter várias réplicas. O parâmetro `HealthInformation` armazena informações sobre o problema de integridade que está sendo reportado. Adicione este namespace ao arquivo **Stateful1.cs**.

    ```csharp
    using System.Fabric.Health;
    ```

    Adicione este código após a chamada `myDictionary.TryGetValueAsync`.

    ```csharp
    if(!result.HasValue)
    {
        var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
    
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

5. Vamos simular essa falha e ver como ela é exibida nas ferramentas de Monitoramento de Integridade. Para simular a falha, comente a primeira linha do código do relatório de integridade adicionado acima, depois de comentar a primeira linha o código terá a seguinte aparência. Agora, isso acionará o relatório de integridade sempre que RunAsync for executado. Após fazer a alteração, execute o aplicativo usando F5.

    ```csharp
    //if(!result.HasValue)
    {
        var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
    
        var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

6. Quando o aplicativo estiver em execução, abra o Gerenciador do Service Fabric para verificar a integridade do aplicativo. Desta vez, o Gerenciador do Service Fabric mostrará o aplicativo como não íntegro. Isso ocorre devido ao erro relatado no código que adicionamos acima. ![Aplicativo não íntegro no Gerenciador do Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

7. Se você selecionar a réplica primária no modo de exibição de árvore do Gerenciador do Service Fabric, você verá que ela também mostra erro de integridade e também exibe os detalhes do relatório de integridade que foram adicionados ao parâmetro `HealthInformation` no código. Você também pode ver os mesmos relatórios de integridade no Powershell, bem como no Portal do Azure. ![Integridade da réplica no Gerenciador do Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Esse relatório permanecerá no Gerenciador de Integridade até que seja substituído por outro relatório ou que esta réplica seja excluída (*como não definimos um TimeToLive para este relatório de integridade no objeto HealthInformation, ele nunca expirará*). Para que a integridade seja consultada e informada, o FabricClient deve estar em um processo com privilégios de Administrador.

## Próximas etapas
[Mergulho na integridade do Service Fabric](service-fabric-health-introduction.md)

<!---HONumber=Nov15_HO4-->