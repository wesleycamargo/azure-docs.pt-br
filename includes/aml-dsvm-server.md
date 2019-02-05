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
ms.openlocfilehash: 12d1576c3bfbf96c0445fcd2a6f0bc37d6a68f11
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302166"
---
1. Conclua o [Início rápido do Python do Azure Machine Learning](../articles/machine-learning/service/quickstart-create-workspace-with-python.md) para criar um workspace.  Fique à vontade ignorar a seção **Usar o notebook** se desejar.
1. Clone [o repositório do GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
1. Adicione um arquivo de configuração do workspace usando qualquer um destes métodos:
    * Copie o arquivo **aml_config\config.json** que você criou usando o guia de início rápido de pré-requisito para o diretório clonado.
    * Criar um novo workspace usando o código no notebook [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) em seu diretório clonado.
1. Inicie o servidor de notebook do seu diretório clonado.
    
    ```shell
    jupyter notebook
    ```