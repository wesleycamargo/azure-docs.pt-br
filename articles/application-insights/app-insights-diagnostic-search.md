---
title: "Usando a Pesquisa de Diagnóstico | Microsoft Docs"
description: "Pesquisar e filtrar eventos, solicitações e rastreamentos de log individuais."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 2a437555-8043-45ec-937a-225c9bf0066b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/09/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 7a9c40081f52b2ffe918f4612f790f7fd08acc5a
ms.openlocfilehash: 50d8c22dd0bdd3618ca0cbcd29ffa290c0c5f35b


---
# <a name="using-diagnostic-search-in-application-insights"></a>Uso de Pesquisa de diagnóstico no Application Insights
A Pesquisa de Diagnóstico é um recurso do [Application Insights][iniciar] que você usa para localizar e explorar itens individuais de telemetria, como exibições de página, exceções ou solicitações da Web. Você também pode exibir rastreamentos de log e eventos que você tenha codificado.

## <a name="where-do-you-see-diagnostic-search"></a>Onde você vê a Pesquisa de Diagnóstico?
### <a name="in-the-azure-portal"></a>No portal do Azure
Você pode abrir a Pesquisa de diagnóstico explicitamente:

![Abra a pesquisa de diagnóstico](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

Ela também é aberta quando você clica em alguns gráficos e itens de grade. Nesse caso, os filtros dessa pesquisa são predefinidos para concentrar-se no tipo de item selecionado por você. 

Por exemplo, se seu aplicativo for um serviço Web, a folha “Visão geral” mostra um gráfico de volume de solicitações. Clique nele e você obterá um gráfico mais detalhado, com uma listagem mostrando quantas solicitações foram feitas para cada URL. Clique em qualquer linha e você obterá uma lista das solicitações individuais para essa URL:

![Abra a pesquisa de diagnóstico](./media/app-insights-diagnostic-search/07-open-from-filters.png)

O corpo principal da Pesquisa de diagnóstico é uma lista de itens de telemetria - solicitações ao servidor, visualizações de página, eventos personalizados que você codificou e assim por diante. Na parte superior da lista, há um gráfico de resumo mostrando contagens de eventos ao longo do tempo.

Normalmente, os eventos aparecem na pesquisa de diagnóstico antes de aparecerem no gerenciador de métricas. Embora a folha seja atualizada em intervalos, você pode clicar em Atualizar se estiver esperando um evento específico.

### <a name="in-visual-studio"></a>No Visual Studio
Abra a janela Pesquisar no Visual Studio:

![](./media/app-insights-diagnostic-search/32.png)

A janela Pesquisar tem os mesmos recursos que o portal da Web:

![](./media/app-insights-diagnostic-search/34.png)

## <a name="sampling"></a>Amostragem
Se o seu aplicativo gerar muita telemetria (e você estiver usando o SDK do ASP.NET versão 2.0.0-beta3 ou posterior), o módulo de amostragem adaptável reduzirá automaticamente o volume enviado ao portal, enviando apenas uma fração representativa de eventos. No entanto, os eventos relacionados à mesma solicitação serão selecionadas ou desmarcadas como um grupo, para que você possa navegar entre os eventos relacionados. 

[Saiba mais sobre amostragem](app-insights-sampling.md).

## <a name="inspect-individual-items"></a>Inspecionar itens individuais
Selecione qualquer item de telemetria para ver os campos-chave e itens relacionados. Se você quiser ver o conjunto completo de campos, clique em "...". 

![Clique em Novo Item de Trabalho, edite os campos e, em seguida, clique em OK.](./media/app-insights-diagnostic-search/10-detail.png)

Para localizar o conjunto completo de campos, use cadeias de caracteres simples (sem curingas). Os campos disponíveis dependem do tipo de telemetria.

## <a name="create-work-item"></a>Criar um item de trabalho
Você pode criar um bug no Visual Studio Team Services com os detalhes de qualquer item de telemetria. 

![Clique em Novo Item de Trabalho, edite os campos e, em seguida, clique em OK.](./media/app-insights-diagnostic-search/42.png)

Na primeira vez que fizer isso, será solicitado que você configure um link para sua conta e projeto do Team Services.

![Preencha a URL do servidor do Team Services e o Nome do projeto e, em seguida, clique em Autorizar](./media/app-insights-diagnostic-search/41.png)

(Você também pode chegar à folha de configuração em Configurações > Itens de Trabalho.)

## <a name="filter-event-types"></a>Filtrar tipos de evento
Abrir a folha de filtro e escolha os tipos de eventos que você deseja ver. (Se posteriormente, você desejar restaurar os filtros com os quais você abriu a folha, clique em Redefinir.)

![Escolha o filtro e selecione os tipos de telemetria](./media/app-insights-diagnostic-search/02-filter-req.png)

Os tipos de evento são:

* **Rastreamento** - logs de diagnóstico, incluindo as chamadas TrackTrace, log4Net, NLog e System.Diagnostic.Trace.
* **Solicitação** -solicitações HTTP recebidas pelo seu aplicativo para servidores, incluindo páginas, scripts, imagens, arquivos de estilo e dados. Esses eventos são usados para criar os gráficos de visão geral de solicitação e de resposta.
* **Exibição da página** - telemetria enviada pelo cliente da Web, usada para criar relatórios de exibição de página. 
* **Evento personalizado** - se tiver inserido chamadas a TrackEvent() para [monitorar o uso][acompanhar], você poderá pesquisá-las aqui.
* **Exceção** - exceções não percebidas no servidor e aquelas que você registra usando TrackException().

## <a name="filter-on-property-values"></a>Filtrar pelos valores de propriedade
Você pode filtrar eventos pelos valores de suas propriedades. As propriedades disponíveis dependem dos tipos de evento que você selecionou. 

Por exemplo, escolha solicitações com um código de resposta específicos.

![Expanda uma propriedade e escolha um valor](./media/app-insights-diagnostic-search/03-response500.png)

Não escolher nenhum valor para uma determinada propriedade tem o mesmo efeito que escolher todos os valores; ele desativará a filtragem para essa propriedade.

### <a name="narrow-your-search"></a>Reduzir o escopo de sua pesquisa
Observe que as contagens à direita dos valores de filtro mostram quantas ocorrências existem no atual conjunto filtrado. 

Neste exemplo, está claro que a solicitação `Reports/Employees` resulta na maioria dos 500 erros:

![Expanda uma propriedade e escolha um valor](./media/app-insights-diagnostic-search/04-failingReq.png)

Além disso, se você quiser ver também quais outros eventos estavam acontecendo durante esse tempo, você pode verificar **Incluir eventos com propriedades indefinidas**.

## <a name="remove-bot-and-web-test-traffic"></a>Remover o tráfego de testes da Web e de bot
Use o filtro **Tráfego real ou sintético** e marque **Real**.

Você também pode filtrar por **Origem do tráfego sintético**.

## <a name="inspect-individual-occurrences"></a>Inspecionar ocorrências individuais
Adicione esse nome de solicitação para o conjunto de filtros e, em seguida, você poderá inspecionar ocorrências individuais daquele evento.

![Selecione um valor](./media/app-insights-diagnostic-search/05-reqDetails.png)

Para eventos de solicitação, os detalhes mostram as exceções ocorridas enquanto a solicitação estava em processamento.

Clique em uma exceção para ver seus detalhes, incluindo o rastreamento de pilha.

![Clique em uma exceção](./media/app-insights-diagnostic-search/06-callStack.png)

## <a name="find-events-with-the-same-property"></a>Encontrar eventos com a mesma propriedade
Localize todos os itens com o mesmo valor da propriedade:

![Clique com o botão direito em uma propriedade](./media/app-insights-diagnostic-search/12-samevalue.png)

## <a name="search-by-metric-value"></a>Pesquisar por valor de métrica
Obtenha todas o solicitações com tempo de resposta > 5 s.  Tempos são representados em tiques: 10.000 tiques = 1 ms.

!["Tempo de resposta":(threshold TO *)](./media/app-insights-diagnostic-search/11-responsetime.png)

## <a name="search-the-data"></a>Pesquisar os dados

> [!NOTE]
> Para escrever consultas mais complexas, abra o [**Analytics**](app-insights-analytics-tour.md) na parte superior da folha Pesquisa.
> 

Você pode pesquisar termos em qualquer um dos valores de propriedade. Isso será especialmente útil se você tiver escrito [eventos personalizados][acompanhar] com valores de propriedade. 

Você talvez queira definir um tempo de intervalo, já que pesquisas em um intervalo mais curto são mais rápidas. 

![Abra a pesquisa de diagnóstico](./media/app-insights-diagnostic-search/appinsights-311search.png)

Observe que você pesquisa por termos e não subcadeias. Os termos são cadeias alfanuméricas que incluem alguns sinais de pontuação como “.” e “_”. Por exemplo:

| Termo | *não* corresponde a | mas corresponde a |
| --- | --- | --- |
| ControladorInicial.Sobre |sobre<br/>inicial |h\*about<br/>home\* |
| ÉLocal |local<br/>is<br/>\*local |isl\*<br/>islocal<br/>i\*l\* |
| Novo Atraso |o a |novo<br/>atrasar<br/>n\* AND d\* |

Estas são algumas expressões de pesquisa que você pode usar:

| Exemplo de consulta | Efeito |
| --- | --- |
| lento |Encontra todos os eventos no intervalo de tempo cujos campos incluem o termo "lento" |
| banco de dados |Corresponde ao banco de dados01, banco de dadosAB, ...<br/>? não é permitido no início de um termo de pesquisa. |
| banco de dados* |Corresponde ao banco de dados, banco de dados01, banco de dadosNNNN<br/>  * não é permitido no início de um termo de pesquisa |
| maçã AND banana |Encontra eventos que contêm os dois termos. Use "AND” em letras maiúsculas, e não "and". |
| maçã OR banana<br/>maçã |Encontra eventos que contêm um dos dois termos. Use "OR", e não "or".</br/>Forma abreviada. |
| maçã NOT banana<br/>maçã -banana |Encontre eventos que contêm um dos termos, mas não o outro.<br/>Forma abreviada. |
| maç* AND banana -(uva pera) |Operadores lógicos e uso de colchetes. |
| "Métrica": 0 TO 500<br/>"Métrica": 500 TO * |Encontre eventos que contêm a medição especificada dentro do intervalo de valores. |



## <a name="save-your-search"></a>Salvar sua pesquisa
Quando você definiu todos os filtros que deseja, você pode salvar a pesquisa como um favorito. Se você trabalha em uma conta organizacional, você pode optar por compartilhá-la com outros membros da equipe.

![Clique em Favorito, defina o nome e clique em Salvar](./media/app-insights-diagnostic-search/08-favorite-save.png)

Para ver a pesquisa novamente, **vá até a folha de visão geral** e abra Favoritos:

![Bloco Favoritos](./media/app-insights-diagnostic-search/09-favorite-get.png)

Se você os salvou com o intervalo de tempo Relativo, a folha reaberta contém os dados mais recentes. Se você os salvou com o intervalo de tempo Absoluto, consulte os mesmos dados, sempre.

## <a name="send-more-telemetry-to-application-insights"></a>Enviar mais telemetria para o Application Insights
Além de telemetria da caixa enviada pelo SDK do Application Insights, você pode:

* Capturar rastreamentos de log da sua estrutura de registros favorita no [.NET][netlogs] ou no [Java][javalogs]. Isso significa que você pode pesquisar os rastreamentos de log e correlacioná-los com outros eventos, exceções e visualizações de página. 
* [Escrever código][acompanhar] para enviar eventos personalizados, exibições de página e exceções. 

[Saiba como enviar logs e telemetria personalizada para o Application Insights][trace].

## <a name="a-namequestionsaq-a"></a><a name="questions"></a>Perguntas e respostas
### <a name="a-namelimitsahow-much-data-is-retained"></a><a name="limits"></a>Que quantidade de dados é mantida?
Até 500 eventos por segundo de cada aplicativo. Os eventos são retidos por sete dias.

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Como consultar dados de POSTAGEM nas minhas solicitações de servidor?
Nós não registramos os dados de POSTAGEM automaticamente, mas você pode usar [chamadas de log ou TrackRastreamento][trace]. Coloque os dados de POSTAGEM no parâmetro de mensagem. Você não pode filtrar a mensagem assim como pode filtrar propriedades, mas o limite de tamanho é maior.

## <a name="a-nameaddanext-steps"></a><a name="add"></a>Próximas etapas
* [Escrever consultas complexas no Analytics](app-insights-analytics-tour.md)
* [Enviar logs e telemetria personalizada para o Application Insights][trace]
* [Configurar testes de disponibilidade e capacidade de resposta][availability]
* [Solução de problemas][qna]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[javalogs]: app-insights-java-trace-logs.md
[netlogs]: app-insights-asp-net-trace-logs.md
[qna]: app-insights-troubleshoot-faq.md
[iniciar]: app-insights-overview.md
[trace]: app-insights-search-diagnostic-logs.md
[acompanhar]: app-insights-api-custom-events-metrics.md




<!--HONumber=Nov16_HO3-->


