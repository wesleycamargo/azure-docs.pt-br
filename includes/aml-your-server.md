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
ms.openlocfilehash: 8d8b314965253dc00b39d0b068b1d6fb3e4aa471
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395613"
---
1. Use as instruções em [Criar um workspace do serviço do Azure Machine Learning](../articles/machine-learning/service/setup-create-workspace.md#portal) para criar um ambiente Miniconda, crie um workspace e grave um arquivo de configuração do workspace (**aml_config/config.json**).

1. Clone [o repositório do GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Adicione um arquivo de configuração do workspace usando qualquer um destes métodos:
    * Copie o arquivo **aml_config/config.json** que você criou usando o início rápido de pré-requisito para o diretório clonado.
    * Crie um novo espaço de trabalho usando o código em [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).
1. Inicie o servidor de notebook do seu diretório clonado.
    
    ```shell
    jupyter notebook
    ```