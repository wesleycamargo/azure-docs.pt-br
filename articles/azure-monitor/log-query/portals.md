---
title: Exibir e analisar dados de log no Azure Monitor | Microsoft Docs
description: Este artigo descreve como usar o Log Analytics no portal do Azure para criar e editar consultas de log no Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: bwren
ms.openlocfilehash: 0e5b9b43e528b37fd994f9131f145abadb33c53b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798469"
---
# <a name="viewing-and-analyzing-log-data-in-azure-monitor"></a>Exibir e analisar dados de log no Azure Monitor
O Log Analytics é a experiência principal para trabalhar com os dados de log e criar consultas no Azure Monitor. Abra o Log Analytics em **Logs** no menu do **Azure Monitor**. Você pode obter uma apresentação para este portal e inspecionar os recursos na [Introdução ao Log Analytics no portal do Azure](get-started-portal.md).

O Log Analytics fornece os seguintes recursos para trabalhar com consultas de log.

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
> O Log Analytics tem a mesma funcionalidade que o portal Análise Avançada que é uma ferramenta externa fora do portal do Azure. O portal Análise Avançada ainda está disponível, mas os links e outros materiais de referência a ele no portal do Azure estão sendo substituídos por esta nova página.

![Log Analytics](media/portals/log-analytics.png)

## <a name="resource-logs"></a>Logs de recursos
O Log Analytics integra-se com diversos recursos do Azure como máquinas virtuais. Isso significa que você pode abrir o Log Analytics diretamente por meio do menu de monitoramento do recurso sem alternar para o Azure Monitor e perder o contexto do recurso. **Logs** ainda não foi habilitada para todos os recursos do Azure, mas ela começará a aparecer no menu do portal para tipos de recursos diferentes.

Ao abrir o Log Analytics de um recurso específico, ele é analisado automaticamente para fazer registros somente desse recurso.   Se você quiser escrever uma consulta que inclua outros registros, precisará abri-la no menu do Azure Monitor.

As opções a seguir ainda não estão disponíveis por meio do modo de exibição de recursos do Log Analytics:

- Salvar
- Definir alerta
- Gerenciador de consultas
- Alternar para o workspace/recurso diferente (não planejado no momento)


## <a name="firewall-requirements"></a>Requisitos de firewall
Seu navegador requer acesso aos endereços a seguir para acessar o Log Analytics.  Se seu navegador estiver acessando o portal do Azure por meio de um firewall, você deverá habilitar o acesso a esses endereços.

| Uri | IP | Portas |
|:---|:---|:---|
| portal.loganalytics.io | Dinâmico | 80.443 |
| api.loganalytics.io    | Dinâmico | 80.443 |
| docs.loganalytics.io   | Dinâmico | 80.443 |


## <a name="next-steps"></a>Próximas etapas

- Percorra um [tutorial usando o Log Analytics](../../azure-monitor/log-query/get-started-portal.md).
- Percorra um [tutorial usando a Pesquisa de logs](../../azure-monitor/learn/tutorial-viewdata.md).

