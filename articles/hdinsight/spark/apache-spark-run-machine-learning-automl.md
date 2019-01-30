---
title: Executar cargas de trabalho do Azure Machine Learning com AutoML (machine learning automatizado) no Apache Spark no Azure HDInsight
description: Saiba como executar cargas de trabalho do Azure Machine Learning com AutoML (aprendizado de máquina automatizado) no Apache Spark no Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: howto
ms.date: 01/14/2019
ms.openlocfilehash: 58852ee5de97bef043f8c0b67d9e90cdc057912a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54440062"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Executar cargas de trabalho do Azure Machine Learning com AutoML (machine learning automatizado) no Apache Spark no Azure HDInsight

O Azure Machine Learning é uma ferramenta colaborativa do tipo "arrastar e soltar", que você pode usar para criar, testar e implantar soluções de análise preditiva em seus dados. O Azure Machine Learning publica modelos como serviços Web que podem ser facilmente consumidos por aplicativos personalizados ou ferramentas de BI como o Excel. AutoML (aprendizado de máquina automatizado) ajuda a criar modelos de aprendizado de máquina de alta qualidade usando otimização e automação inteligentes. AutoML decide o algoritmo e os hiperparâmetros certos a serem usados para tipos de problema específicos.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Instalar o Azure Machine Learning em um cluster do HDInsight

> [!Note]
> O workspace do Azure ML está disponível atualmente nas seguintes regiões: eastus, eastus2 e westcentralus. O cluster do HDInsight também deve ser criado em uma dessas regiões.

Para obter tutoriais gerais do Azure Machine Learning e de aprendizado de máquina automatizado, veja [Tutorial: Crie seu primeiro experimento no de ciência de dados no Azure Machine Learning Studio](../../machine-learning/studio/create-experiment.md) e [Tutorial: Usar o aprendizado de máquina automatizado para compilar o modelo de regressão](../../machine-learning/service/tutorial-auto-train-models.md).
Para instalar o AzureML em seu cluster do Azure HDInsight, execute a ação de script [install_aml](https://commonartifacts.blob.core.windows.net/automl/install_aml.sh) nos nós principais e nos nós de trabalho de um cluster do HDInsight 3.6 Spark 2.3.0 (recomendado). Essa ação de script pode ser executada como parte do processo de criação de cluster ou em um cluster existente no portal do Azure.

Para saber mais sobre as ações de script, leia [Personalizar clusters HDInsight com base em Linux usando a ação de script](../hdinsight-hadoop-customize-cluster-linux.md). Além de instalar os pacotes do Azure Machine Learning e suas dependências, o script também baixa um exemplo de Jupyter Notebook (no caminho `HdiNotebooks/PySpark` do armazenamento padrão). Este Jupyter Notebook demonstra como usar um classificador de aprendizado de máquina automatizado para classificação simples de problema.

> [!Note]
> Pacotes do Azure Machine Learning são instalados no ambiente Python3 conda. O Jupyter Notebook instalado deve ser executado usando o kernel PySpark3.

## <a name="authentication-for-workspace"></a>Autenticação para o workspace

Criação do workspace e envio de teste exigem um token de autenticação. Esse token pode ser gerado usando um [aplicativo do Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). Um [Usuário do Azure AD](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python) também poderá ser usado para gerar o token de autenticação obrigatório se a autenticação multifator não estiver habilitada na conta.  

O snippet de código a seguir cria um token de autenticação usando um **aplicativo do Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                username = '<Azure AD Application ID>',
                password = '<Azure AD Application Key>'
                )
```
O snippet de código a seguir cria um token de autenticação usando um **usuário do Azure AD**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com','my_smart_password')
```

## <a name="loading-dataset"></a>Carregando conjunto de dados

O aprendizado de máquina automatizado no Spark usa **Fluxos de dados**, que são operações imutáveis avaliadas lentamente nos dados.  Um Fluxo de Dados pode carregar um conjunto de dados de um blob com acesso de leitura público ou uma URL de blob com um token SAS.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Você também pode registrar o repositório de dados com o workspace usando um único registro.

## <a name="experiment-submission"></a>Envio de experimento

Na configuração de aprendizado de máquina automatizado, a propriedade `spark_context` deve ser definida para que o pacote seja executado no modo distribuído. A propriedade `concurrent_iterations`, que é o número máximo de iterações executadas em paralelo, deve ser definida como um número menor que os núcleos de executor para o aplicativo Spark.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a motivação por trás do aprendizado de máquina automatizado, veja [Lance modelos no ritmo usando o aprendizado de máquina automatizado da Microsoft](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/).
* [Projeto AutoML da Microsoft Research](https://www.microsoft.com/research/project/automl/)