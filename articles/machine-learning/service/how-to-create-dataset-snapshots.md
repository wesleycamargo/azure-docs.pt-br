---
title: Comparar & reproduzir os dados ao longo do tempo com instantâneos
titleSuffix: Azure Machine Learning service
description: Saiba como comparar dados ao longo do tempo e garantir reprodutibilidade com instantâneos de conjunto de dados
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.date: 05/02/2019
ms.openlocfilehash: 51d0dcfc543834e9a8725d11fa82b566a5132a6b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205009"
---
# <a name="compare-data-and-ensure-reproducibility-with-snapshots-preview"></a>Comparar dados e garantir reprodutibilidade com instantâneos (versão prévia)

Neste artigo, você aprenderá como criar e gerenciar instantâneos da sua [conjuntos de dados do Azure Machine Learning](how-to-create-register-datasets.md) (conjuntos de dados) para que você possa capturar ou comparar dados ao longo do tempo. Conjuntos de dados facilitam a acessar e trabalhar com seus dados na nuvem em vários cenários.

**Conjunto de dados instantâneos** armazenar um perfil (estatísticas de resumo) dos dados no momento em que ele é criado. Você pode optar por armazenar também uma cópia dos dados no seu instantâneo para reprodução.

>[!Important]
> Instantâneos de incorrer em custos de armazenamento. Armazenar uma cópia dos dados no seu instantâneo exige armazenamento ainda mais. Use [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) quando eles não são mais necessários.

## <a name="when-to-use-snapshots"></a>Quando usar instantâneos

Há três usos principais para instantâneos:

+ **Validação do modelo**: Compare o perfil de dados de instantâneos diferentes entre as execuções de treinamento ou nos dados de produção.

+ **Capacidade de reprodução de modelo**: Reproduza os resultados chamando um instantâneo que inclui dados durante o treinamento.

