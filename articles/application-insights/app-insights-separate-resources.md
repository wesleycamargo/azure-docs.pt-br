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
	ms.date="05/04/2016" 
	ms.author="awills"/>

# Separar recursos do Application Insights

A telemetria de diferentes componentes e versões do seu aplicativo deve ser enviada para recursos diferentes do Application Insights ou combinada? Este artigo aborda as práticas recomendadas e técnicas necessárias.

Primeiro, vamos entender a pergunta. Os dados recebidos do aplicativo são armazenados e processados pelo Application Insights em um *recurso* do Microsoft Azure. Cada recurso é identificado por uma *chave de instrumentação* (iKey). Em seu aplicativo, a chave é fornecida ao SDK do Application Insights para que ele possa enviar os dados coletados ao recurso certo. A chave pode ser fornecida no código ou no ApplicationInsights.config. Ao alterar a chave no SDK, você pode direcionar os dados para diferentes recursos.

Em um caso simples, quando você cria o código para um novo aplicativo, também criar um novo recurso no Application Insights. No Visual Studio, o diálogo *novo projeto* faz isso para você.

Se for um site de alto volume, ele poderá ser implantado em mais de uma instância de servidor.

Em cenários mais complexos, você tem um sistema composto por vários componentes, como por exemplo, um site da Web e um processador de back-end.

## Quando usar iKeys separadas

Estas são algumas diretrizes gerais:

* Onde você tem uma unidade de aplicativo implantável independentemente que é executada em um conjunto de instâncias de servidor que podem escaladas ou reduzidas verticalmente de forma independente de outros componentes, geralmente ela seria mapeada para um único recurso, ou seja, terá uma única chave de instrumentação (iKey).
* Por outro lado, as razões para usar iKeys separadas incluem:
 - Facilidade de ler métricas separados de componentes separados.
 - Manter telemetria de baixo volume separado daquela de alto volume, para que a limitação, cotas e amostragem em um fluxo não afete os outros.
 - Configurações de alertas, exportação e itens de trabalho separados.
 - Distribuir [limites](app-insights-pricing.md#limits-summary) como cota de telemetria, limitação e contagem de teste da Web.
 - Código em desenvolvimento e teste deve enviar para uma iKey separada do carimbo de produção.

Muitas experiências do portal do Application Insights são criadas com essas diretrizes em mente. Por exemplo, os servidores exibem segmentos na instância do servidor, supondo que a telemetria sobre um componente lógico possa vir de várias instâncias do servidor.

## iKey único

Onde você envia telemetria de vários componentes para um único iKey:

* Adicione uma propriedade para toda telemetria que permite segmentar e filtrar a identidade do componente. A ID da função é automaticamente adicionada à telemetria das instâncias de função de servidor, mas em outros casos você pode usar um [inicializador de telemetria](app-insights-api-filtering-sampling.md#add-properties) para adicionar a propriedade.
* Atualize os SDKs do Application Insights nos diferentes componentes ao mesmo tempo. A telemetria para uma iKey deve originar-se com a mesma versão do SDK.

## iKeys separado

Onde você tem várias iKeys para diferentes componentes do aplicativo:

* Crie um [painel](app-insights-dashboards.md) para uma exibição da telemetria-chave do seu aplicativo lógico, combinada de diferentes componentes de aplicativo. Painéis podem ser compartilhados, por isso um único modo de exibição do sistema lógico pode ser usado por equipes diferentes.
* Organizar [grupos de recursos](app-insights-resources-roles-access-control.md) no nível de equipe. Permissões de acesso são atribuídas por grupo de recursos, incluindo permissões para configurar alertas.
* Use os [Modelos do Azure Resource Manager e do Powershell](app-insights-powershell.md) para ajudar a gerenciar artefatos como regras de alerta e testes da Web.



## iKeys separados para Desenvolvimento/Teste e Produção

Para facilitar a alteração automática da chave quando o aplicativo é lançado, defina a iKey no código em vez de em ApplicationInsights.config.

### <a name="dynamic-ikey"></a> Chave de instrumentação dinâmica

Defina a chave em um método de inicialização como global.aspx.cs em um serviço ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

Nesse exemplo, as ikeys para os diferentes recursos são colocadas em diferentes versões do arquivo de configuração da Web. Trocar o arquivo de configuração da Web, que pode ser realizado como parte do script versão, alternará o recurso de destino.

### Páginas da Web

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


## Criar um recurso adicional do Application Insights
  
Se você decidir separar a telemetria de diferentes componentes do aplicativo ou de diferentes carimbos (desenvolvimento/teste/produção) do mesmo componente, será necessário criar um novo recurso do Application Insights.

No [portal.azure.com](https://portal.azure.com), adicione um recurso do Application Insights:

![Clique em Novo, Application Insights](./media/app-insights-separate-resources/01-new.png)


* O **tipo de aplicativo** afeta o que você vê na folha de visão geral e as propriedades disponíveis no [explorador de métricas](app-insights-metrics-explorer.md). Se você não vir o tipo de aplicativo, escolha um dos tipos da Web para páginas da Web.
* O **grupo de recursos** é uma conveniência para o gerenciamento de propriedades, como [controle de acesso](app-insights-resources-roles-access-control.md). Você pode usar grupos de recursos separados para desenvolvimento, teste e produção.
* **Assinatura** é a sua conta de pagamento no Azure.
* **Local** é onde podemos manter seus dados. Atualmente ele não pode ser alterado.
* **Adicionar ao painel** coloca um bloco de acesso rápido para o recurso em sua Página Inicial do Azure.

A criação do recurso leva alguns segundos. Quando estiver pronto, você verá um alerta.

(Você pode escrever um [script do PowerShell](app-insights-powershell-script-create-resource.md) para criar um recurso automaticamente.)


## Obter a chave de instrumentação

A chave de instrumentação identifica o recurso que você criou.

![Clique em Essentials, clique na Chave de Instrumentação, CTRL+C](./media/app-insights-separate-resources/02-props.png)

Você precisará das chaves de instrumentação de todos os recursos aos quais seu aplicativo enviará dados.



 

<!---HONumber=AcomDC_0817_2016-->