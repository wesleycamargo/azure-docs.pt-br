---
title: Depurador de Instantâneos do Application Insights do Azure para aplicativos .NET | Microsoft Docs
description: Depure instantâneos são coletados automaticamente quando exceções forem geradas na produção de aplicativos .NET
services: application-insights
documentationcenter: ''
author: pharring
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: mbullwin
ms.openlocfilehash: 0ba58f1384d7c93af30f9b175a5a154811c9a1e0
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Depurar instantâneos em exceções em aplicativos .NET

Quando ocorrer uma exceção, você pode coletar automaticamente um Instantâneo de Depuração de seu aplicativo web ativo. O instantâneo mostra o estado do código-fonte e variáveis no momento em que a exceção foi lançada. O Depurador de Instantâneo (visualização) no [Azure Application Insights](app-insights-overview.md) monitora a telemetria de exceção de seu aplicativo Web. Ele coleta instantâneos em suas exceções com mais lançamentos para que você tenha as informações necessárias para diagnosticar problemas na produção. Inclua o [Pacote de coletor de instantâneos do NuGet](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) em seu aplicativo e, opcionalmente, configure parâmetros de coleta em [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Os instantâneos aparecem nas [exceções](app-insights-asp-net-exceptions.md) no portal do Application Insights.

Você pode exibir instantâneos de depuração no portal para ver a pilha de chamadas e inspecionar variáveis em cada quadro da pilha de chamadas. Para obter uma experiência de depuração mais poderosa com o código-fonte, abra os instantâneos com o Visual Studio 2017 Enterprise [baixando a extensão do Depurador de Instantâneos para o Visual Studio](https://aka.ms/snapshotdebugger). No Visual Studio, também é possível [configurar o Snappoints para fazer instantâneos interativamente](https://aka.ms/snappoint) sem esperar por uma exceção.

Coleta de instantâneo está disponível para:
* Aplicativos ASP.NET e do .NET framework com o .NET Framework 4.5 ou posterior.
* Aplicativos do .NET Core 2.0 e Núcleo do ASP.NET Core 2.0 em execução no Windows.

Os ambientes a seguir são suportados:
* Serviço de Aplicativo do Azure.
* Serviço de Nuvem do Azure executando a família de SO 4 ou posterior.
* Serviços do Azure Service Fabric sendo executados no Windows Server 2012 R2 ou posterior.
* Máquinas Virtuais do Azure executando Windows Server 2012 R2 ou posterior.
* Máquinas locais virtuais ou físicas executando Windows Server 2012 R2 ou posterior.

> [!NOTE]
> Não há suporte para aplicativos cliente (por exemplo, WPF, Windows Forms ou UWP).

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
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>24:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>1</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Os instantâneos são coletados apenas em exceções que são relatadas ao Application Insights. Em alguns casos (por exemplo, versões mais antigas da plataforma .NET), talvez seja necessário [configurar coleta de exceção](app-insights-asp-net-exceptions.md#exceptions) para ver exceções com instantâneos no portal.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Configurar a coleta de instantâneo para aplicativos do Núcleo do ASP.NET 2.0

1. [Habilite o Application Insights no seu aplicativo web Núcleo do ASP.NET](app-insights-asp-net-core.md), se você ainda não tiver feito isso.

    > [!NOTE]
    > Verifique se o seu aplicativo faz referência à versão 2.1.1 ou mais recente do pacote Microsoft.ApplicationInsights.AspNetCore.

2. Inclua o pacote do NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) em seu aplicativo.

3. Modifique a classe `Startup` do seu aplicativo para adicionar e configurar o processador de telemetria do Coletor de Instantâneo.

    Adicione o seguinte usando as instruções para `Startup.cs`

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   using Microsoft.ApplicationInsights.AspNetCore;
   using Microsoft.ApplicationInsights.Extensibility;
   ```

   Adicione a classe `SnapshotCollectorTelemetryProcessorFactory` a seguir para a classe `Startup`.

   ```csharp
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           private readonly IServiceProvider _serviceProvider;

           public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
               _serviceProvider = serviceProvider;

           public ITelemetryProcessor Create(ITelemetryProcessor next)
           {
               var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
               return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
           }
       }
       ...
    ```
    Adicione os serviços `SnapshotCollectorConfiguration` e `SnapshotCollectorTelemetryProcessorFactory` para o pipeline de inicialização:

    ```csharp
       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           // Configure SnapshotCollector from application settings
           services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

           // Add SnapshotCollector telemetry processor.
           services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

           // TODO: Add other services your application needs here.
       }
   }
   ```

4. Configure o Coletor de Instantâneo adicionando uma seção SnapshotCollectorConfiguration ao appsettings.json. Por exemplo: 

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": true,
       "ThresholdForSnapshotting": 5,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"24:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Configurar a coleta de instantâneo para outros aplicativos .NET

1. Se seu aplicativo ainda não está instrumentado com o Application Insights, começar por [habilitando Application Insights e definindo a chave de instrumentação](app-insights-windows-desktop.md).

2. Adicione o pacote do NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) em seu aplicativo.

3. Os instantâneos são coletados apenas em exceções que são relatadas ao Application Insights. Talvez seja necessário modificar o código para relatá-los. O código de tratamento de exceção depende da estrutura do seu aplicativo, mas há um exemplo abaixo:
    ```csharp
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
    ```

## <a name="grant-permissions"></a>Conceder permissões

Os proprietários da assinatura do Azure podem inspecionar instantâneos. Outros usuários devem ter permissão de um proprietário.

Para conceder permissão, atribua a função `Application Insights Snapshot Debugger` aos usuários que irão inspecionar instantâneos. Essa função pode ser atribuída a usuários individuais ou a grupos por proprietários de assinatura para o recurso Application Insights de destino ou seu grupo de recursos ou a assinatura.

1. Abra a folha Controle de Acesso (IAM).
1. Clique no botão +Adicionar.
1. Selecione o Depurador de Instantâneo do Application insights na lista suspensa Funções.
1. Procure e insira um nome para o usuário a ser adicionado.
1. Clique no botão Salvar para adicionar o usuário à função.


> [!IMPORTANT]
> Os instantâneos potencialmente podem conter informações pessoais e outras informações confidenciais em valores de parâmetro e variável.

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Depurar instantâneos no portal do Application Insights

Se um instantâneo estiver disponível para uma determinada ID de problema ou exceção, um botão **Abrir Instantâneo de Depuração** aparecerá na [exceção](app-insights-asp-net-exceptions.md) no portal do Application Insights.

![Botão Abrir Instantâneo de Depuração na exceção](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

Na exibição do Instantâneo de Depuração, você verá uma pilha de chamadas e um painel de variáveis. Quando você seleciona quadros da pilha de chamadas no painel de pilha de chamadas. permite exibir as variáveis locais e os parâmetros da chamada de função no painel de variáveis.

![Exibir Instantâneo de Depuração no portal](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Os instantâneos podem conter informações confidenciais e, por padrão, não estão visíveis. Para exibir instantâneos, você deve ter a função `Application Insights Snapshot Debugger` atribuída a você.

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Depurar instantâneos com o Visual Studio 2017 Enterprise
1. Clique no botão **Baixar Instantâneo** para baixar um arquivo `.diagsession`, que pode ser aberto pelo Visual Studio 2017 Enterprise.

2. Para abrir o arquivo `.diagsession`, primeiro você deve [baixar e instalar a extensão do Depurador de Instantâneo para o Visual Studio](https://aka.ms/snapshotdebugger).

3. Depois de abrir o arquivo de instantâneo, será exibida a página Depuração de Minidespejo no Visual Studio. Clique em **Depurar Código Gerenciado** para iniciar a depuração de instantâneo. O instantâneo abre a linha de código em que a exceção foi lançada e você pode depurar o estado atual do processo.

    ![Exibir instantâneo de depuração no Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

O instantâneo baixado contém os arquivos de símbolo que foram encontrados no servidor de aplicativos Web. Esses arquivos de símbolo são necessários para associar dados do instantâneo ao código-fonte. Para aplicativos do Serviço de Aplicativo, habilite a implantação de símbolo quando você publicar seus aplicativos Web.

## <a name="how-snapshots-work"></a>Como os instantâneos funcionam

Quando seu aplicativo é iniciado, é criado um processo separado de carregador de instantâneos que monitora seu aplicativo quanto a solicitações de instantâneos. Quando um instantâneo é solicitado, é feita uma cópia de sombra do processo em execução em cerca de 10 a 20 milissegundos. O processo de sombra é analisado e um instantâneo é criado enquanto o processo principal continua sendo executado e fornecendo tráfego para os usuários. O instantâneo, então, é carregado para o Application Insights em conjunto com os arquivos de símbolo (.pdb) relevantes necessários para exibir o instantâneo.

## <a name="current-limitations"></a>Limitações atuais

### <a name="publish-symbols"></a>Publicar símbolos
O Depurador de Instantâneo requer que os arquivos de símbolo no servidor de produção decodifiquem variáveis e ofereçam uma experiência de depuração no Visual Studio. A versão 15.2 do Visual Studio 2017 publica símbolos para builds de versão por padrão ao publicar no Serviços de Aplicativos. Em versões anteriores, você precisa adicionar a seguinte linha ao arquivo `.pubxml` de seu perfil de publicação para que os símbolos sejam publicados no modo de versão:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Para a Computação do Azure e outros tipos, certifique-se de que os arquivos de símbolo estejam na mesma pasta que o arquivo .dll do aplicativo principal (geralmente, `wwwroot/bin`) ou que estejam disponíveis no caminho atual.

### <a name="optimized-builds"></a>Builds otimizados
Em alguns casos, variáveis locais não podem ser visualiados em builds de versão devido a otimizações que são aplicadas durante o processo de compilação.

## <a name="troubleshooting"></a>solução de problemas

Estas dicas ajudarão a solucionar problemas com o Depurador do Instantâneo.

### <a name="verify-the-instrumentation-key"></a>Verificar a chave de instrumentação

Certifique-se de que você está usando a chave de instrumentação correta em seu aplicativo publicado. Normalmente, o Application Insights lê a chave de instrumentação do arquivo ApplicationInsights.config. Verifique se o valor é o mesmo que a chave de instrumentação para o recurso do Application Insights que você vê no portal.

### <a name="check-the-uploader-logs"></a>Verificar os logs de carregador

Depois que um instantâneo é criado, um arquivo de minidespejo (. dmp) é criado no disco. Um processo separado do carregador usa esse arquivo de minidespejo e carrega, juntamente com quaisquer PDBs associados, para o armazenamento do Depurador de Instantâneo do Application Insights. Após o minidespejo ser carregado com êxito, ele é excluído do disco. Os arquivos de log para o processo de carregador de são mantidos em disco. Em um ambiente de Serviço de Aplicativo, você pode encontrar esses logs em `D:\Home\LogFiles`. Use o site de gerenciamento do Kudu para o Serviço de Aplicativo para localizar esses arquivos de log.

1. Abra seu aplicativo de Serviço de Aplicativo no portal do Azure.

2. Selecione a folha **Ferramentas Avançadas**, ou pesquise por **Kudu**.
3. Clique em **Ir**.
4. Na caixa de listagem suspensa do **Console de Depuração**, selecione **CMD**.
5. Clique em **Arquivos de Log**.

Você deverá ver pelo menos um arquivo com um nome que começa com `Uploader_` ou `SnapshotUploader_` e uma extensão `.log`. Clique no ícone apropriado para baixar os arquivos de log ou abri-los em um navegador.
O nome do arquivo inclui um sufixo exclusivo que identifica a instância de serviço de aplicativo. Se a instância do Serviço de Aplicativo é hospedada em mais de uma máquina, há arquivos de log separados para cada máquina. Quando o carregador detecta um novo arquivo de minidespejo, ele será registrado no arquivo de log. Aqui está um exemplo de um instantâneo e upload bem-sucedido:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> O exemplo acima é da versão 1.2.0 do pacote do NuGet Microsoft.ApplicationInsights.SnapshotCollector. Em versões anteriores, o processo de carregador é chamado `MinidumpUploader.exe` e o log é menos detalhado.

No exemplo anterior, a chave de instrumentação é `c12a605e73c44346a984e00000000000`. Esse valor deve corresponder à chave de instrumentação para seu aplicativo.
O minidespejo é associado a um instantâneo com a ID `139e411a23934dc0b9ea08a626db16c5`. Você pode usar essa ID mais tarde para localizar a telemetria de exceção associada na análise de Application Insights.

O carregador examina novos PDBs cerca de uma vez a cada 15 minutos. Aqui está um exemplo:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

Para aplicativos que _não_ estão hospedados no Serviço de Aplicativo, os logs de carregador estão na mesma pasta que o minidespejos: `%TEMP%\Dumps\<ikey>` (onde `<ikey>` é a sua chave de instrumentação).

### <a name="troubleshooting-cloud-services"></a>Solucionando problemas dos Serviços de Nuvem
Para funções nos Serviços de Nuvem, a pasta temporária padrão pode ser muito pequena para conter os arquivos de minidespejo, levando a instantâneos perdidos.
O espaço necessário depende do conjunto de trabalho total do seu aplicativo e o número de instantâneos simultâneos.
O conjunto de trabalho de uma função Web do ASP.NET de 32 bits tem normalmente entre 200 MB e 500 MB.
Você deve permitir pelo menos dois instantâneos simultâneos.
Por exemplo, se seu aplicativo usar 1 GB do conjunto de trabalho total, você deve garantir que há pelo menos 2 GB de espaço em disco para armazenar os instantâneos.
Siga estas etapas para configurar a função Serviço de Nuvem com um recurso local dedicado para instantâneos.

1. Adicione um novo recurso de local para seu Serviço de Nuvem ao editar o arquivo de definição (.csdef) do Serviço de Nuvem. O exemplo a seguir define um recurso chamado `SnapshotStore` com um tamanho de 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Modificar o código de inicialização da função para adicionar uma variável de ambiente que aponta para o recurso local `SnapshotStore`. Para Funções de Trabalho, o código deve ser adicionado ao método `OnStart` da sua função:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Para Funções da Web (ASP.NET), o código deve ser adicionado ao método `Application_Start` do seu aplicativo Web:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Atualizar o arquivo ApplicationInsights.config da função para substituir o local da pasta temporária usada pelo `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

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

* [Definir snappoints em seu código](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) para obter instantâneos sem esperar por uma exceção.
* [Diagnosticar exceções em seus aplicativos Web](app-insights-asp-net-exceptions.md) explica como deixar as exceções mais visíveis para o Application Insights.
* A [Detecção Inteligente](app-insights-proactive-diagnostics.md) descobre automaticamente anomalias de desempenho.
