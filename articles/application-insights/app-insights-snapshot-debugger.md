---
title: "Depurador de Instantâneos do Application Insights do Azure para aplicativos .NET | Microsoft Docs"
description: "Depure instantâneos são coletados automaticamente quando exceções forem geradas na produção de aplicativos .NET"
services: application-insights
documentationcenter: 
author: qubitron
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: a499f1d5f3a53a0cafb9056c9b5a4d164f80d8c5
ms.contentlocale: pt-br
ms.lasthandoff: 07/04/2017


---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Depurar instantâneos em exceções em aplicativos .NET

Quando ocorrer uma exceção, você pode coletar automaticamente um Instantâneo de Depuração de seu aplicativo web ativo. O instantâneo mostra o estado do código-fonte e variáveis no momento em que a exceção foi lançada. O Depurador de Instantâneo (visualização) no [Azure Application Insights](app-insights-overview.md) monitora a telemetria de exceção de seu aplicativo Web. Ele coleta instantâneos em suas exceções com mais lançamentos para que você tenha as informações necessárias para diagnosticar problemas na produção. Inclua o [Pacote de coletor de instantâneos do NuGet](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) em seu aplicativo e, opcionalmente, configure parâmetros de coleta em [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Os instantâneos aparecem nas [exceções](app-insights-asp-net-exceptions.md) no portal do Application Insights.

Você pode exibir instantâneos de depuração no portal para ver a pilha de chamadas e inspecionar variáveis em cada quadro da pilha de chamadas. Para obter uma experiência de depuração mais poderosa com o código-fonte, abra os instantâneos com o Visual Studio 2017 Enterprise [baixando a extensão do Depurador de Instantâneos para o Visual Studio](https://aka.ms/snapshotdebugger).

Coleta de instantâneo está disponível para:
* Aplicativos ASP.NET e do .NET framework com o .NET Framework 4.5 ou posterior.
* Aplicativos do .NET Core 2.0 e Núcleo do ASP.NET Core 2.0 em execução no Windows.

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>Configurar a coleta de instantâneo para aplicativos ASP.NET

1. [Habilite o Application Insights no seu aplicativo web](app-insights-asp-net.md), se você ainda não tiver feito isso.

2. Inclua o pacote do NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) em seu aplicativo. 

3. Examine as opções padrão que o pacote adicionou a [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>5</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>06:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in one minute. -->
        <SnapshotsPerMinuteLimit>2</SnapshotsPerMinuteLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>50</SnapshotsPerDayLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Os instantâneos são coletados apenas em exceções que são relatadas ao Application Insights. Em alguns casos (por exemplo, versões mais antigas da plataforma .NET), talvez seja necessário [configurar coleta de exceção](app-insights-asp-net-exceptions.md#exceptions) para ver exceções com instantâneos no portal.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Configurar a coleta de instantâneo para aplicativos do Núcleo do ASP.NET 2.0

1. [Habilite o Application Insights no seu aplicativo web Núcleo do ASP.NET](app-insights-asp-net-core.md), se você ainda não tiver feito isso.

2. Inclua o pacote do NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) em seu aplicativo.

3. Modifique o `ConfigureServices` método na classe do seu aplicativo `Startup` para adicionar o processador de telemetria do coletor de instantâneo.
   ```C#
   using Microsoft.ApplicationInsights.SnapshotCollector;
   ...
   class Startup
   {
       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           services.AddSingleton<Func<ITelemetryProcessor, ITelemetryProcessor>>(next => new SnapshotCollectorTelemetryProcessor(next));
           // TODO: Add any other services your application needs here.
       }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Configurar a coleta de instantâneo para outros aplicativos .NET

1. Se seu aplicativo ainda não está instrumentado com o Application Insights, começar por [habilitando Application Insights e definindo a chave de instrumentação](app-insights-windows-desktop.md).

2. Adicione o pacote do NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) em seu aplicativo.

3. Os instantâneos são coletados apenas em exceções que são relatadas ao Application Insights. Talvez seja necessário modificar o código para relatá-los. O código de tratamento de exceção depende da estrutura do seu aplicativo, mas há um exemplo abaixo:
   ```C#
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }

## Grant permissions

Owners of the Azure subscription can inspect snapshots. Other users must be granted permission by an owner.

To grant permission, assign the `Application Insights Snapshot Debugger` role to users who will inspect snapshots. This role can be assigned to individual users or groups by subscription owners for the target Application Insights resource or its resource group or subscription.

1. On the Application Insights navigation menu, select **Access Control (IAM)**.
2. Click **Roles** > **Application Insights Snapshot Debugger**.
3. Click **Add**, and then select a user or group.

    >[!IMPORTANT] 
    Snapshots can potentially contain personal and other sensitive information in variable and parameter values.

## Debug snapshots in the Application Insights portal

If a snapshot is available for a given exception or a problem ID, an **Open Debug Snapshot** button appears on the [exception](app-insights-asp-net-exceptions.md) in the Application Insights portal.

![Open Debug Snapshot button on exception](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

In the Debug Snapshot view, you see a call stack and a variables pane. When you select frames of the call stack in the call stack pane, you can view local variables and parameters for that function call in the variables pane.

![View Debug Snapshot in the portal](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Snapshots might contain sensitive information, and by default they are not viewable. To view snapshots, you must have the `Application Insights Snapshot Debugger` role assigned to you.

## Debug snapshots with Visual Studio 2017 Enterprise
1. Click the **Download Snapshot** button to download a `.diagsession` file, which can be opened by Visual Studio 2017 Enterprise. 

2. To open the `.diagsession` file, you must first [download and install the Snapshot Debugger extension for Visual Studio](https://aka.ms/snapshotdebugger).

3. After you open the snapshot file, the Minidump Debugging page in Visual Studio appears. Click **Debug Managed Code** to start debugging the snapshot. The snapshot opens to the line of code where the exception was thrown so that you can debug the current state of the process.

    ![View debug snapshot in Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

The downloaded snapshot contains any symbol files that were found on your web application server. These symbol files are required to associate snapshot data with source code. For App Service apps, make sure to enable symbol deployment when you publish your web apps.

## How snapshots work

When your application starts, a separate snapshot uploader process is created that monitors your application for snapshot requests. When a snapshot is requested, a shadow copy of the running process is made in about 10 to 20 minutes. The shadow process is then analyzed, and a snapshot is created while the main process continues to run and serve traffic to users. The snapshot is then uploaded to Application Insights along with any relevant symbol (.pdb) files that are needed to view the snapshot.

## Current limitations

### Publish symbols
The Snapshot Debugger requires symbol files on the production server to decode variables and to provide a debugging experience in Visual Studio. The 15.2 release of Visual Studio 2017 publishes symbols for release builds by default when it publishes to App Service. In prior versions, you need to add the following line to your publish profile `.pubxml` file so that symbols are published in release mode:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Para a Computação do Azure e outros tipos, certifique-se de que os arquivos de símbolo estejam na mesma pasta que o arquivo .dll do aplicativo principal (geralmente, `wwwroot/bin`) ou que estejam disponíveis no caminho atual.

### <a name="optimized-builds"></a>Builds otimizados
Em alguns casos, variáveis locais não podem ser visualiados em builds de versão devido a otimizações que são aplicadas durante o processo de compilação.

## <a name="troubleshooting"></a>Solucionar problemas

Estas dicas ajudarão a solucionar problemas com o Depurador do Instantâneo.

### <a name="verify-the-instrumentation-key"></a>Verificar a chave de instrumentação

Certifique-se de que você está usando a chave de instrumentação correta em seu aplicativo publicado. Normalmente, o Application Insights lê a chave de instrumentação do arquivo ApplicationInsights.config. Verifique se o valor é o mesmo que a chave de instrumentação para o recurso do Application Insights que você vê no portal.

### <a name="check-the-uploader-logs"></a>Verificar os logs de carregador

Depois que um instantâneo é criado, um arquivo de minidespejo (. dmp) é criado no disco. Um processo separado do carregador usa esse arquivo de minidespejo e carrega, juntamente com quaisquer PDBs associados, para o armazenamento do Depurador de Instantâneo do Application Insights. Após o minidespejo ser carregado com êxito, ele é excluído do disco. Os arquivos de log para o carregador de minidespejo são mantidos em disco. Em um ambiente de Serviço de Aplicativo, você pode encontrar esses logs em `D:\Home\LogFiles\Uploader_*.log`. Use o site de gerenciamento do Kudu para o Serviço de Aplicativo para localizar esses arquivos de log.

1. Abra seu aplicativo de Serviço de Aplicativo no portal do Azure.

2. Selecione a folha **Ferramentas Avançadas**, ou pesquise por **Kudu**.
3. Clique em **Ir**.
4. Na caixa de listagem suspensa do **Console de Depuração**, selecione **CMD**.
5. Clique em **Arquivos de Log**.

Você deverá ver pelo menos um arquivo com um nome que começa com `Uploader_` e uma extensão `.log`. Clique no ícone apropriado para baixar os arquivos de log ou abri-los em um navegador.
O nome do arquivo inclui o nome do computador. Se a instância do Serviço de Aplicativo é hospedada em mais de uma máquina, há arquivos de log separados para cada máquina. Quando o carregador detecta um novo arquivo de minidespejo, ele será registrado no arquivo de log. Aqui está um exemplo de um upload bem-sucedido:

```
MinidumpUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:08.0349846Z
MinidumpUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 329.12 MB
    DateTime=2017-05-25T14:25:16.0145444Z
MinidumpUploader.exe Information: 0 : Upload successful.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2017-05-25T14:25:44.2310982Z
MinidumpUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2017-05-25T14:25:44.5435948Z
MinidumpUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:44.6095821Z
```

No exemplo anterior, a chave de instrumentação é `c12a605e73c44346a984e00000000000`. Esse valor deve corresponder à chave de instrumentação para seu aplicativo.
O minidespejo é associado a um instantâneo com a ID `139e411a23934dc0b9ea08a626db16c5`. Você pode usar essa ID mais tarde para localizar a telemetria de exceção associada na análise de Application Insights.

O carregador examina novos PDBs cerca de uma vez a cada 15 minutos. Aqui está um exemplo:

```
MinidumpUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\home\site\wwwroot\ for local PDBs.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\local\Temporary ASP.NET Files\root\a6554c94\e3ad6f22\assembly\dl3\81d5008b\00b93cc8_dec5d201 for local PDBs.
    DateTime=2017-05-25T15:11:38.8160276Z
MinidumpUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2017-05-25T15:11:38.8316450Z
MinidumpUploader.exe Information: 0 : Deleted PDB scan marker D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\.pdbscan.
    DateTime=2017-05-25T15:11:38.8316450Z
```

Para aplicativos que _não_ estão hospedados no Serviço de Aplicativo, os logs de carregador estão na mesma pasta que o minidespejos: `%TEMP%\Dumps\<ikey>` (onde `<ikey>` é a sua chave de instrumentação).

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Usar a pesquisa do Application Insights para encontrar exceções com instantâneos

Quando um instantâneo é criado, a exceção lançada é marcada com uma ID de instantâneo. Quando a telemetria de exceção é relatada ao Application Insights, essa ID do instantâneo é incluída como uma propriedade personalizada. Usando a folha de pesquisa no Application Insights, você pode localizar toda a telemetria com o `ai.snapshot.id` propriedade personalizada.

1. Navegue até o recurso do Application Insights no portal do Azure.
2. Clique em **Pesquisar**.
3. Digite `ai.snapshot.id` na caixa de texto de pesquisa e pressione Enter.

![Pesquisar por telemetria com uma ID de instantâneo no portal](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Se essa pesquisa não retornar resultados, então nenhum instantâneo foi relatado ao Application Insights para seu aplicativo no intervalo de tempo selecionado.

Para pesquisar uma ID de instantâneo específico dos logs do carregador, digite o ID na caixa de pesquisa. Se você não conseguir localizar telemetria para um instantâneo que você sabe que foi carregado, siga estas etapas:

1. Verifique que você está olhando o recurso do Application Insights à direita, verificando a chave de instrumentação.

2. Usando o carimbo de data/hora do log de carregador, ajuste o filtro de intervalo de tempo da pesquisa para cobrir esse intervalo de tempo.

Se você não vir uma exceção com essa ID de instantâneo, a telemetria de exceção não foi relatada ao Application Insights. Essa situação pode ocorrer se o aplicativo falhou após executar o instantâneo, mas antes de relatar a telemetria de exceção. Nesse caso, verifique os logs de Serviço de Aplicativo em `Diagnose and solve problems` para ver se houve reinicializações inesperadas ou exceções sem tratamento.

## <a name="next-steps"></a>Próximas etapas

* [Definir snappoints em seu código](https://azure.microsoft.com/blog/snapshot-debugger-for-azure/) para obter instantâneos sem esperar por uma exceção.
* [Diagnosticar exceções em seus aplicativos Web](app-insights-asp-net-exceptions.md) explica como deixar as exceções mais visíveis para o Application Insights. 
* A [Detecção Inteligente](app-insights-proactive-diagnostics.md) descobre automaticamente anomalias de desempenho.

