---
title: Estágio de aceitação do cliente do ciclo de vida do Processo de Ciência de Dados da Equipe
description: As metas, as tarefas e os resultados para o estágio de aceitação do cliente dos seus projetos de ciência de dados
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 91d645e6120040870c7c1696c7bfd8f68509cb35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303563"
---
# <a name="customer-acceptance-stage-of-the-team-data-science-process-lifecycle"></a>Estágio de aceitação do cliente do ciclo de vida do Processo de Ciência de Dados da Equipe

Este artigo descreve as metas, as tarefas e os resultados associados ao estágio de aceitação do cliente do TDSP (Processo de Ciência de Dados da Equipe). Esse processo fornece um ciclo de vida recomendado que você pode usar para estruturar seus projetos de ciência de dados. O ciclo de vida descreve os estágios principais que os projetos normalmente executam, com frequência de modo iterativo:

   1. **Noções básicas sobre negócios**
   2. **Aquisição de dados e entendimento**
   3. **Modelagem**
   4. **Implantação**
   5. **Aceitação do cliente**

Aqui está uma representação visual do ciclo de vida do TDSP: 

![Ciclo de vida do TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objetivo
**Finalizar as entregas do projeto**: Confirme se o pipeline, o modelo e a implantação em um ambiente de produção atendem aos objetivos do cliente.

## <a name="how-to-do-it"></a>Como fazer isso
Há duas tarefas principais abordadas neste estágio:

   * **Validação do Sistema**: Confirme se o modelo e o pipeline implantados atendem às necessidades do cliente.
   * **Entrega do projeto**: Entregue o projeto a uma entidade que executará o sistema na produção.

O cliente deverá validar se o sistema atende às suas necessidades de negócios e se ele responde às perguntas com uma precisão aceitável para implantar o sistema em produção para uso pelo aplicativo do cliente. Toda a documentação é finalizada e revisada. O projeto é entregue à entidade responsável por operações. Essa entidade poderá ser, por exemplo, uma equipe de TI ou de ciência de dados do cliente ou um agente do cliente responsável por executar o sistema na produção. 

## <a name="artifacts"></a>Artefatos
O principal artefato produzido neste estágio final é o **Relatório de saída do projeto para o cliente**. Este relatório técnico contém todos os detalhes do projeto úteis para saber como operar o sistema. O TDSP oferece um modelo de [Relatório de saída](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md). É possível usar o modelo como ele se encontra ou personalizá-lo para as necessidades específicas do cliente. 


## <a name="next-steps"></a>Próximos passos

Aqui estão os links para cada etapa do ciclo de vida do TDSP:

   1. [Noções básicas sobre negócios](lifecycle-business-understanding.md)
   2. [Aquisição de dados e entendimento](lifecycle-data.md)
   3. [Modelagem](lifecycle-modeling.md)
   4. [Implantação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos passo a passo completos que demonstram todas as etapas do processo para cenários específicos. O artigo [Passo a passos de exemplo](walkthroughs.md) fornece uma lista dos cenários com descrições de links e miniaturas. Os passo a passo ilustram como combinar ferramentas e serviços de nuvem e locais em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 

Para obter exemplos de como executar etapas nos TDSPs que usam o Azure Microsoft Azure Machine Learning Studio, consulte [Use the TDSP with Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) (Usar o TDSP com o Azure Machine Learning).
