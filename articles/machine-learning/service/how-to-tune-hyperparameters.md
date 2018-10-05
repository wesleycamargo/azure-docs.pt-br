---
title: Ajustar os hiperparâmetros para o seu modelo usando o Azure Machine Learning
description: Saiba como ajustar os hiperparâmetros para seu modelo de aprendizado profundo/machine learning usando o serviço Azure Machine Learning. Você verá como definir o espaço de pesquisa de parâmetro, especificar uma métrica principal para otimizar e encerrar no início configurações de mau desempenho.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b6370fd9125c5b14df781b27e028c139175b7589
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405794"
---
# <a name="tune-hyperparameters-for-your-model"></a>Ajustar os hiperparâmetros para o seu modelo

Neste artigo, você aprenderá a ajustar os hiperparâmetros para seu modelo de maneira eficiente. Você verá como definir o espaço de pesquisa de parâmetro, especificar uma métrica principal para otimizar e encerrar no início configurações de mau desempenho. Você também pode visualizar as várias execuções de treinamento e selecionar o melhor desempenho de configuração para o seu modelo.

## <a name="what-are-hyperparameters"></a>O que são hiperparâmetros?
Hiperparâmetros são parâmetros ajustáveis escolhidos antes de treinar um modelo que controla o processo de treinamento. Por exemplo, antes de treinar uma rede neural profunda, você precisará decidir o número de camadas ocultas na rede e o número de nós em cada camada. Esses valores geralmente permanecem constantes durante o processo de treinamento.

Em cenários de aprendizado profundo/aprendizado de máquina, o desempenho do modelo depende muito os valores de hiperparâmetro selecionados. A meta da exploração de hiperparâmetro é pesquisar em várias configurações de hiperparâmetro para encontrar uma configuração que resulte no desempenho esperado. Normalmente, o processo de exploração de hiperparâmetro é arduamente manual, considerando que o espaço de pesquisa é vasto e a avaliação de cada configuração pode ser cara.

O Azure Machine Learning permite automatizar essa exploração de hiperparâmetro de maneira eficiente, economizando tempo e recursos significativos. Você pode especificar o intervalo de valores de hiperparâmetro a explorar e um número máximo de execuções de treinamento para essa exploração. O sistema então inicializa automaticamente várias execuções de treinamento simultâneas com diferentes configurações de parâmetros e localiza a configuração que resulta no melhor desempenho, conforme medido por uma métrica escolhida pelo usuário. Execuções de treinamento com mau desempenho são automaticamente encerradas de modo antecipado, reduzindo o desperdício de recursos de computação. Em vez disso, esses recursos são usados para explorar outras configurações de hiperparâmetro.

Para ajustar os hiperparâmetros para seu modelo usando o serviço Azure Machine Learning, você precisa fazer o seguinte –
* Definir o espaço de pesquisa de hiperparâmetro
* Especificar uma métrica primária para otimizar
* Especificar uma política de encerramento antecipado
* Aloque recursos para ajuste de hiperparâmetro
* Inicializar um teste com a configuração acima

## <a name="define-the-hyperparameter-search-space"></a>Definir o espaço de pesquisa de hiperparâmetro
O Serviço do Azure Machine Learning ajusta automaticamente hiperparâmetros explorando o intervalo de valores definidos para cada hiperparâmetro.

### <a name="types-of-hyperparameters"></a>Tipos de hiperparâmetros
Cada hiperparâmetro pode ser discreto ou contínuo.

#### <a name="discrete-hyperparameters"></a>Hiperparâmetros discretos 
Hiperparâmetros discretos podem ser especificados como um `choice` entre valores discretos. `choice` pode levar um ou mais valores separados por vírgula, um objeto `range` ou qualquer objeto `list` arbitrário. Por exemplo,  

```Python
    {    
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

Neste caso, batch_size pode assumir um dos valores [16, 32, 64, 128] e number_of_hidden_layers pode assumir um dos valores [1, 2, 3, 4].

Hiperparâmetros discretos avançados também podem ser especificados usando uma distribuição. Há suporte para as seguintes distribuições –
* `quniform(low, high, q)` – Retorna um valor como round(uniform(low, high) / q) * q
* `qloguniform(low, high, q)` – Retorna um valor como round(exp(uniform(low, high)) / q) * q
* `qnormal(mu, sigma, q)` – Retorna um valor como round(normal(mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)` – Retorna um valor como round(exp(normal(mu, sigma)) / q) * q

