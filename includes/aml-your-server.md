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
ms.openlocfilehash: 8d21e41ad487ad17598f2320fab5eebae02309e8
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021716"
---
1. Use as instruções em [Criar um espaço de trabalho do Serviço do Azure Machine Learning](../articles/machine-learning/service/setup-create-workspace.md#portal) para:
    * Criar um ambiente Miniconda
    * Instalar o SDK do Azure Machine Learning para Python
    * Criar um workspace
    * Gravar um arquivo de configuração do espaço de trabalho (**aml_config/config.json**).
    
1. Clone [o repositório do GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Adicione um arquivo de configuração do espaço de trabalho usando qualquer um destes métodos:
    * Copie o arquivo **aml_config/config.json** que você criou na etapa 1 no diretório clonado.

    * No [portal do Azure](https://ms.portal.azure.com), selecione **Baixar config.json** na seção **Visão geral** do seu espaço de trabalho. 

    ![Baixe o config.json](./media/aml-dsvm-server/download-config.png)

    * Crie um novo espaço de trabalho usando o código em [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).

1. Inicie o servidor de notebook do seu diretório clonado.
    
    ```shell
    jupyter notebook
    ```