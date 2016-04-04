<properties
   pageTitle="Relatar e verificar a integridade com o Service Fabric do Azure| Microsoft Azure"
   description="Saiba como enviar relatórios de integridade do seu código de serviço e verificar a integridade do serviço usando as ferramentas de monitoramento de integridade fornecidas pelo Service Fabric do Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="punewa"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/18/2016"
   ms.author="toddabel"/>

# Relatar e verificar a integridade de serviço
Quando seus serviços enfrentam problemas, a sua capacidade de reagir e corrigir os incidentes e as interrupções resultantes depende da capacidade de detectar os problemas rapidamente. Ao relatar problemas e falhas ao gerenciador de integridade do Service Fabric usando seu código de serviço, é possível usar ferramentas padrão de monitoramento de integridade fornecidas pelo Service Fabric para verificar o status de integridade.

Há duas maneiras para relatar a integridade do serviço.

1. Usando os objetos [Partition](https://msdn.microsoft.com/library/system.fabric.istatefulservicepartition.aspx) ou [CodePackageActivationContext](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.aspx). Com os objetos `Partition` e `CodePackageActivationContext`, você pode relatar a integridade de elementos que fazem parte do contexto atual. Por exemplo, o código que está sendo executado como parte de uma réplica pode relatar a integridade apenas dessa réplica, da partição a qual pertence e do aplicativo do qual faz parte.

2. Usando `FabricClient`. Você só poderá usar `FabricClient` para relatar a integridade do código do serviço se o cluster não for [seguro](service-fabric-cluster-security.md) ou se o serviço estiver sendo executado com privilégios de administrador. Isso não acontecerá na maioria dos cenários reais. Com o FabricClient, você poderá relatar a integridade de qualquer entidade que faz parte do cluster. Mas o ideal é que o código do serviço envie apenas relatórios relacionados à sua própria integridade.

Este artigo mostra um exemplo de como relatar a integridade do código do serviço e como verificar o status da integridade usando as ferramentas fornecidas pelo Service Fabric. Este artigo é uma rápida introdução aos recursos de monitoramento de integridade no Service Fabric. Para obter informações mais detalhadas, você pode ler a série de artigos explicativos sobre integridade, começando com o link no fim deste documento.

## Pré-requisitos
Você deve ter o seguinte instalado:

   * Visual Studio 2015
   * SDK da Malha do Serviço

## Para criar um cluster de desenvolvimento local seguro
Inicie o PowerShell com privilégios de administrador e execute os comandos a seguir.

![Criar um cluster de desenvolvimento local seguro](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

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

6. Você também pode verificar a integridade usando o PowerShell. Você pode verificar a integridade de um aplicativo usando ```Get-ServiceFabricApplicationHealth``` e verificar a integridade do serviço usando ```Get-ServiceFabricServiceHealth```. O relatório de integridade para o mesmo aplicativo no PowerShell tem esta aparência.

    ![Aplicativo de integridade no PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## Para adicionar eventos de integridade personalizados ao seu código de serviço
Os modelos de projeto do Visual Studio da Malha do Serviço contêm amostra de código. As etapas a seguir mostram como você pode relatar eventos de integridade personalizados a partir do código de seu serviço. Esses relatórios serão exibidos automaticamente nas ferramentas padrão para monitoramento de integridade fornecidas pelo Service Fabric, como o Gerenciador do Service Fabric, a exibição de integridade do Portal do Azure e o PowerShell.

1. Abra novamente o aplicativo que você criou anteriormente no Visual Studio ou crie um novo usando um serviço com estado dos modelos do Visual Studio.

2. Em seguida, abra o arquivo **Stateful1.cs** e procure a chamada `myDictionary.TryGetValueAsync` no método *RunAsync*. Você pode ver que isso retorna um `result` que mantém o valor atual do contador, pois a lógica principal deste aplicativo é manter uma contagem em execução. Se esse fosse um aplicativo real e a falta de resultado representasse uma falha, seria necessário indicar isso por meio da integridade.

3. Para relatar um evento de integridade para a falta de resultado representando uma falha, adicione o código a seguir após a chamada `myDictionary.TryGetValueAsync`. Relatamos a integridade da réplica, uma vez que ela está sendo relatada de um serviço com estado. O parâmetro `HealthInformation` armazena informações sobre o problema de integridade que está sendo reportado. Adicione este namespace ao arquivo **Stateful1.cs**.

    ```csharp
    using System.Fabric.Health;
    ```

    Adicione este código após a chamada `myDictionary.TryGetValueAsync`.

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```

    Para um serviço sem estado, use o código a seguir.

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```

    Se o serviço estiver sendo executado com privilégios de administrador ou se o cluster não for [seguro](service-fabric-cluster-security.md), também será possível usar o FabricClient para relatar a integridade, conforme mostrado abaixo.

    Crie o FabricClient após a declaração `var myDictionary`

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    E adicione este código após a chamada `myDictionary.TryGetValueAsync`.

    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

4. Vamos simular essa falha e ver como ela é exibida nas ferramentas de monitoramento de integridade. Para simular a falha, comente a primeira linha no código de relatório de integridade adicionado acima. Depois de comentar a primeira linha, o código se parecerá com o mostrado abaixo. Agora, isso acionará o relatório de integridade sempre que RunAsync for executado. Depois de fazer a alteração, execute o aplicativo usando **F5**.

    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```

5. Quando o aplicativo estiver em execução, abra o Gerenciador do Service Fabric para verificar a integridade do aplicativo. Desta vez, o Gerenciador do Service Fabric mostrará o aplicativo como não íntegro. Isso ocorre devido ao erro relatado no código que adicionamos acima.

    ![Aplicativo não íntegro no Gerenciador do Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

6. Se você selecionar a réplica primária na exibição de árvore do Gerenciador do Service Fabric, verá que ela mostra a integridade em erro também. Ela também exibe os detalhes do relatório de integridade que foram adicionados ao parâmetro `HealthInformation` no código. Você pode ver os mesmos relatórios de integridade no PowerShell, bem como no portal do Azure.

    ![Integridade da réplica no Gerenciador do Service Fabric](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Esse relatório permanecerá no gerenciador de integridade até que seja substituído por outro relatório ou até que essa réplica seja excluída. Como não definimos uma vida útil para esse relatório de integridade no objeto HealthInformation, ele nunca vai expirar.

Recomendamos que a integridade seja relatada no nível mais granular, que no caso acima é a réplica. Você também pode relatar a integridade em um `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Para relatar a integridade de em `Application`, `DeployedApplication` e `DeployedServicePackage`, use `CodePackageActivationContext`

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## Próximas etapas
[Aprofunde-se na integridade do Service Fabric](service-fabric-health-introduction.md)

<!---HONumber=AcomDC_0323_2016-->