#### <a name="continuous-hyperparameters"></a>Hiperparâmetros contínuos 
Hiperparâmetros contínuos podem ser especificados como uma distribuição em um intervalo de valores contínuos. Distribuições com suporte incluem –
* `uniform(low, high)` – Retorna um valor distribuído uniformemente entre baixo e alto
* `loguniform(low, high)` – Retorna um valor traçado conforme exp(uniform(low, high)) de modo que o logaritmo do valor retornado seja distribuído uniformemente
* `normal(mu, sigma)` – Retorna um valor real normalmente distribuído com sigma de desvio padrão e mu médio
* `lognormal(mu, sigma)` – Retorna um valor traçado conforme exp(normal(mu, sigma)) de modo que o logaritmo do valor retornado é normalmente distribuído

Aqui está um exemplo de uma definição de espaço de parâmetro –

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Este exemplo define um espaço de pesquisa com dois parâmetros – learning_rate e keep_probability. learning_rate terá uma distribuição normal com um desvio padrão de 3 e valor médio de 10. keep_probability terá uma distribuição uniforme com um valor mínimo de 0,05 e um valor máximo de 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Amostragem do espaço do hiperparâmetro
O usuário também especifica o método de amostragem de parâmetro a ser usado na definição de espaço do hiperparâmetro especificada. O serviço do Azure Machine Learning dá suporte para amostragem Aleatória, amostragem de Grade e amostragem Bayesiana.

#### <a name="random-sampling"></a>Amostragem Aleatória
Na amostragem Aleatória, os valores de hiperparâmetro serão selecionadas aleatoriamente do espaço de pesquisa definido. A amostragem Aleatória permite que o espaço de pesquisa inclua hiperparâmetros discretos e contínuos. Por exemplo,

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Amostragem de Grade
A amostragem de Grade realiza uma pesquisa de grade simples em todos os valores viáveis no espaço de pesquisa definido. Pode ser usada apenas com hiperparâmetros especificados usando `choice`. Por exemplo, o espaço a seguir tem um total de seis exemplos –

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Amostragem Bayesiana
A amostragem Bayesiana baseia-se no algoritmo de Otimização Bayesiana e faz escolhas inteligentes nos valores de hiperparâmetro a serem amostrados a seguir. Ela escolhe essa amostra com base no desempenho de amostras anteriores de modo que a nova amostra aprimore a métrica primária relatada.

Ao usar a amostragem Bayesiana, o número de execuções simultâneas tem um impacto sobre a eficácia do processo de ajuste. Normalmente, um número menor de execuções simultâneas pode levar a uma melhor convergência de amostragem. Isso ocorre porque o menor grau de paralelismo aumenta o número de execuções que se beneficiam de execuções concluídas anteriormente.

