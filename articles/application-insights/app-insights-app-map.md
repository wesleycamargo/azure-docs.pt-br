---
title: Mapa de aplicativos no Azure Application Insights | Microsoft Docs
description: Monitorar topologias complexas de aplicativos com o mapa do aplicativo
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 3bbed59bf93eab5e729fbdd3ccae04599ac47081
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="application-map-triage-distributed-applications"></a>Mapa do aplicativo: aplicativos distribuídos por triagem
O mapa do aplicativo ajuda você a identificar gargalos de desempenho ou pontos de acesso com falha em todos os componentes dos seus aplicativos distribuídos. Cada nó do mapa representa um componente de aplicativo ou suas dependências e esses nós também têm KPIs de integridade e alertas de status. Você pode clicar em qualquer componente para obter diagnóstico mais detalhado, como eventos do Application Insights. Se seu aplicativo usar os serviços do Azure, você também poderá clicar no diagnóstico do Azure, como nas recomendações do Assistente do Banco de Dados SQL.

## <a name="what-is-a-component"></a>O que é um componente?

Os componentes são partes independentes dos aplicativos de microsserviços/distribuídos. As equipes de operações e desenvolvedores têm acesso ou visibilidade nível de código para telemetria gerada por esses componentes de aplicativos. 

* Os componentes são diferentes das dependências externas "observadas", como SQL, EventHub etc., a qual sua organização/equipe pode não ter acesso (código ou telemetria).
* Os componentes são executados em qualquer número de instâncias de contêiner/função/servidor.
* Os componentes podem ser chaves de instrumentação do Application Insights separadas (mesmo se as assinaturas forem diferentes) ou diferentes funções relatando para uma única chave de instrumentação do Application Insights. A experiência do mapa de visualização mostra os componentes independentemente de como eles estão configurados.

## <a name="composite-application-map-preview"></a>Mapa do Aplicativo de Composição (versão prévia)
*Essa é uma versão prévia antecipada; vamos adicionar mais recursos ao mapa. Adoraríamos receber seus comentários sobre a nova experiência. Você pode alternar facilmente entre a experiência clássica e a versão prévia.*

Habilite "Mapa do Aplicativo de Composição" na [lista de visualizações](app-insights-previews.md) ou clique em "Mapa de visualização" no botão de alternância no canto superior direito. Você pode usar essa opção para voltar para a experiência clássica.
![Habilitar mapa de visualização](media/app-insights-app-map/preview-from-classic.png)

>[!Note]
Essa visualização substitui a visualização "Mapa do Aplicativo com Várias Funções" anterior. Desta vez, use-a para exibir toda a topologia em vários níveis de dependências de componente de aplicativo. Envie seus comentários. Vamos adicionar mais recursos, semelhantes aos que são compatíveis com o mapa clássico.

Você pode ver a topologia do aplicativo completa em vários níveis de componentes de aplicativos relacionados. Os componentes podem ser recursos diferentes do Application Insights ou funções diferentes em um único recurso. O mapa do aplicativo localiza os componentes seguindo qualquer chamada de dependência HTTP feita entre os servidores com o SDK do Application Insights instalado. 

Essa experiência começa com a descoberta progressiva dos componentes. Quando você carrega a visualização pela primeira vez, um conjunto de consultas é disparado para descobrir os componentes relacionados a esse componente. Um botão no canto superior esquerdo será atualizado com o número de componentes em seu aplicativo, conforme eles são descobertos. 
![Mapa de visualização](media/app-insights-app-map/preview.png)

Ao clicar em "Atualizar componentes do mapa", o mapa será atualizado com todos os componentes descobertos até aquele momento.
![Visualizar o mapa carregado](media/app-insights-app-map/components-loaded-hierarchical.png)

Se todos os componentes são funções em um único recurso do Application Insights, essa etapa de descoberta não é necessária. A carga inicial para esse tipo de aplicativo terá todos os respectivos componentes.

Uma das principais metas da nova experiência é possibilitar a visualização de topologias complexas com centenas de componentes. A nova experiência é compatível com a aplicação de zoom e adiciona detalhes conforme você amplia. Você pode reduzir para exibir mais componentes em um relance e ainda identificar componentes com taxas de falha mais altas. 

![Níveis de zoom](media/app-insights-app-map/zoom-levels.png)

