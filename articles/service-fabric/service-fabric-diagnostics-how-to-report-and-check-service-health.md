<properties
   pageTitle="Relatar e verificar a integridade com o Service Fabric do Azure| Microsoft Azure"
   description="Saiba como enviar relatórios de integridade do seu código de serviço e verificar a integridade do serviço usando as ferramentas de monitoramento de integridade fornecidas pelo Service Fabric do Azure."
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


# Relatar e verificar a integridade de serviço
Quando seus serviços enfrentam problemas, a sua capacidade de reagir e corrigir os incidentes e as interrupções resultantes depende da capacidade de detectar os problemas rapidamente. Ao relatar problemas e falhas ao gerenciador de integridade do Service Fabric usando seu código de serviço, é possível usar ferramentas padrão de monitoramento de integridade fornecidas pelo Service Fabric para verificar o status de integridade.

Este artigo mostra um exemplo de como adicionar um relatório de integridade a um serviço e como é possível verificar o status de integridade usando as ferramentas fornecidas pelo Service Fabric. Este artigo é uma rápida introdução aos recursos de monitoramento de integridade no Service Fabric. Para obter informações mais detalhadas, você pode ler a série de artigos explicativos sobre integridade, começando com o link no fim deste documento.

## Pré-requisitos
Você deve ter o seguinte instalado: SDK do Service Fabric do * Visual Studio 2015 *

## Para implantar um aplicativo e verificar sua integridade
Para implantar um aplicativo e verificar sua integridade, siga estas etapas:

1. Inicie o Visual Studio como um administrador.

2. Crie um projeto para um serviço com estado.

  ![Criar um aplicativo do Service Fabric com serviço com estado](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. Pressione **F5** para executar o aplicativo no modo de depuração. O aplicativo será implantado no cluster local.

4. Quando o aplicativo estiver em execução, inicie o Gerenciador do Service Fabric clicando com o botão direito no aplicativo da bandeja do sistema do Gerenciador de Cluster Local e escolha **Gerenciar Cluster Local** no menu de contexto.

![Iniciar o Gerenciador do Service Fabric da bandeja do sistema](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)

5. A integridade do aplicativo deve ser exibida de acordo com a imagem abaixo. Neste momento, o aplicativo deve estar íntegro, sem erros.

  ![Aplicativo de integridade no Gerenciador do Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. Você também pode verificar a integridade usando o PowerShell. Você pode verificar a integridade de um aplicativo usando ```Get-ServiceFabricApplicationHealth``` e verificar a integridade do serviço usando ```Get-ServiceFabricServiceHealth```. O relatório de integridade para o mesmo aplicativo no PowerShell tem esta aparência. ![Aplicativo de integridade no PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## Para adicionar eventos de integridade personalizados ao seu código de serviço
Os modelos de projeto do Visual Studio da Malha do Serviço contêm amostra de código. As etapas a seguir mostram como você pode relatar eventos de integridade personalizados a partir do código de seu serviço. Esses relatórios serão exibidos automaticamente nas ferramentas padrão para monitoramento de integridade fornecidas pelo Service Fabric, como o Gerenciador do Service Fabric, a exibição de integridade do Portal do Azure e o PowerShell.

1. Abra novamente o aplicativo que você criou anteriormente no Visual Studio ou crie um novo usando um serviço com estado dos modelos do Visual Studio.
2. Abra o arquivo **Stateful1.cs**. Localize a declaração para `var myDictionary` e adicione o código abaixo logo após a declaração `var myDictionary`. O objeto `fabricClient` criado aqui será usado posteriormente para relatar a integridade.

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    Adicione também este namespace ao arquivo **Stateful1.cs**.

    ```csharp
    using System.Fabric;
    ```

4. Em seguida, procure a chamada `myDictionary.TryGetValueAsync` no método *RunAsync*. Você pode ver que isso retorna um `result` que mantém o valor atual do contador, pois a lógica principal deste aplicativo é manter uma contagem em execução. Se esse fosse um aplicativo real e a falta de resultado representasse uma falha, seria necessário relatar isso ao gerenciador de integridade.
5. Para relatar um evento de integridade para a falta de resultado representando uma falha, adicione o código a seguir após a chamada `myDictionary.TryGetValueAsync`. Reportamos o evento como um `StatefulServiceReplicaHealthReport`, uma vez que ele está sendo reportado de um serviço com estado. PartitionId e ReplicaId passados para o evento de relatório ajudarão a identificar a origem desse relatório quando você visualizá-lo em uma das ferramentas de monitoramento de integridade. Isso é importante porque um serviço com estado implantado pode ter várias partições e cada partição pode ter várias réplicas. O parâmetro `HealthInformation` armazena informações sobre o problema de integridade que está sendo reportado. Adicione este namespace ao arquivo **Stateful1.cs**.

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

5. Vamos simular essa falha e ver como ela é exibida nas ferramentas de monitoramento de integridade. Para simular a falha, comente a primeira linha no código de relatório de integridade adicionado acima. Depois de comentar a primeira linha, o código se parecerá com o mostrado abaixo. Agora, isso acionará o relatório de integridade sempre que RunAsync for executado. Depois de fazer a alteração, execute o aplicativo usando **F5**.

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

7. Se você selecionar a réplica primária na exibição de árvore do Gerenciador do Service Fabric, verá que ela mostra a integridade em erro também. Ela também exibe os detalhes do relatório de integridade que foram adicionados ao parâmetro `HealthInformation` no código. Você pode ver os mesmos relatórios de integridade no PowerShell, bem como no Portal do Azure. ![Integridade da réplica no Gerenciador do Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Esse relatório permanecerá no gerenciador de integridade até que seja substituído por outro relatório ou até que essa réplica seja excluída. Como não definimos uma vida útil para esse relatório de integridade no objeto HealthInformation, ele nunca vai expirar. Para que a integridade seja consultada e relatada, o FabricClient precisa estar em um processo com privilégios administrativos.

## Próximas etapas
[Aprofunde-se na integridade do Service Fabric](service-fabric-health-introduction.md)

<!---HONumber=AcomDC_1223_2015-->