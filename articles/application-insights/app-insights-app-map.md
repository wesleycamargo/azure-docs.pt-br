---
title: Mapa de aplicativos no Application Insights | Microsoft Docs
description: Uma apresentação visual das dependências entre componentes do aplicativo, rotuladas com alertas e KPIs.
services: application-insights
documentationcenter: ''
author: SoubhagyaDash
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/15/2016
ms.author: awills

---
# Mapa de Aplicativos no Application Insights
No [Application Insights do Visual Studio](app-insights-overview.md), o Mapa de Aplicativos é um layout visual das relações de dependência dos componentes de seu aplicativo. Cada componente mostra KPIs, como carga, desempenho, falhas e alertas, para ajudá-lo a descobrir possíveis componentes que estejam causando uma falha ou um problema de desempenho. Você pode clicar em qualquer componente para ver um diagnóstico mais detalhado, tanto do Application Insights quanto, se seu aplicativo usa os serviços do Azure, do Diagnóstico do Azure, como, por exemplo as recomendações do Advisor do Banco de dados SQL.

Assim como outros gráficos, você pode fixar um mapa de aplicativos no painel do Azure, onde ele fica totalmente funcional.

## Abrir o mapa de aplicativos
Abra o mapa na folha de visão geral do seu aplicativo:

![abrir mapa de aplicativos](./media/app-insights-app-map/01.png)

![mapa de aplicativos](./media/app-insights-app-map/02.png)

O mapa mostra:

* Testes de disponibilidade
* Componente do lado do cliente (monitorado com o SDK do JavaScript)
* Componente do lado do servidor
* Dependências dos componentes do cliente e do servidor

Você pode expandir e recolher grupos de link de dependência:

![recolher](./media/app-insights-app-map/03.png)

Se você tiver um grande número de dependências de um tipo (SQL, HTTP, etc.), elas podem aparecer agrupadas.

![dependências agrupadas](./media/app-insights-app-map/03-2.png)

## Identificar problemas
Cada nó possui indicadores de desempenho relevantes, como as taxas de carga, de desempenho e de falha do componente.

Ícones de aviso destacam possíveis problemas. Um aviso laranja significa que existem falhas em solicitações, exibições de página ou chamadas de dependência. Vermelho significa uma taxa de falha acima de 5%.

![ícones de falha](./media/app-insights-app-map/04.png)

Alertas ativos também aparecem:

![alertas ativos](./media/app-insights-app-map/05.png)

Se você usa o SQL Azure, há um ícone que mostra quando há recomendações sobre como melhorar o desempenho.

![Recomendações do Azure](./media/app-insights-app-map/06.png)

Clique em um ícone para obter mais detalhes:

![recomendações do azure](./media/app-insights-app-map/07.png)

## Clique para Diagnóstico
Cada um dos nós no mapa oferece cliques direcionados para diagnóstico. As opções variam dependendo do tipo de nó.

![opções de servidor](./media/app-insights-app-map/09.png)

Para componentes que são hospedados no Azure, as opções incluem links diretos para eles.

## Filtros e o intervalo de tempo
Por padrão, o mapa resume todos os dados disponíveis para o intervalo de tempo escolhido. Mas você pode filtrá-lo para incluir apenas nomes de operação ou dependências específicas.

* Nome da operação: isso inclui tipos de solicitação do lado servidor e visualizações de página. Com essa opção, o mapa mostra o KPI no nó do lado do servidor e do cliente somente para operações selecionadas. Ele mostra as dependências chamadas no contexto dessas operações específicas.
* Nome de base de dependência: isso inclui as dependências de lado do navegador do AJAX e dependências do lado do servidor. Se você relatar telemetria de dependência personalizada com a API TrackDependency, elas também serão exibidas aqui. Você pode selecionar quais dependências mostrar no mapa. Observe que, neste momento, isso não filtrará as solicitações do lado do servidor ou os modos de exibição de página no lado do cliente.

![Definir filtros](./media/app-insights-app-map/11.png)

## Salvar filtros
Para salvar os filtros que você aplicou, fixe na exibição filtrada em um [painel](app-insights-dashboards.md).

![Fixar no painel](./media/app-insights-app-map/12.png)

## Comentários
Por favor, [faça comentários por meio da opção Comentários no portal](app-insights-get-dev-support.md).

![Imagem de MapLink-1](./media/app-insights-app-map/13.png)

<!---HONumber=AcomDC_0622_2016-->