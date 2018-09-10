---
title: Exibir e analisar dados no Azure Log Analytics | Microsoft Docs
description: Este artigo descreve os portais que você pode usar no Azure Log Analytics para criar e editar pesquisas de logs.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: magoedte; bwren
ms.component: na
ms.openlocfilehash: 386aad94461fa3f2ceafb7564342797eefa2f086
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246357"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>Exibir e analisar dados no Log Analytics
Há duas opções disponíveis no portal do Azure para analisar dados armazenados na análise de Logs e para criar consultas de análise ad hoc. As consultas que você cria usando esses portais podem ser usadas para outros recursos, como alertas e painéis.

## <a name="log-analytics-page-preview"></a>Página do Log Analytics (versão prévia)
Abra a página do Log Analytics em **Logs (versão prévia)** no menu do Log Analytics. Isso é uma nova experiência para trabalhar com dados de log e criar consultas. Você pode obter uma apresentação para este portal e inspecionar os recursos na [Introdução à página do Log Analytics no portal do Azure](query-language/get-started-analytics-portal.md).

A página do Log Analytics fornece as seguintes melhorias ao longo de experiência [Pesquisa de logs](#log-search).

* Várias guias – crie guias separadas para trabalhar com várias consultas.
* Visualizações avançadas – variedade de opções de gráficos.
* Intellisense e preenchimento automático de linguagem aprimorado.
* Realce de sintaxe – melhora a legibilidade das consultas. 
* Gerenciador de consultas – consultas e funções de acesso salvo.
* Exibição de esquema – examine a estrutura dos dados para ajudar a escrever consultas.
* Compartilhar – crie links para consultas ou fixe consultas em qualquer painel do Azure compartilhado.
* Análise Inteligente - identifica picos em gráficos e uma rápida análise da causa.
* Seleção de coluna – classifique e agrupe colunas nos resultados da consulta.

> [!NOTE]
> A página do Log Analytics tem a mesma funcionalidade que o portal Análise Avançada que é uma ferramenta externa fora do portal do Azure. O portal Análise Avançada ainda está disponível, mas os links e outros materiais de referência a ele no portal do Azure estão sendo substituídos por esta nova página.

![Portal Análise Avançada](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


### <a name="firewall-requirements"></a>Requisitos de firewall
O navegador requer acesso aos endereços a seguir para acessar o portal do Log Analytics e o portal Análise Avançada.  Se seu navegador estiver acessando o portal do Azure por meio de um firewall, você deverá habilitar o acesso a esses endereços.

| Uri | IP | Portas |
|:---|:---|:---|
| portal.loganalytics.io | Dinâmico | 80.443 |
| api.loganalytics.io    | Dinâmico | 80.443 |
| docs.loganalytics.io   | Dinâmico | 80.443 |


## <a name="log-search"></a>Pesquisa de log
Abra a página de pesquisa de Logs em **Logs** no menu do Log Analytics ou no menu **Log Analytics** do Azure Monitor. Isso é adequado para análise de dados de log usando consultas básicas. Ela fornece vários recursos para editar consultas sem ter um conhecimento completo da linguagem de consulta.  É possível obter um resumo desses recursos em [Criar pesquisas de logs no Azure Log Analytics usando a Pesquisa de Logs](log-analytics-log-search-log-search-portal.md). 


![Página da Pesquisa de Logs](media/log-analytics-log-search-portals/log-search-portal.png)


## <a name="next-steps"></a>Próximas etapas

- Explorar um [tutorial usando Pesquisa de Logs](log-analytics-tutorial-viewdata.md) para aprender a criar consultas usando a linguagem de consulta
- Explorar uma [lição usando o portal Análise Avançada](query-language/get-started-analytics-portal.md) que fornece a mesma experiência que a página do Log Analytics.

