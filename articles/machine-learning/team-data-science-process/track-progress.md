---
title: "Execução de projetos de ciência de dados – Azure Machine Learning | Microsoft Docs"
description: "Como um cientista de dados pode acompanhar o progresso de um projeto de ciência de dados."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: bradsev;
ms.openlocfilehash: c8e1882570a8cfcb4a75f0904a2138d007e0bc5b
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2017
---
# <a name="track-progress-of-data-science-projects"></a>Controlar o progresso de projetos de ciência de dados

Os gerentes de grupos de Ciência de Dados, líderes de equipe e líderes de projeto precisam acompanhar o progresso de seus projetos de equipe, o trabalho que foi feito neles e por quem e o que permaneceu na lista de tarefas pendentes. 

## <a name="vsts-dashboards"></a>Painéis do VSTS
Se você estiver usando o Visual Studio Team Services (VSTS), é possível criar painéis para acompanhar as atividades e os itens de trabalho associados a um determinado projeto do Agile. 

Para saber mais sobre como criar e personalizar os painéis e widgets no Visual Studio Team Services, veja os seguintes conjuntos de instruções:

- [Adicionar e gerenciar painéis](https://docs.microsoft.com/vsts/report/dashboards/dashboards)
- [Adicione widgets a um painel](https://docs.microsoft.com/vsts/report/dashboards/add-widget-to-dashboard).

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

Para obter uma descrição de como criar esses gráficos, veja os guias de início rápido e os tutoriais em [Painéis](https://docs.microsoft.com/vsts/report/dashboards/).
 
## <a name="next-steps"></a>Próximas etapas

Também são fornecidas instruções passo a passo que demonstram todas as etapas do processo para **cenários específicos**. Eles serão listados e vinculados a descrições em miniatura no artigo [Instruções passo a passo de exemplo](walkthroughs.md). Eles ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 
