---
title: Iniciar, monitorar e cancelar as execuções de treinamento em Python
titleSuffix: Azure Machine Learning service
description: Saiba como começar, defina o status da marca e organizar seus experimentos de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/05/2019
ms.openlocfilehash: 82df2258116ce55fa440b67ec0a66b106d0d72c7
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471487"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Iniciar, monitorar e cancelar as execuções de treinamento em Python

O [SDK de aprendizado de máquina do Azure para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) fornece vários métodos para monitorar, organizar e gerenciar suas execuções de treinamento e experimentação.

Estas instruções mostram exemplos das seguintes tarefas:

* [Monitor de desempenho de execução](#monitor)
* [Cancelar ou deixar de execuções](#cancel)
* [Criar filho é executado](#children)
* [Marcar e encontrar execuções](#tag)

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do seguinte:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* Um workspace de serviço do Azure Machine Learning. Ver [criar um espaço de trabalho do serviço de Azure Machine Learning](setup-create-workspace.md).

* SDK de aprendizado de máquina do Azure para Python instalada (versão 1.0.21 ou posterior). Para instalar ou atualizar para a versão mais recente do SDK, vá para o [instalar/atualizar o SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) página.

    Para verificar sua versão do SDK do Azure Machine Learning, use o código a seguir.

    ```Python
    print(azureml.core.VERSION)
    ```

<a name="monitor"></a>

## <a name="start-a-run-and-set-its-status"></a>Iniciar uma execução e definir seu status

Configurar seu experimento, importando o [espaço de trabalho](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), [experimentar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [execute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py), e [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) classes a partir de [azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) pacote.

```Python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Iniciar uma execução e seu processo de registro em log com o [ `start_logging()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) método.

```Python
notebook_run = exp.start_logging()

notebook_run.log(name="message", value="Hello from run!")
```

Obter o status da execução com [ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--).

```Python
print(notebook_run.get_status())
```

Para obter detalhes adicionais sobre o uso de execução [ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--).

```Python
notebook_run.get_details()
```

Quando sua execução for concluída com êxito, use o [ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) método marcá-lo como concluído.

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

Você também pode usar do Python `with...as` padrão. Nesse contexto, a execução automaticamente marcará em si como concluído quando a execução estiver fora do escopo. Dessa forma, você não precisa marcar manualmente a execução como concluído.

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

<a name="cancel"></a>

## <a name="cancel-or-fail-runs"></a>Cancelar ou deixar de execuções

 Se você notar um engano ou sua execução parece levar algum tempo para concluir, use o [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) método para parar a execução antes que ele termine e marcá-la como cancelada.

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

Se sua execução for concluída, mas contém um erro semelhante, o script de treinamento incorreto foi usado, você pode usar o [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) método marcá-lo como falha.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

<a name="children"></a>

## <a name="create-child-runs"></a>Criar filho é executado

Crie filho é executado para agrupar execuções relacionadas, como para iterações de ajuste de parâmetro diferente.

Este exemplo de código usa o script hello_with_children.py para criar um lote de cinco execuções de filho de dentro de uma execução enviada usando o [ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) método.

```Python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Filho é executado concluído automaticamente quando eles passam fora do escopo.

Você também pode iniciar execuções filho individualmente, mas como cada criação resulta em uma chamada de rede, é menos eficiente do que enviar um lote de execuções.

Use o [ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) executa o método para consultar o filho de um pai específico.

```Python
list(parent_run.get_children())
```

<a name="tag"></a>

## <a name="tag-and-find-runs"></a>Marcar e encontrar execuções

No serviço de Azure Machine Learning, você pode usar marcas e propriedades para ajudar a organizar e consultar as execuções para obter informações importantes.

### <a name="add-properties-and-tags"></a>Adicionar marcas e propriedades

Use o [ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) para adicionar metadados pesquisáveis para suas execuções. Por exemplo, o código a seguir adiciona a propriedade de "author" para a execução.

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Propriedades são imutáveis, que é útil como um registro permanente para fins de auditoria. O exemplo de código a seguir resultará em erro, já que nós já adicionamos "azureml-usuário" como a propriedade "author" no código anterior.

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

Marcas, no entanto, são mutáveis. Use [ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) para adicionar informações pesquisáveis e significativas para os consumidores do seu experimento.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Você também pode adicionar uma marca de cadeia de caracteres simples. Ele aparece no dicionário com valor de marca `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>Marcas e propriedades de consulta

Você pode consultar é executado dentro de um experimento que correspondem a marcas e propriedades específicas.

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>Blocos de anotações de exemplo

Os seguintes blocos de anotações demonstram conceitos neste artigo:

* Para saber mais sobre APIs de registro, consulte o [notebook da API de registro em log](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Para obter informações adicionais sobre o gerenciamento é executado com o SDK do Azure Machine Learning, consulte o [Gerenciar execuções de notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Próximas etapas

* Para saber como registrar em log as métricas para seus testes, consulte o [registrar métricas durante execuções de treinamento](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiment) artigo.