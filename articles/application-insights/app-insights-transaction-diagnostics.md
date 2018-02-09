---
title: "Diagnóstico de transação do Azure Application Insights | Microsoft Docs"
description: "Diagnóstico de transação de ponta a ponta do Application Insights"
services: application-insights
documentationcenter: .net
author: SoubhagyaDash
manager: victormu
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2018
ms.author: sdash
ms.openlocfilehash: 8c1d8600b7f4aaa1e95f4acfbbdd55fdbfebb8fb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Diagnóstico de transação entre componentes unificado

*Atualmente, essa experiência está em versão prévia e substitui as folhas de diagnóstico existentes para solicitações, dependências e exceções do servidor.*

A versão prévia apresenta uma nova experiência de diagnóstico unificado que correlaciona automaticamente a telemetria no servidor de todos os seus componentes monitorados do Application Insights em uma única visualização. Não importa se você tem vários recursos com chaves de instrumentação separadas, o Application Insights detecta o relacionamento subjacente e permite diagnosticar facilmente o componente, a dependência ou a exceção do aplicativo que causou uma desaceleração ou falha na transação.

## <a name="what-does-component-mean-in-the-context-of-application-insights"></a>O que significa componente no contexto do Application Insights?

Os componentes são partes independentes dos aplicativos de microsserviços/distribuídos. As equipes de operações e desenvolvedores têm acesso ou visibilidade nível de código para telemetria gerada por esses componentes de aplicativos.

* Os componentes são diferentes das dependências externas "observadas", como SQL, EventHub etc., a qual sua organização/equipe pode não ter acesso (código ou telemetria).
* Os componentes são executados em qualquer número de instâncias de contêiner/função/servidor.
* Os componentes podem ser chaves de instrumentação do Application Insights separadas (mesmo se as assinaturas forem diferentes) ou diferentes funções relatando para uma única chave de instrumentação do Application Insights. A nova experiência mostra detalhes em todos os componentes, independentemente de como foram configurados.

> [!Tip]
> Para obter os melhores resultados, assegure-se de que todos os componentes são instrumentados com os SDKs estáveis mais recentes do Application Insights. Se houver diferentes recursos do Application Insights, assegure-se de ter direitos apropriados para exibir a telemetria.

## <a name="enable-and-access"></a>Habilitar e acessar
Habilitar "Detalhes unificados: Diagnóstico de transação E2E" a partir da [lista de visualizações](app-insights-previews.md)

![Habilitar visualização](media/app-insights-e2eTxn-diagnostics/previews.png)

Atualmente, essa versão prévia está disponível para solicitações, dependências e exceções do servidor. É possível acessar a nova experiência dos **Resultado da pesquisa**, **Desempenho** ou experiências de triagem de **Falha**. A versão prévia substitui as folhas de detalhes clássicas correspondentes.

![Amostras de desempenho](media/app-insights-e2eTxn-diagnostics/performanceSamplesClickThrough.png)

## <a name="transaction-diagnostics-experience"></a>Experiência de diagnóstico de transação
Essa exibição tem três partes principais: um gráfico de transações entre componentes, uma lista de sequência de tempo de toda a telemetria de uma operação de componente específica e o painel de detalhes para qualquer item de telemetria selecionado à esquerda.

![Partes principais](media/app-insights-e2eTxn-diagnostics/3partsCrossComponent.png)

### <a name="1-cross-component-transaction-chart"></a>[1] Gráfico de transações entre componentes

Esse gráfico fornece uma linha do tempo com barras horizontais para a duração das solicitações e dependências entre componentes. As exceções coletadas também são marcadas na linha do tempo.

* A linha superior nesse gráfico representa o ponto de entrada, a solicitação de entrada para o primeiro componente chamado nessa transação. A duração é o tempo total decorrido para a conclusão da transação.
* Todas as chamadas para dependências externas são linhas não recolhíveis simples, com ícones que representam o tipo de dependência.
* As chamadas para outros componentes são linhas recolhíveis. Cada linha corresponde a uma operação específica invocada no componente.
* Por padrão, a solicitação, dependência ou exceção solicitada inicialmente é exibida no gráfico.
* Selecione qualquer linha para ver os detalhes à direita. Clique em "Abrir rastreamentos do profiler" ou "Abrir instantâneo de depuração" para diagnósticos de nível de código nos painéis de detalhes correspondentes.

> [!NOTE]
As chamadas para outros componentes têm duas linhas: uma linha representa a chamada de saída (dependência) do componente do chamador e a outra linha corresponde à solicitação de entrada no componente chamado. O ícone principal e o estilo distinto das barras de duração ajudarão a diferenciá-las.

