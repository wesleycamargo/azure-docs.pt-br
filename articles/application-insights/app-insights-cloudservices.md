<properties
   pageTitle="Application Insights para Serviços de Nuvem do Azure"
   description="Monitorar suas funções da Web e de trabalho com eficiência com o Application Insights"
   services="application-insights"
   documentationCenter=""
   authors="soubhagyadash"
   manager="douge"
   editor="alancameronwills"/>

<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.tgt_pltfrm="ibiza"
   ms.topic="article"
   ms.workload="tbd"
   ms.date="11/15/2015"
   ms.author="sdash"/>

# Application Insights para Serviços de Nuvem do Azure


*O Application Insights está em modo de visualização*

Os [aplicativos de serviço de Nuvem do Microsoft Azure](https://azure.microsoft.com/services/cloud-services/) podem ser monitorados pelo [Application Insights do Visual Studio][start] quanto a disponibilidade, desempenho, falhas e uso. Com os comentários que você obtiver sobre o desempenho e a eficiência de seu aplicativo em uso, você pode fazer escolhas informadas sobre a direção do projeto em cada ciclo de vida de desenvolvimento.

![Exemplo](./media/app-insights-cloudservices/sample.png)

Precisa de uma assinatura do [Microsoft Azure](http://azure.com). Entre com uma conta da Microsoft, que você pode ter para o Windows, XBox Live ou outros serviços de nuvem da Microsoft.


#### Aplicativo de exemplo instrumentado com o Application Insights

Vejamos esse [aplicativo de exemplo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) no qual o Application Insights é adicionado a um serviço de nuvem com duas funções de trabalho hospedadas no Azure.

O que se segue informa como adaptar seu próprio projeto de serviço de nuvem da mesma maneira.

## Criar um recurso do Application Insights para cada função

Um recurso do Application Insights é onde os dados de telemetria serão analisados e exibidos.

1.  No [Portal do Azure][portal], crie um novo recurso do Application Insights. Para o tipo de aplicativo, escolha o aplicativo ASP.NET. 

    ![Clique em Novo, Application Insights](./media/app-insights-cloudservices/01-new.png)

2.  Faça uma cópia da chave de instrumentação. Você precisará disso em breve para configurar o SDK.

    ![Clique em Propriedades, selecione a chave e pressione ctrl + C](./media/app-insights-cloudservices/02-props.png)


Geralmente, é melhor criar um recurso separado para os dados de cada função da Web e de trabalho.

Como alternativa, você pode enviar dados de todas as funções a apenas um recurso, mas definir uma [propriedade padrão][apidefaults] para que você possa filtrar ou agrupar os resultados de cada função.

## <a name="sdk"></a>Instalar o SDK em cada projeto


1. No Visual Studio, edite os pacotes do NuGet do seu projeto de aplicativo da nuvem.

    ![Clique com o botão direito no projeto e selecione Gerenciar Pacotes Nuget](./media/app-insights-cloudservices/03-nuget.png)


2. Adicione o pacote do NuGet [Application Insights para Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web). Esta versão do SDK inclui módulos que adicionam o contexto do servidor, como informações de função. Para funções de trabalho, use o Application Insights para Windows Services.

    ![Pesquise "Application Insights"](./media/app-insights-cloudservices/04-ai-nuget.png)


3. Configure o SDK para enviar dados ao recurso do Application Insights.

    Defina a chave de instrumentação como um parâmetro de configuração no arquivo `ServiceConfiguration.Cloud.cscfg`. ([Exemplo de código](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg)).
 
    ```XML
     <Role name="WorkerRoleA"> 
      <Setting name="APPINSIGHTS_INSTRUMENTATIONKEY" value="YOUR IKEY" /> 
     </Role>
    ```
 
    Em uma função de inicialização adequada, defina a chave de instrumentação no parâmetro de configuração:

    ```C#
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```

    Observação, o mesmo nome `APPINSIGHTS_INSTRUMENTATIONKEY` da definição de configuração será usado pelo relatório de Diagnóstico do Azure.


    Faça isso para cada função em seu aplicativo. Veja os exemplos:
 
 * [Função Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
 * [Função de trabalho](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
 * [Para páginas da Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13)   

4. Defina o arquivo ApplicationInsights.config para sempre ser copiado no diretório de saída. 

    (No arquivo. config, você verá mensagens solicitando que você coloque a chave de instrumentação lá. No entanto, para aplicativos em nuvem é melhor defini-la por meio do arquivo .cscfg. Isso garante que a função seja identificada corretamente no portal).


#### Executar e publicar o aplicativo

Execute o aplicativo e entre no Azure. Abra os recursos do Application Insights que você criou, e veja que os pontos de dados individuais aparecerem em [Pesquisa](app-insights-diagnostic-search.md), e os dados agregados em [Metrics Explorer](app-insights-metrics-explorer.md).

Adicione mais telemetria: confira as seções a seguir e publique seu aplicativo para obter comentários em tempo real sobre o diagnóstico e de uso.


#### Não há dados?

* Abra o bloco [Pesquisar][diagnostic] para ver eventos individuais.
* Use o aplicativo abrindo páginas diferentes, para que ele gere alguma telemetria.
* Aguarde alguns segundos e clique em Atualizar.
* Consulte [Solucionar problemas][qna].



## Mais telemetria

As seções a seguir mostram como obter a telemetria adicional de diferentes aspectos de seu aplicativo.


## Acompanhar as solicitações das funções de trabalho

Nas funções web, o módulo de solicitações automaticamente coleta dados sobre solicitações HTTP. Consulte o [MVCWebRole de exemplo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole) para obter exemplos de como você pode substituir o comportamento de coleção padrão.

Você pode capturar o desempenho das chamadas a funções de trabalho acompanhando-as da mesma maneira como faz com solicitações HTTP. No Application Insights, o tipo de Solicitação de telemetria mede uma unidade de trabalho do servidor nomeada que pode ser cronometrada e pode ter êxito ou falhar independentemente. Embora as solicitações HTTP sejam capturadas automaticamente pelo SDK, você pode inserir seu próprio código para acompanhar as solicitações para funções de trabalho.

Consulte as duas funções de trabalho de exemplo instrumentadas para solicitações de relatório: [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA) e [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## Diagnóstico do Azure

[Os dados do Diagnóstico do Azure](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) incluem logs de aplicativo, contadores de desempenho e eventos de gerenciamento de função. Você pode enviá-los ao Application Insights para que possa vê-los junto com o restante da sua telemetria, facilitando o diagnóstico dos problemas.

Os diagnósticos do Azure são especialmente úteis se uma função falhar inesperadamente ou não puder ser iniciada.

1. Clique com o botão direito na função (não no projeto!) para abrir suas Propriedades, e selecione **Habilitar o Diagnóstico**, **Enviar diagnósticos ao Application Insights**.

    ![Pesquise "Application Insights"](./media/app-insights-cloudservices/21-wad.png)

    **Ou, se o seu aplicativo já estiver publicado e em execução**, abra o Gerenciador de Servidores ou o Gerenciador de Nuvem, clique com o botão direito em seu aplicativo e selecione a mesma opção.

3.  Selecione o mesmo recurso do Application Insights de sua outra telemetria.

    Se quiser, você pode definir um recurso diferente em configurações diferentes do serviço (Nuvem, Local) para ajudar a manter os dados de desenvolvimento separados dos dados em tempo real.

3. Como opção, [exclua alguns diagnósticos do Azure](app-insights-azure-diagnostics.md) que você deseja encaminhar ao Application Insights. O padrão é tudo.

### Exibir eventos de diagnóstico do Azure

Onde encontrar o diagnóstico:

* Os contadores de desempenho são exibidos como métricas personalizadas. 
* Os logs de eventos do Windows são mostrados como eventos de rastreamentos e personalizados.
* Logs de aplicativo, Logs de ETW e logs de infraestrutura de diagnóstico aparecem como rastreamentos.

Para ver os contadores de eventos e os contadores de desempenho, abra o [Metrics Explorer](app-insights-metrics-explorer.md) e adicione um novo gráfico:


![](./media/app-insights-cloudservices/23-wad.png)

Use [Pesquisa](app-insights-diagnostic-search.md) para pesquisar os vários logs de rastreamento enviados pelo Diagnóstico do Azure. Por exemplo, se você tivesse uma exceção não tratada em uma função que fizesse essa função parar de funcionar e reciclar, essas informações seria mostradas no canal Aplicativo do Log de eventos do Windows. Você pode usar a funcionalidade Pesquisar para ver o erro do log de eventos do Windows e obter o rastreamento de pilha completo da exceção, permitindo que você encontre a causa principal do problema.


![](./media/app-insights-cloudservices/25-wad.png)

## Diagnósticos do aplicativo

O diagnóstico do Azure inclui automaticamente as entradas de log geradas por seu aplicativo usando System.Diagnostics.Trace.

Porém, se você já usa as estruturas Log4N ou NLog, você também poderá [capturar seus rastreamentos de log][netlogs].

[Acompanhe métricas e eventos personalizados][api] no cliente, no servidor ou em ambos, para saber mais sobre o desempenho e uso de seu aplicativo.

## Dependências

O SDK do Application Insights pode relatar chamadas de seu aplicativo para as dependências externas, como APIs REST e servidores SQL. Isso permite que você veja se uma determinada dependência está causando falhas ou respostas lentas.

Para rastrear dependências, você precisa configurar a função web/de trabalho com o [Application Insights Agent](app-insights-monitor-performance-live-website-now.md), também conhecido como "Monitor de status".

Para usar o Application Insights Agent com suas funções web/de trabalho:

* Adicione a pasta do [AppInsightsAgent](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent) e os dois arquivos dela em seus projetos de função web/de trabalho. Certifique-se de definir suas propriedades de compilação para que elas sempre sejam copiadas no diretório de saída. Esses arquivos instalam o agente.
* Adicione as tarefas de inicialização ao arquivo CSDEF conforme mostrado [aqui](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/AzureEmailService/ServiceDefinition.csdef#L18).
* OBSERVAÇÃO: as *funções de trabalho* requerem três variáveis de ambiente, conforme mostrado [aqui](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/AzureEmailService/ServiceDefinition.csdef#L44). Isso não é necessário para funções web.

Aqui está um exemplo do que você vê no portal do Application Insights:

* Diagnósticos avançados com solicitações automaticamente correlacionadas e dependências:

    ![](./media/app-insights-cloudservices/SMxacy4.png)

* Desempenho da função web, com as informações de dependência:

    ![](./media/app-insights-cloudservices/6yOBtKu.png)

* Aqui está uma captura de tela das solicitações e as informações de dependência para uma função de trabalho:

    ![](./media/app-insights-cloudservices/a5R0PBk.png)

## Exceções

Consulte [Monitoramento de exceções no Application Insights](app-insights-asp-net-exceptions.md) para obter informações sobre como você pode coletar exceções sem tratamento de diferentes tipos de aplicativos Web.

A função web de exemplo tem controladores MVC5 e API Web 2. As exceções sem tratamento das 2 são capturadas com o seguinte:

* Configuração do [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) [aqui](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) para controladores MVC5
* Configuração do [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) [aqui](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) para controladores da API Web 2

Para funções de trabalho: há duas maneiras de rastrear exceções.

* TrackException(ex)
* Se você tiver adicionado o pacote de NuGet do ouvinte de rastreamento Application Insights, você pode usar System.Diagnostics.Trace para registrar exceções. [Exemplo de código.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

## Contadores de desempenho

Os seguintes contadores são coletados por padrão:

    * \Process(??APP_WIN32_PROC??)\% Processor Time
	* \Memory\Available Bytes
	* \.NET CLR Exceptions(??APP_CLR_PROC??)# of Exceps Thrown / sec
	* \Process(??APP_WIN32_PROC??)\Private Bytes
	* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
	* \Processor(_Total)\% Processor Time

Além disso, o seguinte também é coletado para funções web:

	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec	
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

Você pode especificar contadores de desempenho personalizados adicionais, ou outros, do Windows, conforme mostrado [aqui](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14)

  ![](./media/app-insights-cloudservices/OLfMo2f.png)

## Telemetria correlacionada para funções de trabalho

É uma experiência de diagnóstico avançada, quando você pode ver o que levou a uma solicitação com falha ou alta latência. Com as funções da web, o SDK automaticamente configura a correlação entre a telemetria relacionada. Para funções de trabalho, você pode usar um inicializador de telemetria personalizado para definir um atributo de contexto Operation.Id comum para todas as telemetrias para obter isso. Isso permitirá que você veja se o problema de latência/falha foi causado devido a alguma dependência ou ao seu código, rapidamente!

Faça assim:

* Defina a ID de correlação em uma CallContext conforme mostrado [aqui](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). Nesse caso, estamos usando a ID da solicitação como a id de correlação
* Adicione uma implementação personalizada de TelemetryInitializer, que definirá o Operation.Id ao conjunto correlationId acima. Mostrado aqui: [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)
* Adicione o inicializador de telemetria personalizado. Você pode fazer isso no arquivo ApplicationInsights.config ou no código conforme mostrado [aqui](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233)

É isso! A experiência do portal já está conectada para ajudá-lo a ver todas as telemetrias associadas rapidamente:

![](./media/app-insights-cloudservices/bHxuUhd.png)



## Telemetria do cliente

[Adicione o SDK do JavaScript a suas páginas da Web][client] para obter a telemetria baseada em navegador, como contagens de exibição de página, tempos de carregamento de página, exceções de script e para permitir que você escreva telemetria personalizada em seus scripts de página.

## Testes de disponibilidade

[Configure os testes da Web][availability] para certificar-se de manter seu aplicativo operante e responsivo.



## Exemplo

[O exemplo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) monitora um serviço que tem uma função web e duas funções de trabalho.

## Tópicos relacionados

* [Configurar o envio dos Diagnósticos do Azure ao Application Insights](app-insights-azure-diagnostics.md)
* [Usando o PowerShell para enviar diagnósticos do Azure ao Application Insights](app-insights-powershell-azure-diagnostics.md)



[api]: app-insights-api-custom-events-metrics.md
[apidefaults]: app-insights-api-custom-events-metrics.md#default-properties
[apidynamicikey]: app-insights-api-custom-events-metrics.md#dynamic-ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

<!---HONumber=AcomDC_0218_2016-->