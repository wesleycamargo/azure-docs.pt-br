---
title: Analisar dados usando o Jupyter Notebook e Kqlmagic
description: Este tópico mostra como analisar dados usando o Jupyter Notebook e KQLmagic
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 7152b1d09a11d5860d52b5f73ae601422bd0f722
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2018
ms.locfileid: "53654445"
---
# <a name="analyze-data-using-jupyter-notebook-and-kqlmagic"></a>Analisar dados usando o Jupyter Notebook e Kqlmagic
O Jupyter Notebook é um aplicativo Web de código-fonte aberto que permite que criar e compartilhar documentos que contêm código ao vivo, equações, visualizações e texto narrativo. O uso inclui limpeza de dados e transformação, simulação numérica, modelagem estatística, visualização de dados e aprendizado de máquina.
O [Jupyter Notebook](https://jupyter.org/) dá suporte a funções mágicas que ampliam os recursos do kernel, oferecendo suporte a comandos adicionais. Kqlmagic é uma função que estende os recursos do kernel no Jupyter Notebook ao Python para poder executar consultas de linguagem do Kusto nativamente. Você pode facilmente combinar a linguagem de consulta Python e Kusto para consultar e visualizar dados usando a rica biblioteca Plot.ly integrada com comandos `render`. Há suporte para fontes de dados para execução de consultas. Essas fontes de dados incluem o Azure Data Explorer, um serviço de exploração de dados rápido e altamente escalonável para dados de log e de telemetria, bem como o Log Analytics e Application Insights.

## <a name="prerequisites"></a>Pré-requisitos
- Uma conta de e-mail da organização que é membro do Azure Active Directory (AAD).
- Jupyter Notebook instalado no computador local ou use o Azure Notebooks e clone o exemplo [Azure Notebooks](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)


## <a name="install-kqlmagic-library"></a>Instalar a biblioteca Kqlmagic

1. Instale Kqlmagic:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```

2. Carregue Kqlmagic:

    ```python
    reload_ext Kqlmagic
    ```

## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Conecte-se ao cluster de ajuda do Azure Data Explorer

Use o seguinte comando para se conectar ao banco de dados *Amostras* hospedado no cluster *Ajuda*. Para usuários do AAD não Microsoft, substitua o nome do locatário `Microsoft.com` com seu locatário do AAD.


```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Consultar e visualizar

Consulte de dados usando o [operador renderizar](/azure/kusto/query/renderoperator) e visualize dados usando a biblioteca ploy.ly. Essa consulta e visualização fornece uma experiência integrada que usa um KQL nativo. O Kqlmagic dá suporte a maioria dos gráficos, exceto `timepivot`, `pivotchart`, e `ladderchart`. A renderização é compatível com todos os atributos, exceto `kind`, `ysplit`, e `accumulate`. 

### <a name="query-and-render-piechart"></a>Consultar e renderizar o gráfico de pizza

```python
%%kql 
StormEvents 
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Consultar e renderizar o gráfico de tempo

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Os gráficos são interativos. Selecione um intervalo de tempo para ampliar um horário específico.

### <a name="customize-the-chart-colors"></a>Personalizar a legenda de cores
Se não gostar da paleta de cores padrão, personalize os gráficos usando as opções de paleta. As paletas disponíveis podem ser encontradas aqui: [Escolha a paleta de cores para o resultado do gráfico de consulta Kqlmagic](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. Para uma lista de paletas:

    ```python
    %kql --palettes -popup_window
    ```

1. Selecione a paleta de cores`cool` e renderize a consulta novamente:

    ```python
    %%kql -palette_name "cool"
    StormEvents 
    | summarize statecount=count() by State
    | sort by statecount 
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parametrize-a-query-with-python"></a>Parametrizar uma consulta com o Python

O Kqlmagic permite um intercâmbio simples entre a linguagem de consulta Kusto e Python. Para saber mais: [Parametrizar sua consulta Kqlmagic com Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 

### <a name="use-a-python-variable-in-your-kql-query"></a>Usar uma variável de Python em sua consulta KQL

Use o valor de uma variável de Python em sua consulta para filtrar os dados:

```python
statefilter = ["TEXAS", "KANSAS"]
```

```python
%%kql
let _state = statefilter;
StormEvents 
| where State in (_state) 
| summarize statecount=count() by bin(StartTime,1d), State
| render timechart title = "Trend"
```

### <a name="convert-query-results-to-pandas-dataframe"></a>Converter os resultados da consulta em DataFrame do Pandas 

Você pode acessar os resultados de uma consulta KQL no DataFrame Pandas. Acesse os últimos resultados da consulta executada pela variável `_kql_raw_result_` e converta facilmente os resultados em DataFrame Pandas da seguinte maneira:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Exemplo 

Em muitos cenários de análise, você talvez queira criar blocos de anotações reutilizáveis que contêm muitas consultas e alimentam os resultados de uma consulta em consultas subsequentes. O exemplo a seguir usa a variável de Python `statefilter` para filtrar os dados.

1. Executar uma consulta para exibir os estados de 10 principais com máximo `DamageProperty`:

    ```python
    %%kql
    StormEvents 
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Execute uma consulta para extrair o estado superior e defina-o em uma variável de Python:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Executar uma consulta usando a instrução `let` e a variável de Python:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state) 
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Execute o comando de Ajuda: 

    ```python
    %kql --help "help"
    ```

## <a name="next-steps"></a>Próximas etapas
    
Execute o comando de ajuda para explorar os seguintes notebooks de amostra que contêm todos os recursos com suporte:
- [Introdução ao Kqlmagic para o Azure Data Explorer ](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Introdução ao Kqlmagic para Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Introdução ao Kqlmagic para Log Analytics](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Parametrizar sua consulta Kqlmagic com Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Escolha a paleta de cores para o resultado do gráfico de consulta Kqlmagic](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)



