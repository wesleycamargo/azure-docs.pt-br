<properties 
	pageTitle="Pesquise por logs de diagnóstico" 
	description="Pesquise logs gerados com Trace, NLog ou Log4Net." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-09" 
	ms.author="awills"/>
 
# Pesquisa de diagnóstico no Application Insights

Um dos métodos de depuração mais tradicionais é inserir linhas de código que emitem um log de rastreamento. [O Application Insights][start] pode capturar seus logs do servidor Web e ajudá-lo a pesquisa e filtrá-los. Se você já usa o log4Net, NLog ou System.Diagnostics.Trace, você pode capturar esses logs com nosso adaptador. Ou então, você pode usar os métodos TrackTrace e TrackException incorporados ao SDK do Application Insights.

Os resultados da pesquisa também podem incluir o modo de exibição de página normal os eventos de solicitação que são usados para criar os relatórios de [uso][usage] e [desempenho][perf], juntamente com quaisquer [chamadas TrackEvent personalizadas][track] que você tenha escrito.


2. [Instalar um adaptador para sua estrutura de log?](#capture)
+ [Inserir chamadas de log de diagnóstico](#pepper)
+ [Exceções](#exceptions)
+ [Ver dados de log](#view)
+ [Dados do log de pesquisa](#search)
+ [Solucionar problemas](#questions)
+ [Próximas etapas](#next)



## <a name="capture"></a> Instalar um adaptador para sua estrutura de log?

Se você ainda não [instalou o Application Insights em seu projeto][start], faça isso agora.

Se você usa as chamadas do SDK Track*() interno do Application Insights, não será necessário um adaptador - [pule para a próxima seção](#pepper).

Para pesquisar os logs gerados com o log4Net, NLog ou System.Diagnostics.Trace, instale o adaptador apropriado:

1. Se você planeja usar o log4Net ou NLog, instale-o em seu projeto. 
2. No Gerenciador de Soluções, clique com o botão direito do mouse no seu projeto e escolha  **Gerenciar Pacotes NuGet**.
3. Selecione Online > Todos, selecione **Incluir Pré-lançamento** e pesquise por "Microsoft.ApplicationInsights"

    ![Get the prerelease version of the appropriate adapter](./media/app-insights-search-diagnostic-logs/appinsights-36nuget.png)

4. Selecione o pacote apropriado entre:
  + Microsoft.ApplicationInsights.TraceListener (para capturar chamadas do System.Diagnostics.Trace)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

O pacote NuGet instala os assemblies necessários e também modifica o app.config ou web. config.

## <a name="pepper"></a>3. Inserir chamadas de log de diagnóstico

Insira chamadas de log de eventos usando a estrutura de log que tiver escolhido. 

Por exemplo, se você usar o SDK do Application Insights, você pode inserir:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Ou então, se você usar System.Diagnostics.Trace:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se você preferir log4net ou NLog:

    logger.Warn("Slow response - database01");

Execute o aplicativo no modo de depuração ou implante-o em seu servidor web.

### <a name="exceptions"></a>Exceções

Para enviar as exceções ao log:

JavaScript no cliente

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C# no servidor

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB no servidor

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

Os parâmetros de medidas e propriedades são opcionais, mas são úteis para filtrar e adicionar informações extras. Por exemplo, se você tiver um aplicativo que pode executar vários jogos, será possível localizar todos os relatórios de exceção relacionados a um jogo específico. Você pode adicionar quantos itens desejar a cada dicionário.

## <a name="view"></a>4. Ver dados de log


1. No Application Insights, abra a pesquisa de diagnóstico.

    ![Open diagnostic search](./media/app-insights-search-diagnostic-logs/appinsights-30openDiagnostics.png)
   
2. Defina o filtro para os tipos de evento que você gostaria de ver.

    ![Open diagnostic search](./media/app-insights-search-diagnostic-logs/appinsights-331filterTrace.png)


Os tipos de evento são:

* **Rastreamento** - pesquise logs de diagnóstico que você capturou de seu servidor web. Isso inclui chamadas log4Net, NLog, System.Diagnostic.Trace e ApplicationInsights TrackTrace.
* **Solicitação** - pesquise solicitações HTTP recebidas pelo componente servidor do seu aplicativo da Web, incluindo solicitações de páginas, solicitações de dados, imagens e assim por diante. Os eventos que você verá são a telemetria enviada pelo SDK do servidor do Application Insights, eventos que são usados para criar o relatório de contagem de solicitações.
* **Exibição de Página** - eventos de exibição da página de pesquisa. Esses eventos são enviados pelo cliente Web e são usados para criar relatórios no modo de exibição de página. (Se você não vê nada aqui, configure o [monitoramento de cliente Web][usage].)
* **Evento personalizado** - se você tiver inserido chamadas TrackEvent() e TrackMetric() para [monitorar o uso][track], você pode pesquisá-las aqui.

Selecione qualquer evento de log para ver os detalhes. 

![Open diagnostic search](./media/app-insights-search-diagnostic-logs/appinsights-32detail.png)

Você pode usar cadeias simples (sem curingas) para filtrar os dados dentro de um item.

Os campos disponíveis dependem da estrutura de log e dos parâmetros que você usou na chamada.


## <a name="search"></a>5. Pesquisar os dados

Defina um intervalo de tempo e pesquise pelos termos. Pesquisas que levam em consideração intervalos de tempo menores são mais rápidas. 

![Open diagnostic search](./media/app-insights-search-diagnostic-logs/appinsights-311search.png)

Observe que você pesquisa por termos, e não subcadeias. Os termos são cadeias alfanuméricas que incluem alguns sinais de pontuação como "." e "_". Por exemplo:

<table>
  <tr><th>o termo</th><th>NÃO corresponde a</th><th>, mas estes são correspondentes a</th></tr>
  <tr><td>HomeController.About</td><td>about<br/>home</td><td>h*about<br/>home*</td></tr>
  <tr><td>IsLocal</td><td>local<br/>is<br/>*local</td><td>isl*<br/>islocal<br/>i*l</td></tr>
  <tr><td>New Delay</td><td>w d</td><td>new<br/>delay<br/>n* AND d*</td></tr>
</table>

Estas são algumas expressões de pesquisa que você pode usar:

<table>
                    <tr>
                      <th>
                        <p>Exemplo de consulta</p>
                      </th>
                      <th>
                        <p>Efeito</p>
                      </th>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">lento</span>
                        </p>
                      </td>
                      <td>
                        <p>Encontra todos os eventos no intervalo de tempo cujos campos incluem o termo "lento"</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">banco de dados??</span>
                        </p>
                      </td>
                      <td>
                        <p>Corresponde a banco de dados01, banco de dadosAB, ...</p>
                        <p>? não é permitido no início de um termo de pesquisa.</p>
                      </td>
                    </tr>
                     <tr>
                      <td>
                        <p>
                          <span class="code">banco de dados*</span>
                        </p>
                      </td>
                      <td>
                        <p>Corresponde a banco de dados, banco de dados01, banco de dadosNNNN</p>
                        <p>* não é permitido no início de um termo de pesquisa</p>
                      </td>
                    </tr>
                   <tr>
                      <td>
                        <p>
                          <span class="code">maçã AND banana</span>
                        </p>
                      </td>
                      <td>
                        <p>Encontra eventos que contêm os dois termos. Use "AND" em letras maiúsculas, e não "and".</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">maçã OR banana</span>
                        </p>
                        <p>
                          <span class="code">maçã banana</span>
                        </p>
                      </td>
                      <td>
                        <p>Encontra eventos que contêm um dos dois termos. Use "OR", e não "or".</p>
                        <p>Forma curta.</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">maçã NOT banana</span>
                        </p>
                        <p>
                          <span class="code">maçã -banana</span>
                        </p>
                      </td>
                      <td>
                        <p>Encontra eventos que contêm um dos termos, mas não o outro.</p>
                        <p>Forma curta.</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>maç* AND banana NOT (uva OR pera)</p>
                        <p>
                          <span class="code">maç* AND banana -(uva pera)</span>
                        </p>
                      </td>
                      <td>
                        <p>Operadores lógicos e uso de colchetes.</p>
                        <p>Forma mais curta.</p>
                      </td>
                    </tr>
       <!-- -- fielded search feature not ready yet --
                    <tr>
                      <td>
                        <p>
                          <span class="code">message:slow</span>
                        </p>
                        <p>
                          <span class="code">endereço IP:(10.0.0.* OU 192.168.0.*)</span>
                        </p>
                        <p>
                          <span class="code">properties.logEventInfo.level:Error</span>
                        </p>
                      </td>
                      <td>
                        <p>Corresponda ao campo especificado. Por padrão, todos os campos são pesquisados. Para ver quais campos estão disponíveis, selecione um evento para ver os detalhes.</p>
                      </td>
                    </tr>
 -->
</table>


## <a name="questions"></a>Perguntas e respostas

### <a name="emptykey"></a>Eu recebo um erro "Chave de instrumentação não pode ser vazio"

Parece que você instalou o pacote de Nuget de adaptador para registro em log sem instalar o Application Insights.

No Gerenciador de Soluções, clique com o botão direito do mouse em `ApplicationInsights.config` e escolha **Atualizar o Application Insights**. Você obterá uma caixa de diálogo que solicita que você entre no Azure e crie um recurso de Application Insights, ou então reutilize um recurso existente. Isso deve corrigir o erro.

### <a name="limits"></a>Que quantidade de dados é mantida?

Até 500 eventos por segundo de cada aplicativo. Os eventos são retidos por sete dias.

### <a name="cani"></a>Posso...?

- Definir alertas para eventos e exceções
- Exportar logs para análise posterior
- Pesquisar propriedades específicas

Não ainda, mas todos esses recursos estão na lista de pendências.

## <a name="add"></a>Próximas etapas

* [Configurar testes de disponibilidade e capacidade de resposta][availability]
* [Solucionar problemas][qna]





[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]





<!--HONumber=46--> 
 