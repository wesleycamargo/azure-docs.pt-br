---
title: Azure Application Insights para ASP.NET Core | Microsoft Docs
description: Monitorar aplicativos web de disponibilidade, desempenho e uso.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: 6635906b6aae418fa3754f1152fae3e0d8903ffc
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989746"
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights para ASP.NET Core

Azure Application Insights fornece monitoramento detalhado do seu aplicativo web até o nível de código. Você pode monitorar facilmente o aplicativo web quanto à sua disponibilidade, desempenho e uso. Você também pode identificar e diagnosticar erros rapidamente em seu aplicativo sem esperar que um usuário os relate.

Este artigo o orienta pelo processo de criação de um exemplo do aplicativo ASP.NET Core [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) no Visual Studio e como iniciar o monitoramento com o Azure Application Insights.

## <a name="prerequisites"></a>Pré-requisitos

- SDK do NET Core 2.0.0 ou posterior.
- Versão do [Visual Studio 2017](https://www.visualstudio.com/downloads/) 15.7.3 ou superior com a carga de trabalho de desenvolvimento da Web e do ASP.NET. 

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Criar um projeto ASP.NET Core no Visual Studio

1. Clique com botão direito e inicie **Visual Studio 2017** como administrador.
2. Selecione **Arquivo** > **Novo** > **Projeto** (Ctrl-Shift-N).

   ![Captura de tela do Menu Arquivo Novo Projeto no Visual Studio](./media/app-insights-asp-net-core/001-new-project.png)

3. Expanda **Visual C#** > Selecione **.NET Core** > **Aplicativo Web do ASP.NET Core**. Insira um **Nome** > **Nome da solução** > Verificar **Criar novo repositório do Git**.

   ![Captura de tela do Assistente Arquivo Novo Projeto no Visual Studio](./media/app-insights-asp-net-core/002-asp-net-core-web-application.png)

4. Selecione **.Net Core** > **Core ASP.NET 2.0** **Aplicativo Web** > **OK**.

    ![Captura de tela do Menu de Seleção Arquivo Novo Projeto no Visual Studio](./media/app-insights-asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Pesquisa do Application Insights

Por padrão, no Visual Studio versão 2015 Atualização 2 ou superior com um projeto baseado em ASP.NET Core 2+, é possível aproveitar a [pesquisa do Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio) mesmo antes de adicionar explicitamente Insights de Aplicativo ao seu projeto.

Para testar essa funcionalidade:

1. Executar seu aplicativo clicando em IIS Express ![Captura de tela do ícone IIS Express do Visual Studio](./media/app-insights-asp-net-core/004-iis-express.png)

2. Selecione **Exibir** > **Outras janelas** > **Pesquisa do Application Insights**.

   ![Captura de tela das Ferramentas de Diagnóstico do Visual Studio](./media/app-insights-asp-net-core/005-view-other-windows-search.png)

3. No momento, a telemetria da sessão de depuração está disponível apenas para análise local. Para habilitar completamente o Application Insights, selecione **Preparação para telemetria** no canto superior direito ou siga as etapas na próxima seção.

   ![Captura de tela da Pesquisa do Application Insights do Visual Studio](./media/app-insights-asp-net-core/006-search.png)

> [!NOTE]
> Para saber mais sobre como os recursos de iluminação do Visual Studio como a [Pesquisa do Application Insights](app-insights-visual-studio.md) e o [CodeLens](app-insights-visual-studio-codelens.md) localmente antes de ter adicionado o Application Insights ao seu projeto ASP.NET Core, confira a explicação ao [final deste artigo.](#Application-Insights-search-continued)

## <a name="add-application-insights-telemetry"></a>Adicionar Application Insights Telemetry

1. Selecione **Projeto** > **Adicionar Application Insights Telemetry...**. (Ou clique com o botão direito do mouse em **Serviços Conectados** e selecione Adicionar Serviço Conectado.)

    ![Captura de tela do Menu de Seleção Arquivo Novo Projeto no Visual Studio](./media/app-insights-asp-net-core/007-project-add-telemetry.png)

2. Selecione **Introdução**. (Dependendo de sua versão do Visual Studio, o texto pode variar um pouco. Algumas versões mais antigas têm um botão **Iniciar gratuitamente**.)

    ![Captura de tela do Menu de Seleção Arquivo Novo Projeto no Visual Studio](./media/app-insights-asp-net-core/008-get-started.png)

3. Selecione uma **Assinatura** > **Recurso** > **Registrar-se**.

## <a name="changes-made-to-your-project"></a>Alterações feitas ao seu projeto

Agora o Application Insights está com baixa sobrecarga. Para examinar as modificações feitas em seu projeto, adicionando a telemetria do Application Insights:

Selecione **Exibir** > **Team Explorer** (Ctrl +\, Ctrl + M) > **Projeto** > **Alterações**

- Total de quatro alterações:

  ![Captura de tela de arquivos alterados, adicionando o Application Insights](./media/app-insights-asp-net-core/009-changes.png)

- Um novo arquivo é criado:

   _ConnectedService.json_

```json
{
  "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
  "Version": "8.12.10405.1",
  "GettingStartedDocument": {
    "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
  }
}
```

- Três arquivos são modificados: (comentários adicionais adicionados para realçar alterações)

  _appsettings.json_

```json
{
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Warning"
    }
  },
// Changes to file post adding Application Insights Telemetry:
  "ApplicationInsights": {
    "InstrumentationKey": "10101010-1010-1010-1010-101010101010"
  }
}
//
```

  _ContosoDotNetCore.csproj_

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp2.0</TargetFramework>
 <!--Changes to file post adding Application Insights Telemetry:-->
    <ApplicationInsightsResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsResourceId>
    <ApplicationInsightsAnnotationResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsAnnotationResourceId>
<!---->
  </PropertyGroup>
  <ItemGroup>
 <!--Changes to file post adding Application Insights Telemetry:-->
    <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.1.1" />
<!---->
    <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.8" />
  </ItemGroup>
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
  </ItemGroup>
<!--Changes to file post adding Application Insights Telemetry:-->
  <ItemGroup>
    <WCFMetadata Include="Connected Services" />
  </ItemGroup>
<!---->
</Project>
```

   _Program.cs_

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace DotNetCore
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
// Change to file post adding Application Insights Telemetry:
                .UseApplicationInsights()
//
                .UseStartup<Startup>()
                .Build();
    }
}
```

## <a name="synthetic-transactions-with-powershell"></a>Transações sintéticas com o PowerShell

Para automatizar solicitações com relação ao seu aplicativo com transações sintéticas.

1. Executar seu aplicativo clicando em IIS Express ![Captura de tela do ícone IIS Express do Visual Studio](./media/app-insights-asp-net-core/004-iis-express.png)

2. Copie a URL da barra de endereços do navegador. Está no formato `http://localhost:{random port number}`

   ![Captura de tela da barra de endereço da URL do navegador](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Execute o seguinte loop de PowerShell para criar 100 transações sintéticas no seu aplicativo de teste. Modifique o número de porta após **localhost:** para corresponder a url que você copiou na etapa anterior.

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Abrir o Portal do Application Insights

Após executar o PowerShell a partir da seção anterior, inicie o Application Insights para exibir as transações e confirmar que os dados estão sendo coletados. 

No menu do Visual Studio, selecione **Projeto** > **Application Insights** > **abrir Portal do Application Insights**

   ![Captura de tela da Visão geral do Application Insights](./media/app-insights-asp-net-core/010-portal.png)

> [!NOTE]
> A captura de tela de exemplo acima **Live Stream**, **Tempo de carregamento da exibição de página**, e **Solicitações com falha** atualmente não são coletados. A próxima seção acompanhará a adição de cada uma delas. Se você já estiver coletando **Live Stream**, e **Tempo de carregamento da exibição de página**, siga as etapas de apenas **Solicitações com falha**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Coletar Solicitações com falha, Live Stream e o Tempo de carregamento da exibição de página

### <a name="failed-requests"></a>Solicitações com falha

Tecnicamente **Solicitações com falha** estão sendo coletadas, mas não ocorreu nenhuma ainda. Para acelerar o processo ao longo de uma exceção personalizada pode ser adicionado ao projeto existente para simular uma exceção do mundo real. Se seu aplicativo ainda está em execução no Visual Studio antes de continuar **Interrompa a depuração** (Shift + F5)

1. No **Gerenciador de Soluções** > expanda **Páginas** > **About.cshtml** > Abrir **About.cshtml.cs**.

   ![Captura de tela do Gerenciador de Soluções do Visual Studio](./media/app-insights-asp-net-core/011-about.png)

2. Adicione uma exceção em ``Message=`` e salve a alteração no arquivo.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc.RazorPages;

    namespace DotNetCore.Pages
    {
        public class AboutModel : PageModel
        {
            public string Message { get; set; }

            public void OnGet()
            {
                Message = "Your application description page.";
                throw new Exception("Test Exception");
            }
        }
    }
    ```

### <a name="live-stream"></a>Live Stream

Para acessar a funcionalidade Live Stream do Application Insights com atualização do ASP.NET Core para os pacotes NuGet do **Microsoft.ApplicationInsights.AspNetCore 2.2.0**.

No Visual Studio, selecione **Projeto** > **Gerenciar pacotes NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > Versão **2.2.0** > **Atualização**.

  ![Captura de tela do Gerenciador de Pacotes NuGet](./media/app-insights-asp-net-core/012-nuget-update.png)

Vários prompts de confirmação ocorrerão. Leia e aceite se concordar com as alterações.

### <a name="page-view-load-time"></a>Tempo de Carregamento de Exibição de Página

1. No Visual Studio, navegue até **Gerenciador de Soluções** > **Páginas** > dois arquivos precisarão ser modificados: _Layout.cshtml_ e _ViewImports.cshtml_

2. Em __ViewImports.cshtml_, adicione:

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. Em **_Layout.cshtml** adicione a linha abaixo antes da marca ``</head>``, mas também antes de quaisquer outros scripts.

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Testar Solicitações com falha, Live Stream e o Tempo de carregamento da exibição de página

Para testar e confirmar que tudo está funcionando:

1. Executar seu aplicativo clicando em IIS Express ![Captura de tela do ícone IIS Express do Visual Studio](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Navegue até a página **Sobre** para disparar a exceção de teste. (Se estiver executando no modo de depuração, será necessário clicar em **Continuar** no Visual Studio para que a exceção seja exibida no Application Insights.)

3. Executar novamente o script de transação do PowerShell de antes (talvez seja necessário ajustar o número da porta no script.)

4. Se a visão geral do Application Insights não ainda estiver aberta, no menu do Visual Studio selecione **Projeto** > **Application Insights** > **Abrir o Portal do Application Insights**. 

   > [!TIP]
   > Se você não estiver vendo seu novo tráfego ainda, verifique o **Intervalo de tempo** e clique em **Atualizar**.

   ![Captura de tela da janela de Visão geral](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Selecionar Live Stream

   ![Captura de tela de Live Metrics Stream](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Se seu script do PowerShell está em execução ainda, você deverá ver as métricas em tempo real, se tiver sido interrompido, execute o script novamente com o Live Stream aberto.)

## <a name="app-insights-sdk-comparison"></a>Comparação SDK do App Insights

O grupo de produto do Application Insights vem trabalhando duro para alcançar a paridade de recursos entre o [SDK do .NET Framework completo](https://github.com/Microsoft/ApplicationInsights-dotnet) e o SDK do .NET Core. A versão 2.2.0 do [SDK do ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore) para o Application Insights cobriu amplamente a lacuna de recursos.

Para saber mais sobre as diferenças e as compensações entre [.NET e .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

   | Comparação SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Live Metrics**      | **+** |**-** | **+** |
   | **Canal de Telemetria do Servidor** | **+** |**-** | **+**|
   |**Amostragem Adaptável**| **+** | **-** | **+**|
   | **Chamadas de Dependência SQL**     | **+** |**-** | **+**|
   | **Contadores de Desempenho*** | **+** | **-**| **-**|

_Contadores de Desempenho_ neste contexto refere-se a [contadores de desempenho do servidor](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) como processador, memória e utilização de disco.

## <a name="open-source-sdk"></a>SDK do código-fonte aberto
[Ler e contribuir para o código](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="application-insights-search-continued"></a>Continuação da pesquisa do Application Insights

Para entender melhor como a pesquisa do Application Insights funciona no Visual Studio para um projeto ASP.NET Core 2 mesmo quando uma instalação explícita dos pacotes NuGet do Application Insights ainda não aconteceu. Pode ser útil examinar a Saída de depuração.

Se você pesquisar a saída da palavra _insight_, serão realçados os resultados semelhantes aos seguintes:

```DebugOuput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

O CoreCLR está carregando dois assemblies: 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

E o _não configurado_ em cada instância da telemetria do Application Insights indica que este aplicativo não está associado a um ikey para que os dados gerados enquanto o aplicativo está sendo executado não estejam sendo enviados para o Azure e só estejam disponíveis para análise e pesquisa locais.

Parte de como isso é possível é que o pacote NuGet _Microsoft.AspNetCore.All_ aceita como uma dependência [_Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1)

![Captura de tela do grafo de dependência do NuGet para Microsoft.AspNETCore.all](./media/app-insights-asp-net-core/013-dependency.png)

Fora do Visual Studio, se você estivesse editando um projeto ASP.NET Core em VSCode ou algum outro editor, esses assemblies não seriam carregados automaticamente durante a depuração se você não tivesse adicionado explicitamente o Application Insights ao seu projeto.

No entanto, no Visual Studio, esta iluminação de recursos locais do Application Insights de assemblies externos é realizada usando a [Interface IHostingStartup](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1), que adiciona dinamicamente o Application Insights durante a depuração.

Para saber mais sobre como aprimorar um aplicativo de um [assembly externo no ASP.NET Core com IHostingStartup](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="how-to-disable-application-insights-in-visual-studio-net-core-projects"></a>Como desabilitar o Application Insights em projetos .NET Core do Visual Studio

Embora a iluminação automática da funcionalidade de pesquisa do Application Insights possa ser útil para alguns, ver a telemetria de depuração gerada quando você não a estava esperando pode ser confuso.

Se desabilitar apenas a geração de telemetria for suficiente, será possível adicionar esse bloco de código ao método de configuração do seu arquivo _Startup.cs_:

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

O CoreCLR ainda carregará _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ e _Microsoft.ApplicationInsights.AspNetCore.dll_, mas eles não farão nada.

Se deseja desabilitar completamente o Application Insights em seu projeto .NET Core do Visual Studio, o método preferencial é selecionar **Ferramentas** > **Opções** > **Projetos e Soluções** > **Projetos Web** > e marcar a caixa para desabilitar o Application Insights local para projetos Web do ASP.NET Core. Essa funcionalidade foi adicionada no Visual Studio 15.6.

![Captura de tela da tela Projetos Web da Janela de Saída do Visual Studio](./media/app-insights-asp-net-core/014-disable.png)

Se você estiver executando uma versão anterior do Visual Studio e desejar remover completamente todos os assemblies carregados por meio da IHostingStartup, será possível adicionar:

`.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")`

ao _Program.cs_:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace DotNetCore
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
                .UseStartup<Startup>()
                .Build();
    }
}
```

Ou será possível adicionar ``"ASPNETCORE_preventHostingStartup": "True"`` às variáveis de ambiente _launchSettings.json_.

O problema com usar qualquer um desses métodos é que ele não desabilitará o Application Insights, ele desabilitará qualquer coisa no Visual Studio que estava utilizando a funcionalidade de iluminação da IHostingStartup.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Próximas etapas
* [Explorar os Fluxos dos Usuários](app-insights-usage-flows.md) para entender como os usuários navegam por meio de seu aplicativo.
* [Configure a Coleção de Instantâneos](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) para ver o estado do código-fonte e as variáveis no momento em que uma exceção é lançada.
* [Use a API](app-insights-api-custom-events-metrics.md) para enviar seus próprios eventos e métricas para uma exibição mais detalhada do desempenho e do uso do aplicativo.
* [Testes de disponibilidade](app-insights-monitor-web-app-availability.md) verificam seu aplicativo constantemente em todo o mundo.
