---
title: Crie e registre os conjuntos de dados com seu espaço de trabalho
titleSuffix: Azure Machine Learning service
description: Saiba como criar conjuntos de dados de várias fontes e registrar os conjuntos de dados com o seu espaço de trabalho
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/19
ms.openlocfilehash: 4b3fa69156146037ff59a41eab8c8373f6e01dc4
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029109"
---
# <a name="create-and-register-azure-machine-learning-datasets-preview"></a>Criar e registrar os conjuntos de dados do Azure Machine Learning (versão prévia)

Neste artigo, você aprenderá os fluxos de trabalho do Azure Machine Learning para criar e registrar os conjuntos de dados e como acessá-los para reutilização em experimentos locais e remotos.

Azure Machine Learning conjuntos de dados (visualização) facilitam acessar e trabalhar com seus dados. Conjuntos de dados de gerenciar dados em vários cenários, como criação de pipeline e treinamento do modelo. Usando o [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), você pode trabalhar com dados em formatos populares, acessar o armazenamento subjacente, explorar e preparar dados, gerenciar o ciclo de vida das definições de conjunto de dados diferente e comparar entre conjuntos de dados usados em treinamento e em produção.

## <a name="prerequisites"></a>Pré-requisitos

Para criar e registrar os conjuntos de dados, você precisa:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* Um workspace de serviço do Azure Machine Learning. Ver [criar um espaço de trabalho do serviço de Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* A SDK para Python de aprendizado de máquina do Azure. Para instalar ou atualizar para a versão mais recente do SDK, consulte [instalar ou atualizar o SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

## <a name="create-datasets-from-local-files"></a>Criar conjuntos de dados de arquivos locais

Carregar arquivos em seu computador local, especificando o caminho de arquivo ou pasta com o [ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) método a partir de `Dataset` classe.  Esse método executa as etapas a seguir sem exigir que você especifique o tipo de arquivo ou argumentos de análise:

* Inferindo e definindo o delimitador.
* Ignorando os registros vazios na parte superior do arquivo.
* Inferindo e definir a linha de cabeçalho.
* Inferindo e convertendo tipos de dados de coluna.

```Python
from azureml.core import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Alternativamente, utilize as funções específicas do arquivo para controlar explicitamente a análise do seu arquivo. Atualmente, o SDK de Datasets suporta [delimitados](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-allfileshavesameheaders--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-norows--0---comment-none--include-path-false--archive-options-none-), [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-), [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-), [binário](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-), e [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-) formatos de arquivo.

## <a name="create-datasets-from-azure-datastores"></a>Criar conjuntos de dados de armazenamentos de dados do Azure

Para criar conjuntos de dados de um armazenamento de dados do Azure, certifique-se a:

* Verifique se que você tiver o colaborador ou proprietário de acesso para o repositório de dados do Azure registrado.

* Importar o [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) e [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#definition) e `Dataset` pacotes do SDK.

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
```

 O `get()` método recupera um armazenamento de dados existente no espaço de trabalho.

```
dstore = Datastore.get(workspace, datastore_name)
```

Use o `from_delimited_files()` método para ler arquivos delimitados e criar conjuntos de dados na memória.

```Python
# create an in-memory Dataset on your local machine
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

||ID|Número do Caso|Data|Bloco|IUCR|Texto Primário|DESCRIÇÃO|Descrição do Local|Detenção|Nacional|...|Ward|Área da Comunidade|Código do FBI|Coordenada X|Coordenada Y|Ano|Atualizado Em|Latitude|Longitude|Local padrão|
|--|--|---|---|---|---|----|------|-------|------|-----|---|----|----|-----|-----|------|----|-----|----|----|-----
|0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|PRÁTICA ENGANOSA|ROUBO DE IDENTIDADES FINANCEIRAS AO LONGO DE US $ 300|OUTROS|FALSE|FALSE|...|9|50|11|1183356|1831503|2016|5/11/2016 15:48|41.69283384|-87.60431945|(41.692833841, -87.60431945)|
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|ROUBO| DA CRIAÇÃO|RESIDÊNCIA|FALSE|FALSE|...|21|71|6|1166776|1850053|2016|5/12/2016 15:48|41.74410697|-87.66449429|(41.744106973, -87.664494285)
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|PRÁTICA ENGANOSA|US $300 DE ROUBO DE IDENTIDADES FINANCEIRAS E, EM|RESIDÊNCIA|FALSE|FALSE|...|19|74|11|||2016|5/12/2016 15:50
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE AVE|1120|PRÁTICA ENGANOSA|FALSIFICAÇÃO|RESIDÊNCIA|FALSE|FALSE|...|9|49|10|||2016|5/13/2016 15:51
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|ROUBO|DA CRIAÇÃO|ESCOLA, PÚBLICA, CRIANDO|FALSE|FALSE|...|40|13|6|||2016|5/25/2016 15:59|

## <a name="register-your-datasets-with-workspace"></a>Registrar seus conjuntos de dados com o espaço de trabalho

Use o [ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) método para registrar os conjuntos de dados para seu espaço de trabalho para o compartilhamento e reutilização dentro da sua organização e em vários experimentos.

```Python
dataset = dataset.register(workspace = 'workspace_name',
                           name = "dataset_crime",
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> A configuração de parâmetro padrão para `register()` é ' exist_ok = False'. Se você tentar registrar um conjunto de dados com o mesmo nome sem alterar essa configuração, ocorrerá um erro.

O `register()` método atualiza a definição de um conjunto de dados já está registrado com a configuração de parâmetro `exist_ok = True`.

```Python
dataset = dataset.register(workspace = workspace_name,
                           name = "dataset_crime",
                           description = 'Training data',
                           exist_ok = True)
```

Use `list()` para ver todos os conjuntos de dados registrados no seu espaço de trabalho.

```Python
Dataset.list(workspace_name)
```

O código anterior resulta no seguinte:

```Python
[Dataset(Name: dataset_crime,
         Workspace: workspace_name)]
```

## <a name="access-datasets-in-workspace"></a>Acesso a conjuntos de dados no espaço de trabalho

Conjuntos de dados registrados estão acessíveis e consumíveis localmente, remotamente e em clusters de computação como a computação do Azure Machine Learning. Para reutilizar seu conjunto de dados registrado em experimentos e ambientes de computação, use o código a seguir para obter o conjunto de dados registrado e espaço de trabalho por nome.

```Python
workspace = Workspace.from_config()

dataset = workspace.Datasets['dataset_crime']
```

## <a name="next-steps"></a>Próximas etapas

* [Explorar e preparar os conjuntos de dados](how-to-explore-prepare-data.md).
* [Gerenciar o ciclo de vida das definições de conjunto de dados](how-to-manage-dataset-definitions.md).
* Para obter um exemplo de como usar conjuntos de dados, consulte o [notebooks de exemplo](https://aka.ms/dataset-tutorial).