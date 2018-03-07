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
ms.topic: article
ms.date: 02/21/2018
ms.author: mbullwin
ms.openlocfilehash: e9fb3e68db66449d9ca3b43e6974910cb9477e62
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2018
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights para ASP.NET Core

Azure Application Insights fornece monitoramento detalhado do seu aplicativo web até o nível de código. Você pode monitorar facilmente o aplicativo web quanto à sua disponibilidade, desempenho e uso. Você também pode identificar e diagnosticar erros rapidamente em seu aplicativo sem esperar que um usuário os relate.

Este artigo o orienta pelo processo de criação de um exemplo do aplicativo ASP.NET Core [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) no Visual Studio e como iniciar o monitoramento com o Azure Application Insights.

## <a name="prerequisites"></a>pré-requisitos

- SDK do NET Core 2.0.0 ou posterior.
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) versão 15.3 ou posterior com a carga de trabalho ASP.NET e desenvolvimento web.

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Criar um projeto ASP.NET Core no Visual Studio

1. Clique com botão direito e inicie **Visual Studio 2017** como administrador.
2. Selecione **Arquivo** > **Novo** > **Projeto** (Ctrl-Shift-N).

   ![Captura de tela do Menu Arquivo Novo Projeto no Visual Studio](./media/app-insights-asp-net-core/0001-file-new-project.png)

3. Expanda **Visual C#** > Selecione **.NET Core** > **Aplicativo Web do ASP.NET Core**. Insira um **Nome** > **Nome da solução** > Verificar **Criar novo repositório do Git**.

   ![Captura de tela do Assistente Arquivo Novo Projeto no Visual Studio](./media/app-insights-asp-net-core/0002-new-project-web-application.png)

4. Selecione **.Net Core** > **Core ASP.NET 2.0** **Aplicativo Web** > **OK**.

    ![Captura de tela do Menu de Seleção Arquivo Novo Projeto no Visual Studio](./media/app-insights-asp-net-core/0003-dot-net-core.png)

## <a name="add-application-insights-telemetry"></a>Adicionar Application Insights Telemetry

1. Selecione **Projeto** > **Adicionar Application Insights Telemetry...** (Alternativamente clique **Serviços Conectados** > Adicionar Serviço Conectado.)

    ![Captura de tela do Menu de Seleção Arquivo Novo Projeto no Visual Studio](./media/app-insights-asp-net-core/0004-add-application-insights-telemetry.png)

2. Selecione **Iniciar Gratuitamente**.

    ![Captura de tela do Menu de Seleção Arquivo Novo Projeto no Visual Studio](./media/app-insights-asp-net-core/0005-start-free.png)

3. Selecione uma opção apropriada **Assinatura** > **Recurso** > e se deseja ou não permitir a coleta de mais de 1 GB por mês > **Registrar**.

    ![Captura de tela do Menu de Seleção Arquivo Novo Projeto no Visual Studio](./media/app-insights-asp-net-core/0006-register.png)

## <a name="changes-made-to-your-project"></a>Alterações feitas ao seu projeto

O Application Insights possui sobrecarga muito baixa. Para examinar as modificações feitas em seu projeto, adicionando a telemetria do Application Insights:

Selecione **Exibir** > **Team Explorer** (Ctrl +\, Ctrl + M) > **Projeto** > **Alterações**

- Total de quatro alterações:

  ![Captura de tela de arquivos alterados, adicionando o Application Insights](./media/app-insights-asp-net-core/0007-changes.png)

- Um novo arquivo é criado:

   **ConnectedService.json**

  ![Captura de tela de arquivos alterados, adicionando o Application Insights](./media/app-insights-asp-net-core/0008-connectedservice-json.png)

- Três arquivos são modificados:

  **appsettings.json**

   ![Captura de tela de arquivos alterados, adicionando o Application Insights](./media/app-insights-asp-net-core/0009-appsettings-json.png)

  **ContosoDotNetCore.csproj**

   ![Captura de tela de arquivos alterados, adicionando o Application Insights](./media/app-insights-asp-net-core/0010-contoso-netcore-csproj.png)

   **Program.cs**

   ![Captura de tela de arquivos alterados, adicionando o Application Insights](./media/app-insights-asp-net-core/0011-program-cs.png)

## <a name="synthetic-transactions-with-powershell"></a>Transações sintéticas com o PowerShell

Iniciar seu aplicativo e, em seguida, clicar em links manualmente podem ser usados para gerar tráfego de teste. No entanto, geralmente é útil criar uma transação sintética simple no PowerShell.

