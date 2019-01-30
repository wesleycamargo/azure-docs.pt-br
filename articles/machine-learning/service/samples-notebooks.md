---
title: Exemplos de Notebooks Jupyter
titleSuffix: Azure Machine Learning service
description: Encontre e use notebooks Jupyter de exemplo para explorar o serviço de Azure Machine Learning no Python.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6befe3a3fee80dd65fd3ac5be241c558707224e6
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811087"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Usar notebooks Jupyter no serviço do Azure Machine Learning


Para sua conveniência, desenvolvemos uma série de notebooks Jupyter Python que você pode usar para explorar o serviço do Azure Machine Learning. 

Saiba como usar o serviço com a documentação neste site e usar esses notebooks para personalizá-los conforme sua situação. 

## <a name="prerequisite"></a>Pré-requisito

Conclua o [Início rápido do Python do Azure Machine Learning](quickstart-get-started.md) para criar um espaço de trabalho e iniciar os Azure Notebooks.

## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>Teste o Azure Notebooks: notebooks gratuitos do Jupyter na nuvem

É fácil começar a usar o Azure Notebooks! O [SDK do Azure Machine Learning para Python](https://aka.ms/aml-sdk) já está instalado e configurado para você no [Azure Notebooks](https://notebooks.azure.com/). A instalação e as atualizações futuras são gerenciadas automaticamente por meio dos serviços do Azure.
  
+ Para executar os **notebooks principais do tutorial**:
  1. Vá até o [Azure Notebooks](https://notebooks.azure.com/).
    
  1. Localize a pasta de **tutoriais** na biblioteca de **Introdução** que você criou durante o início rápido do pré-requisito.
    
  1. Abra o notebook que você deseja executar.
    
+ Para executar **outros notebooks**:

  1. [Importar os notebooks de exemplo](https://aka.ms/aml-clone-azure-notebooks) no Azure Notebooks.

  1. Adicione um arquivo de configuração do espaço de trabalho à biblioteca usando qualquer um destes métodos:
     + Copie o arquivo **config.json** da biblioteca de **Introdução** para a nova biblioteca clonada.

     + Crie um novo espaço de trabalho usando o código em [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).
    
  1. Abra o notebook que você deseja executar.     


## <a name="use-a-data-science-virtual-machine-dsvm"></a>DSVM (Máquina Virtual de Ciência de Dados)

O [SDK do Azure Machine Learning para Python](https://aka.ms/aml-sdk) e o notebook já estão instalados e configurados para você em uma DSVM. Use estas etapas para executar os notebooks.

1. [Criar uma DSVM](how-to-configure-environment.md#dsvm).

1. Clone [o repositório do GitHub](https://aka.ms/aml-notebooks).

1. Adicione um arquivo de configuração do espaço de trabalho à biblioteca usando qualquer um destes métodos:
    * Copie o arquivo **aml_config\config.json** que você criou usando o guia de início rápido de pré-requisito para o diretório clonado.

    * Crie um novo espaço de trabalho usando o código em [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).

1. Inicie o servidor de notebook do seu diretório clonado.

## <a name="use-your-own-jupyter-notebook-server"></a>Use seu próprio servidor de notebook do Jupyter

Use estas etapas para criar um servidor de notebook local do Jupyter no seu computador.

1. Conclua o início rápido de pré-requisito, no qual são instalados os SDKs do Azure Machine Learning.

1. Clone [o repositório do GitHub](https://aka.ms/aml-notebooks).

1. Adicione um arquivo de configuração do espaço de trabalho à biblioteca usando qualquer um destes métodos:
    * Copie o arquivo **aml_config\config.json** que você criou usando o guia de início rápido de pré-requisito para o diretório clonado.
    
    * Crie um novo espaço de trabalho usando o código em [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).

1. Inicie o servidor de notebook do seu diretório clonado.

1. Vá para a pasta que contém o notebook.

1. Abra o bloco de anotações.

<a name="auto"></a>

## <a name="automated-ml-setup"></a>Configuração automatizada de ML 

**Essas etapas se aplicam somente para os notebooks na pasta `automated-machine-learning`.**

Embora você possa usar qualquer uma das opções acima, também pode instalar o ambiente e criar um espaço de trabalho ao mesmo tempo com as instruções a seguir. 

1. Instale o [Mini-conda](https://conda.io/miniconda.html). Escolha a versão 3.7 ou posterior. Siga os prompts para instalar. 
   >[!NOTE]
   >Você pode usar um conda existente desde que seja a versão 4.4.10 ou posterior. Use `conda -V` para exibir a versão. Você pode atualizar uma versão do conda com o comando: `conda update conda`. Não é necessário instalar o mini-conda especificamente.

1. Baixe os notebooks de exemplo do [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning
) como um zip e extraia o conteúdo para um diretório local. Os notebooks do aprendizado de máquina automatizada estão na pasta `how-to-use-azureml/automated-machine-learning`.

1. Configure um novo ambiente do Conda. 
   1. Abra um prompt do Conda em seu computador local.
   
   1. Navegue até os arquivos extraídos em seu computador local.
   
   1. Abra a pasta `automated-machine-learning`.
   
   1. Execute `automl_setup.cmd` no prompt do Conda para Windows ou o arquivo `.sh` para seu sistema operacional. Pode levar cerca de 10 minutos para executá-lo.

      O script de instalação:
      + Cria um novo ambiente do Conda
      + Instala os pacotes necessários
      + Configura o widget
      + Inicia um notebook do Jupyter
      
   >[!NOTE]
   > O script usa o nome do ambiente Conda como um parâmetro opcional. O nome do ambiente do Conda padrão é `azure_automl`. O comando exato depende do sistema operacional. Será útil se você criar um ambiente ou atualizar para uma nova versão. Por exemplo, é possível usar 'automl_setup.cmd azure_automl_sandbox' para criar um nome de ambiente azure_automl_sandbox. 
      
1. Depois que o script for concluído, você verá uma home page do notebook do Jupyter no seu navegador.

1. Navegue até o caminho onde você salvou os notebooks. 

1. Abra a pasta automated-machine-learning, depois abra o notebook `configuration.ipynb`. 

1. Execute as células no notebook para registrar o provedor de recursos de Serviços de Machine Learning e criar um espaço de trabalho.

Agora você está pronto para abrir e executar os notebooks salvos em seu computador local.


## <a name="next-steps"></a>Próximas etapas

Explorar o [repositório de notebooks do GitHub para o serviço de Azure Machine Learning](https://aka.ms/aml-notebooks)

Tente estes tutoriais:
+ [Treinar e implantar um modelo de classificação de imagem com MNIST](tutorial-train-models-with-aml.md)

+ [Preparar os dados e usar aprendizado de máquina automatizado para treinar um modelo de regressão com o conjunto de dados de táxi de NYC](tutorial-data-prep.md)
