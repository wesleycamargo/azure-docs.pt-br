---
title: 'collectd: estatísticas de desempenho para Java em Unix no Application Insights'
description: Monitoramento estendido do seu site Java com o plug-in CollectD para Application Insights
services: application-insights
documentationcenter: java
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: awills

---
# collectd: métricas de desempenho do Unix no Application Insights
*O Application Insights está em modo de visualização.*

Para explorar as métricas de desempenho do sistema Unix no [Application Insights](app-insights-overview.md), instale [collectd](http://collectd.org/), juntamente com seu plug-in do Application Insights. Essa solução de software livre reúne várias estatísticas de sistema e de rede.

Normalmente, você usará o collectd se já tiver [instrumentado seu serviço web Java com o Application Insights][java]. Isso oferece a você mais dados para ajudá-lo a aprimorar o desempenho do aplicativo ou para diagnosticar problemas.

![Exemplo de gráficos](./media/app-insights-java-collectd/sample.png)

## Obter a chave de instrumentação
No [portal do Microsoft Azure](https://portal.azure.com), abra o recurso [Application Insights](app-insights-overview.md) onde você deseja que os dados sejam exibidos. (Ou [crie um novo recurso](app-insights-create-new-resource.md).)

Faça uma cópia da chave de instrumentação que identifica o recurso.

![Procure tudo, abra seu recurso e, no menu suspenso Essentials, selecione e copie a Chave de Instrumentação](./media/app-insights-java-collectd/02-props.png)

## Instalar o plug-in e collectd
Em seus computadores com o servidor Unix:

1. Instale [collectd](http://collectd.org/) versão 5.4.0 ou posterior.
2. Baixe o [plug-in do gravador collectd do Application Insights](https://aka.ms/aijavasdk). Observe o número de versão.
3. Copie o plug-in JAR em `/usr/share/collectd/java`.
4. Edite `/etc/collectd/collectd.conf`:
   * Verifique se [o plug-in do Java](https://collectd.org/wiki/index.php/Plugin:Java) está habilitado.
   * Atualize o JVMArg para java.class.path de modo a incluir o JAR a seguir. Atualize o número de versão para corresponder àquela que você baixou:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Adicione esse trecho usando a chave de instrumentação do seu recurso:

```

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Veja o exemplo de parte de um arquivo de configuração:

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
. ...

Configure outros [plug-ins collectd](https://collectd.org/wiki/index.php/Table_of_Plugins), que possam coletar vários dados de diferentes fontes.

Reinicie o collectd, de acordo com seu [manual](https://collectd.org/wiki/index.php/First_steps).

## Exibir os dados no Application Insights
No recurso Application Insights, abra o [Metrics Explorer e adicione gráficos][metrics], selecionando as métricas que deseja ver na categoria Personalizado.

![](./media/app-insights-java-collectd/result.png)

Por padrão, as métricas são agregadas em todos os computadores host dos quais as métricas foram coletadas. Para exibir as métricas por host, na folha Detalhes do gráfico, ative Agrupamento e escolha Agrupar por CollectD-Host.

## Excluir o carregamento de estatísticas específicas
Por padrão, o plug-in do Application Insights envia todos os dados coletados por todos os plug-ins collect ‘read’ habilitados.

Para excluir dados de plug-ins ou fontes de dados específicos:

* Edite o arquivo de configuração.
* Em `<Plugin ApplicationInsightsWriter>`, adicione linhas diretivas como esta:

| Diretiva | Efeito |
| --- | --- |
| `Exclude disk` |Excluir todos os dados coletados pelo plug-in `disk` |
| `Exclude disk:read,write` |Exclua as fontes denominadas `read` e `write` do plug-in `disk`. |

Diretivas separadas por uma nova linha.

## Problemas?
*Não vejo dados no portal*

* Abra [Pesquisar][diagnostic] para ver se os eventos brutos aparecem. Às vezes, eles levam mais tempo para aparecer no Metrics Explorer.
* Talvez você precise [definir exceções de firewall para dados de saída](app-insights-ip-addresses.md)
* Habilite o rastreamento no plug-in do Application Insights. Adicione esta linha em `<Plugin ApplicationInsightsWriter>`:
  * `SDKLogger true`
* Abra um terminal e inicie collectd no modo detalhado para ver todos os problemas que ele está reportando:
  * `sudo collectd -f`

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md



<!---HONumber=AcomDC_0824_2016-->