1. Executar seu aplicativo clicando em IIS Express ![Captura de tela do ícone IIS Express do Visual Studio](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Copie a URL da barra de endereços do navegador. Ele está no formato http://localhost:{número da porta aleatória}

   ![Captura de tela da barra de endereço da URL do navegador](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Execute o seguinte loop de PowerShell para criar 100 transações sintéticas no seu aplicativo de teste. Modifique o número de porta após **localhost:** para corresponder a url que você copiou na etapa anterior.

   ```PS
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Abrir o Portal do Application Insights

Após executar o PowerShell a partir da seção anterior, inicie o Application Insights para exibir as transações e confirmar que os dados estão sendo coletados. 

No menu do Visual Studio, selecione **Projeto** > **Application Insights** > **abrir Portal do Application Insights**

   ![Captura de tela da Visão geral do Application Insights](./media/app-insights-asp-net-core/0014-portal-01.png)

> [!NOTE]
> A captura de tela de exemplo acima **Live Stream**, **Tempo de carregamento da exibição de página**, e **Solicitações com falha** atualmente não são coletados. A próxima seção acompanhará a adição de cada uma delas. Se você já estiver coletando **Live Stream**, e **Tempo de carregamento da exibição de página**, siga as etapas de apenas **Solicitações com falha**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Coletar Solicitações com falha, Live Stream e o Tempo de carregamento da exibição de página

### <a name="failed-requests"></a>Solicitações com falha

Tecnicamente **Solicitações com falha** estão sendo coletadas, mas não ocorreu nenhuma ainda. Para acelerar o processo ao longo de uma exceção personalizada pode ser adicionado ao projeto existente para simular uma exceção do mundo real. Se seu aplicativo ainda está em execução no Visual Studio antes de continuar **Interrompa a depuração** (Shift + F5)

1. No **Gerenciador de Soluções** > expanda **Páginas** > **About.cshtml** > Abrir **About.cshtml.cs**.

   ![Captura de tela do Gerenciador de Soluções do Visual Studio](./media/app-insights-asp-net-core/0015-solution-explorer-about.png)

2. Adicionar uma Exceção em ``Message=`` > salvar a alteração no arquivo.

   ```C#
   throw new Exception("Test Exception");
   ```

   ![Captura de tela do código de exceção](./media/app-insights-asp-net-core/000016-exception.png)

### <a name="live-stream"></a>Live Stream

Para acessar a funcionalidade Live Stream do Application Insights com atualização do ASP.NET Core para os pacotes NuGet do **Microsoft.ApplicationInsights.AspNetCore 2.2.0**.

No Visual Studio, selecione **Projeto** > **Gerenciar pacotes NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > Versão **2.2.0** > **Atualização**.

  ![Captura de tela do Gerenciador de Pacotes NuGet](./media/app-insights-asp-net-core/0017-update-nuget.png)

Vários prompts de confirmação ocorrerão, leia e aceite se concordar com as alterações.

### <a name="page-view-load-time"></a>Tempo de Carregamento de Exibição de Página

1. No Visual Studio, navegue até **Gerenciador de Soluções** > **Páginas** > dois arquivos precisam ser modificados: **_Layout.cshtml**, e **_ ViewImports.cshtml**

2. Em **_ViewImports.cshtml**, adicionar:

   ```C#
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```
     ![Captura de tela de alteração de código _ViewImports.cshtml](./media/app-insights-asp-net-core/00018-view-imports.png)

3. Em **Layout.cshtml** adicione a linha abaixo antes da marca ``</head>``, mas antes de quaisquer outros scripts.

    ```C#
    @Html.Raw(snippet.FullScript)
    ```
    ![Captura de tela de alteração de código layout.cshtml](./media/app-insights-asp-net-core/0018-layout-cshtml.png)

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Testar Solicitações com falha, Live Stream e o Tempo de carregamento da exibição de página

Agora que você concluiu as etapas anteriores, você pode testar e confirmar se tudo está funcionando.

1. Executar seu aplicativo clicando em IIS Express ![Captura de tela do ícone IIS Express do Visual Studio](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Navegue até a página **Sobre** para disparar a exceção de teste. (Se você estiver executando no modo de depuração, você precisará clicar em **Continuar** no Visual Studio antes da exceção ser separada por Application Insights.)

3. Executar novamente o script de transação do PowerShell simulado anterior (talvez seja necessário ajustar o número da porta no script.)

4. Se a visão geral do Application Insights não ainda estiver aberta, no menu do Visual Studio selecione **Projeto** > **Application Insights** > **Abrir o Portal do Application Insights**. 

   > [!TIP]
   > Se você não estiver vendo seu novo tráfego ainda, verifique o **Intervalo de tempo** e clique em **Atualizar**.

   ![Captura de tela da janela de Visão geral](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Selecionar Live Stream

   ![Captura de tela de Live Metrics Stream](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Se seu script do PowerShell está em execução ainda, você deverá ver as métricas em tempo real, se interrompeu, execute o script novamente com o Live Stream aberto.)

## <a name="app-insights-sdk-comparison"></a>Comparação SDK do App Insights

O grupo do produto do Application Insights vem trabalhando duro para alcançar a paridade de recursos mais próxima possível entre o [SDK do .NET Framework completo](https://github.com/Microsoft/ApplicationInsights-dotnet) e o SDK do .Net Core. A versão 2.2.0 do [SDK do ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore) para o Application Insights cobriu amplamente a lacuna de recursos.

Para saber mais sobre as diferenças e as compensações entre [.NET e .NET Core](https://docs.microsoft.com/en-us/dotnet/standard/choosing-core-framework-server).

   | Comparação SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Live Metrics**      | **+** |**-** | **+** |
   | **Canal de Telemetria do Servidor** | **+** |**-** | **+**|
   |**Amostragem Adaptável**| **+** | **-** | **+**|
   | **Chamadas de Dependência SQL**     | **+** |**-** | **+**|
   | **Contadores de Desempenho*** | **+** | **-**| **-**|

_Contadores de Desempenho_ neste contexto refere-se a [contadores de desempenho do servidor](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-performance-counters) como processador, memória e utilização de disco.

## <a name="open-source-sdk"></a>SDK do código-fonte aberto
[Ler e contribuir para o código](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Próximas etapas
* [Explorar os Fluxos dos Usuários](app-insights-usage-flows.md) para entender como os usuários navegam por meio de seu aplicativo.
* [Use a API](app-insights-api-custom-events-metrics.md) para enviar seus próprios eventos e métricas para uma exibição mais detalhada do desempenho e do uso do aplicativo.
* [Testes de disponibilidade](app-insights-monitor-web-app-availability.md) verificam seu aplicativo constantemente em todo o mundo.