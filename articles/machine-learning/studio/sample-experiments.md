---
title: Cria experimentos do Machine Learning Studio a partir de exemplos - Azure | Microsoft Docs
description: Saiba como usar os experimentos de amostra do machine learning para criar novos experimentos com a Galeria de IA do Azure e o Azure Machine Learning Studio.
keywords: exemplos de machine learning, experiência de exemplo, amostra de machine learning, exemplos de IA
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 81e6c1d8-682c-4db3-bfd5-d7bfb1150ff3
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.openlocfilehash: 568732c5a1d2abbb9f304b624d885b2a3c692706
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52306673"
---
# <a name="create-machine-learning-experiments-from-working-examples-in-azure-ai-gallery"></a>Criar experiências de machine learning desde exemplos de trabalho na Galeria de IA do Azure

Saiba como usar os experimentos de exemplo na [Galeria de IA do Azure](https://gallery.cortanaintelligence.com/), em vez de criar experimentos de machine learning a partir do zero. Use os exemplos para compilar sua própria solução de machine learning.

A galeria contém experimentos de exemplo da equipe de Microsoft Azure Machine Learning, bem como exemplos compartilhados pela comunidade de Machine Learning. Você pode fazer perguntas ou postar comentários sobre os experimentos

Para saber como usar a galeria, assista ao vídeo de 3 minutos [Copiar o trabalho de outras pessoas para fazer a ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) da série [Ciência de Dados para Iniciantes](data-science-for-beginners-the-5-questions-data-science-answers.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="find-an-experiment-to-copy-in-azure-ai-gallery"></a>Localizar uma experiência para copiar na Galeria de IA do Azure
Para ver quais experimentos estão disponíveis, vá para a [Galeria](https://gallery.cortanaintelligence.com/) e clique em **Experimentos** na parte superior da página.

### <a name="find-the-newest-or-most-popular-experiments"></a>Localizar os experimentos mais recentes ou mais populares
Nessa página, você pode ver os experimentos **Adicionados recentemente**, descer para examinar **O que é popular** ou os **Testes populares da Microsoft** mais recentes.

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>Procurar um experimento que atenda aos requisitos específicos
Para procurar todos os experimentos:

1. Clique em **Procurar tudo** na parte superior da página.
2. No lado esquerdo, em **Refinar por**, na seção **Categorias**, selecione **Experimento** para ver todos os experimentos na Galeria.
3. Você pode encontrar os experimentos que atendem aos seus requisitos de duas maneiras diferentes:
   * **Selecione os filtros à esquerda.** Por exemplo, para procurar experimentos que usem um algoritmo de detecção de anomalias baseado em PCA: em **Categorias**, clique em **Experimento**. Em seguida, em **Algoritmos Usados**, clique em **Mostrar tudo** e, na caixa de diálogo, escolha **Detecção de Anomalias Baseada em PCA**. Talvez seja necessário rolar para vê-lo.<br></br>
     ![Selecionar filtros](./media/sample-experiments/choose-an-algorithm.png)
   * **Use a caixa de pesquisa.**  Por exemplo, para encontrar os experimentos fornecidos pela Microsoft relacionados ao reconhecimento de dígito e que usam um algoritmo de máquina do vetor com suporte de duas classes, digite "reconhecimento de dígito" na caixa de pesquisa. Em seguida, selecione os filtros **Experimento**, **Apenas o conteúdo da Microsoft** e **Máquina do Vetor com Suporte de Duas Classes**:<br></br>
     ![Usar a caixa de pesquisa](./media/sample-experiments/search-for-experiments.png)
4. Clique em um experimento para saber mais sobre ele.
5. Para executar e/ou modificar o experimento, clique em **Abrir no Estúdio** na página do experimento. <br></br>

    ![Teste de exemplo](./media/sample-experiments/example-experiment.png)

    > [!NOTE]
    > Quando abre um experimento no Machine Learning Studio pela primeira vez, você pode experimentar gratuitamente ou comprar uma assinatura do Azure. [Saiba mais sobre a avaliação gratuita do Machine Learning Studio versus serviço pago](https://azure.microsoft.com/pricing/details/machine-learning/)
    >
    >

## <a name="create-a-new-experiment-using-an-example-as-a-template"></a>Criar um novo experimento usando um exemplo como modelo
Você também pode criar um novo experimento no Machine Learning Studio usando um exemplo da galeria como modelo.

1. Entre com suas credenciais de conta da Microsoft no [Estúdio](https://studio.azureml.net) e clique em **Novo** para criar um experimento.
2. Navegue pelo conteúdo de exemplo e clique em um.

Um novo experimento é criado em seu workspace de Machine Learning usando o experimento de exemplo como modelo.

## <a name="next-steps"></a>Próximas etapas
* [Importar dados de várias fontes](import-data.md)
* [Tutorial de início rápido para a linguagem R no Machine Learning](r-quickstart.md)
* [Implantar um serviço Web do Machine Learning](publish-a-machine-learning-web-service.md)
