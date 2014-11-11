<properties title="Azure Machine Learning Sample: Network intrusion detection" pageTitle="Machine Learning Sample: Network intrusion detection | Azure" description="A sample Azure Machine Learning experiment that uses a classification model to determine which network activities are a part of network intrusion." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Amostra de Aprendizado de Máquina do Azure: Detecção de intrusão na rede

<em>Você pode encontrar o teste de amostra associado a esse modelo no Estúdio AM na seção **TESTES** sob a guia **AMOSTRAS**. O nome do teste é:</em>

    Sample Experiment - Network Intrusion Detection - Development

## Descrição do problema

A demonstração usa vários recursos da rede para detectar quais atividades de rede fazem parte de uma intrusão/ataque. O problema de classificação binária não é muito difícil já que os recursos são bem preditivos de uma classe e as classes estão bastante equilibradas. Como diversos recursos fazem parte do rótulo, é importante remover as partes do rótulo antes de treinar um classificador.

## Dados

1.  Os dados, de KDD copa 1999, incluem conjuntos de dados de treinamento e teste. As precisões de classificação no conjunto de dados de treinamento são historicamente maiores do que o conjunto de dados de teste, já que novas intrusões na rede são introduzidas no conjunto de teste. O conjunto de dados de treinamento possui aproximadamente 126K de linhas e 43 colunas, incluindo os rótulos. Como as três colunas fazem parte das informações de rótulo, há 40 colunas disponíveis para treinamento do modelo. Essas 40 colunas são, em sua maioria, numéricas com poucos recursos de cadeia de caracteres/categóricos. Os dados de teste possuem aproximadamente 22,5K de exemplos de teste (com as mesmas 43 colunas nos dados de treinamento).
2.  Os dados foram carregados para um blob público para fácil acesso. A AM do Azure fornece módulos leitores para ler dados de diferentes armazenamentos, como blobs que usam alguns cliques simples, sem a necessidade de escrever códigos.

## Modelo

1.  A primeira etapa no modelo é a condição de dados para classificação posterior. A coluna “Classe" contém os rótulos para previsão. Esses rótulos são “normais" (nenhuma intrusão) ou o nome de um ataque. Alguns ataques não possuem muitos exemplos nos dados de treinamento e há novos ataques no conjunto de dados de teste, portanto, convertemos esses rótulos de multiclasse nos rótulos de classe binária para tornar o problema exequível. O estúdio fornece modelo integrado para facilitar essa etapa de pré-processamento. O módulo do valor indicador é usado para binarizar os rótulos de classe e, então, o módulo de coluna do projeto é usado para selecionar essa coluna de rótulo de classe binarizada (excluindo a coluna de rótulo de classe original).
2.  Como parte do desenvolvimento desse teste, vemos pela seleção/correlação de recursos que muitos recursos estão altamente correlacionados com o rótulo. Isso é comum em conjuntos de dados sintéticos. O AM de Nuvem fornece essa visualização em poucos cliques, para fazer isso, selecione a opção visualizar na saída do módulo do projeto e clique no cabeçalho de um dos recursos para iniciar o visualizador e, então, selecione o rótulo de classe na comparação do menu suspenso.
3.  Como uns poucos recursos estão altamente correlacionados com o rótulo, testaremos as combinações de classificadores lineares e não lineares com e sem a seleção do recurso para determinar qual modelo é o melhor. Essa comparação é obtida por meio da avaliação do módulo de modelo.

## Resultados

1.  O classificador não linear (árvore de decisão aumentada) executa um pouco melhor do que o classificador linear (regressão logística):

![][0]

Agora, queremos comparar uma árvore de decisão aumentada com a seleção do recurso. O desempenho de classificação é bastante similar, mas a árvore de decisão aumentada em todos os recursos é um pouco melhor e será usada para o fluxo de trabalho de classificação. Observe que essa alta classificação AUC é típica para esse conjunto de dados.

![][1]

<!-- Removed until this part is fixed ## Operationalization ##   We wanted to see our model in action, to do this we wanted a request-response service around the model we just learned. Doing this is straightforward and can be achieved in few simple clicks in studio:   1. First step is to save the learned model (by right clicking on the classifier module output)  1. Now create a new experiment and search for saved model and drop it in the panel for new experiment  1. Then we need to replicate the steps we did for pre-process, again we can just select the modules from experiment we need and copy and paste to the new experiment  1. The last step is to plug in the test data and run the experiment, once the run is finished we can define the input and output points for the service  1. Again, using one click we can publish the service end points that is deployed right away and can be tested using a friendly user interface - all within studio    -->

  [0]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-1.png
  [1]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-2.png
