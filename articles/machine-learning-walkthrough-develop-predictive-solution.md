<properties title="Develop a predictive solution with Azure Machine Learning" pageTitle="Develop a predictive solution with Machine Learning | Azure" description="Walkthrough of how to create a predictive analytics experiment in Azure Machine Learning Studio" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Desenvolver uma solução preditiva com o Aprendizado de Máquina do Azure

Suponha que você precise prever o risco de crédito de uma pessoa com base nas informações dadas em um aplicativo de crédito.

É um problema complexo, claro, mas vamos simplificar os parâmetros da questão um pouco e usá-la como um exemplo de como é possível usar o Aprendizado de Máquina do Microsoft Azure com o Estúdio AM e o serviço da API de MA para criar essa solução de análise preditiva.

Neste passo a passo, seguiremos o processo de desenvolvimento de um modelo de análise preditiva no Estúdio AM e de publicação no serviço da API do AM. Começaremos com dados de risco de crédito disponíveis publicamente, desenvolveremos e treinaremos um modelo preditivo baseado nesses dados e publicaremos o modelo como um serviço Web que pode ser usado por outros.

Seguiremos estas etapas:

1.  [Criar um espaço de trabalho do AM][]
2.  [Fazer upload de dados existentes][]
3.  [Criar um novo experimento][]
4.  [Treinar e avaliar os modelos][]
5.  [Publicar o serviço Web][]
6.  [Acessar o serviço Web][]

Este passo a passo é baseado em uma versão simplificada do
[Experimento de amostra de previsão de risco de crédito][] incluído com o Estúdio AM.

  [Criar um espaço de trabalho do AM]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [Fazer upload de dados existentes]: ../machine-learning-walkthrough-2-upload-data/
  [Criar um novo experimento]: ../machine-learning-walkthrough-3-create-new-experiment/
  [Treinar e avaliar os modelos]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [Publicar o serviço Web]: ../machine-learning-walkthrough-5-publish-web-service/
  [Acessar o serviço Web]: ../machine-learning-walkthrough-6-access-web-service/
  [Experimento de amostra de previsão de risco de crédito]: ../machine-learning-sample-credit-risk-prediction/