Clique em qualquer componente para ver as respectivas informações e acesse a experiência de triagem de desempenho e falha desse componente.

![Submenu](media/app-insights-app-map/preview-flyout.png)


## <a name="classic-application-map"></a>Mapa do Aplicativo Clássico

O mapa mostra:

* Testes de disponibilidade
* Componente do lado do cliente (monitorado com o SDK do JavaScript)
* Componente do lado do servidor
* Dependências dos componentes do cliente e do servidor

![mapa de aplicativos](./media/app-insights-app-map/02.png)

Você pode expandir e recolher grupos de link de dependência:

![recolher](./media/app-insights-app-map/03.png)

Se você tiver muitas dependências de um tipo (SQL, HTTP, etc.), elas poderão aparecer agrupadas. 

![dependências agrupadas](./media/app-insights-app-map/03-2.png)

## <a name="spot-problems"></a>Identificar problemas
Cada nó possui indicadores de desempenho relevantes, como as taxas de carga, de desempenho e de falha do componente. 

Ícones de aviso destacam possíveis problemas. Um aviso laranja significa que existem falhas em solicitações, exibições de página ou chamadas de dependência. Vermelho significa uma taxa de falha acima de 5%. Se você quiser ajustar esses limites, abra Opções.

![ícones de falha](./media/app-insights-app-map/04.png)

Alertas ativos também aparecem: 

![alertas ativos](./media/app-insights-app-map/05.png)

Se você usa o SQL Azure, há um ícone que mostra quando há recomendações sobre como melhorar o desempenho. 

![Recomendações do Azure](./media/app-insights-app-map/06.png)

Clique em um ícone para obter mais detalhes:

![Recomendações do Azure](./media/app-insights-app-map/07.png)

## <a name="diagnostic-click-through"></a>Clique para Diagnóstico
Cada um dos nós no mapa oferece cliques direcionados para diagnóstico. As opções variam dependendo do tipo de nó.

![opções de servidor](./media/app-insights-app-map/09.png)

Para componentes que são hospedados no Azure, as opções incluem links diretos para eles.

## <a name="filters-and-time-range"></a>Filtros e o intervalo de tempo
Por padrão, o mapa resume todos os dados disponíveis para o intervalo de tempo escolhido. Mas você pode filtrá-lo para incluir apenas nomes de operação ou dependências específicas.

* Nome da operação: isso inclui tipos de solicitação do lado servidor e exibições de página. Com essa opção, o mapa mostra o KPI no nó do lado do servidor/cliente somente para operações selecionadas. Ele mostra as dependências chamadas no contexto dessas operações específicas.
* Nome base de dependência: isso inclui as dependências de navegador do AJAX e dependências do lado do servidor. Se você relatar telemetria de dependência personalizada com a API TrackDependency, ela também será exibida aqui. Você pode selecionar quais dependências mostrar no mapa. Atualmente, essa seleção não filtra as solicitações do lado do servidor nem as exibições de página do lado do cliente.

![Definir filtros](./media/app-insights-app-map/11.png)

## <a name="save-filters"></a>Salvar filtros
Para salvar os filtros que você aplicou, fixe na exibição filtrada em um [painel](app-insights-dashboards.md).

![Fixar no painel](./media/app-insights-app-map/12.png)

## <a name="error-pane"></a>Painel de erros
Quando você clica em um nó no mapa, é exibido no lado direito um painel de erros resumindo as falhas do nó. As falhas são agrupadas primeiro segundo a ID da operação e, em seguida, segundo a ID do problema.

![Painel de erros](./media/app-insights-app-map/error-pane.png)

Clicar em uma falha leva você até a instância mais recente dessa falha.

## <a name="resource-health"></a>Integridade de recursos
Para alguns tipos de recursos, sua integridade é exibida na parte superior do painel de erros. Por exemplo, clicar em um nó do SQL mostrará a integridade do banco de dados e todos os alertas que tiverem sido disparados.

![Integridade de recursos](./media/app-insights-app-map/resource-health.png)

Você pode clicar no nome do recurso para exibir as métricas de visão geral padrão para esse recurso.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Comentários
Por favor, faça comentários por meio da opção Comentários no portal.

![Imagem de MapLink-1](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>Próximas etapas

* [portal do Azure](https://portal.azure.com)