Amostragem Bayesiana dá apenas suporte a distribuições `choice` e `uniform` no espaço de pesquisa. Por exemplo, 

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> A amostragem Bayesiana não oferece suporte a nenhuma política de encerramento antecipado (veja [Especificar uma política de encerramento antecipado](#specify-an-early-termination-policy)). Se estiver usando a amostragem de parâmetro Bayesiana, defina a política como `None`. Não especificar uma política de encerramento com amostragem Bayesiana terá o mesmo efeito.
>
> ```Python
> early_termination_policy = None
> ```

## <a name="specify-a-primary-metric-to-optimize"></a>Especificar uma métrica primária para otimizar
Ao ajustar hiperparâmetros, você precisa especificar a métrica primária que deseja que o experimento de ajuste de hiperparâmetro otimize. Cada execução de treinamento é avaliada para essa métrica primária e execuções com desempenho ruim (em que a métrica primária não atende aos critérios definidos pela política de rescisão antecipada) serão encerradas. Além de especificar o nome da métrica primária, você também precisará especificar a meta da otimização – maximizar ou minimizar a métrica primária.
* `primary_metric_name`: o nome da métrica primária a otimizar. O nome da métrica primária deve corresponder exatamente ao nome da métrica registrada pelo script de treinamento. Veja [Registrar em log métricas de ajuste de hiperparâmetro](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: pode ser tanto `PrimaryMetricGoal.MAXIMIZE` quanto `PrimaryMetricGoal.MINIMIZE` e determina se a métrica primária será maximizada ou minimizada ao avaliar as execuções. 

Por exemplo –
```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```
Isso otimizará as execuções para maximizar a "precisão".

### <a name="log-metrics-for-hyperparameter-tuning"></a>Registrar em log métricas de ajuste de hiperparâmetro
Para usar o serviço de Azure Machine Learning para ajuste de hiperparâmetro, o script de treinamento para o seu modelo precisará relatar métricas relevantes enquanto o modelo é executado. O usuário especifica a métrica principal que deseja que o serviço use para avaliar o desempenho de execução e o script de treinamento precisará registrar essa métrica em log. Veja [Especificar uma métrica primária para otimizar](#specify-a-primary-metric-to-optimize).

Você pode atualizar o seu script de treinamento para registrar essa métrica em log usando o seguinte snippet de amostra –

```Python
from azureml.core.run import Run
run_logger = Run.get_submitted_run()
run_logger.log("accuracy", float(val_accuracy))
```

Neste exemplo, o script de treinamento calcula o `val_accuracy` e registra em log essa "precisão", que é usada como a métrica principal. Cabe ao desenvolvedor modelo determinar a frequência para relatar essa métrica.

## <a name="specify-an-early-termination-policy"></a>Especificar uma Política de Encerramento Antecipado
Ao usar o serviço Azure Machine Learning para ajustar hiperparâmetros, execuções de mau desempenho são encerradas antecipadamente. Isso reduz o desperdício de recursos e usa esses recursos para explorar outras configurações de parâmetro.

Ao usar uma política de encerramento antecipado, um usuário pode configurar os seguintes parâmetros que controlam quando uma política é aplicada –
* `evaluation_interval`: a frequência para aplicar a política. Cada vez que o script de treinamento registra em log a métrica primária conta como um intervalo. Assim, um `evaluation_interval` de 1 aplicará a política sempre outro que o script de treinamento relatar a métrica primária. Um `evaluation_interval` de 2 aplicará a política sempre outro que o script de treinamento relatar a métrica primária. Este é um parâmetro opcional e, se não for especificado, `evaluation_interval` será definido como 1 por padrão.
* `delay_evaluation`: atrasa a primeira avaliação de política para um número especificado de intervalos. Este é um parâmetro opcional que permite que todas as configurações sejam executadas para um número mínimo inicial de intervalos, evitando o encerramento prematuro de execuções de treinamento. Se for especificado, a política se aplicará a cada múltiplo de evaluation_interval maior ou igual a delay_evaluation.

O Serviço Azure Machine Learning tem suporte para as seguintes políticas de término antecipado –

### <a name="bandit-policy"></a>Política Bandit
A Política Bandit é uma política de encerramento com base em intervalo de avaliação e quantidade de margem de atraso/fator de margem de atraso. Essa política encerra todas as execuções em que a métrica primária não está dentro do fator de margem de atraso/quantidade de margem de atraso especificado em relação à execução de treinamento de melhor desempenho. Usa os seguintes parâmetros de configuração –
* `slack_factor` ou `slack_amount`: a margem de atraso permitida em relação à execução de treinamento de melhor desempenho. `slack_factor` especifica a margem de atraso permitida como uma proporção. `slack_amount` especifica a margem de atraso permitida como um valor absoluto, em vez de uma proporção.

    Por exemplo, considere uma política Bandit sendo aplicada no intervalo de 10. Suponha que a execução de melhor desempenho no intervalo 10 tenha relatado uma métrica primária de 0,8 com uma meta para maximizar a métrica primária. Se a política tiver sido especificada com um `slack_factor` de 0,2, qualquer execução de treinamento cuja melhor métrica no intervalo de 10 seja menor que 0,66 (0,8/(1+`slack_factor`)) será encerrada. Se, em vez disso, a política tiver sido especificada com um `slack_amount` de 0,2, qualquer execução de treinamento cuja melhor métrica no intervalo de 10 seja menor que 0,6 (0,8 – `slack_amount`) será encerrada.
* `evaluation_interval`: a frequência para aplicar a política (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação de política para um número especificado de intervalos (parâmetro opcional).

Considere este exemplo –

```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

Neste exemplo, a política de encerramento inicial é aplicada a cada intervalo quando as métricas são relatadas, começando no intervalo de avaliação 5. Qualquer execução cuja melhor métrica seja menor que (1/(1+0,1) ou 91% da execução de melhor desempenho será encerrada.

### <a name="median-stopping-policy"></a>Política de Encerramento Mediana
A Política de Encerramento Mediana é uma política de encerramento inicial com base em médias corridas de métricas primárias relatadas pelas execuções. Esta política computa as médias corridas em todas as execuções de treinamento e encerra execuções cujo desempenho seja pior do que a mediana das médias corridas. Esta política usa os seguintes parâmetros de configuração –
* `evaluation_interval`: a frequência para aplicar a política (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação de política para um número especificado de intervalos (parâmetro opcional).

Considere este exemplo –

```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

Neste exemplo, a política de encerramento inicial é aplicada a cada intervalo começando em um intervalo de avaliação 5. Uma execução será encerrada no intervalo de 5 se a sua melhor métrica primária for pior do que a mediana de médias corridas em intervalos de 1:5 em todas as execuções de treinamento.

### <a name="truncation-selection-policy"></a>Política de Seleção de Truncamento
A Política de Seleção de Truncamento cancela um dado percentual das execuções de menor desempenho a cada intervalo de avaliação. As execuções são comparadas com base em seu desempenho na métrica primária e os X% inferiores são encerrados. Usa os seguintes parâmetros de configuração –
* `truncation_percentage`: o percentual de execuções de menor desempenho a encerrar a cada intervalo de avaliação. Deve ser um valor inteiro entre 1 e 99.
* `evaluation_interval`: a frequência para aplicar a política (parâmetro opcional).
* `delay_evaluation`: atrasa a primeira avaliação de política para um número especificado de intervalos (parâmetro opcional).

Considere este exemplo –

```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

Neste exemplo, a política de encerramento inicial é aplicada a cada intervalo começando em um intervalo de avaliação 5. Uma execução será encerrada no intervalo de 5 se seu desempenho no intervalo de 5 estiver nos 20% mais baixos de desempenho de todas as execuções em um intervalo de 5.

### <a name="no-termination-policy"></a>Nenhuma Política de Encerramento
Se desejar que todas as execuções de treinamento sejam executadas até a conclusão, use NoTerminationPolicy. Isso terá o efeito de não aplicar nenhuma política de encerramento antecipado. Por exemplo, 

```Python
from azureml.train.hyperdrive import NoTerminationPolicy
early_termination_policy = NoTerminationPolicy()
```

### <a name="default-policy"></a>Política padrão
Se nenhuma política for especificada, o serviço de ajuste de hiperparâmetro usará uma Política de Encerramento Mediana com `evaluation_interval` 1 e `delay_evaluation` 5 por padrão. Essas são configurações conservadoras, que podem fornecer aproximadamente 25 a 35% de economia sem perda na métrica primária (com base em nossos dados de avaliação).

## <a name="allocate-resources-for-hyperparameter-tuning"></a>Aloque recursos para ajuste de hiperparâmetro
Você pode controlar seu orçamento de recursos para seu experimento de ajuste de hiperparâmetro especificando o número total máximo de execuções de treinamento e, opcionalmente, a duração máxima (em minutos) de seu experimento de ajuste de hiperparâmetro. 
* `max_total_runs`: número total máximo de execuções de treinamento que serão criadas. Esse é um limite superior – poderemos ter menos execuções, por exemplo, se o espaço do hiperparâmetro for finito e tiver menos amostras. Deve ser um número entre 1 e 1000.
* `max_duration_minutes`: duração máxima do experimento de ajuste de hiperparâmetro em minutos. Este é um parâmetro opcional e, se estiver presente, todas as execuções que podem estar em execução após esta duração serão canceladas automaticamente.

>[!NOTE] 
>Se tanto `max_total_runs` quanto `max_duration_minutes` forem especificados, o experimento de ajuste de hiperparâmetro será encerrado quando o primeiro dos dois desses limites for atingido.

Além disso, você pode especificar o número máximo de execuções de treinamento a serem executadas simultaneamente durante sua pesquisa de ajuste de hiperparâmetro.
* `max_concurrent_runs`: é o número máximo de execuções a serem executadas simultaneamente em um determinado momento. Se nenhum for especificado, todas as `max_total_runs` serão iniciadas em paralelo. Se especificado, deverá ser um número entre 1 e 100.

>[!NOTE] 
>O número de execuções simultâneas é ligado aos recursos disponíveis no destino de computação especificado. Portanto, você precisará garantir que o destino de computação tenha os recursos disponíveis para a simultaneidade desejada.

Você pode alocar recursos para ajuste de hiperparâmetro conforme mostrado neste exemplo –
```Python
max_total_runs=20,
max_concurrent_runs=4
```
Isso configurará o experimento de ajuste de hiperparâmetro para usar um máximo de 20 execuções no total, executando quatro configurações por vez.

## <a name="configure-your-hyperparameter-tuning-experiment"></a>Configurar seu experimento de ajuste de hiperparâmetro
Você pode configurar seu experimento de ajuste de hiperparâmetro usando o espaço de pesquisa de hiperparâmetro definido, a política de encerramento antecipado, a métrica principal e a alocação de recurso das seções acima. Além disso, você precisará fornecer um `estimator` que será chamado com os hiperparâmetros de amostra. O `estimator` descreve o script de treinamento que você executa, os por trabalho (GPU única ou múltipla) e o destino de computação a ser usado. Uma vez que a simultaneidade para seu experimento de ajuste de hiperparâmetro é restrita nos recursos disponíveis, você precisará garantir que seu destino de computação especificado no `estimator` tenha recursos suficientes para simultaneidade desejada. (Veja [como treinar modelos](how-to-train-ml-models.md) para obter mais informações sobre estimadores).

Aqui está um exemplo de como você pode configurar seu experimento de ajuste de hiperparâmetro –

```Python
from azureml.train.hyperdrive import HyperDriveRunConfig
hyperdrive_run_config = HyperDriveRunConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-your-hyperparameter-tuning-experiment"></a>Enviar seu experimento de ajuste de hiperparâmetro
Depois de definir sua configuração de ajuste de parâmetro, você pode enviar um experimento usando esta configuração –

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

em que `experiment_name` é o nome que você deseja atribuir ao seu experimento de ajuste de hiperparâmetro e `workspace` é o workspace no qual você deseja criar o experimento (veja o [link](/concept-azure-machine-learning-architecture.md) para obter mais informações sobre experimentos).

## <a name="visualize-your-hyperparameter-tuning-experiment"></a>Visualizar seu experimento de ajuste de hiperparâmetro
O SDK do Azure Machine Learning fornece um widget de bloco de anotações que pode ser usado para visualizar o progresso de suas execuções de treinamento. O snippet a seguir pode ser usado para visualizar todas as suas execuções de ajuste de hiperparâmetro em um só lugar –

```Python
from azureml.train.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Isso exibirá uma tabela com detalhes sobre as execuções de treinamento para cada uma das configurações de hiperparâmetro. Por exemplo,

![tabela de ajuste de hiperparâmetro](media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Você também pode visualizar o desempenho de cada uma das execuções à medida que o treinamento avança. Por exemplo,

![gráfico de ajuste de hiperparâmetro](media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Por fim, você pode identificar visualmente a correlação entre desempenho e valores de hiperparâmetros individuais usando um Gráfico de Coordenadas Paralelas. Por exemplo, 

![coordenadas paralelas de ajuste de hiperparâmetro](media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)

Como alternativa, você pode visualizar todas as execuções de ajuste de hiperparâmetro no portal da Web do Azure. Veja o [link](/how-to-track-experiments.md/#view-the-experiment-in-the-web-portal) para obter mais informações sobre como exibir um teste no portal da Web. Por exemplo, –

![portal de ajuste de hiperparâmetro](media/how-to-tune-hyperparameters/HyperparameterTuningPortal.png)

## <a name="find-the-configuration-that-resulted-in-the-best-performance"></a>Localize a configuração que resultou no melhor desempenho
Depois que todas as execuções de ajuste de hiperparâmetro forem concluídas, você poderá identificar a configuração de melhor desempenho e os valores de hiperparâmetro correspondentes usando o seguinte snippet de código –

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>Exemplo de bloco de anotações
Consulte 
* `training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb` para obter um tutorial sobre ajuste de hiperparâmetros para um modelo do Tensorflow. 

Veja este caderno:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas
* [Rastrear um experimento](how-to-track-experiments.md)
* [Implantar um modelo treinado](how-to-deploy-and-where.md)
