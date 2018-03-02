---
title: Assistente de Fonte de Dados do azure para o Azure Machine Learning | Microsoft Docs
description: Explica o assistente de fonte de dados do workbench AML
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: dd603f07c20811543e07b21683b065bc873786a5
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="data-source-wizard"></a>Assistente de Fonte de Dados #

O Assistente de Fonte de Dados é uma maneira rápida e fácil de levar um conjunto de dados para o Azure ML Workbench sem código. É onde você também pode selecionar uma estratégia de exemplo para o conjunto de dados e os tipos de dados para cada coluna. 

## <a name="step-1-trigger-the-data-source-wizard"></a>Etapa 1: disparar o assistente de fonte de dados ## 

Levar dados para um projeto usando o assistente de fonte de dados. Selecione o botão **+** ao lado da caixa de pesquisa na exibição de dados e escolha Adicionar Fonte de Dados. 

![adicionar fonte de dados](media/data-source-wizard/add-data-source.png)

## <a name="step-2-select-where-data-is-stored"></a>Etapa 2: Selecionar o local para armazenar os dados ##
Primeiro, especifique como os dados estão no momento. Eles podem estar armazenados em um arquivo simples ou em um diretório, um arquivo parquet, um arquivo do Excel ou um banco de dados. Para obter mais informações, consulte [Fontes de dados com suporte](data-prep-appendix2-supported-data-sources.md).

![etapa 1](media/data-source-wizard/step1.png)

## <a name="step-3-select-data-file"></a>Etapa 3: Selecionar o arquivo de dados ##
Para um arquivo/diretório, especifique o caminho do arquivo. Escolha na lista suspensa o local dos dados – pode ser um caminho de arquivo local ou um Armazenamento de Blobs do Azure. 

Especifique o caminho digitando-o ou clicando no botão **Procurar…** para procurá-lo. Você pode procurar um diretório ou um ou mais arquivos.

Clique em **Concluir** para aceitar os padrões para o restante das etapas ou em Avançar para prosseguir para a próxima etapa.


![etapa 4](media/data-source-wizard/step2.png)

## <a name="step-4-choose-file-parameters"></a>Etapa 4: Escolher parâmetros de arquivo ##

O Assistente de Fonte de Dados pode detectar automaticamente o tipo de arquivo, os separadores e a codificação. Também mostraria um exemplo da aparência dos dados. Você também pode alterar qualquer um desses parâmetros manualmente. 

![etapa 5](media/data-source-wizard/step3.png)

## <a name="step-5-set-data-types-for-columns"></a>Etapa 5: Definir tipos de dados para colunas ##

O Assistente de Fonte de Dados detecta automaticamente os tipos de dados das colunas do conjunto de dados. Se perder um ou você desejar impor um tipo de dados, você poderá alterar manualmente o tipo de dados. A coluna **EXEMPLO DE DADOS DE SAÍDA** mostra exemplos de como os dados se parecem.

Para colunas onde a preparação de dados infere para conter datas, você pode ser solicitado a selecionar a ordem do mês e dia no formato da data. Por exemplo, 1/2/2013 pode representar 2 de janeiro (para isso, selecione *Mês-Dia*) ou 1 de fevereiro (selecione *Dia-Mês*).

![etapa 6](media/data-source-wizard/step4.png)

## <a name="step-6-choose-sampling-strategy-for-data"></a>Etapa 6: Escolher a estratégia de amostragem de dados ##

Você pode especificar uma ou mais estratégias de amostragem para o conjunto de dados e escolher uma como a estratégia ativa. O padrão é carregar as 10.000 linhas principais. Você pode editar este exemplo clicando no botão **Editar** na barra de ferramentas ou adicionar uma nova estratégia clicando em +Novo. As estratégias atualmente com suporte são

-     Número máximo de linhas
-     Número aleatório de linhas
-     Percentual aleatório de linhas
-     Arquivo completo

Você pode especificar quantas estratégias de amostragem desejar, mas apenas uma pode ser definida como ativa durante a preparação de dados. Você pode definir qualquer estratégia como ativa selecionando-a e clicando em Definir como Ativa na barra de ferramentas.

Dependendo da origem dos dados, algumas estratégias de exemplo podem não ter suporte. Para obter mais informações sobre amostragem, consulte a seção de amostragem [neste documento](data-prep-user-guide.md) 

![etapa 6](media/data-source-wizard/step5.png)

## <a name="step-7-path-column-handling"></a>Etapa 7: Tratamento de coluna de caminho ##

Se o caminho do arquivo incluir dados importantes, você poderá escolher incluí-los como a primeira coluna no conjunto de dados. Essa opção seria útil se você estivesse trazendo vários arquivos. Caso contrário, você pode optar por não incluí-lo.

![etapa 7](media/data-source-wizard/step6.png)

Depois de clicar em Concluir, uma nova fonte de dados será adicionada ao projeto. Você pode encontrá-la no grupo Fontes de Dados na Exibição de Dados ou como um arquivo .dsource na **Exibição de Arquivo**.
