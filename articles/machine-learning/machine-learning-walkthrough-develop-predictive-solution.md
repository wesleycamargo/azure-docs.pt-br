---
title: "Uma solução preditiva para o risco de crédito com o Machine Learning | Microsoft Docs"
description: "Um passo a passo detalhado mostrando como criar uma solução de análise preditiva para avaliação de risco de crédito no Estúdio de Aprendizado de Máquina do Azure."
keywords: "risco de crédito, solução de análise preditiva, avaliação de riscos"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 43300854-a14e-4cd2-9bb1-c55c779e0e93
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 07cb3fe0d5688d5b63fe3312cad14c2274a58a09
ms.openlocfilehash: e98a64910f28da0a8a9b4a58c717c40d791ccf00


---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Passo a passo: Desenvolver uma solução de análise preditiva para avaliação de risco de crédito no Aprendizado de Máquina do Azure

Neste passo a passo, vamos conferir de forma aprofundada o processo de desenvolvimento de uma solução no Machine Learning Studio. Vamos desenvolver um modelo de análise preditiva no Machine Learning Studio e implantá-lo como um serviço Web do Azure Machine Learning em que o modelo pode fazer previsões usando novos dados. 

> [!TIP]
> Este passo a passo pressupõe que você tenha usado o Machine Learning Studio pelo menos uma vez antes e tenha noções básicas sobre conceitos de aprendizado de máquina, embora ele pressuponha que você não seja um especialista neles.
> 
>Se você nunca usou **Azure Machine Learning Studio** antes, convém iniciar pelo tutorial [Criar seu primeiro experimento de ciência de dados no Azure Machine Learning Studio](machine-learning-create-experiment.md). Esse tutorial apresenta o Machine Learning Studio pela primeira vez, mostrando os conceitos básicos de como arrastar e soltar módulos para o experimento, conectá-los, executar o experimento e examinar os resultados.
>
>Se você é novo no machine learning, a série de vídeos [Ciência de dados para iniciantes](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) pode ser um bom lugar para começar. Esta série de vídeos é uma excelente introdução ao machine learning usando linguagem e conceitos cotidianos.
> 

## <a name="the-problem"></a>O problema

Suponha que você precise prever o risco de crédito de uma pessoa com base nas informações dadas em um aplicativo de crédito.  

A avaliação de risco de crédito é um problema complexo, mas vamos simplificar um pouco os parâmetros da pergunta. Em seguida, vamos usá-lo como um exemplo de como você pode usar o Microsoft Azure Machine Learning com o Estúdio de Aprendizado de Máquina e o serviço Web do Machine Learning para criar essa solução de análise preditiva.  

## <a name="the-solution"></a>A solução

Neste passo a passo detalhado, começaremos com os dados de risco de crédito disponíveis publicamente, desenvolveremos e treinaremos um modelo preditivo baseado nesses dados e implantaremos o modelo como um serviço Web que pode ser usado por outros para a avaliação de risco de crédito.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Para criar uma solução de avaliação de risco de crédito, vamos acompanhar estas etapas:  

1. [Criar um espaço de trabalho do Aprendizado de Máquina](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [Carregar dados existentes](machine-learning-walkthrough-2-upload-data.md)
3. [Criar um novo teste](machine-learning-walkthrough-3-create-new-experiment.md)
4. [Treinar e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [Implantar o serviço Web](machine-learning-walkthrough-5-publish-web-service.md)
6. [Acessar o serviço Web](machine-learning-walkthrough-6-access-web-service.md)

Este passo a passo se baseia em uma versão simplificada do teste de exemplo [Classificação Binária: previsão do risco de crédito](http://go.microsoft.com/fwlink/?LinkID=525270) na [Galeria do Cortana Intelligence](http://gallery.cortanaintelligence.com/).


> [!TIP]
> Para baixar e imprimir um diagrama que fornece uma visão geral dos recursos do Estúdio de Aprendizado de Máquina, consulte [Diagrama de visão geral dos recursos do Estúdio de Aprendizado de Máquina do Azure](machine-learning-studio-overview-diagram.md).
> 
> 



<!--HONumber=Dec16_HO3-->


