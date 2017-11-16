---
title: "O ciclo de vida do Processo de Ciência de Dados da Equipe – Azure | Microsoft Docs"
description: "As etapas necessárias para executar os projetos de ciência de dados"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev;
ms.openlocfilehash: 5d69d4d2371b42b3595cf3dc71d99d913e225c59
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2017
---
# <a name="the-team-data-science-process-lifecycle"></a>O ciclo de vida do Processo de Ciência de Dados de Equipe

O TDSP (Processo de Ciência de Dados da Equipe) fornece um ciclo de vida recomendado que você pode usar para estruturar seus projetos de ciência de dados. O ciclo de vida descreve as etapas, do início ao fim, que os projetos normalmente seguem quando são executados. Se usar outro ciclo de vida de ciência de dados, como [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining) (Processo Padrão para Data Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) (Descoberta de Conhecimento em Banco de Dados) ou o próprio processo personalizado da sua organização, você ainda poderá usar o TDSP baseado em tarefas. 

Esse ciclo de vida foi projetado para projetos de ciência de dados que devem ser fornecidos como parte de aplicativos inteligentes. Esses aplicativos implantam modelos de machine learning ou de inteligência artificial para análise preditiva. Os projetos de ciência de dados exploratórios e os projetos de análise ad hoc também podem se beneficiar do uso desse processo. Mas, para esses projetos, algumas das etapas descritas aqui podem não ser necessárias. 

O ciclo de vida do TDSP é composto por cinco estágios principais executados de forma iterativa. Esses estágios incluem:

   1. [Noções básicas sobre negócios](lifecycle-business-understanding.md)
   2. [Aquisição de dados e entendimento](lifecycle-data.md)
   3. [Modelagem](lifecycle-modeling.md)
   4. [Implantação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Aqui está uma representação visual do ciclo de vida do TDSP: 

![Ciclo de vida do TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


O ciclo de vida do TDSP é modelado como uma sequência de etapas iteradas que fornecem diretrizes sobre as tarefas necessárias para usar os modelos preditivos. Implante os modelos preditivos no ambiente de produção que você planeja usar para criar aplicativos inteligentes. A meta deste ciclo de vida do processo é dar continuidade a um projeto de ciência de dados em direção a um ponto final claro de engajamento. A ciência de dados é um exercício de pesquisa e descoberta. A capacidade de comunicar tarefas para sua equipe e seus clientes usando um conjunto bem definido de artefatos que empregam modelos padronizados ajuda a evitar confusão. Usar esses modelos também aumenta a chance da concluir um projeto de ciência de dados complexo com êxito.

Para cada estágio, fornecemos as seguintes informações:

   * **Metas**: os objetivos específicos.
   * **Como fazer isso**: uma estrutura de tópicos de tarefas específicas e diretrizes sobre como conclui-las.
   * **Artefatos**: as entregas e o suporte para produzi-las.

## <a name="next-steps"></a>Próximas etapas

Fornecemos passo a passo completos que demonstram todas as etapas do processo para cenários específicos. O artigo [Passo a passos de exemplo](walkthroughs.md) fornece uma lista dos cenários com descrições de links e miniaturas. Os passo a passo ilustram como combinar ferramentas e serviços de nuvem e locais em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 

Para obter exemplos de como executar etapas nos TDSPs que usam o Azure Microsoft Azure Machine Learning Studio, consulte [Use the TDSP with Azure Machine Learning](http://aka.ms/datascienceprocess) (Usar o TDSP com o Azure Machine Learning).
