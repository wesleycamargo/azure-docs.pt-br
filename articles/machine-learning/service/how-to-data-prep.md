---
title: Preparar os dados com o SDK de preparação de dados do Machine Learning para Python – Azure
description: Saiba como usar o SDK de preparação de dados do Azure Machine Learning para o Python para carregar dados de vários formatos, transformá-los para serem mais utilizáveis e gravá-los em um local para serem acessados por seus modelos.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 11/27/2018
ms.openlocfilehash: 91d0f3565db484504a67a3b6ae0989b9291cd24f
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446404"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>Preparar dados para modelagem com o Azure Machine Learning

Neste artigo, você aprenderá sobre os casos de uso e recursos exclusivos do SDK de Preparação dos Dados do Azure Machine Learning. A preparação de dados é a parte mais importante de um fluxo de trabalho de aprendizado de máquina. Dados do mundo real geralmente são divididos, inconsistentes ou não podem ser usados como dados de treinamento sem significativas de limpeza e transformação. Correção de erros e anomalias nos dados brutos e criação de novos recursos que são relevantes para o problema que você está tentando resolver, aumentará a precisão do modelo. O SDK é projetado para ser familiar aos usuários de outras bibliotecas de preparação de dados comuns, oferecendo vantagens para os principais cenários e mantendo a interoperabilidade com essas outras bibliotecas.

Você pode preparar seus dados em Python usando o [SDK de preparação de dados do Azure Machine Learning](https://aka.ms/data-prep-sdk).

## <a name="azure-machine-learning-data-prep-sdk"></a>SDK de preparação de dados do Azure Machine Learning

O [SDK de Preparação de Dados do Azure Machine Learning](https://aka.ms/data-prep-sdk) é uma biblioteca Python que oferece:

* Transformações de economia de tempo inteligentes como Agrupamento Difuso, Coluna Derivada por Exemplo, Divisão Automática, Arquivo de Leitura Inteligente e processamento do esquema irregular à direita.
* Uma única API que funciona em pequenos dados localmente ou em dados grandes na nuvem, com **algumas ou nenhuma alteração de código**.
* A capacidade de dimensionar com mais eficiência em um único computador com o uso de uma abordagem de streaming para processar os dados, em vez de carregar na memória.

### <a name="install-the-sdk"></a>Instalar o SDK

Instale o SDK em seu ambiente de Python usando o comando a seguir.

```shell
pip install azureml-dataprep
```

Use o código a seguir para importar o pacote.

```python
import azureml.dataprep as dprep
```

### <a name="examples-and-reference"></a>Exemplos e referência

Para saber mais sobre os módulos e funções desse SDK, consulte o [Documentos de referência do SDK de Preparação de Dados](https://aka.ms/data-prep-sdk).

Os exemplos a seguir destacam algumas das funcionalidades exclusivas do SDK, incluindo:

* Detecção automática de tipo de arquivo
* Transformações inteligentes
* Estatísticas de resumo
* Funcionalidade de ambiente cruzada


#### <a name="automatic-file-type-detection"></a>Detecção automática de tipo de arquivo

Use a `smart_read_file()` função para carregar os dados sem ter que especificar o tipo de arquivo. Essa função reconhece automaticamente e analisa o tipo de arquivo.

```python
dataflow = dprep.smart_read_file(path="<your-file-path>")
```

#### <a name="intelligent-transforms"></a>Transformações inteligentes

Use o SDK para dividir e derivar colunas por exemplo e inferência de tipos para automatizar a engenharia de recursos. Suponha que você tenha um campo em seu objeto de fluxo de dados denominado `datetime` com um valor de `2018-09-15 14:30:00`.

Para dividir automaticamente o `datetime` campo, chame a função a seguir.

```python
new_dataflow = dataflow.split_column_by_example(source_column="datetime")
```

Não definindo o parâmetro de exemplo, a função automaticamente dividirá os `datetime` campo em dois novos campos `datetime_1` e `datetime_2`. Os valores resultantes são `2018-09-15` e `14:30:00`, respectivamente. Também é possível fornecer um padrão de exemplo e o SDK preverá e executará sua transformação desejada. Usando o mesmo `datetime` objeto, o código a seguir criará uma nova coluna `datetime_weekday` para o dia da semana com base no exemplo fornecido.

```python
new_dataflow = dataflow.derive_column_by_example(
        source_columns="datetime", 
        new_column_name="datetime_weekday", 
        example_data=[("2009-01-04 10:12:00", "Sunday"), ("2013-08-22 17:00:00", "Thursday")]
    )
```

#### <a name="summary-statistics"></a>Estatísticas de resumo

Você pode gerar estatísticas de resumo rápidas para um fluxo de dados com uma linha de código. Esse método oferece uma maneira conveniente de entender seus dados e como são distribuídos.

```python
dataflow.get_profile()
```

Chamar essa função em um objeto de fluxo de dados resultará em saída como a tabela a seguir.

![Saída das Estatísticas de Resumo](./media/concept-data-preparation/output-example.png)

## <a name="multiple-environment-compatibilities"></a>Várias compatibilidades de ambiente

O SDK também permite que objetos de fluxo de dados a serem serializados e abertos em *qualquer* ambiente do Python. O ambiente em que ele é aberto pode ser diferente do que o ambiente em que ele foi salvo. Essa funcionalidade permite a transferência fácil entre ambientes do Python e integração rápida com modelos de Azure Machine Learning.

Use o código a seguir para salvar seus objetos de fluxo de dados.

```python
package = dprep.Package([dataflow_1, dataflow_2])
package.save("<your-local-path>")
```

Use o seguinte código para reabrir o pacote em qualquer ambiente e recuperar uma lista de objetos de fluxo de dados.

```python
package = dprep.Package.open("<your-local-path>")
dataflow_list = package.dataflows
```

## <a name="data-preparation-pipeline"></a>Pipeline de preparação de dados

Para ver exemplos detalhados e o código para cada etapa de preparação, use os guias de instruções a seguir:

1. [Carregar dados](how-to-load-data.md), que podem estar em vários formatos
2. [Transformar](how-to-transform-data.md)-los em uma estrutura mais utilizável
3. [Gravar](how-to-write-data.md) esses dados em um local acessível aos seus modelos

![Processo de preparação de dados](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>Próximas etapas

Examine um [notebook de exemplo](https://github.com/Microsoft/AMLDataPrepDocs/tree/master/tutorials/getting-started/getting-started.ipynb) de preparação de dados usando o SDK de preparação de dados do Azure Machine Learning.

Documentação de referência do SDK de [Preparação de Dados do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py).
