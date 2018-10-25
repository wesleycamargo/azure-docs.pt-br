---
title: Depurador de Instantâneos do Application Insights do Azure para aplicativos .NET | Microsoft Docs
description: Depure instantâneos são coletados automaticamente quando exceções forem geradas na produção de aplicativos .NET
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/10/2018
ms.reviewer: pharring
ms.author: mbullwin
ms.openlocfilehash: 6dd39fddd99f5f8ea9329f21c271ed4c1063362d
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078963"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Depurar instantâneos em exceções em aplicativos .NET

Quando ocorrer uma exceção, você pode coletar automaticamente um Instantâneo de Depuração de seu aplicativo web ativo. O instantâneo mostra o estado do código-fonte e variáveis no momento em que a exceção foi lançada. O Depurador de Instantâneo (visualização) no [Azure Application Insights](app-insights-overview.md) monitora a telemetria de exceção de seu aplicativo Web. Ele coleta instantâneos em suas exceções com mais lançamentos para que você tenha as informações necessárias para diagnosticar problemas na produção. Inclua o [Pacote de coletor de instantâneos do NuGet](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) em seu aplicativo e, opcionalmente, configure parâmetros de coleta em [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Os instantâneos aparecem nas [exceções](app-insights-asp-net-exceptions.md) no portal do Application Insights.

Você pode exibir instantâneos de depuração no portal para ver a pilha de chamadas e inspecionar variáveis em cada quadro da pilha de chamadas. Para obter uma experiência de depuração mais poderosa com o código-fonte, abra os instantâneos com o Visual Studio 2017 Enterprise [baixando a extensão do Depurador de Instantâneos para o Visual Studio](https://aka.ms/snapshotdebugger). No Visual Studio, também é possível [configurar o Snappoints para fazer instantâneos interativamente](https://aka.ms/snappoint) sem esperar por uma exceção.

Instantâneos de depuração são armazenados por sete dias. Essa política de retenção é definida por aplicativo. Se for necessário aumentar esse valor, você poderá solicitar o aumento abrindo um caso de suporte no portal do Azure.

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
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
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
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Configurar a coleta de instantâneo para outros aplicativos .NET

1. Se seu aplicativo ainda não está instrumentado com o Application Insights, comece [habilitando o Application Insights e definindo a chave de instrumentação](app-insights-windows-desktop.md).

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

O acesso a instantâneos é protegido por RBAC (controle de acesso baseado em função). Para inspecionar um instantâneo, primeiro você deve ser adicionado à função necessária por um proprietário da assinatura.

> [!NOTE]
> Proprietários e colaboradores não têm automaticamente essa função. Se quiserem exibir instantâneos, eles deverão ser adicionados à função.

Os proprietários de assinaturas deverão atribuir a função `Application Insights Snapshot Debugger` aos usuários que inspecionarão os instantâneos. Essa função pode ser atribuída a usuários individuais ou a grupos por proprietários de assinatura para o recurso Application Insights de destino ou seu grupo de recursos ou a assinatura.

1. Navegue até o recurso Application Insights no portal do Azure.
1. Clique em **Controle de Acesso (IAM)**.
1. Clique no botão **+Adicionar**.
1. Selecione o **Depurador de Instantâneos do Application Insights** na lista suspensa **Funções**.
1. Procure e insira um nome para o usuário a ser adicionado.
1. Clique no botão **Salvar** para adicionar o usuário à função.


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

O instantâneo baixado contém os arquivos de símbolo encontrados no servidor de aplicativos Web. Esses arquivos de símbolo são necessários para associar dados do instantâneo ao código-fonte. Para aplicativos do Serviço de Aplicativo, habilite a implantação de símbolo quando você publicar seus aplicativos Web.

## <a name="how-snapshots-work"></a>Como os instantâneos funcionam

O Coletor de Instantâneo é implementado como um [Processador do Application Insights Telemetry](app-insights-configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Quando seu aplicativo é executado, o Processador de Telemetria do Coletor de Instantâneo será adicionado ao pipeline de telemetria do aplicativo.
Cada vez que o aplicativo chama [TrackException](app-insights-asp-net-exceptions.md#exceptions), o Coletor de Instantâneo calcula uma ID do problema do tipo de exceção sendo gerada e o método que a gerou.
Cada vez que o aplicativo chama TrackException, um contador é incrementado para a ID do Problema apropriada. Quando o contador alcançar o valor `ThresholdForSnapshotting`, a ID do Problema será adicionada a um Plano de Coleta.

O Snapshot Collector também monitora as exceções geradas assinando o evento [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception). Quando esse evento for acionado, a ID do Problema da exceção será calculada e comparada com as IDs de Problema no Plano de Coleta.
Se houver uma correspondência, será criado um instantâneo do processo em execução. O instantâneo será atribuído a um identificador exclusivo e a exceção será marcada com esse identificador. Depois que o manipulador FirstChanceException retornar, a exceção lançada será processada como normal. Por fim, a exceção acessa o método TrackException novamente, em que, junto com o identificador de instantâneo, será relatada ao Application Insights.

O processo principal continuará a executar e atender o tráfego para os usuários com pouca interrupção. Enquanto isso, o instantâneo será entregue ao processo Carregador de Instantâneo. O Carregador de Instantâneo criará um minidespejo e o carregará no Application Insights junto com qualquer arquivo de símbolo (.pdb) relevante.

> [!TIP]
> - Um instantâneo do processo é um clone suspenso do processo em execução.
> - Criar o instantâneo leva cerca de 10 a 20 milissegundos.
> - O valor padrão para `ThresholdForSnapshotting` é 1. Esse também é o valor mínimo. Portanto, seu aplicativo deve disparar a mesma exceção **duas vezes** antes de um instantâneo ser criado.
> - Defina `IsEnabledInDeveloperMode` como true se desejar gerar instantâneos durante a depuração no Visual Studio.
> - A taxa de criação de instantâneos é limitada pela configuração `SnapshotsPerTenMinutesLimit`. Por padrão, o limite é um instantâneo a cada dez minutos.
> - Não é possível carregar mais de 50 instantâneos por dia.

## <a name="current-limitations"></a>Limitações atuais

### <a name="publish-symbols"></a>Publicar símbolos
O Depurador de Instantâneo requer que os arquivos de símbolo no servidor de produção decodifiquem variáveis e ofereçam uma experiência de depuração no Visual Studio.
A versão 15.2 (ou superior) do Visual Studio 2017 publica símbolos para builds de versão por padrão ao publicar no Serviço de Aplicativo. Em versões anteriores, você precisa adicionar a seguinte linha ao arquivo `.pubxml` de seu perfil de publicação para que os símbolos sejam publicados no modo de versão:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Para a Computação do Azure e outros tipos, certifique-se de que os arquivos de símbolo estejam na mesma pasta que o arquivo .dll do aplicativo principal (geralmente, `wwwroot/bin`) ou que estejam disponíveis no caminho atual.

### <a name="optimized-builds"></a>Builds otimizados
Em alguns casos, variáveis locais não podem ser exibidas em builds de versão devido a otimizações aplicadas pelo compilador JIT.
No entanto, nos Serviços de Aplicativos do Azure, o Coletor de Instantâneo pode cancelar a otimização dos métodos que geraram a exceção que fazem parte do Plano de Coleta.

> [!TIP]
> Instale a extensão de site do Application Insights no seu Serviço de Aplicativo para obter suporte de desotimização.

## <a name="troubleshooting"></a>solução de problemas

Estas dicas ajudarão a solucionar problemas com o Depurador do Instantâneo.

### <a name="use-the-snapshot-health-check"></a>Use a verificação de integridade do instantâneo
Vários problemas comuns resultam na não exibição do Abrir Instantâneo de Depuração. Usando um Coletor de Instantâneo desatualizado, por exemplo; alcançando o limite de carregamento diário; ou talvez o instantâneo está simplesmente demorando muito para carregar. Use a Verificação de Integridade de Instantâneo para solucionar problemas comuns.

Há um link no painel de exceção do modo de rastreamento de ponta a ponta que leva você para a Verificação de Integridade de Instantâneo.

![Entre na verificação de integridade do instantâneo](./media/app-insights-snapshot-debugger/enter-snapshot-health-check.png)

A interface interativa, parecida com um bate-papo, procura problemas comuns e orienta você na correção deles.

![Verificação de Integridade](./media/app-insights-snapshot-debugger/healthcheck.png)

Se isso não resolver o problema, consulte as etapas manuais de solução de problemas a seguir.

### <a name="verify-the-instrumentation-key"></a>Verificar a chave de instrumentação

Certifique-se de que está usando a chave de instrumentação correta no aplicativo publicado. Normalmente, a chave de instrumentação é lida do arquivo ApplicationInsights.config. Verifique se o valor é o mesmo da chave de instrumentação para o recurso do Application Insights que você vê no portal.

### <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Atualize para a versão mais recente do pacote NuGet

Use o Gerenciador de Pacotes NuGet do Visual Studio para verificar se você está usando a versão mais recente do Microsoft.ApplicationInsights.SnapshotCollector. Notas de versão podem ser encontradas em https://github.com/Microsoft/ApplicationInsights-Home/issues/167

### <a name="check-the-uploader-logs"></a>Verificar os logs de carregador

Depois que um instantâneo é criado, um arquivo de minidespejo (. dmp) é criado no disco. Um processo separado do carregador cria esse arquivo de minidespejo e o carrega, com quaisquer PDBs associados, para o armazenamento do Depurador de Instantâneos do Application Insights. Após o minidespejo ser carregado com êxito, ele será excluído do disco. Os arquivos de log do processo de carregador são mantidos em disco. Em um ambiente de Serviço de Aplicativo, você pode encontrar esses logs em `D:\Home\LogFiles`. Use o site de gerenciamento do Kudu para o Serviço de Aplicativo para localizar esses arquivos de log.

1. Abra seu aplicativo de Serviço de Aplicativo no portal do Azure.
2. Clique em **Ferramentas Avançadas** ou pesquise **Kudu**.
3. Clique em **Ir**.
4. Na caixa de listagem suspensa do **Console de Depuração**, selecione **CMD**.
5. Clique em **Arquivos de Log**.

Você deverá ver pelo menos um arquivo com um nome que começa com `Uploader_` ou `SnapshotUploader_` e uma extensão `.log`. Clique no ícone apropriado para baixar os arquivos de log ou abri-los em um navegador.
O nome do arquivo inclui um sufixo exclusivo que identifica a instância de serviço de aplicativo. Se a instância do Serviço de Aplicativo é hospedada em mais de uma máquina, há arquivos de log separados para cada máquina. Quando o carregador detectar um novo arquivo de minidespejo, ele será registrado no arquivo de log. Aqui está um exemplo de um instantâneo e upload bem-sucedido:

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

Para aplicativos que _não_ estão hospedados no Serviço de Aplicativo, os logs de carregador estão na mesma pasta que o minidespejo: `%TEMP%\Dumps\<ikey>` (em que `<ikey>` é sua chave de instrumentação).

### <a name="troubleshooting-cloud-services"></a>Solucionando problemas dos Serviços de Nuvem
Para funções nos Serviços de Nuvem, a pasta temporária padrão pode ser muito pequena para conter os arquivos de minidespejo, levando a instantâneos perdidos.
O espaço necessário depende do conjunto de trabalho total do seu aplicativo e o número de instantâneos simultâneos.
O conjunto de trabalho de uma função Web do ASP.NET de 32 bits tem normalmente entre 200 MB e 500 MB.
Permita pelo menos dois instantâneos simultâneos.
Por exemplo, se seu aplicativo usa 1 GB do conjunto de trabalho total, você deve garantir que há pelo menos 2 GB de espaço em disco para armazenar instantâneos.
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

### <a name="overriding-the-shadow-copy-folder"></a>Substituição da pasta de cópia de sombra

Quando é iniciado, o Snapshot Collector tenta encontrar uma pasta no disco que seja adequada para executar o processo do Carregador de instantâneo. A pasta escolhida é conhecida como a pasta de cópia de sombra.

O Snapshot Collector verifica alguns locais bem conhecidos, certificando-se de ter as permissões para copiar os binários do carregador de instantâneos. As variáveis de ambiente a seguir são usadas:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

Se não for possível encontrar uma pasta adequada, o Snapshot Collector relatará um erro afirmando que _"Não foi possível localizar uma pasta de cópia de sombra adequada."_

Se a cópia falhar, o Snapshot Collector relatará um erro `ShadowCopyFailed`.

Se o carregador não puder ser iniciado, o Snapshot Collector relatará um erro de `UploaderCannotStartFromShadowCopy`. O corpo da mensagem frequentemente contém `System.UnauthorizedAccessException`. Esse erro geralmente ocorre porque o aplicativo está sendo executado em uma conta com permissões reduzidas. A conta tem permissão para gravar na pasta de cópia de sombra, mas não tem permissão para executar o código.

Uma vez que esses erros geralmente ocorrem durante a inicialização, eles geralmente serão seguidos por um erro `ExceptionDuringConnect` afirmando que houve _"Falha ao iniciar o carregador."_

Para solucionar esses erros, você pode especificar a pasta de cópia de sombra manualmente por meio da opção de configuração `ShadowCopyFolder`. Por exemplo, usando ApplicationInsights.config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Ou, se estiver usando appsettings.json com um aplicativo .NET Core:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Usar a pesquisa do Application Insights para encontrar exceções com instantâneos

Quando um instantâneo é criado, a exceção lançada é marcada com uma ID de instantâneo. A ID do instantâneo é incluída como uma propriedade personalizada quando a telemetria de exceção é relatada ao Application Insights. Usando a **Pesquisa** no Application Insights, você pode localizar toda a telemetria com a propriedade personalizada `ai.snapshot.id`.

1. Navegue até o recurso do Application Insights no portal do Azure.
2. Clique em **Pesquisar**.
3. Digite `ai.snapshot.id` na caixa de texto de pesquisa e pressione Enter.

![Pesquisar por telemetria com uma ID de instantâneo no portal](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Se essa pesquisa não retornar resultados, então nenhum instantâneo foi relatado ao Application Insights para seu aplicativo no intervalo de tempo selecionado.

Para pesquisar uma ID de instantâneo específico dos logs do carregador, digite o ID na caixa de pesquisa. Se você não conseguir localizar telemetria para um instantâneo que você sabe que foi carregado, siga estas etapas:

1. Verifique que você está olhando o recurso do Application Insights à direita, verificando a chave de instrumentação.

2. Usando o carimbo de data/hora do log de carregador, ajuste o filtro de intervalo de tempo da pesquisa para cobrir esse intervalo de tempo.

Se você não vir uma exceção com essa ID de instantâneo, a telemetria de exceção não foi relatada ao Application Insights. Essa situação pode ocorrer se o aplicativo falhou após executar o instantâneo, mas antes de relatar a telemetria de exceção. Nesse caso, verifique os logs de Serviço de Aplicativo em `Diagnose and solve problems` para ver se houve reinicializações inesperadas ou exceções sem tratamento.

### <a name="edit-network-proxy-or-firewall-rules"></a>Editar regras de firewall ou proxy de rede

Se o aplicativo se conectar à Internet por meio de um proxy ou firewall, talvez será necessário editar as regras para permitir que o aplicativo se comunique com o serviço de Depurador de Instantâneos. Veja uma [lista de endereços IP e portas usadas pelo Depurador de Instantâneos](app-insights-ip-addresses.md#snapshot-debugger).

## <a name="next-steps"></a>Próximas etapas

* [Definir snappoints em seu código](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) para obter instantâneos sem esperar por uma exceção.
* [Diagnosticar exceções em seus aplicativos Web](app-insights-asp-net-exceptions.md) explica como deixar as exceções mais visíveis para o Application Insights.
* A [Detecção Inteligente](app-insights-proactive-diagnostics.md) descobre automaticamente anomalias de desempenho.
