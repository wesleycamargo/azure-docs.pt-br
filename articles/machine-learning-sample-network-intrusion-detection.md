<properties title="Azure Machine Learning Sample: Network intrusion detection" pageTitle="Amostra de Aprendizado de Máquina: Detecção de intrusão na rede | Azure" description="A sample Azure Machine Learning experiment that uses a classification model to determine which network activities are a part of network intrusion." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />



# Amostra de aprendizado de máquina do Azure: Detecção de intrusão na rede

>[AZURE.NOTE]
>A [amostra de experimento] e a [amostra de conjunto de dados] associados a este modelo estão disponíveis no Estúdio AM. Consulte a seguir para obter mais detalhes.
[Amostra de experimento]: #sample-experiment
[Amostra de conjunto de dados]: #sample-dataset


## Descrição do problema ##

A demonstração usa vários recursos da rede para detectar quais atividades de rede fazem parte de uma intrusão/ataque. O problema de classificação binária não é muito difícil já que os recursos são bem preditivos de uma classe e as classes estão bastante equilibradas.  Como diversos recursos fazem parte do rótulo, é importante remover as partes do rótulo antes de treinar um classificador. 

## Dados ##

1. Os dados, de KDD copa 1999, incluem conjuntos de dados de treinamento e teste.  As precisões de classificação no conjunto de dados de treinamento são historicamente maiores do que o conjunto de dados de teste, já que novas intrusões na rede são introduzidas no conjunto de teste.  O conjunto de dados de treinamento possui aproximadamente 126K de linhas e 43 colunas, incluindo os rótulos.  Como as três colunas fazem parte das informações de rótulo, há 40 colunas disponíveis para treinamento do modelo.  Essas 40 colunas são, em sua maioria, numéricas com poucos recursos de cadeia de caracteres/categóricos. Os dados de teste possuem aproximadamente 22,5 K de exemplos de teste (com as mesmas 43 colunas nos dados de treinamento). 
1. Os dados foram carregados para um blob público para fácil acesso. O AM do Azure fornece módulos leitores para ler dados de diferentes armazenamentos, como blobs que usam alguns cliques simples, sem a necessidade de escrever códigos. 

## Modelo ##

1. A primeira etapa no modelo é a condição de dados para classificação posterior.  A coluna "Classe" contém os rótulos para prever.  Esses rótulos são "normal" (nenhuma intrusão) ou o nome de um ataque.  Alguns ataques não têm muitos exemplos nos dados de treinamento e existem novos ataques do conjunto de dados de teste, portanto, convertemos esses rótulos multiclasse em rótulos de classe binária para tornar o problema viável.  O estúdio fornece modelo integrado para facilitar essa etapa de pré-processamento. O módulo do valor indicador é usado para binarizar os rótulos de classe e, então, o módulo de coluna do projeto é usado para selecionar essa coluna de rótulo de classe binarizada (excluindo a coluna de rótulo de classe original).  
1. Como parte do desenvolvimento desse teste, vemos pela seleção/correlação de recursos que muitos recursos estão altamente correlacionados com o rótulo. Isso é comum em conjuntos de dados sintéticos.  O AM de Nuvem fornece essa visualização em poucos cliques. Para fazer isso, selecione a opção visualizar na saída do módulo do projeto e clique no cabeçalho de um dos recursos para iniciar o visualizador, depois selecione o rótulo de classe na comparação do menu suspenso.
1. Como uns poucos recursos estão altamente correlacionados com o rótulo, testaremos as combinações de classificadores lineares e não lineares com e sem a seleção do recurso para determinar qual modelo é o melhor.  Essa comparação é obtida por meio da avaliação do módulo de modelo.    

## Resultados ##

1. O classificador não linear (árvore de decisão aumentada) executa um pouco melhor do que o classificador linear (regressão logística): 

![][1]


Agora, queremos comparar uma árvore de decisão aumentada com a seleção do recurso. O desempenho de classificação é bastante similar, mas a árvore de decisão aumentada em todos os recursos é um pouco melhor e será usada para o fluxo de trabalho de classificação.  Observe que essa alta classificação AUC é típica para esse conjunto de dados. 


![][2]

<!-- Removed until this part is fixed
## Operationalization ##


We wanted to see our model in action, to do this we wanted a request-response service around the model we just learned. Doing this is straightforward and can be achieved in few simple clicks in studio: 

1. First step is to save the learned model (by right clicking on the classifier module output) 
1. Now create a new experiment and search for saved model and drop it in the panel for new experiment 
1. Then we need to replicate the steps we did for pre-process, again we can just select the modules from experiment we need and copy and paste to the new experiment 
1. The last step is to plug in the test data and run the experiment, once the run is finished we can define the input and output points for the service 
1. Again, using one click we can publish the service end points that is deployed right away and can be tested using a friendly user interface - all within studio   
-->


## Amostra de teste

A seguinte amostra de teste associada a esse modelo está disponível no Estúdio AM na seção **TESTES** sob a guia **AMOSTRAS**.

> **Amostra de teste - Detecção de intrusão na rede - Desenvolvimento**


## Conjunto de dados de amostra

O seguinte conjunto de dados de exemplo usado por essa experiência está disponível no Estúdio AM na paleta de módulo em **Conjuntos de dados salvos**.

###network_intrusion_detection.csv
[AZURE.INCLUDE [machine-learning-sample-dataset-network-intrusion](../includes/machine-learning-sample-dataset-network-intrusion.md)]




[1]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-1.png
[2]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-2.png