+ **Acompanhar dados ao longo do tempo**: Veja como o conjunto de dados evoluiu por [comparando perfis](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#compare-profiles-rhs-dataset-snapshot--include-columns-none--exclude-columns-none--histogram-compare-method--histogramcomparemethod-wasserstein--0--)
  
## <a name="prerequisites"></a>Pré-requisitos

Para criar instantâneos de conjunto de dados, você precisa de um conjunto de dados registrados de aprendizado de máquina do Azure. Se você não tiver uma, consulte [conjuntos de dados de criar e registrar](how-to-create-register-datasets.md).

## <a name="create-dataset-snapshots"></a>Criar instantâneos de conjunto de dados

Para criar um instantâneo de um conjunto de dados, use [ `dataset.create_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?#create-snapshot-snapshot-name--compute-target-none--create-data-snapshot-false--target-datastore-none-) do SDK do Azure Machine Learning.

Por padrão, o instantâneo armazena o perfil (estatísticas de resumo) dos dados com a versão mais recente [definição de conjunto de dados](how-to-manage-dataset-definitions.md) aplicado. Uma definição de conjunto de dados contém um registro de todas as etapas de transformação definidos para os dados. É uma ótima maneira de fazer com que a preparação de dados funcione reproduzível.

Opcionalmente, você também pode incluir uma cópia dos dados no seu instantâneo adicionando `create_data_snapshot = True`.  Esses dados podem ser úteis para reprodução.

Este exemplo usa [amostragem de dados crime](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) e um conjunto de dados chamado `dataset_crime` criadas usando o artigo ["conjuntos de dados Create e registre-se"](how-to-create-register-datasets.md).

```Python
from azureml.core.workspace import Workspace
from azureml.core.dataset import Dataset
from azureml.data.dataset_snapshot import DatasetSnapshot
import datetime

# get existing workspace
workspace = Workspace.from_config()

# get existing, named dataset:
dataset = workspace.datasets['dataset_crime']

# assign name to snapshot
snapshot_name = 'snapshot_' + datetime.datetime.today().strftime('%Y%m%d%H%M%S')

# create snapshot to store profile from a remote environment
# and include copy of data
snapshot = dataset.create_snapshot(snapshot_name = snapshot_name,
                                   compute_target = remote_compute_target,
                                   create_data_snapshot = True)
```

Como os instantâneos são criados de forma assíncrona, use o [ `wait_for_completion()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#wait-for-completion-show-output-true--status-update-frequency-10-) método para monitorar o processo.

```Python
# monitor process every 10 seconds
snapshot.wait_for_completion(show_output=True, status_update_frequency=10)

# return snapshot just created
dataset.get_snapshot(snapshot_name)
```

Saída:

```
Action status: Running
Action status: Running
Action status: Completed


DatasetSnapshot(Name: snapshot_20190411165420,
Dataset Id: 66cd0830-2f72-41e5-b677-d2d953f54821,
Workspace: "your_workspace_name",
Dataset definition version: 1,
Snapshot created date: 2019-05-11 17:24:00+00:00)
```

Use [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) quando eles não são mais necessários.

## <a name="find-snapshots"></a>Localizar instantâneos

Para recuperar um instantâneo existente, use [ `get_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-snapshot-snapshot-name-).

Para obter uma lista de seus instantâneos salvos de um determinado conjunto de dados, use [ `get_all_snapshots()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-all-snapshots--).

```Python
# Get named snapshot for this dataset
dataset.get_snapshot(snapshot_name)

# Get all snapshots for this dataset
dataset.get_all_snapshots()
```

## <a name="get-data-and-profiles-from-snapshots"></a>Obter dados e perfis de instantâneos

Cada instantâneo gera um perfil do conjunto de dados, que inclui as estatísticas de resumo e lhe dá a opção de salvar uma cópia dos seus dados.

### <a name="get-the-data-profile"></a>Obter o perfil de dados

Use [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) para acessar o perfil de seus dados.  Você pode usar esse perfil para comparar dados de treinamento e de produção, por exemplo.

```Python
snapshot.get_profile()
```

||Type|Min|max|Count|Contagem faltando|Sem contagem faltando|Percentual faltando|Contagem de erros|Contagem vazia|0,1% quantil|1% quantil|5% quantil|25% quantil|50% quantil|75% quantil|95% quantil|99% quantil|99,9% quantil|Média|Desvio padrão|Variação|Distorção|Curtose
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e + 07|1.05351e + 07|10.0|0,0|10.0|0,0|0,0|0,0|1.04986e + 07|1.04992e + 07|1.04986e + 07|1.05166e + 07|1.05209e + 07|1.05259e + 07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e + 08|-0.495701|-1.02814
Número do Caso|FieldType.STRING|HZ239907|HZ278872|10.0|0,0|10.0|0,0|0,0|0,0||||||||||||||
Data|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10.0|0,0|10.0|0,0|0,0|0,0||||||||||||||
Bloco|FieldType.STRING|004XX S KILBOURN AVE|113XX S PRAIRIE AVE|10.0|0,0|10.0|0,0|0,0|0,0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0,0|10.0|0,0|0,0|0,0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Texto Primário|FieldType.STRING|PRÁTICA ENGANOSA|ROUBO|10.0|0,0|10.0|0,0|0,0|0,0||||||||||||||
DESCRIÇÃO|FieldType.STRING|VERIFICAÇÃO DE FALSA|AO LONGO DE US $500|10.0|0,0|10.0|0,0|0,0|0,0||||||||||||||
Descrição do Local|FieldType.STRING||ESCOLA, PÚBLICA, CRIANDO|10.0|0,0|10.0|0,0|0,0|1.0||||||||||||||
Detenção|FieldType.BOOLEAN|Falso|Falso|10.0|0,0|10.0|0,0|0,0|0,0||||||||||||||
Nacional|FieldType.BOOLEAN|Falso|Falso|10.0|0,0|10.0|0,0|0,0|0,0||||||||||||||
Superar|FieldType.INTEGER|531|2433|10.0|0,0|10.0|0,0|0,0|0,0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
Distrito|FieldType.INTEGER|5|24|10.0|0,0|10.0|0,0|0,0|0,0|5|5|5|6|13|19|24|24|24|13,5|6.94822|48.2778|0.0930109|-1.62325
Ward|FieldType.INTEGER|1|48|10.0|0,0|10.0|0,0|0,0|0,0|1|5|1|9|22,5|40|48|48|48|24,5|16.2635|264.5|0.173723|-1.51271
Área da Comunidade|FieldType.INTEGER|4|77|10.0|0,0|10.0|0,0|0,0|0,0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379

### <a name="get-the-data-from-the-snapshot"></a>Obter os dados do instantâneo

Para obter uma cópia dos dados salvos em um conjunto de dados de instantâneo, gerar um DataFrame pandas com os [ `to_pandas_dataframe()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#to-pandas-dataframe--) método.

Esse método falhará se uma cópia dos dados não foi solicitada durante a criação do instantâneo.

```Python
snapshot.to_pandas_dataframe().head(3)
```

||ID|Número do Caso|Data|Bloco|IUCR|Texto Primário|DESCRIÇÃO|Descrição do Local|Detenção|Nacional|...|Ward|Área da Comunidade|Código do FBI|Coordenada X|Coordenada Y|Ano|Atualizado Em|Latitude|Longitude|Local padrão
-|--|-----------|----|-----|----|------------|-----------|--------------------|------|--------|---|----|--------------|--------|------------|------------|----|----------|--------|---------|--------
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|1153|PRÁTICA ENGANOSA|ROUBO DE IDENTIDADES FINANCEIRAS AO LONGO DE US $ 300|OUTROS|Falso|Falso|...|9|50|11|1183356.0|1831503.0|2016|2016-05-11 15:48:00|41.692834|-87.604319|(41.692833841, -87.60431945)
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|890|ROUBO|DA CRIAÇÃO|RESIDÊNCIA|Falso|Falso|...|21|71|6|1166776.0|1850053.0|2016|2016-05-12 15:48:00|41.744107|-87.664494|(41.744106973, -87.664494285)
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO AVE|1154|PRÁTICA ENGANOSA|US $300 DE ROUBO DE IDENTIDADES FINANCEIRAS E, EM|RESIDÊNCIA|Falso|Falso|...|19|74|11|NaN|NaN|2016|2016-05-12 15:50:00|NaN|NaN|

## <a name="next-steps"></a>Próximas etapas

* Consulte o SDK [visão geral](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) para padrões de design e exemplos de uso.

* Para obter um exemplo do uso de instantâneos do conjunto de dados, consulte o [notebooks de exemplo](https://aka.ms/dataset-tutorial).
