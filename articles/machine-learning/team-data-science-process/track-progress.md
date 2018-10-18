---
title: Execução de projetos de ciência de dados – Azure Machine Learning | Microsoft Docs
description: Como um cientista de dados pode acompanhar o progresso de um projeto de ciência de dados.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: deguhath
ms.openlocfilehash: 32390b05d2ec258a68ed4f53135399675105a7e9
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302078"
---
# <a name="track-progress-of-data-science-projects"></a>Controlar o progresso de projetos de ciência de dados

Os gerentes de grupos de Ciência de Dados, líderes de equipe e líderes de projeto precisam acompanhar o progresso de seus projetos, o trabalho que foi feito neles e por quem e o que permaneceu na lista de tarefas pendentes. 

## <a name="azure-devops-dashboards"></a>Dashboards do Azure DevOps
Se você estiver usando o Azure DevOps, será possível criar dashboards para acompanhar as atividades e os itens de trabalho associados a um determinado projeto do Agile. 

Para saber mais sobre como criar e personalizar os dashboards e widgets no Azure DevOps, veja os seguintes conjuntos de instruções:

- [Adicionar e gerenciar painéis](https://docs.microsoft.com/azure/devops/report/dashboards/dashboards)
- [Adicione widgets a um painel](https://docs.microsoft.com/azure/devops/report/dashboards/add-widget-to-dashboard).

## <a name="example-dashboard"></a>Painel Exemplo

Aqui está um painel de exemplo simples que é criado para acompanhar as atividades de um projeto de ciência de dados do Agile, bem como o número de confirmações de sprint para repositórios associados. O painel **superior esquerdo** mostra:

- a contagem regressiva do sprint atual, 
- o número de confirmações para cada repositório nos últimos sete dias
- o item de trabalho para usuários específicos. 

Os painéis restantes mostram o diagrama de fluxo cumulativo (CFD), o burndown e o burnup para um projeto:

- **Inferior esquerdo**: CFD a quantidade de trabalho em um determinado estado, mostrando os aprovados em cinza, os confirmados em azul e os concluídos em verde.
- **Superior direito**: gráfico de burndown o trabalho que resta concluir versus o tento restante).
- **Inferior direito**: gráfico de burnup o trabalho concluído versus a quantidade total de trabalho.

![painel Transações da Web](./media/track-progress/dashboard.png)

Para obter uma descrição de como criar esses gráficos, veja os guias de início rápido e os tutoriais em [Painéis](https://docs.microsoft.com/azure/devops/report/dashboards/).
 
## <a name="next-steps"></a>Próximas etapas

Também são fornecidas instruções passo a passo que demonstram todas as etapas do processo para **cenários específicos**. Eles estão listados e vinculados a descrições em miniatura no artigo [Instruções passo a passo de exemplo](walkthroughs.md). Eles ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 
