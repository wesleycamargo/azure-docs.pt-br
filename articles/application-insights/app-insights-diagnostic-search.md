<properties 
	pageTitle="Uso da Pesquisa de Diagnóstico" 
	description="Pesquisar e filtrar eventos, solicitações e rastreamentos de log individuais." 
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
	ms.date="07/08/2015" 
	ms.author="awills"/>
 
# Uso de Pesquisa de diagnóstico no Application Insights

Pesquisa de diagnóstico é a folha no [Application Insights][start] que você usa para localizar e explorar os itens individuais de telemetria, como visualizações de página, exceções ou solicitações da Web. Você também pode exibir rastreamentos de log e eventos que você tenha codificado.

## Quando você vê a Pesquisa de diagnóstico?

Você pode abrir a Pesquisa de diagnóstico explicitamente:

![Abra a pesquisa de diagnóstico](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)


Ela também é aberta quando você clica em alguns gráficos e itens de grade. Nesse caso, os filtros dessa pesquisa são predefinidos para concentrar-se no tipo de item selecionado por você.

Por exemplo, se seu aplicativo for um serviço Web, a folha “Visão geral” mostra um gráfico de volume de solicitações. Clique nele e você obterá um gráfico mais detalhado, com uma listagem mostrando quantas solicitações foram feitas para cada URL. Clique em qualquer linha e você obterá uma lista das solicitações individuais para essa URL:

![Abra a pesquisa de diagnóstico](./media/app-insights-diagnostic-search/07-open-from-filters.png)


O corpo principal da Pesquisa de diagnóstico é uma lista de itens de telemetria - solicitações ao servidor, visualizações de página, eventos personalizados que você codificou e assim por diante. Na parte superior da lista, há um gráfico de resumo mostrando contagens de eventos ao longo do tempo.


## Inspecionar itens individuais

Selecione qualquer item de telemetria para ver os campos-chave e itens relacionados. Se você quiser ver o conjunto completo de campos, clique em "...".

![Abra a pesquisa de diagnóstico](./media/app-insights-diagnostic-search/10-detail.png)

Para localizar o conjunto completo de campos, use cadeias de caracteres simples (sem curingas). Os campos disponíveis dependem do tipo de telemetria.

## Filtrar tipos de evento

Abrir a folha de filtro e escolha os tipos de eventos que você deseja ver. (Se posteriormente, você desejar restaurar os filtros com os quais você abriu a folha, clique em Redefinir.)


![Escolha o filtro e selecione os tipos de telemetria](./media/app-insights-diagnostic-search/02-filter-req.png)


Os tipos de evento são:

* **Rastreamento** - logs de diagnóstico, incluindo as chamadas TrackTrace, log4Net, NLog e System.Diagnostic.Trace.
* **Solicitação** -solicitações HTTP recebidas pelo seu aplicativo para servidores, incluindo páginas, scripts, imagens, arquivos de estilo e dados. Esses eventos são usados para criar os gráficos de visão geral de solicitação e de resposta.
* **Exibição da página** - telemetria enviada pelo cliente da Web, usada para criar relatórios de exibição de página. 
* **Evento personalizado** - se você tiver inserido chamadas TrackEvent() e TrackMetric() para [monitorar o uso][track], você pode pesquisar por elas aqui.
* **Exceção** - exceções não percebidas no servidor e aquelas que você registra usando TrackException().

## Filtrar pelos valores de propriedade

Você pode filtrar eventos pelos valores de suas propriedades. As propriedades disponíveis dependem dos tipos de evento que você selecionou.

Por exemplo, escolha solicitações com um código de resposta específicos.

![Expanda uma propriedade e escolha um valor](./media/app-insights-diagnostic-search/03-response500.png)

Não escolher nenhum valor para uma determinada propriedade tem o mesmo efeito que escolher todos os valores; ele desativará a filtragem para essa propriedade.


### Reduzir o escopo de sua pesquisa

Observe que as contagens à direita dos valores de filtro mostram quantas ocorrências existem no atual conjunto filtrado.

Neste exemplo, está claro que a solicitação `Reports/Employees` resulta na maioria dos 500 erros:

![Expanda uma propriedade e escolha um valor](./media/app-insights-diagnostic-search/04-failingReq.png)

Além disso, se você quiser ver também quais outros eventos estavam acontecendo durante esse tempo, você pode verificar **Incluir eventos com propriedades indefinidas**.

## Remover o tráfego de testes da Web e de bot

Use o filtro **Tráfego real ou sintético** e marque **Real**.

Você também pode filtrar por **Origem do tráfego sintético**.

## Inspecionar ocorrências individuais

Adicione esse nome de solicitação para o conjunto de filtros e, em seguida, você poderá inspecionar ocorrências individuais daquele evento.

![Selecione um valor](./media/app-insights-diagnostic-search/05-reqDetails.png)

Para eventos de solicitação, os detalhes mostram as exceções ocorridas enquanto a solicitação estava em processamento.

Clique em uma exceção para ver seus detalhes, incluindo o rastreamento de pilha.

