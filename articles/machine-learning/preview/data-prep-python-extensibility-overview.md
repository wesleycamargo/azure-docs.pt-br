---
title: "Usar a extensibilidade do Python com a Preparação de Dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece uma visão geral e alguns exemplos detalhados de como usar o código Python para estender a funcionalidade de preparação de dados"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 3c3864480d2fcba4f6d388d4e0d00b917cb62d2b
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2018
---
# <a name="data-preparations-python-extensions"></a>Extensões do Python para Preparação de Dados
Como uma forma de preencher as lacunas de funcionalidade entre os recursos internos, a Preparação de Dados do Azure Machine Learning inclui a extensibilidade em vários níveis. Neste documento, descrevemos a extensibilidade por meio de script do Python. 

## <a name="custom-code-steps"></a>Etapas de código personalizado 
A Preparação de Dados tem as seguintes etapas personalizadas nas quais os usuários podem escrever o código:

* Leitor de arquivo *
* Gravador*
* Adicionar Coluna
* Filtro Avançado
* Transformar fluxo de dados
* Transformar Partição

* Essas etapas não têm suporte no momento em uma execução do Spark.

## <a name="code-block-types"></a>Tipos de bloco de código 
Para cada uma dessas etapas, há suporte para dois tipos de bloco de código. Primeiro, há suporte para uma expressão de Python básica que é executada no estado em que se encontra. Em segundo lugar, há suporte para um módulo de Python em que podemos chamar uma função específica com uma assinatura conhecida no código que você fornecer.

Por exemplo, você pode adicionar uma nova coluna que calcula o log de outra coluna de uma das duas maneiras a seguir:

Expression 

```python    
    math.log(row["Score"])
```

Módulo 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


A transformação Adicionar Coluna no modo de Módulo espera encontrar uma função chamada `newvalue` que aceita uma variável de linha e retorna o valor da coluna. Esse módulo pode incluir qualquer quantidade de código Python com outras funções, importações etc.

Os detalhes de cada ponto de extensão são discutidos nas seções a seguir. 

## <a name="imports"></a>Importações 
Se você usar o tipo de bloco Expressão, ainda poderá adicionar instruções **import** ao seu código. Todas devem estar agrupadas nas linhas superiores do seu código.

Correto 

```python
import math 
import numpy 
math.log(row["Score"])
```
 

Erro  

```python
import math  
math.log(row["Score"])  
import numpy
```
 
 
Se você usar o tipo de bloco Módulo, poderá seguir todas as regras de Python normais para usar a instrução **import**. 

## <a name="default-imports"></a>Importações padrão
As importações a seguir são sempre incluídas e utilizáveis no código. Não é necessário reimportá-las. 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="install-new-packages"></a>Instalar novos pacotes
Para usar um pacote que não está instalado por padrão, primeiro é necessário instalá-lo nos ambientes que a Preparação de Dados usa. Essa instalação precisa ser feita tanto no seu computador local quanto em todos os destinos de computação nos quais você deseja executar.

Para instalar os pacotes em um destino de computação, você precisa modificar o arquivo conda_dependencies.yml localizado na pasta aml_config sob a raiz do seu projeto.

### <a name="windows"></a>Windows 
Para encontrar o local no Windows, localize a instalação específica de aplicativo do Python e seu diretório de scripts. O local padrão é:  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts.` 

Em seguida, execute um dos comandos a seguir: 

`conda install <libraryname>` 

ou o 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
Para encontrar o local em um Mac, localize a instalação específica de aplicativo do Python e seu diretório de scripts. O local padrão é: 

`/Users/<user>/Library/Caches/AmlWorkbench>/Python/bin` 

Em seguida, execute um dos comandos a seguir: 

`./conda install <libraryname>`

ou o 

`./pip install <libraryname>`

## <a name="use-custom-modules"></a>Use módulos personalizados
Em Transformar Fluxo de Dados (Script), escreva um código Python como este:

```python
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction1
df = ExtensionFunction1(df)
```

Em Adicionar Coluna (Script), defina Code Block Type = Module e escreva o código Python a seguir:

```python 
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction2

def newvalue(row):
    return ExtensionFunction2(row)
