---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 01/25/2019
ms.openlocfilehash: 18ba86ce7876ba8275eb4853e4fc9ea0f35fa186
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302167"
---
1. Conclua o [Início rápido do Python do Azure Machine Learning](../articles/machine-learning/service/quickstart-create-workspace-with-python.md) para instalar o SDK e criar um workspace.  Fique à vontade ignorar a seção **Usar o notebook** se desejar.
1. Clone [o repositório do GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Adicione um arquivo de configuração do workspace usando qualquer um destes métodos:
    * Copie o arquivo **aml_config\config.json** que você criou usando o guia de início rápido de pré-requisito para o diretório clonado.
    * Crie um novo espaço de trabalho usando o código em [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).
1. Inicie o servidor de notebook do seu diretório clonado.
    
    ```shell
    jupyter notebook
    ```