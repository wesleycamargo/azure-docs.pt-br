---
title: Executar scripts de aprendizado de máquina do Python
titleSuffix: Azure Machine Learning Studio
description: Saiba como usar o Python no Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 4b4f3877b56752756050de0af226571ac2a93293
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57888137"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Executar scripts Python de aprendizado de máquina no Azure Machine Learning Studio

Python é uma ferramenta valiosa no conjunto de ferramentas de muitos cientistas de dados. Ele é usado em todos os estágios de fluxos de trabalho de aprendizado de máquina típica incluindo exploração de dados, extração de recursos, treinamento do modelo e validação e implantação.

Este artigo descreve como você pode usar o módulo Executar Script Python para usar o código do Python em seus experimentos do Azure Machine Learning Studio e serviços da web.

## <a name="using-the-execute-python-script-module"></a>Usando o módulo Executar Script Python

A principal interface para Python no Studio é por meio de [Executar Script Python] [ execute-python-script] módulo. Ele aceita até três entradas e produz até duas saídas, semelhantes do [Executar Script R] [ execute-r-script] módulo. Código do Python é inserido na caixa de parâmetro por meio de uma função de ponto de entrada especialmente denominada chamada `azureml_main`.

![Executar o módulo de Script do Python](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Código do python de exemplo na caixa de parâmetro de módulo](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Parâmetros de entrada

Entradas para o módulo de Python são expostas como quadros de dados Pandas. O `azureml_main` função aceita até dois opcional Pandas quadros de dados como parâmetros.

O mapeamento entre as portas de entrada e os parâmetros de função é posicional:

- A primeira porta de entrada conectada é mapeada para o primeiro parâmetro da função.
- A segunda entrada (se conectada) é mapeada para o segundo parâmetro da função.
- A terceira entrada é usada para [importar os módulos de Python adicionais](#import-modules).

Uma semântica de como as portas de entrada são mapeadas para parâmetros de mais detalhada a `azureml_main` função são mostrados abaixo.

![Tabela de configurações de porta de entrada e a assinatura resultante do Python](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Valores de retorno de saída

O `azureml_main` função deve retornar um DataFrame Pandas único empacotados em um Python [sequência](https://docs.python.org/2/c-api/sequence.html) como uma tupla, lista ou matriz NumPy. O primeiro elemento dessa sequência é retornado para a primeira porta de saída do módulo. A segunda porta de saída do módulo é usada para [visualizações](#visualizations) e não requer um valor de retorno. Esse esquema é mostrado abaixo.

![Mapeamento de portas para parâmetros de entrada e retornar o valor para a porta de saída](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Conversão de tipos de dados de entrada e saída

Conjuntos de dados do Studio não são os mesmos como quadros de dados Panda. Como resultado, os conjuntos de dados de entrada no Studio são convertidos em dados do Pandas e quadros de dados de saída são convertidas novamente em conjuntos de dados do Studio. Durante esse processo de conversão, as conversões a seguir também são executadas:

 **Tipo de dados do Python** | **Procedimento de tradução do Studio** |
| --- | --- |
| Cadeias de caracteres e numéricos| Traduzido como está |
| Pandas "NA" | Traduzida como 'Valor ausente' |
| Vetores de índice | Não há suporte para * |
| Nomes de coluna não-cadeia de caracteres | Chamar `str` em nomes de coluna |
| Nomes de coluna duplicados | Adicione um sufixo numérico: (1), (2), (3), e assim por diante.

**Todos os quadros de dados de entrada na função Python sempre têm um índice numérico de 64 bits de 0 ao número de linhas menos 1*

## <a id="import-modules"></a>Importando os módulos de script de Python existentes

O back-end usado para executar o Python se baseia [Anaconda](https://store.continuum.io/cshop/anaconda/), um amplamente usado distribuição científica de Python. Ele vem com quase 200 dos pacotes Python mais comuns usados em cargas de trabalho centrado em dados. Atualmente não há suporte para o uso de sistemas de gerenciamento de pacote, como o Pip ou Conda instalar e gerenciar bibliotecas externas no Studio.  Se você achar necessário para incorporar bibliotecas adicionais, use o cenário a seguir como guia.

Um caso de uso comum é incorporar scripts Python existentes a experimentos do Studio. O [Executar Script Python] [ execute-python-script] módulo aceita um arquivo zip que contém módulos Python na terceira porta de entrada. O arquivo é descompactado pela estrutura de execução no tempo de execução e o conteúdo é adicionado ao caminho da biblioteca do interpretador de Python. A função do ponto de entrada `azureml_main` pode, então, importar esses módulos diretamente. 

Por exemplo, considere o arquivo Hello.py que contém uma função simples "Hello, World".

![Função definida pelo usuário no arquivo Hello.py](./media/execute-python-scripts/figure4.png)

Em seguida, criamos um arquivo Hello.zip que contenha o Hello.py:

![Arquivo zip que contém o código Python definido pelo usuário](./media/execute-python-scripts/figure5.png)

Carregue o arquivo zip como um conjunto de dados no Studio. Em seguida, criar e executar um experimento que usa o código Python no arquivo Hello zip ao anexá-lo à terceira porta de entrada a **Executar Script Python** módulo, conforme mostrado na imagem a seguir.

![Exemplo de experimento com Hello como uma entrada para um módulo Executar Script Python](./media/execute-python-scripts/figure6a.png)

![Código Python definido pelo usuário carregado como um arquivo zip](./media/execute-python-scripts/figure6b.png)

A saída do módulo mostra que o arquivo zip foi descompactado e a função `print_hello` foi executada.

![Saída do módulo que mostra a função definida pelo usuário](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Acessar Blobs de armazenamento do Azure

Você pode acessar dados armazenados em uma conta de armazenamento de BLOBs do Azure usando as seguintes etapas:

1. Baixe o [pacote de armazenamento de BLOBs do Azure para Python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) localmente.
1. Carregue o arquivo zip para seu espaço de trabalho do Studio como um conjunto de dados.
1. Criar seu objeto BlobService com `protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Desabilitar **transferência segura obrigatória** em seu armazenamento **configuração** guia de configuração

![Desabilitar a transferência segura obrigatória no portal do Azure](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Operacionalizando scripts Python

Quaisquer módulos [Executar Script Python][execute-python-script] usados em um teste de pontuação são chamados quando publicados como um serviço Web. Por exemplo, a imagem abaixo mostra um experimento de pontuação que contém o código para avaliar uma única expressão de Python.

![Espaço de trabalho do Studio para um serviço web](./media/execute-python-scripts/figure3a.png)

![Expressão de Python Pandas](./media/execute-python-scripts/python-script-with-python-pandas.png)

Um serviço web criado com base neste teste seria executar as seguintes ações:

1. Usar uma expressão de Python como entrada (como uma cadeia de caracteres)
1. Enviar a expressão de Python para o interpretador do Python
1. Retorna uma tabela que contém a expressão e o resultado avaliado.

## <a id="visualizations"></a>Trabalhando com visualizações

Gráficos plotados criado usando o MatplotLib que podem ser retornados pela [Executar Script Python][execute-python-script]. No entanto, gráficos não são redirecionados automaticamente para imagens que eles sejam quando usando o R. Portanto, o usuário deve salvar explicitamente qualquer gráfico para arquivos PNG.

Para gerar imagens do MatplotLib, você deve executar as seguintes etapas:

1. Alterne o back-end para "AGG" do processador padrão com base em Qt.
1. Crie um novo objeto de figura.
1. Obtenha o eixo e gerar todos os gráficos nele.
1. Salve a figura em um arquivo PNG.

Esse processo é ilustrado nas imagens a seguir que cria uma matriz de gráfico de dispersão usando a função scatter_matrix no Pandas.

![Código para salvar figuras MatplotLib em imagens](./media/execute-python-scripts/figure-v1-8.png)

![Clique em Visualizar em um módulo Executar Script Python para exibir os números](./media/execute-python-scripts/figure-v2-9a.png)

![Visualizando gráficos de um experimento de exemplo usando o código do Python](./media/execute-python-scripts/figure-v2-9b.png)

É possível retornar diferentes figuras salvando-as como diferentes imagens. O tempo de execução do Studio seleciona todas as imagens e os concatena para visualização.

## <a name="advanced-examples"></a>Exemplos avançados

O ambiente Anaconda instalado no Studio contém pacotes comuns, como NumPy, SciPy e Scikits-Learn. Esses pacotes podem ser usados com eficiência para processamento de dados em um pipeline de aprendizado de máquina.

Por exemplo, o seguinte experimento e o script ilustram o uso de ensemble Learning em Scikits-Learn para calcular pontuações de importância de recursos para um conjunto de dados. As pontuações podem ser usadas para executar a seleção de recurso supervisionada antes de passar para outro modelo.

Aqui está a função Python usada para calcular as pontuações de importância e ordenar os recursos com base nas pontuações:

![Função para classificar recursos por pontuação](./media/execute-python-scripts/figure8.png)

Depois, o experimento seguinte calcula e retorna as pontuações de importância dos recursos do conjunto de dados “Pima Indian Diabetes” no Azure Machine Learning Studio:

![Experimento para classificar recursos no conjunto de dados Pima Indian Diabetes usando Python](./media/execute-python-scripts/figure9a.png)

![Visualização da saída do módulo Executar Script Python](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Limitações

O [Executar Script Python] [ execute-python-script] módulo atualmente tem as seguintes limitações:

### <a name="sandboxed-execution"></a>Execução em modo seguro

O tempo de execução do Python é uma área restrita no momento e não permite o acesso à rede ou sistema de arquivos local de uma maneira persistente. Todos os arquivos salvos localmente são isolados e excluídos após a conclusão do módulo. O código Python não pode acessar a maioria das pastas do computador em que é executado, com exceção do diretório atual e seus subdiretórios.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Falta de sofisticadas de desenvolvimento e suporte de depuração

 O módulo Python atualmente não dá suporte a recursos de IDE, como IntelliSense e depuração. Além disso, se o módulo falhar em tempo de execução, o rastreamento de pilha do Python completo estará disponível. Porém, ele deve ser exibido no log de saída para o módulo. No momento, recomendamos que você desenvolva e depure scripts Python em um ambiente como IPython e depois importe o código para o módulo.

### <a name="single-data-frame-output"></a>Saída de quadro de dados único

 O ponto de entrada do Python para retornar somente uma estrutura de dados como saída. Não é possível no momento retornar objetos arbitrários do Python como modelos treinados diretamente para o tempo de execução do Studio. Assim como o módulo [Executar Script R][execute-r-script], que tem a mesma limitação, é possível em muitos casos serializar objetos em uma matriz de bytes e retorná-la dentro de um quadro de dados.

### <a name="inability-to-customize-python-installation"></a>Incapacidade de personalizar a instalação do Python

Atualmente, a única maneira de adicionar módulos personalizados do Python é por meio do mecanismo de compactação de arquivo zip descrito anteriormente. Embora isso seja viável para pequenos módulos, é complicado para módulos grandes (especialmente os módulos com DLLs nativos) ou um grande número de módulos.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, confira o [Centro de Desenvolvedores do Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script