### <a name="2-time-sequenced-telemetry-of-the-selected-component-operation"></a>[2] Telemetria sequenciada no tempo da operação do componente selecionado

Qualquer linha selecionada no gráfico de transações entre componentes está relacionada a uma operação invocada em um componente específico. Essa operação de componente selecionada é refletida no título da seção inferior. Abra essa seção para ver uma sequência de tempo fixo de toda a telemetria relacionada a essa operação específica. É possível selecionar qualquer item de telemetria nessa lista para ver os detalhes correspondentes à direita.

![Sequência de tempo de toda a telemetria](media/app-insights-e2eTxn-diagnostics/allTelemetryDrawerOpened.png)

### <a name="3-details-pane"></a>[3] Painel de detalhes

Esse painel mostra o detalhe dos itens selecionados de qualquer uma das duas seções à esquerda. "Mostrar tudo" lista todos os atributos padrão que são coletados. Todos os atributos personalizados são listados separadamente abaixo do conjunto padrão.

![Detalhes de exceção](media/app-insights-e2eTxn-diagnostics/exceptiondetail.png)

## <a name="profiler-and-snapshot-debugger"></a>Profiler e depurador de instantâneos

O [Application Insights Profiler](app-insights-profiler.md) ou o [depurador de instantâneos](app-insights-snapshot-debugger.md) ajuda com diagnósticos de nível de código de problemas de falha e desempenho. Com essa experiência, você pode ver Rastreamentos do Profiler ou instantâneos de qualquer componente com um único clique.

Se você não conseguiu colocar o Profiler em funcionamento, entre em contato com**serviceprofilerhelp@microsoft.com**

Se você não conseguiu colocar o Depurador de Instantâneos em funcionamento, entre em contato com**snapshothelp@microsoft.com**

![Integração do depurador](media/app-insights-e2eTxn-diagnostics/debugSnapshot.png)

## <a name="faq"></a>Perguntas frequentes

*Eu vejo um único componente no gráfico e os outros somente estão mostrando como dependências externas sem qualquer detalhe do que aconteceu dentro desses componentes.*

Motivos possíveis:

* Os outros componentes são instrumentados com Application Insights?
* Eles estão usando o SDK estável mais recente do Application Insights?
* Se esses componentes são recursos separados do Application Insights, você precisou ter acesso à telemetria?

Se você tiver acesso e os componentes estiverem instrumentados com os SDKs mais recentes do Application Insights, avise-nos através do canal de comentários superior direito.

*Eu tenho apenas dependências externas. Devo me preocupar com essa versão prévia?*

Sim. A nova experiência unifica toda a telemetria do servidor em um modo de exibição único. Futuramente, as folhas de detalhe mais antigas serão substituídas por essa experiência, então, experimente-a e envie seus comentários para nós. Confira como parece ser uma transação de componente único:

![Experiência de componente único](media/app-insights-e2eTxn-diagnostics/singleComponent.png)

*Eu vejo linhas duplicadas para as dependências. Isso é esperado?*

Nesse momento, mostramos a chamada de dependência de saída separada da solicitação de entrada. Normalmente, as duas chamadas parecem idênticas, apenas o valor da duração sendo diferente devido à viagem de ida e volta da rede. O ícone principal e o estilo distinto das barras de duração ajudarão a diferenciá-las. Essa apresentação dos dados está confusa? Envie-nos seus comentários!

*O que significa o relógio distorcer entre instâncias do componente diferentes?*

As linhas do tempo são ajustadas para as distorções do relógio no gráfico de transações. É possível visualizar os carimbos de data/hora exatos no painel de detalhes ou usando o Analytics.

*Por que na nova experiência está faltando a maioria das consultas de itens relacionados?*

Esse comportamento é intencional. Todos os itens relacionados, em todos os componentes, já estão disponíveis no lado esquerdo (seções superior e inferior). A nova experiência tem dois itens relacionados que o lado esquerdo não abrange: toda a telemetria de cinco minutos antes e depois desse evento e a linha do tempo do usuário.

*Por que a nova experiência não tem o recurso que eu mais gostava nas folhas antigas?*

Envie comentários! Nós queremos analisar suas preocupações antes que essa experiência esteja normalmente disponível, momento em que as exibições antigas serão preteridas. Por enquanto, é possível desabilitar a versão prévia para retornar às lâminas antigas.

## <a name="known-issues"></a>Problemas conhecidos

* Falha nas amostras do link do Mapa de Aplicativos para as lâminas de detalhe mais antigas.
* Os insights baseados em autocluster nos resultados da pesquisa vinculam às lâminas detalhadas mais antigas.
* A integração do item de trabalho não está disponível na nova experiência.
