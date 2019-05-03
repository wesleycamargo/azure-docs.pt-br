---
title: 'Execute Script Python: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo Executar Script Python no serviço do Azure Machine Learning para executar o código Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 6e61ed5a18e69b107b78bf2042de21d14cd6beb5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029124"
---
# <a name="execute-python-script-module"></a>Executar o módulo de Script do Python

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para executar o código Python. Para obter mais informações sobre os princípios de arquitetura e design do Python, consulte [o artigo a seguir.](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

Com o Python, você pode executar tarefas que não são atualmente suportadas por módulos existentes, como:

+ Visualizar dados usando `matplotlib`
+ Usando bibliotecas de Python para enumerar os conjuntos de dados e modelos em seu espaço de trabalho
+ Ler, carregar e manipulação de dados de fontes não são compatíveis com o [importação de dados](./import-data.md) módulo
+ Executar seu próprio código de aprendizagem profunda 


O Azure Machine Learning usa a distribuição Anaconda do Python, que inclui muitos utilitários comuns para processamento de dados. Atualizaremos o Anaconda versão automaticamente. Versão atual é:
 -  Distribuição do anaconda 4.5 + para o Python 3.6 

Os pacotes pré-instalados são:
-  asn1crypto = = 0.24.0
- attrs==19.1.0
- azure-common==1.1.18
- azure-storage-blob==1.5.0
- azure-storage-common==1.4.0
- certifi==2019.3.9
- cffi==1.12.2
- chardet==3.0.4
- cryptography==2.6.1
- distribuição = = 1.4.0
- idna==2.8
- jsonschema==3.0.1
- lightgbm==2.2.3
- more-itertools==6.0.0
- numpy==1.16.2
- pandas==0.24.2
- Almofadado = = 6.0.0
- pip==19.0.3
- pyarrow = = 0.12.1
- pycparser = = 2.19
- pycryptodomex==3.7.3
- pyrsistent = = 0.14.11
- python-dateutil==2.8.0
- pytz = = 2018.9
- requests==2.21.0
- scikit-learn==0.20.3
- scipy==1.2.1
- setuptools==40.8.0
- six==1.12.0
- torch==1.0.1.post2
- torchvision==0.2.2.post3
- urllib3 = = 1.24.1
- roda = = 0.33.1 

 Para instalar outros pacotes não está na lista previamente instalada, por exemplo *scikit-misc*, adicione o seguinte código ao seu script: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>Como usar

O **Executar Script Python** módulo contém código Python de exemplo que você pode usar como ponto de partida. Para configurar o **Executar Script Python** módulo, você fornece um conjunto de entradas e o código do Python para executar na **script Python** caixa de texto.

1. Adicione a **Executar Script Python** módulo ao seu experimento.

2. Adicionar e conectar-se no **Dataset1** quaisquer conjuntos de dados da interface que você deseja usar para a entrada. Fazer referência a esse conjunto de dados no seu script Python **DataFrame1**.

    Uso de um conjunto de dados é opcional, se você deseja gerar os dados usando o Python ou usa o código Python para importar os dados diretamente para o módulo.

    Este módulo oferece suporte a adição de um segundo conjunto de dados no **Dataset2**. O segundo conjunto de dados em seu script do Python como DataFrame2 de referência.

    Conjuntos de dados armazenados no Azure Machine Learning são convertidos automaticamente para **pandas** quadros de dados quando carregado com este módulo.

    ![Executar o mapa de entrada do Python](media/module/python-module.png)

4. Para incluir novos pacotes do Python ou código, adicione o arquivo compactado que contém esses recursos personalizados em **pacote de Script**. A entrada para **pacote de Script** deve ser um arquivo compactado já carregado em seu espaço de trabalho. 

    Qualquer arquivo contido no arquivo morto compactado carregado pode ser usado durante a execução de teste. Se o arquivo inclui uma estrutura de diretório, a estrutura é preservada, mas você deve colocar um diretório chamado **src** ao caminho.

5. No **script Python** caixa de texto, digite ou cole o script de Python válido.

    O **script Python** caixa de texto é preenchida previamente com algumas instruções em comentários e código de exemplo para acesso a dados e de saída. **Você deve editar ou substituir esse código.** Certifique-se de seguir as convenções do Python sobre recuo e maiusculas e minúsculas.

    + O script deve conter uma função chamada `azureml_main` como ponto de entrada para esse módulo.
    + A função de ponto de entrada pode conter até dois argumentos de entrada: `Param<dataframe1>` e `Param<dataframe2>`
    + Conectado à terceira porta de entrada de arquivos compactados são descompactados e armazenados no diretório, `.\Script Bundle`, que também é adicionado para o Python `sys.path`. 

    Portanto, se o arquivo zip contiver `mymodule.py`, importe-o usando `import mymodule`.

    + Dois conjuntos de dados podem ser retornados para a interface, que deve ser uma sequência de tipo `pandas.DataFrame`. Você pode criar outras saídas em seu código Python e gravá-los diretamente no armazenamento do Azure.

6. Execute o experimento, ou selecione o módulo e clique em **executar selecionado** para executar apenas o script de Python.

    Todos os dados e o código é carregado em uma máquina virtual e executar usando o ambiente de Python especificado.

## <a name="results"></a>Resultados

Os resultados de quaisquer computações realizadas pelo código Python incorporado devem ser fornecidos como um pandas. DataFrame, que é automaticamente convertido para o formato de conjunto de dados do Azure Machine Learning, para que você pode usar os resultados com outros módulos do teste.

O módulo retorna dois conjuntos de dados:  
  
+ **Resultados de conjunto de dados 1**, definido pela primeira dataframe pandas retornado no script de Python

+ **2 de conjunto de dados de resultado**, definido pela segunda dataframe pandas retornado no script de Python


## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 