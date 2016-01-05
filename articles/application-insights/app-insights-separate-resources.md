<properties 
	pageTitle="Separar os recursos do Application Insights para desenvolvimento, teste e produção" 
	description="Monitorar o desempenho e o uso de seu aplicativo em diferentes estágios de desenvolvimento" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/25/2015" 
	ms.author="awills"/>

# Separar os recursos do Application Insights para desenvolvimento, teste e produção


Para evitar confundir a telemetria das versões de depuração, teste e produção do seu aplicativo, crie recursos do [Application Insights][start] separados para receber os dados de cada versão.

Os dados recebidos do aplicativo são armazenados e processados pelo Application Insights em um *recurso* do Microsoft Azure. Cada recurso é identificado por uma *chave de instrumentação*. Em seu aplicativo, a chave é fornecida ao SDK do Application Insights para que ele possa enviar os dados coletados ao recurso certo. A chave pode ser fornecida no código ou no ApplicationInsights.config. Ao alterar a chave no SDK, você pode direcionar os dados para diferentes recursos.


## Criar um recurso do Application Insights
  

No [portal.azure.com](https://portal.azure.com), adicione um recurso do Application Insights:

![Clique em Novo, Application Insights](./media/app-insights-separate-resources/01-new.png)


* O **tipo de aplicativo** afeta o que você vê na folha de visão geral e as propriedades disponíveis no [explorador de métricas][metrics]. Se você não vir o tipo de aplicativo, escolha um dos tipos da Web para páginas da Web e um dos tipos de telefone para outros dispositivos.
* O **grupo de recursos** é uma conveniência para o gerenciamento de propriedades, como [controle de acesso](app-insights-resources-roles-access-control.md). Você pode usar grupos de recursos separados para desenvolvimento, teste e produção.
* **Assinatura** é a sua conta de pagamento no Azure.
* **Local** é onde podemos manter seus dados. Atualmente ele não pode ser alterado.
* **Adicionar ao quadro inicial** coloca um bloco de acesso rápido para o recurso em sua página inicial do Azure. 

A criação do recurso leva alguns segundos. Quando estiver pronto, você verá um alerta.

(Você pode escrever um [script do PowerShell](app-insights-powershell-script-create-resource.md) para criar um recurso automaticamente.)


## Copiar a chave de instrumentação

A chave de instrumentação identifica o recurso que você criou.

![Clique em Essentials, clique na Chave de Instrumentação, CTRL+C](./media/app-insights-separate-resources/02-props.png)

Você precisará das chaves de instrumentação de todos os recursos aos quais seu aplicativo enviará dados.


## <a name="dynamic-ikey"></a> Chave de instrumentação dinâmica

Geralmente, o SDK obtém a iKey de ApplicationInsights.config. Em vez disso, facilite a mudança configurando-a no seu código.

Defina a chave em um método de inicialização como global.aspx.cs em um serviço ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

Nesse exemplo, as ikeys para os diferentes recursos são colocadas em diferentes versões do arquivo de configuração da Web. Trocar o arquivo de configuração da Web também trocará o recurso de destino.

#### Páginas da Web

A iKey também é usada nas páginas da Web do aplicativo, no [script que você obteve da folha de início rápido](app-insights-javascript.md). Em vez de codificá-la literalmente no script, gere-a a partir do estado do servidor. Por exemplo, em um aplicativo ASP.NET:

*JavaScript no Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...





<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_1203_2015-->