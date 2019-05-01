---
title: Executar cargas de trabalho do Azure Machine Learning com AutoML (aprendizado de máquina automatizado) no Apache Spark no Azure HDInsight
description: Saiba como executar cargas de trabalho do Azure Machine Learning com AutoML (aprendizado de máquina automatizado) no Apache Spark no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 5135de0fc87af227073f96c653d928ace1a50fd0
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917048"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Executar cargas de trabalho do Azure Machine Learning com AutoML (aprendizado de máquina automatizado) no Apache Spark no Azure HDInsight

O Azure Machine Learning simplifica e acelera a construção, treinamento e implantação de modelos de aprendizado de máquina. No (AutoML) de aprendizado de máquina automatizada, comece com os dados de treinamento que tem um recurso de destino definida e, em seguida, iterar por meio de combinações de algoritmos e seleções de recurso para selecionar automaticamente o melhor modelo para seus dados com base nas pontuações de treinamento. HDInsight permite que os clientes provisionem clusters com centenas de nós. AutoML em execução no Spark em um cluster do HDInsight permite que os usuários usem a capacidade de computação entre esses nós para executar trabalhos de treinamento de uma forma de escalabilidade horizontal e para executar vários trabalhos de treinamento em paralelo. Isso permite que os usuários executem AutoML experimentos compartilhando a computação com seus outras cargas de trabalho de big data.
 

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Instalar o Azure Machine Learning em um cluster do HDInsight

Para obter tutoriais gerais do aprendizado de máquina automatizados, consulte [Tutorial: Usar o aprendizado de máquina automatizado para compilar o modelo de regressão](../../machine-learning/service/tutorial-auto-train-models.md).
Todos os novos clusters Spark HDInsight vêm pré-instalados com o SDK do AzureML AutoML. Você pode começar com AutoML no HDInsight com esse [exemplo de notebook Jupyter](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-hdi). Este Jupyter Notebook demonstra como usar um classificador de aprendizado de máquina automatizado para classificação simples de problema.

> [!Note]
> Pacotes do Azure Machine Learning são instalados no ambiente Python3 conda. O Jupyter Notebook instalado deve ser executado usando o kernel PySpark3.

Como alternativa, você pode usar notebooks Zeppelin para usar AutoML também.

> [!Note]
> Zeppelin tem um [problema conhecido](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) na qual o PySpark3 não escolher a versão correta do Python. Use a solução documentada.

## <a name="authentication-for-workspace"></a>Autenticação para o workspace

Criação do workspace e envio de teste exigem um token de autenticação. Esse token pode ser gerado usando um [aplicativo do Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). Um [Usuário do Azure AD](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python) também poderá ser usado para gerar o token de autenticação obrigatório se a autenticação multifator não estiver habilitada na conta.  

O snippet de código a seguir cria um token de autenticação usando um **aplicativo do Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                service_principal_id = '<Azure AD Application ID>',
                service_principal_password = '<Azure AD Application Key>'
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

No [configuração de aprendizado de máquina automatizados](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig), a propriedade `spark_context` deve ser definido para o pacote seja executado no modo distribuído. A propriedade `concurrent_iterations`, que é o número máximo de iterações executadas em paralelo, deve ser definida como um número menor que os núcleos de executor para o aplicativo Spark.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a motivação por trás de aprendizado de máquina automatizados, consulte [automatizada de modelos de versão no ritmo da Microsoft usando o aprendizado de máquina!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Para obter mais detalhes sobre como usar recursos de ML automatizado do Azure ML, consulte [New automatizada de recursos de aprendizado de máquina no serviço de Azure Machine Learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Projeto AutoML da Microsoft Research](https://www.microsoft.com/research/project/automl/)
