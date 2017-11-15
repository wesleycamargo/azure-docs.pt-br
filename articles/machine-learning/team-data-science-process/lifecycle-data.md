---
title: "Estágio de aquisição de dados e entendimento do ciclo de vida do Processo de Ciência de Dados da Equipe – Azure | Microsoft Docs"
description: "As metas, as tarefas e os resultados para o estágio de aquisição de dados e entendimento dos seus projetos de ciência de dados"
services: machine-learning
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
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: bfdc6339d8247f3acdf3b7797035e155ef0c1fd1
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2017
---
# <a name="data-acquisition-and-understanding"></a>Compreensão e aquisição de dados

Este artigo descreve as metas, as tarefas e os resultados associados ao estágio de aquisição de dados e entendimento do TDSP (Processo de Ciência de Dados da Equipe). Esse processo fornece um ciclo de vida recomendado que você pode usar para estruturar seus projetos de ciência de dados. O ciclo de vida descreve os estágios principais que os projetos normalmente executam, com frequência de modo iterativo:

   1. **Noções básicas sobre negócios**
   2. **Aquisição de dados e entendimento**
   3. **Modelagem**
   4. **Implantação**
   5. **Aceitação do cliente**

Aqui está uma representação visual do ciclo de vida do TDSP: 

![Ciclo de vida do TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Metas
* Produza um conjunto de dados limpo e de alta qualidade cuja relação com as variáveis de destino seja compreendida. Localize o conjunto de dados no ambiente de análise apropriado para você estar pronto para o modelo.
* Desenvolva uma arquitetura da solução do pipeline de dados que atualize e pontua os dados regularmente.

## <a name="how-to-do-it"></a>Como fazer isso
Há três tarefas principais abordadas neste estágio:

   * **Ingestão dos dados** no ambiente de análise da meta.
   * **Exploração dos dados** para determinar se a qualidade dos dados é adequada para responder à pergunta. 
   * **Configuração de um pipeline de dados** para pontuar dados novos ou atualizados regularmente.

### <a name="ingest-the-data"></a>Ingerir os dados
Configure o processo para mover os dados dos locais de origem para os locais de destino em que você executa operações de análise, como treinamento e previsões. Para obter detalhes técnicos e opções sobre como mover os dados com vários serviços de dados do Azure, consulte [Carregar dados em ambientes de armazenamento para análise](ingest-data.md). 

### <a name="explore-the-data"></a>Explorar os dados
Antes de treinar seus modelos, você precisa desenvolver uma compreensão total dos dados. Em geral, conjuntos de dados do mundo real apresentam ruído, são valores ausentes ou têm uma série de outras discrepâncias. É possível usar o resumo e a visualização de dados para auditar a qualidade dos dados e fornecer as informações necessárias para processá-los antes que estejam prontos para modelagem. Esse processo costuma ser iterativo.

O TDSP fornece um utilitário automatizado chamado [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) para ajudar a visualizar os dados e preparar relatórios de resumo de dados. Recomendamos que você comece com o IDEAR primeiro para explorar os dados a fim de ajudar a desenvolver uma compreensão inicial dos dados de forma interativa sem nenhuma codificação. Em seguida, é possível escrever código personalizado para exploração e visualização de dados. Para obter diretrizes sobre como limpar os dados, consulte [Tarefas para preparar dados para o machine learning avançado](prepare-data.md).  

Depois que você estiver satisfeito com a qualidade dos dados limpos, a próxima etapa será entender melhor os padrões inerentes aos dados. Isso ajuda você a escolher e desenvolver um modelo preditivo apropriado para seu destino. Procure saber até que medida os dados estão bem conectados com o destino. Em seguida, determine se há dados suficientes para passar para as próximas etapas de modelagem. Novamente, esse processo costuma ser iterativo. Talvez você precise encontrar novas fontes de dados que tenham dados mais precisos ou mais relevantes para aumentar o conjunto de dados inicialmente identificado no estágio anterior. 

### <a name="set-up-a-data-pipeline"></a>Configurar um pipeline de dados
Além da ingestão inicial e limpeza dos dados, normalmente, você precisa configurar um processo para pontuar novos dados ou atualizar os dados regularmente como parte de um processo de aprendizado contínuo. Faça isso configurando um pipeline de dados ou um fluxo de trabalho. O artigo [Mover dados de uma instância do SQL Server local para um Banco de Dados SQL do Azure com Azure Data Factory](move-sql-azure-adf.md) oferece um exemplo de como configurar um pipeline com o [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

Neste estágio, você desenvolverá uma arquitetura da solução do pipeline de dados. Você desenvolverá o pipeline em paralelo com o próximo estágio do projeto de ciência de dados. Dependendo das suas necessidades de negócios e das restrições dos seus sistemas existentes aos quais esta solução está sendo integrada, o pipeline poderá ser um dos seguintes: 

   * Baseado em lote
   * Streaming ou tempo real 
   * Um híbrido 

## <a name="artifacts"></a>Artefatos
Veja a seguir os resultados deste estágio:

   * [Relatório de qualidade de dados](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): este relatório inclui resumos de dados, as relações entre cada atributo e o destino, classificação de variável e mais. A ferramenta [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) fornecida como parte do TDSP pode gerar rapidamente este relatório em qualquer conjunto de dados de tabela, como um arquivo CSV ou uma tabela relacional. 
   * **Arquitetura da solução**: a arquitetura da solução pode ser um diagrama ou uma descrição do pipeline de dados usado para executar a pontuação ou previsões sobre novos dados após a criação de um modelo. Também contém o pipeline para treinar novamente seu modelo com base nos novos dados. Armazene o documento no diretório [Projeto](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) quando você usar o modelo de estrutura de diretório do TDSP.
   * **Decisão de ponto de verificação**: antes de começar a engenharia de recursos completos e a criação do modelo, é possível reavaliar o projeto para determinar se o valor esperado é suficiente para continuar investindo nele. Por exemplo, talvez você esteja pronto para continuar, precise coletar mais dados ou abandone o projeto, pois não existem dados para responder à pergunta.

## <a name="next-steps"></a>Próximas etapas

Aqui estão os links para cada etapa do ciclo de vida do TDSP:

   1. [Noções básicas sobre negócios](lifecycle-business-understanding.md)
   2. [Aquisição de dados e entendimento](lifecycle-data.md)
   3. [Modelagem](lifecycle-modeling.md)
   4. [Implantação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos passo a passo completos que demonstram todas as etapas do processo para cenários específicos. O artigo [Passo a passos de exemplo](walkthroughs.md) fornece uma lista dos cenários com descrições de links e miniaturas. Os passo a passo ilustram como combinar ferramentas e serviços de nuvem e locais em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 

Para obter exemplos de como executar etapas nos TDSPs que usam o Azure Microsoft Azure Machine Learning Studio, consulte [Use the TDSP with Azure Machine Learning](http://aka.ms/datascienceprocess) (Usar o TDSP com o Azure Machine Learning).