![Clique em uma exceção](./media/app-insights-diagnostic-search/06-callStack.png)

## Encontrar eventos com a mesma propriedade

Localize todos os itens com o mesmo valor da propriedade:

![Clique com o botão direito em uma propriedade](./media/app-insights-diagnostic-search/12-samevalue.png)

## Pesquisar por valor de métrica

Obtenha todas o solicitações com tempo de resposta > 5 s. Tempos são representados em tiques: 10.000 tiques = 1 ms.

!["Tempo de resposta":(threshold TO *)](./media/app-insights-diagnostic-search/11-responsetime.png)



## Pesquisar os dados

Você pode pesquisar termos em qualquer um dos valores de propriedade. Isso é especialmente útil se você tiver gravado [eventos personalizados][track] com valores de propriedade.

Você talvez queira definir um tempo de intervalo, já que pesquisas em um intervalo mais curto são mais rápidas.

![Abra a pesquisa de diagnóstico](./media/app-insights-diagnostic-search/appinsights-311search.png)

Observe que você pesquisa por termos e não subcadeias. Os termos são cadeias alfanuméricas que incluem alguns sinais de pontuação como “.” e “_”. Por exemplo:

Termo|*não* corresponde a|mas corresponde a
---|---|---
ControladorInicial.Sobre|sobre<br/>inicial|i*sobre<br/>inicial*
ÉLocal|local<br/>é<br/>*local|él*<br/>élocal<br/>é*l
Novo Atraso|o a|novo<br/>atraso<br/>n* AND d*


Estas são algumas expressões de pesquisa que você pode usar:

Exemplo de consulta | Efeito 
---|---
lento|Encontra todos os eventos no intervalo de tempo cujos campos incluem o termo "lento"
banco de dados??|As correspondências banco de dados01, banco de dadosAB, ...<br/>? não são permitidas no início de um termo de pesquisa.
banco de dados*|As correspondências banco de dados, banco de dados01, banco de dadosNNNN<br/>* não são permitidas no início de um termo de pesquisa
maçã AND banana|Encontra eventos que contêm os dois termos. Use "AND” em letras maiúsculas, e não "and".
maçã OR banana<br/>maçã|Encontra eventos que contêm um dos dois termos. Use "OR" não "or".</br/>Forma abreviada.
maçã NOT banana<br/>maçã -banana|Encontre eventos que contêm um dos termos, mas não o outro.<br/>Forma abreviada.
maç* AND banana -(uva pera)|Operadores lógicos e uso de colchetes.
"Métrica": 0 TO 500<br/>"Métrica": 500 TO * | Encontre eventos que contêm a medição especificada dentro do intervalo de valores.


## Salvar sua pesquisa

Quando você definiu todos os filtros que deseja, você pode salvar a pesquisa como um favorito. Se você trabalha em uma conta organizacional, você pode optar por compartilhá-la com outros membros da equipe.

![Clique em Favorito, defina o nome e clique em Salvar](./media/app-insights-diagnostic-search/08-favorite-save.png)


Para ver a pesquisa novamente, **vá até a folha de visão geral** e abra Favoritos:

![Bloco Favoritos](./media/app-insights-diagnostic-search/09-favorite-get.png)

Se você os salvou com o intervalo de tempo Relativo, a folha reaberta contém os dados mais recentes. Se você os salvou com o intervalo de tempo Absoluto, consulte os mesmos dados, sempre.


## Enviar mais telemetria para o Application Insights

Além de telemetria da caixa enviada pelo SDK do Application Insights, você pode:

* Capturar rastreamentos de log da sua estrutura de registros favorita no [.NET][netlogs] ou [Java][javalogs]. Isso significa que você pode pesquisar os rastreamentos de log e correlacioná-los com outros eventos, exceções e visualizações de página. 
* [Escrever código][track] para enviar eventos personalizados, visualizações de página e exceções. 

[Saiba como enviar logs e telemetria personalizada para o Application Insights][trace].


## <a name="questions"></a>Perguntas e respostas

### <a name="limits"></a>Que quantidade de dados é mantida?

Até 500 eventos por segundo de cada aplicativo. Os eventos são retidos por sete dias.

### Como consultar dados de POSTAGEM nas minhas solicitações de servidor?

Nós não registramos os dados de POSTAGEM automaticamente, mas você pode usar [chamadas de log ou TrackTrace][trace]. Coloque os dados de POSTAGEM no parâmetro de mensagem. Você não pode filtrar a mensagem assim como pode filtrar propriedades, mas o limite de tamanho é maior.

## <a name="add"></a>Próximas etapas

* [Enviar logs e telemetria personalizada para o Application Insights][trace]
* [Configurar testes de disponibilidade e capacidade de resposta][availability]
* [Solucionar problemas][qna]



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[javalogs]: app-insights-java-trace-logs.md
[netlogs]: app-insights-asp-net-trace-logs.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-get-started.md
[trace]: app-insights-search-diagnostic-logs.md
[track]: app-insights-custom-events-metrics-api.md

 

<!---HONumber=July15_HO4-->