```
Para contextos de execução diferentes (local, docker spark), aponte o caminho absoluto para o local correto. Você talvez queira usar "os.getcwd() + relativePath" para localizá-lo.


## <a name="column-data"></a>Dados de coluna 
Os dados de coluna podem ser acessados de uma linha usando a notação de ponto ou a notação de chave-valor. Os nomes de colunas que contêm espaços ou caracteres especiais não podem ser acessados usando a notação de ponto. A variável `row` sempre deve ser definida em ambos os modos de extensões do Python (Módulo e Expressão). 

Exemplos 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="file-reader"></a>Leitor de arquivos 
### <a name="purpose"></a>Finalidade 
Esse ponto de extensão do Leitor de arquivos permite controlar totalmente o processo de leitura de um arquivo em um fluxo de dados. O sistema chama o código e transmite a lista de arquivos que você deve processar. Seu código precisa criar e retornar um dataframe Pandas. 

>[!NOTE]
>Esse ponto de extensão não funciona no Spark. 


### <a name="how-to-use"></a>Como usar 
Acesse esse ponto de extensão no assistente **Abrir Fonte de Dados**. Escolha **Arquivo** na primeira página e, em seguida, escolha o local do arquivo. Na página **Escolher Parâmetros do Arquivo**, abra a lista suspensa **Tipo de Arquivo** e escolha **Arquivo Personalizado (Script)**. 

O código recebe um dataframe Pandas chamado “df” que contém informações sobre os arquivos que você precisa ler. Se você optar por abrir um diretório que contém vários arquivos, o dataframe terá mais de uma linha.  

Esse dataframe tem as seguintes colunas:

- Caminho: o arquivo a ser lido.
- PathHint: informa onde o arquivo está localizado. Valores: Local, AzureBlobStorage e AzureDataLakeStorage.
- AuthenticationType: o tipo de autenticação usado para acessar o arquivo. Valores: None, SasToken e OAuthToken.
- AuthenticationValue: contém None ou o token a ser usado.

### <a name="syntax"></a>Sintaxe 
Expression 

```python
    paths = df['Path'].tolist()  
    df = pd.read_csv(paths[0])
```


Módulo  
```python
PathHint = Local  
def read(df):  
    paths = df['Path'].tolist()  
    filedf = pd.read_csv(paths[0])  
    return filedf  
```
 

## <a name="writer"></a>Gravador 
### <a name="purpose"></a>Finalidade 
O ponto de extensão do Gravador permite controlar totalmente o processo de gravação de dados de um fluxo de trabalho. O sistema chama o código e transmite um dataframe. Seu código pode usar o dataframe para gravar dados de maneira que desejar. 

>[!NOTE]
>O ponto de extensão do Gravador não funciona no Spark.


### <a name="how-to-use"></a>Como usar 
Adicione esse ponto de extensão usando o bloco Gravar Fluxo de Dados (Script). Ele está disponível no menu **Transformações** no nível superior.

### <a name="syntax"></a>Sintaxe 
Expression

```python
    df.to_csv('c:\\temp\\output.csv')
```

Módulo

```python
def write(df):  
    df.to_csv('c:\\temp\\output.csv')  
    return df
```
 
 
Este bloco de gravação personalizado pode existir no meio de uma lista de etapas. Se você usar um Módulo, a função de gravação deverá retornar o dataframe que é a entrada para a etapa seguinte. 

## <a name="add-column"></a>Adicionar Coluna 
### <a name="purpose"></a>Finalidade
O ponto de extensão Adicionar Coluna permite escrever em Python para calcular uma nova coluna. O código que você grava tem acesso à linha inteira. Ele deve retornar um novo valor de coluna para cada linha. 

### <a name="how-to-use"></a>Como usar
Adicione esse ponto de extensão usando o bloco Adicionar Coluna (Script). Ele está disponível no menu **Transformações** no nível superior, bem como no menu de contexto de **Coluna**. 

### <a name="syntax"></a>Sintaxe
Expression

```python
    math.log(row["Score"])
```

Módulo 

```python
def newvalue(row):  
     return math.log(row["Score"])
