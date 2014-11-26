<properties title="Search diagnostic logs with Application Insights" pageTitle="Search diagnostic logs" description="Search logs generated with Trace, NLog, or Log4Net." metaKeywords="analytics web test" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# Pesquisar logs de diagnóstico com o Application Insights

Você pode coletar e pesquisar dados de diagnóstico do System.Diagnostics.Trace, NLog e Log4Net. O Application Insights oferece uma ferramenta eficaz e de fácil utilização para coletar e investigar eventos registrados de uma ou mais fontes, complementando os recursos de monitoramento de integridade do aplicativo.

O aplicativo Web monitorado pode ser hospedado localmente ou em uma máquina virtual, ou pode ser um site do Microsoft Azure.

1.  [Adicionar um adaptador de log][Adicionar um adaptador de log]
-   [Configurar a coleta de diagnóstico][Configurar a coleta de diagnóstico]
-   [Inserir instruções de log, compilar e implantar][Inserir instruções de log, compilar e implantar]
-   [Ver dados de log][Ver dados de log]
-   [Pesquisar os dados][Pesquisar os dados]
-   [Próximas etapas][Próximas etapas]

## <a name="add"></a>1. Adicionar um adaptador de log

1.  Se ainda não tiver feito isso, [adicione o Application Insights ao seu projeto de serviço Web][adicione o Application Insights ao seu projeto de serviço Web] no Visual Studio.

    Se adicionar o Application Insights após ter adicionado o registro em log ao seu projeto, você verá que o adaptador de log já foi configurado - basta [reimplantar seu projeto][Inserir instruções de log, compilar e implantar] e [ver os dados][Ver dados de log].

2.  No Gerenciador de Soluções, no menu de contexto do seu projeto, selecione **Gerenciar Pacotes NuGet**.
3.  Selecione Online \> Todos, selecione **Incluir pré-lançamento** e pesquise "Microsoft.ApplicationInsights"

    ![Obtenha a versão de pré-lançamento do adaptador correto][Obtenha a versão de pré-lançamento do adaptador correto]

4.  Obtenha a versão de pré-lançamento do pacote correto - um entre:

	-   Microsoft.ApplicationInsights.TraceListener
	-   Microsoft.ApplicationInsights.NLogTarget
	-   Microsoft.ApplicationInsights.Log4NetAppender

## <a name="configure"></a>2. Configurar a coleta de diagnóstico

### Para System.Diagnostics.Trace

Em Web.config, insira o código a seguir na seção `<configuration>`:

    <system.diagnostics>
     <trace autoflush="true" indentsize="0">
      <listeners>
       <add name="myAppInsightsListener"  
          type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, 
         Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
     </trace>
    </system.diagnostics> 

### Para NLog

Em Nlog.config, mescle os seguintes trechos nas seções `<extensions>`, `<targets>` e `<rules>`. Crie essas seções se for necessário.

    <extensions> 
     <add  assembly="Microsoft.ApplicationInsights.NLogTarget" /> 
    </extensions> 

    <targets>
     <target xsi:type="ApplicationInsightsTarget" name="aiTarget"/>
    </targets>

    <rules>
     <logger name="*" minlevel="Trace" writeTo="aiTarget"/>
    </rules>

### Para Log4Net

Em Web.config, mescle estes trechos nas seções `<configsections>` e `<log4net>`:

    <configSections>
      <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net" />
    </configSections>

    <log4net>
     <root>
      <level value="ALL" /> <appender-ref ref="aiAppender" />
     </root>
     <appender name="aiAppender" type="Microsoft.ApplicationInsights.Log4NetAppender.ApplicationInsightsAppender, Microsoft.ApplicationInsights.Log4NetAppender">
      <layout type="log4net.Layout.PatternLayout">
       <conversionPattern value="%date [%thread] %-5level %logger - %message%newline" />
      </layout>
     </appender>
    </log4net>

## <a name="deploy"></a>3. Inserir instruções de log, compilar e implantar

Insira chamadas de log de eventos usando a estrutura de log que tiver escolhido. Por exemplo, se usar o Log4Net, você poderá ter chamadas como

    log.Warn("Slow response - database01");

Eventos registrados em log serão enviados ao Application Insights no desenvolvimento e na operação.

## <a name="view"></a>4. Ver dados de log

No Application Insights, abra a pesquisa de diagnóstico.

![Abra a pesquisa de diagnóstico][Abra a pesquisa de diagnóstico]

Selecione qualquer evento de log para ver os detalhes.

![Abra a pesquisa de diagnóstico][1]

Os campos disponíveis dependem da estrutura de log e dos parâmetros que você usou na chamada.

Você pode usar cadeias simples (sem curingas) para filtrar os dados dentro de um item.

## <a name="search"></a>5. Pesquisar os dados

Defina um intervalo de tempo e pesquise pelos termos. Pesquisas que levam em consideração intervalos de tempo menores são mais rápidas.

![Abra a pesquisa de diagnóstico][2]

Observe que você pesquisa por termos, e não subcadeias. Os termos são cadeias alfanuméricas que incluem alguns sinais de pontuação como “.” e “\_”. Por exemplo:

