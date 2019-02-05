---
title: Exemplos de Notebooks Jupyter
titleSuffix: Azure Machine Learning service
description: Encontre e use notebooks Jupyter de exemplo para explorar o serviço de Azure Machine Learning no Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 5ba555ad31545e1ae1aa822ec58b0bd22ef486ac
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55295143"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Usar notebooks Jupyter no serviço do Azure Machine Learning

Para sua conveniência, desenvolvemos uma série de notebooks Jupyter Python que você pode usar para explorar o serviço do Azure Machine Learning. 

Saiba como usar o serviço com a documentação neste site e usar esses notebooks para personalizá-los conforme sua situação. 

Use um dos caminhos abaixo para executar um servidor de notebook com estes notebooks de exemplo.  Quando o servidor estiver em execução, encontre notebooks do tutorial na pasta **tutoriais** ou explore recursos diferentes da pasta **how-to-use-azureml**.


## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>Teste o Azure Notebooks: notebooks gratuitos do Jupyter na nuvem

É fácil começar a usar o Azure Notebooks! O [SDK do Azure Machine Learning para Python](https://aka.ms/aml-sdk) já está instalado e configurado para você no [Azure Notebooks](https://notebooks.azure.com/). A instalação e as atualizações futuras são gerenciadas automaticamente por meio dos serviços do Azure.
  
[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]


## <a name="use-a-data-science-virtual-machine-dsvm"></a>DSVM (Máquina Virtual de Ciência de Dados)

O [SDK do Azure Machine Learning para Python](https://aka.ms/aml-sdk) e o notebook já estão instalados e configurados para você em uma DSVM. 

Depois de [criar uma DSVM](how-to-configure-environment.md#dsvm), siga estas etapas na DSVM para executar os notebooks.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]


## <a name="use-your-own-jupyter-notebook-server"></a>Use seu próprio servidor de notebook do Jupyter

Use estas etapas para criar um servidor de notebook local do Jupyter no seu computador.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

<a name="automated-ml-setup"></a>

## <a name="automated-machine-learning-setup"></a>Configuração de aprendizado de máquina automatizado 

_Estas etapas se aplicam somente a notebooks na pasta **how-to-use-azureml/automated-machine-learning**._

Embora você possa usar qualquer uma das opções acima, também pode instalar o ambiente e criar um espaço de trabalho ao mesmo tempo com as instruções a seguir. 

1. Instale o [Mini-conda](https://conda.io/miniconda.html). Escolha a versão 3.7 ou posterior. Siga os prompts para instalar. 
   >[!NOTE]
   >Você pode usar um conda existente desde que seja a versão 4.4.10 ou posterior. Use `conda -V` para exibir a versão. Você pode atualizar uma versão do conda com o comando: `conda update conda`. Não é necessário instalar o mini-conda especificamente.

1. Baixe os notebooks de exemplo do [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning
) como um zip e extraia o conteúdo para um diretório local. Os notebooks do aprendizado de máquina automatizada estão na pasta `how-to-use-azureml/automated-machine-learning`.

1. Configure um novo ambiente do Conda. 
   1. Abra um prompt do Conda em seu computador local.
   
   1. Navegue até os arquivos extraídos em seu computador local.
   
   1. Abra a pasta **automated-machine-learning**.
   
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

1. Abra a pasta automated-machine-learning e, em seguida, abra o notebook **configuration.ipynb**. 

1. Execute as células no notebook para registrar o provedor de recursos de Serviços de Machine Learning e criar um espaço de trabalho.

Agora você está pronto para abrir e executar os notebooks salvos em seu computador local.


## <a name="next-steps"></a>Próximas etapas

Explorar o [repositório de notebooks do GitHub para o serviço de Azure Machine Learning](https://aka.ms/aml-notebooks)

Tente estes tutoriais:
+ [Treinar e implantar um modelo de classificação de imagem com MNIST](tutorial-train-models-with-aml.md)

+ [Preparar os dados e usar aprendizado de máquina automatizado para treinar um modelo de regressão com o conjunto de dados de táxi de NYC](tutorial-data-prep.md)
