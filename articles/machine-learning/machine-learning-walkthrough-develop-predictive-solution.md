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
ms.date: 09/16/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: aa64dc7f5bb3e928aac30987b0904435c603829c


---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Passo a passo: Desenvolver uma solução de análise preditiva para avaliação de risco de crédito no Aprendizado de Máquina do Azure
Suponha que você precise prever o risco de crédito de uma pessoa com base nas informações dadas em um aplicativo de crédito.  

A avaliação de risco de crédito é um problema complexo, mas vamos simplificar um pouco os parâmetros da pergunta. Em seguida, podemos usá-lo como um exemplo de como você pode usar o Aprendizado de Máquina do Microsoft Azure com o Estúdio de Aprendizado de Máquina e o serviço Web de Aprendizado de Máquina para criar essa solução de análise preditiva.  

Neste passo a passo detalhado, seguiremos o processo de desenvolvimento de um modelo de análise preditiva no Estúdio de Aprendizado de Máquina e, em seguida, implantá-lo como um serviço Web do Aprendizado de Máquina do Azure. Começaremos com os dados de risco de crédito disponíveis publicamente, desenvolveremos e treinaremos um modelo preditivo baseado nesses dados e implantaremos o modelo como um serviço Web que pode ser usado por outros para a avaliação de risco de crédito.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

<!-- -->

> [!TIP]
> Para baixar e imprimir um diagrama que fornece uma visão geral dos recursos do Estúdio de Aprendizado de Máquina, consulte [Diagrama de visão geral dos recursos do Estúdio de Aprendizado de Máquina do Azure](machine-learning-studio-overview-diagram.md).
> 
> 

Para criar uma solução de avaliação de risco de crédito, vamos acompanhar estas etapas:  

1. [Criar um espaço de trabalho do Aprendizado de Máquina](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [Carregar dados existentes](machine-learning-walkthrough-2-upload-data.md)
3. [Criar um novo teste](machine-learning-walkthrough-3-create-new-experiment.md)
4. [Treinar e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [Implantar o serviço Web](machine-learning-walkthrough-5-publish-web-service.md)
6. [Acessar o serviço Web](machine-learning-walkthrough-6-access-web-service.md)

Este passo a passo se baseia em uma versão simplificada do teste de exemplo [Classificação Binária: previsão do risco de crédito](http://go.microsoft.com/fwlink/?LinkID=525270) na [Galeria do Cortana Intelligence](http://gallery.cortanaintelligence.com/).




<!--HONumber=Nov16_HO2-->


