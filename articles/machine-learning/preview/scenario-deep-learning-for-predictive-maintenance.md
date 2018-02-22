---
title: "Aprendizado profundo para cenários do mundo real de manutenção preditiva - Azure | Microsoft Docs"
description: "Saiba como replicar o tutorial no aprendizado profundo para manutenção preditiva com Workbench do Microsoft Azure Machine Learning."
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: 7d4fe98b5c45767fb06391218e80789fc0c96a3b
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenario"></a>Aprendizado profundo para cenários do mundo real de manutenção preditiva.

O aprendizado profundo é uma das tendências mais populares no aprendizado de computador, com aplicações em muitas áreas, inclusive:
- carros autônomos e robótica
- reconhecimento de fala e imagem
- previsão financeira.

Também conhecidos como redes neurais profundas (DNNs), esses métodos são inspirados por neurônios individuais dentro do cérebro (redes neurais biológicas).

O impacto do tempo de inatividade não agendado de um equipamento pode ser prejudicial para qualquer negócio. É essencial manter os equipamentos de campo em execução para maximizar o uso e o desempenho e minimizar o tempo de inatividade não agendado e dispendioso. A identificação antecipada de problemas pode ajudar a alocar recursos de manutenção limitados de maneira mais econômica e a melhorar a qualidade e os processos da cadeia de fornecimento. 

