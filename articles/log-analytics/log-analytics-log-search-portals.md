---
title: Portais para criar e editar consultas de log no Azure Log Analytics | Microsoft Docs
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
ms.date: 06/11/2018
ms.author: magoedte; bwren
ms.component: na
ms.openlocfilehash: e2ea0bf1fb3f1c63f4e6f037e465e8fdfd9a4374
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133188"
---
# <a name="portals-for-creating-and-editing-log-queries-in-azure-log-analytics"></a>Portais para criar e editar consultas de log no Azure Log Analytics

Você pode usar pesquisas de logs em vários locais pelo Log Analytics para recuperar dados do espaço de trabalho.  No entanto, para realmente criar e editar consultas, além de trabalhar interativamente com os dados retornados, você tem duas opções que são descritas abaixo.  

## <a name="log-search"></a>Pesquisa de log 
A página Pesquisa de Logs pode ser acessada no Portal do Azure.  Ele é adequado para criar consultas básicas que podem ser criadas em uma única linha.  A Pesquisa de Logs pode ser usada sem iniciar um portal externo e você pode usá-la para executar uma variedade de funções com pesquisas de logs, incluindo a criação de regras de alerta, a criação de grupos de computadores e a exportação dos resultados da consulta.  

A Pesquisa de Logs fornece vários recursos para editar a consulta sem ter um conhecimento completo da linguagem de consulta.  É possível obter um resumo desses recursos em [Criar pesquisas de logs no Azure Log Analytics usando a Pesquisa de Logs](log-analytics-log-search-log-search-portal.md).


![Página da Pesquisa de Logs](media/log-analytics-log-search-portals/log-search-portal.png)

## <a name="advanced-analytics-portal"></a>Portal Análise Avançada
O portal Análise Avançada é um portal dedicado que fornece funcionalidade avançada não disponível na Pesquisa de Logs do Portal do Azure.  Os recursos incluem a capacidade de editar uma consulta em várias linhas e de executar código seletivamente, além do IntelliSense sensível ao contexto e da análise inteligente.  O portal Análise Avançada é mais adequado para a criação de consultas complexas, que são salvas como uma pesquisa de logs ou então copiadas e coladas em outros elementos do Log Analytics.  Inicie o portal Análise Avançada de um link na página Pesquisa de Logs.

![Portal Análise Avançada](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


Devido a seus recursos avançados, normalmente você usará o portal Análise Avançada como a principal ferramenta para criar e editar consultas.  Depois de determinar se a consulta funciona conforme o esperado, copie-a e cole-a em outro lugar, como a página Pesquisa de Logs ou o Designer de Exibição.  

### <a name="firewall-requirements"></a>Requisitos de firewall
Seu navegador requer acesso aos endereços a seguir para acessar o portal de Advanced Analytics.  Se seu navegador estiver acessando o portal do Azure por meio de um firewall, você deverá habilitar o acesso a esses endereços.

| Uri | IP | Portas |
|:---|:---|:---|
| portal.loganalytics.io | Dinâmico | 80.443 |
| api.loganalytics.io    | Dinâmico | 80.443 |
| docs.loganalytics.io   | Dinâmico | 80.443 |


## <a name="next-steps"></a>Próximas etapas

- Percorra um tutorial sobre como usar a [Pesquisa de Logs](log-analytics-tutorial-viewdata.md) para aprender a criar consultas usando a linguagem de consulta
- Faça check-out no [portal Análises Avançadas](https://go.microsoft.com/fwlink/?linkid=856587) para criar consultas sofisticadas e usar como um ambiente de desenvolvimento para suas pesquisas de logs.