```
 

## <a name="advanced-filter"></a>Filtro Avançado
### <a name="purpose"></a>Finalidade 
O ponto de extensão Filtro Avançado permite escrever um filtro personalizado. Você tem acesso a toda a linha e seu código deve retornar True (incluir a linha) ou False (excluir a linha). 

### <a name="how-to-use"></a>Como usar
Adicione esse ponto de extensão usando o bloco Filtro Avançado (Script). Ele está disponível no menu **Transformações** no nível superior. 

### <a name="syntax"></a>Sintaxe

Expression

```python
    row["Score"] > 95
```

Módulo  

```python
def includerow(row):  
    return row["Score"] > 95
```
 

## <a name="transform-dataflow"></a>Transformar fluxo de dados
### <a name="purpose"></a>Finalidade 
O ponto de extensão Transformar Fluxo de Dados permite transformar completamente o fluxo de dados. Você tem acesso a um dataframe Pandas que contém todas as colunas e linhas que você está processando. Seu código deve retornar um dataframe Pandas com os novos dados. 

>[!NOTE]
>No Python, todos os dados que deverão ser carregados na memória estarão em um dataframe Pandas se essa extensão for usada. 
>
>No Spark, todos os dados são coletados em um nó único trabalhador. Se os dados forem muito grandes, um trabalho poderá ficar sem memória. Use com cuidado.

### <a name="how-to-use"></a>Como usar 
Adicione esse ponto de extensão usando o bloco Transformar Fluxo de Dados (Script). Ele está disponível no menu **Transformações** no nível superior. 
### <a name="syntax"></a>Sintaxe 

Expression

```python
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Módulo 

```python
def transform(df):  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()  
    return df
```
  

## <a name="transform-partition"></a>Transformar Partição  
### <a name="purpose"></a>Finalidade 
O ponto de extensão Transformar Partição permite transformar uma partição do fluxo de dados. Você tem acesso a um dataframe Pandas que contém todas as colunas e linhas da partição. Seu código deve retornar um dataframe Pandas com os novos dados. 

>[!NOTE]
>No Python, você pode acabar com uma única partição ou várias delas, dependendo do tamanho dos dados. No Spark, você estará trabalhando com um dataframe que contém os dados para uma partição em determinado nó de trabalho. Em ambos os casos, não é possível supor que tem você tem acesso a todo o conjunto de dados. 


### <a name="how-to-use"></a>Como usar
Você pode adicionar esse ponto de extensão usando o bloco Transformar Partição (Script). Ele está disponível no menu **Transformações** no nível superior. 

### <a name="syntax"></a>Sintaxe 

Expression 

```python
    df['partition-id'] = index  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Módulo 

```python
def transform(df, index):
    df['partition-id'] = index
    df['index-column'] = range(1, len(df) + 1)
    df = df.reset_index()
    return df
```


## <a name="datapreperror"></a>DataPrepError  
### <a name="error-values"></a>Valores de erro  
Na Preparação de Dados, existe o conceito de valores de erro. 

É possível encontrar os valores de erro em código Python personalizado. Eles são instâncias de uma classe de Python chamada `DataPrepError`. Essa classe encapsula uma exceção de Python e tem algumas propriedades. As propriedades contêm informações sobre o erro que ocorreu ao processar o valor original, bem como o próprio valor original. 


### <a name="datapreperror-class-definition"></a>Definição da classe DataPrepError
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
A criação de um DataPrepError no framework do Python de Preparação de Dados geralmente tem esta aparência: 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>Como usar 
É possível gerar DataPrepErrors como valores retornados usando o método de criação anterior quando o Python é executado em um ponto de extensão. É muito mais provável encontrar DataPrepErrors ao processar dados em um ponto de extensão. Neste ponto, o código Python personalizado precisa tratar um DataPrepError como um tipo de dados válido.

#### <a name="syntax"></a>Sintaxe 
Expression 
```python 
    if (isinstance(row["Score"], DataPrepError)): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
```python 
    if (hasattr(row["Score"], "originalValue")): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
Módulo 
```python 
def newvalue(row): 
    if (isinstance(row["Score"], DataPrepError)): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
``` 
```python 
def newvalue(row): 
    if (hasattr(row["Score"], "originalValue")): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
```  