Uma estratégia de manutenção preditiva (PM) usa métodos de aprendizado de computador para determinar a condição do equipamento a fim de realizar preventivamente a manutenção e evitar o desempenho da máquina adverso. Em PM, dados, coletados ao longo do tempo para monitorar o estado da máquina, são análises para encontrar padrões que possam ser usados para prever falhas. Redes de [memória de muito curto prazo (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) são atrativas para essa configuração porque foram projetadas para aprender com sequências de dados.

## <a name="link-to-the-gallery-github-repository"></a>Link para o repositório GitHub da galeria

Este é o link para o repositório público do GitHub de relatórios de problemas e contribuições: [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) 

## <a name="use-case-overview"></a>Visão geral de casos de uso

Este tutorial usa o exemplo de eventos de execução-para-falha do mecanismo de aeronave simulada para demonstrar o processo de modelagem de manutenção preditiva. O cenário é descrito em [Manutenção preditiva](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3)

A suposição principal nessa configuração é que o mecanismo se desgasta progressivamente ao longo da vida útil. A degradação pode ser detectada em medidas de sensor do mecanismo. O PM tenta modelar a relação entre as alterações nesses valores de sensor e as falhas no histórico. O modelo pode acabar prevendo quando, e o mecanismo poderá falhar no futuro com base no estado atual de medidas do sensor.

Este cenário cria uma rede LSTM para prever a vida útil restante (RUL) de mecanismos de aeronave usando valores de sensor históricos. Usando [Keras](https://keras.io/) com a estrutura de aprendizado profundo [Tensorflow](https://www.tensorflow.org/) como um mecanismo de computação, o cenário treina LSTM com um conjunto de mecanismos e testa a rede em um conjunto de mecanismo despercebidos.

## <a name="prerequisites"></a>pré-requisitos
- Uma [conta do Azure](https://azure.microsoft.com/free/) (avaliações gratuitas estão disponíveis).
- Workbench do Azure Machine Learning, com um espaço de trabalho criado.
- Para operacionalização do modelo: a operacionalização de aprendizado de máquina do Azure, com um ambiente de implantação local configurado e uma [conta de Gerenciamento de Modelos do Machine Learning do Azure](model-management-overview.md).

## <a name="create-a-new-workbench-project"></a>Criar um novo projeto de Workbench

Crie um novo projeto usando este exemplo como modelo:

1. Abra o Machine Learning Workbench.
2. Na página **Projetos**, selecione **+** e **Novo Projeto**.
3. No painel **Criar Novo Projeto**, preencha as informações do seu novo projeto.
4. Na caixa de pesquisa **Pesquisar modelos de projeto**, digite "Manutenção preditiva" e selecione o modelo **Aprendizado profundo para cenário de manutenção preditiva**.
5. Clique no botão **Criar**

## <a name="prepare-the-notebook-server-computation-target"></a>Preparar o destino de computação do servidor de notebook

Para executar no computador local, no menu `File` do AML Workbench, selecione `Open Command Prompt` ou `Open PowerShell CLI`. A interface CLI permite que você acesse os seus serviços do Azure usando os comandos `az`. Primeiro, faça logon em sua conta com o comando:

```
az login
``` 

Esse comando fornece uma chave de autenticação a ser usada com a URL `https:\\aka.ms\devicelogin`. A CLI aguarda até que a operação de logon de dispositivo retorne e forneça algumas informações de conexão. Em seguida, se você tiver uma instalação de [docker](https://www.docker.com/get-docker) local, prepare o ambiente de computação local com os seguintes comandos:

```
az ml experiment prepare --target docker --run-configuration docker
```

É preferível executar em uma [Máquina virtual de ciência de dados para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) por causa dos requisitos de memória e disco. Depois que o DSVM estiver configurado, prepare o ambiente do docker remoto com os dois comandos a seguir:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Uma vez conectado ao contêiner do docker remoto, prepare o ambiente de computação do docker de DSVM usando: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Com o ambiente de computação do docker preparado, abra o servidor do Notebook Jupyter na guia Notebooks do AML Workbench ou inicie um servidor baseado em navegador com: 
```
az ml notebook start
```

Os notebooks de exemplo são armazenados no diretório `Code`. Os notebooks são configurados para serem executados em sequência, começando no primeiro notebook (`Code\1_data_ingestion.ipynb`). Ao abrir cada notebook, uma solicitação pedirá que você selecione o kernel de computação. Escolha o kernel `[Project_Name]_Template [Connection_Name]` para executar no DSVM configurado anteriormente.

## <a name="data-description"></a>Descrição dos dados

O modelo usa três conjuntos de dados como entradas, nos arquivos **PM_train.txt**, **PM_test.txt** e **PM_truth.txt**. 

-  **Treinar dados**: dados de execução-para-falha do mecanismo de aeronave. Os dados de treinamento (PM_train.txt) consistem em várias séries temporais multivariadas, com *ciclo* como a unidade de tempo. Ela inclui 21 leituras do sensor para cada ciclo. 

    - Cada série temporal é gerada com base em um mecanismo diferente do mesmo tipo. Cada mecanismo inicia com diferentes níveis de desgaste inicial e alguma variação de fabricação exclusiva. Essa informação é desconhecida ao usuário. 

    - Nesses dados simulados, supõe-se que o mecanismo opere normalmente no início de cada série temporal. Ele começa a se degradar em algum momento durante a série de ciclos operacionais. A degradação progride e aumenta em magnitude. 

    - Quando um limite predefinido é atingido, o mecanismo é considerado inseguro para outras operações. O último ciclo de cada série temporal é o ponto de falha desse mecanismo.

-   **Dados de teste**: Os dados operacionais do mecanismo de aeronave, sem eventos de falhas registrados. Os dados de teste (PM_test.txt) têm o mesmo esquema de dados que os dados de treinamento. A única diferença é que os dados não indicam quando a falha ocorre (o último período de tempo *não* representa o ponto de falha). Não é conhecido quantos ciclos mais esse mecanismo pode durar antes de começar a falhar.

- **Dados da verdade**: as informações dos ciclos verdadeiros restantes para cada mecanismo nos dados de teste. Os dados da verdade fornecem o número de ciclos de trabalho restantes para os mecanismos nos dados de teste.

## <a name="scenario-structure"></a>Estrutura do cenário

O fluxo de trabalho do cenário é dividido em três etapas, cada uma executada em notebooks Jupyter. Cada notebook produz artefatos de dados mantidos localmente para uso nos seguintes notebooks: 

### <a name="task-1-data-ingestion-and-preparation"></a>Tarefa 1: Preparação e ingestão de dados

O Notebook do Jupyter de ingestão de dados em `Code/1_data_ingestion_and_preparation.ipnyb` carrega os três conjuntos de dados de entrada no formato de dataframe Pandas. Em seguida, ele prepara os dados para modelagem e faz a visualização preliminar de alguns dados. Os conjuntos de dados são armazenados localmente no contexto de computação a ser usado no notebook de criação do modelo.

### <a name="task-2-model-building-and-evaluation"></a>Tarefa 2: Criação de modelo e avaliação

O Notebook do Jupyter de criação do modelo em `Code/2_model_building_and_evaluation.ipnyb` lê os conjuntos de dados de treinamento e teste do disco e cria uma rede LSTM para o conjunto de dados de treinamento. O desempenho do modelo é medido no conjunto de teste. O modelo resultante é serializado e armazenado no contexto de computação local para uso na tarefa de operacionalização.

### <a name="task-3-operationalization"></a>Tarefa 3: Operacionalização

O Notebook do Jupyter de operacionalização em `Code/3_operationalization.ipnyb` utiliza o modelo armazenado para construir funções e esquemas para chamar o modelo em um serviço Web hospedado no Azure. O notebook testa as funções e compacta os ativos no arquivo `LSTM_o16n.zip`, que é carregado no contêiner de armazenamento do Azure para a implantação.

O arquivo de implantação `LSTM_o16n.zip` contém os seguintes artefatos:

- `webservices_conda.yaml` define os pacotes python necessários para executar o modelo LSTM no destino de implantação.  
- `service_schema.json` define o esquema de dados esperado pelo modelo LSTM.     
- `lstmscore.py` define as funções que o destino de implantação está executando para pontuar novos dados.    
- `modellstm.json` define a arquitetura LSTM. As funções lstmscore.py leem a arquitetura e os pesos para inicializar o modelo.
- `modellstm.h5` define os pesos de modelo.
- `test_service.py` Um script de teste que chama o ponto de extremidade de implantação com registros de dados de teste. 
- `PM_test_files.pkl` O script `test_service.py` lê dados históricos do mecanismo do arquivo `PM_test_files.pkl` e envia ao serviço Web ciclos suficientes para o LSTM retornar uma probabilidade de falha do mecanismo.

O Notebook testa as funções usando a definição de modelo antes de compactar os ativos de operacionalização para implantação. Instruções para configurar e testar o serviço Web estão incluídas ao final do notebook `Code/3_operationalization.ipnyb`.

## <a name="conclusion"></a>Conclusão

Este tutorial usa um cenário simples com valores de sensor para fazer previsões. Cenários de manutenção preditiva mais avançados, como o [Notebook R do guia de modelagem da manutenção preditiva](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1), podem usar muitas fontes de dados, como registros de histórico de manutenção, logs de erros e recursos de computador. Fontes de dados adicionais podem exigir tratamentos diferentes a serem usados com aprendizado profundo.


## <a name="references"></a>Referências

Há outros exemplos de casos de uso de manutenção preditiva disponíveis em uma variedade de plataformas:

* [Modelo da solução de manutenção preditiva](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guia de modelagem de manutenção preditiva](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Guia de modelagem de manutenção preditiva usando os Serviços R do SQL](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Bloco de anotações do Python do guia de modelagem de manutenção preditiva](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Manutenção preditiva usando o PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

* [Cenário do mundo real de manutenção preditiva](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-predictive-maintenance)

## <a name="next-steps"></a>Próximas etapas

Existem vários outros cenários de exemplo disponíveis no Azure Machine Learning Workbench que demonstram os recursos adicionais do produto. 