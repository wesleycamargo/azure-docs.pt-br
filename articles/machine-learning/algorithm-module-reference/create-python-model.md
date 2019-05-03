---
title: 'Crie modelo de Python: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o modelo de criar o modelo de Python no serviço Azure Machine Learning para criar o módulo personalizado de modelagem ou processamento de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ea9b50cede3e2d264ca0476b6a987ca6896c3c79
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029739"
---
# <a name="create-python-model"></a>Criar modelo de Python

Este artigo descreve como usar o **criar um modelo de Python** módulo para criar um modelo treinado de um script do Python. 

Você pode basear o modelo em qualquer aprendiz que está incluído em um pacote do Python no ambiente do Azure Machine Learning. 

Depois de criar o modelo, você pode usar [treinar modelo](train-model.md) para treinar o modelo em um conjunto de dados, como qualquer outro aprendiz no Azure Machine Learning. O modelo treinado pode ser passado para [modelo de pontuação](score-model.md) para usar o modelo para fazer previsões. O modelo treinado, em seguida, pode ser salvos e o fluxo de trabalho de pontuação pode ser publicado como um serviço web.

> [!WARNING]
> Atualmente, não é possível passar os resultados de pontuação de um modelo de Python para [avaliar modelo](evaluate-model.md). Se você precisar avaliar um modelo, você pode escrever script de Python personalizado e executá-lo usando o [Executar Script Python](execute-python-script.md) módulo.  


## <a name="how-to-configure-create-python-model"></a>Como configurar criar modelo de Python

Use este módulo requer conhecimento intermediário ou especialista do Python. O módulo suporta o uso de qualquer aprendiz que está incluído nos pacotes Python já está instalados no Azure Machine Learning. Consulte a lista de pacotes pré-instalados do Python no [Executar Script Python](execute-python-script.md).
  

Este artigo mostra como usar o **criar um modelo de Python** com um teste simples. Abaixo está o grafo do experimento.

![create-python-model](./media/module/aml-create-python-model.png)

1.  Clique em **criar um modelo de Python**, edite o script para implementar seu processo de modelagem ou o processo de gerenciamento de dados. Você pode basear o modelo em qualquer aprendiz que está incluído em um pacote do Python no ambiente do Azure Machine Learning.


    Abaixo está um código de exemplo do classificador Naive Bayes de duas classes usando o popular *sklearn* pacote.

```Python

# The script MUST define a class named AzureMLModel.
# This class MUST at least define the following three methods:
    # __init__: in which self.model must be assigned,
    # train: which trains self.model, the two input arguments must be pandas DataFrame,
    # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
# The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


import pandas as pd
from sklearn.naive_bayes import GaussianNB


class AzureMLModel:
    def __init__(self):
        self.model = GaussianNB()
        self.feature_column_names = list()

    def train(self, df_train, df_label):
        self.feature_column_names = df_train.columns.tolist()
        self.model.fit(df_train, df_label)

    def predict(self, df):
        return pd.DataFrame(
            {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
             'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
        )


```


2. Conectar-se a **criar um modelo de Python** módulo que você acabou de criar para um **modelo de treinamento** e **modelo de pontuação**

3. Se você precisa avaliar o modelo, adicione uma [Executar Script Python](execute-python-script.md) e edite o script de Python para implementar a avaliação.

Abaixo está o exemplo de código de avaliação.

```Python


# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to 
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):
    
    from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
    import pandas as pd
    import numpy as np
    
    scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
    ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
    ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
    probabilities = scores["probabilities"]
    
    accuracy, precision, recall, auc = \
    accuracy_score(ytrue, ypred),\
    precision_score(ytrue, ypred),\
    recall_score(ytrue, ypred),\
    roc_auc_score(ytrue, probabilities)
    
    metrics = pd.DataFrame();
    metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
    metrics["Value"] = [accuracy, precision, recall, auc]
    
    return metrics,

```
