<properties 
	pageTitle="Application Insights para aplicativos C++" 
	description="Analise a utilização e o desempenho de seu aplicativo C++ com o Application Insights."
	services="application-insights" 
	documentationCenter="cpp" 
	authors="crystk" 
	manager="jakubo"/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="universal" 
    ms.devlang="na" 
    ms.topic="article" 
	ms.date="06/03/2015" 
    ms.author="crystk"/>

# Application Insights para aplicativos C++

O Application Insights do Visual Studio permite que você monitore seu aplicativo móvel quanto ao uso, eventos e falhas.

## Requisitos

Você precisará de:

* Uma assinatura do [Microsoft Azure](http://azure.com). Entre com uma conta da Microsoft, que você pode ter para o Windows, XBox Live ou outros serviços de nuvem da Microsoft.
* Visual Studio 2015 ou posterior.

## Criar um recurso do Application Insights

No [Portal do Azure][portal], crie um novo recurso do Application Insights. Selecione a opção Windows Phone ou Windows Store.

![Clique em Novo, Serviços de Desenvolvedor, Application Insights](./media/app-insights-windows-cpp/01-universal.png)

A folha que será aberta é o local em que você verá os dados de uso e desempenho sobre seu aplicativo. Para retornar a ela na próxima vez em que fizer logon no Azure, você deverá encontrar um bloco para ela na tela inicial. Como alternativa, clique em Procurar para localizá-la.

####  Faça uma cópia da chave de instrumentação.

A chave identifica o recurso, e você a instalará em breve no SDK para direcionar os dados ao recurso.

![Clique em Propriedades, selecione a chave e pressione ctrl + C](./media/app-insights-windows-cpp/02-props-asp.png)

## <a name="sdk"></a> Instale o SDK em seu aplicativo


1. No Visual Studio, edite os pacotes do NuGet do seu projeto de aplicativo de área de trabalho.

    ![Clique com o botão direito no projeto e selecione Gerenciar Pacotes Nuget](./media/app-insights-windows-cpp/03-nuget.png)

2. Instale o SDK do Application Insights para Aplicativos C++.

    ![Selecione **Online**, **Incluir pré-lançamento**, e procure "Application Insights"](./media/app-insights-windows-cpp/04-ai-nuget.png)

3. Nas configurações do projeto para lançamento e depuração:
  - Adicione $(SolutionDir)packages\ApplicationInsights-CPP.1.0.0-Beta\src\inc às propriedades do projeto -> Diretórios VC++ -> Incluir Diretórios
  - Adicione $(SolutionDir)packages\ApplicationInsights.1.0.0-Beta\lib\native<TIPO DE PLATAFORMA>\release\AppInsights_Win10-UAP às propriedades do projeto -> Diretórios VC + + -> Diretórios de Biblioteca

4. Adicione ApplicationInsights.winmd como uma referência ao projeto por meio de $(SolutionDir)packages\ApplicationInsights.1.0.0-Beta\lib\native<TIPO DE PLATAFORMA> \release\ApplicationInsights
5. Adicione o AppInsights_Win10-UAP.dll de $(SolutionDir)packages\ApplicationInsights.1.0.0-Beta\lib\native<TIPO DE PLATAFORMA>\release\AppInsights_Win10-UAP. Vá até as propriedades e defina o conteúdo como SIM. Isso copiará a dll para o diretório de compilação.

## Usar o SDK

Inicialize o SDK e inicie o rastreamento de telemetria.

1. Em App.xaml.h: 
  - Adicione: `ApplicationInsights::CX::SessionTracking^ m_session;`
2. Em App.xaml.cpp:
  - Adicione: `using namespace ApplicationInsights::CX;`

  - Em App:App()
	
     `// this will do automatic session tracking and automatic page view collection` `m_session = ref new ApplicationInsights::CX::SessionTracking();`

  - Depois de criar o quadro raiz (geralmente ao final de App::OnLaunched), inicialize m_session:
	
    ```
    String^ iKey = L"<YOUR INSTRUMENTATION KEY>";
    m_session->Initialize(this, rootFrame, iKey);
	```

3. Para usar o rastreamento em outro lugar no aplicativo, você pode declarar uma instância do cliente de Telemetria.


```

    using namespace ApplicationInsights::CX;
    TelemetryClient^ tc = ref new TelemetryClient(L"<YOUR INSTRUMENTATION KEY>");
	tc->TrackTrace(L"This is my first trace");
    tc->TrackEvent(L"I'M ON PAGE 1");
    tc->TrackMetric(L"Test Metric", 5.03);
```


## <a name="run"></a> Execute seu projeto

Execute o aplicativo para gerar telemetria. Você pode executá-lo em modo de depuração em seu computador de desenvolvimento, ou publicá-lo e permitir que os usuários o executem.

## Exibir seus dados no Application Insights

Retorne ao http://portal.azure.com e navegue até o recurso do Application Insights.

Clique em Pesquisar para abrir a [Pesquisa de Diagnóstico][diagnostic], que é onde os primeiros eventos serão exibidos. Se você não vir nada, espere um ou dois minutos e clique em Atualizar.

![Clique em Pesquisa de Diagnóstico](./media/app-insights-windows-cpp/21-search.png)

Na medida em que seu aplicativo for usado, os dados aparecerão na folha de visão geral.

![Folha de visão geral](./media/app-insights-windows-cpp/22-oview.png)

Clique em qualquer gráfico para obter mais detalhes. Por exemplo, falhas:

![Clique no gráfico de falhas](./media/app-insights-windows-cpp/23-crashes.png)


## <a name="usage"></a>Próximas etapas

[Acompanhar o uso do seu aplicativo][track]

[Usar a API para enviar métricas e eventos personalizados][api]

[Pesquisa de diagnóstico][diagnostic]

[Gerenciador de Métricas][metrics]

[Solucionar problemas][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-custom-events-metrics-api.md

 

<!---HONumber=62-->