<table>
<tr><th> Termo                    </th><th> NÃO corresponde a 				</th><th> mas corresponde a </th></tr>
<tr><td> ControladorInicial.Sobre </td><td> sobre<br/>inicial           	</td><td> i*sobre<br/>inicial*          </td></tr>
<tr><td> ÉLocal                   </td><td> local<br/>é<br/>*local   	    </td><td> él*<br/>élocal<br/>é*l               </td></tr>
<tr><td> Novo Atraso              </td><td> o a               				</td><td> novo<br/>atraso<br/>n* AND d*        </td></tr>
</table>

Estas são algumas expressões de pesquisa que você pode usar:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tr class="header">
<th align="left"><p>Exemplo de consulta</p></th>
<th align="left"><p>Efeito</p></th>
</tr>
<tr class="odd">
<td align="left"><p><span class="code">lento</span></p></td>
<td align="left"><p>Encontra todos os eventos no intervalo de tempo cujos campos incluem o termo &quot;lento&quot;</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">banco de dados??</span></p></td>
<td align="left"><p>Corresponde a banco de dados01, banco de dadosAB, ...</p>
<p>? não é permitido no início de um termo de pesquisa.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><span class="code">banco de dados*</span></p></td>
<td align="left"><p>Corresponde a banco de dados, banco de dados01, banco de dadosNNNN</p>
<p>* não é permitido no início de um termo de pesquisa.</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">maçã AND banana</span></p></td>
<td align="left"><p>Encontra eventos que contêm os dois termos. Use &quot;AND” em letras maiúsculas, e não &quot;and&quot;.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><span class="code">maçã OR banana</span></p>
<p><span class="code">maçã banana</span></p></td>
<td align="left"><p>Encontra eventos que contêm um dos dois termos. Use &quot;OR&quot;, e não &quot;or&quot;.</p>
<p>Forma curta.</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">maçã NOT banana</span></p>
<p><span class="code">maçã -banana</span></p></td>
<td align="left"><p>Encontra eventos que contêm um dos termos, mas não o outro.</p>
<p>Forma curta.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>maç* AND banana NOT (uva OR pera)</p>
<p><span class="code">maç* AND banana -(uva pera)</span></p></td>
<td align="left"><p>Operadores lógicos e uso de colchetes.</p>
<p>Forma mais curta.</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">message:slow</span></p>
<p><span class="code">ipaddress:(10.0.0.* OR 192.168.0.*)</span></p>
<p><span class="code">properties.logEventInfo.level:Error</span></p></td>
<td align="left"><p>Corresponda ao campo especificado. Por padrão, todos os campos são pesquisados. Para ver quais campos estão disponíveis, selecione um evento para ver os detalhes.</p></td>
</tr>
</table>

## <a name="add"></a>Próximas etapas

-   [Adicionar o Application Insights a um projeto][adicione o Application Insights ao seu projeto de serviço Web]
-   [Configurar testes de disponibilidade e capacidade de resposta][Configurar testes de disponibilidade e capacidade de resposta]
-   [Solucionar problemas][Solucionar problemas]

## Saiba mais

-   [Application Insights][Application Insights]
-   [Adicionar o Application Insights ao seu projeto][adicione o Application Insights ao seu projeto de serviço Web]
-   [Monitorar um servidor Web online agora][Monitorar um servidor Web online agora]
-   [Explorar métricas no Application Insights][Explorar métricas no Application Insights]
-   [Pesquisa de log de diagnóstico][Pesquisa de log de diagnóstico]
-   [Acompanhamento de disponibilidade com testes na Web][Configurar testes de disponibilidade e capacidade de resposta]
-   [Acompanhamento de uso com eventos e métricas][Acompanhamento de uso com eventos e métricas]
-   [Perguntas e respostas e solução de problemas][Solucionar problemas]

<!--Link references-->

  [Adicionar um adaptador de log]: #add
  [Configurar a coleta de diagnóstico]: #configure
  [Inserir instruções de log, compilar e implantar]: #deploy
  [Ver dados de log]: #view
  [Pesquisar os dados]: #search
  [Próximas etapas]: #next
  [adicione o Application Insights ao seu projeto de serviço Web]: ../app-insights-monitor-application-health-usage/
  [Obtenha a versão de pré-lançamento do adaptador correto]: ./media/appinsights/appinsights-36nuget.png
  [Abra a pesquisa de diagnóstico]: ./media/appinsights/appinsights-30openDiagnostics.png
  [1]: ./media/appinsights/appinsights-32detail.png
  [2]: ./media/appinsights/appinsights-31search.png
  [Configurar testes de disponibilidade e capacidade de resposta]: ../app-insights-monitor-web-app-availability/
  [Solucionar problemas]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Monitorar um servidor Web online agora]: ../app-insights-monitor-performance-live-website-now/
  [Explorar métricas no Application Insights]: ../app-insights-explore-metrics/
  [Pesquisa de log de diagnóstico]: ../app-insights-search-diagnostic-logs/
  [Acompanhamento de uso com eventos e métricas]: ../app-insights-track-usage-custom-events-metrics/
