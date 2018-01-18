---
title: "Aprendizado profundo para cenários do mundo real de manutenção preditiva - Azure | Microsoft Docs"
description: "Saiba como replicar o tutorial no aprendizado profundo para manutenção preditiva com Workbench do Microsoft Azure Machine Learning."
services: machine-learning
author: FrancescaLazzeri
ms.author: Lazzeri
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: a55209256c29fa62cc2da72f9653fbc7fc0e7c54
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>Aprendizado profundo para cenários do mundo real de manutenção preditiva

O aprendizado profundo é uma das tendências mais populares no aprendizado de computador. O aprendizado profundo é usado em vários campos e aplicativos, incluindo carros sem motorista, reconhecimento de fala e imagem, robótica e finanças. O aprendizado profundo é um conjunto de algoritmos inspirado pela forma do cérebro (redes neurais biológicas) e aprendizado de computador. Cientistas cognitivos geralmente se referem ao aprendizado profundo como redes neurais artificiais (ANNs).

A manutenção preditiva também é popular. No modo de manutenção preditivo, várias técnicas diferentes são projetadas para ajudar a determinar a condição do equipamento e para prever quando a manutenção deve ser executada. Algumas utilizações comuns da manutenção preditiva são a previsão de falha, o diagnóstico de falha (análise de causa raiz), a detecção de falha, a classificação do tipo de falha e a recomendação de ações de manutenção ou atenuação após a falha.

Nos cenários de manutenção preditiva, os dados são coletados ao longo do tempo para monitorar o estado do equipamento. O objetivo é encontrar padrões que podem ajudar a prever e, finalmente, evitar falhas. Usar as redes de [Memória de Longo e Curto Períodos (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) é um método de aprendizado profundo especialmente atraente na manutenção preditiva. As redes LSTM são boas no aprendizado de sequências. Os dados de série temporal podem ser usados para examinar longos períodos de tempo para detectar padrões de falha.

Neste tutorial, vamos criar uma rede LSTM para o conjunto de dados e cenário descritos em [Manutenção Preditiva](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3). Usamos a rede para prever a vida útil restante dos mecanismos de aeronave. O modelo usa valores de sensor de aeronave simulada para prever quando um mecanismo de aeronave falhará no futuro. Usando essa previsão, a manutenção pode ser planejada antecipadamente para evitar uma falha.

Este tutorial usa a biblioteca de aprendizado profundo [Keras](https://keras.io/) e o Kit de Ferramentas Cognitivas da Microsoft [CNTK](https://docs.microsoft.com/cognitive-toolkit/Using-CNTK-with-Keras) como um back-end.

O repositório público GitHub que tem os exemplos para este tutorial está em [https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance).

## <a name="use-case-overview"></a>Visão geral de casos de uso

Este tutorial usa o exemplo de eventos de execução-para-falha do mecanismo de aeronave simulada para demonstrar o processo de modelagem de manutenção preditiva. 

A pressuposição implícita dos dados de modelos descritos aqui é que o ativo tem um padrão de degradação de progresso. O padrão é refletido em medidas de sensor do ativo. Examinando os valores de sensor do ativo ao longo do tempo, o algoritmo de aprendizado de máquina pode aprender a relação entre os valores de sensor, as alterações nos valores de sensor e as falhas de histórico. Essa relação é usada para prever falhas no futuro. 

Sugerimos que você examine o formato de dados e conclua todas as três etapas do modelo antes de substituir os dados de exemplo pelos seus próprios dados.

## <a name="prerequisites"></a>Pré-requisitos

- Uma [conta do Azure](https://azure.microsoft.com/free/) (avaliações gratuitas estão disponíveis).
- Workbench do Azure Machine Learning, com um espaço de trabalho criado.
- Para operacionalização do modelo: a operacionalização de aprendizado de máquina do Azure, com um ambiente de implantação local configurado e uma [conta de Gerenciamento de Modelos do Machine Learning do Azure](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview).

## <a name="create-a-new-workbench-project"></a>Criar um novo projeto de Workbench

Crie um novo projeto usando este exemplo como modelo:

1. Abra o Machine Learning Workbench.
2. Na página **Projetos**, selecione **+** e **Novo Projeto**.
3. No painel **Criar Novo Projeto**, preencha as informações do seu novo projeto.
4. Na caixa de pesquisa **Pesquisar modelos de projeto**, digite **Manutenção preditiva** e selecione o modelo.
5. Selecione **Criar**.

## <a name="prepare-the-notebook-server-computation-target"></a>Preparar o destino de computação do servidor de notebook

No seu computador local, no menu do **arquivo** do Workbench do Microsoft Azure Machine Learning, selecione **abrir o Prompt de comando** ou **abrir o PowerShell**. Na janela do prompt de comando da opção que você escolher, execute os comandos a seguir:

`az ml experiment prepare --target docker --run-configuration docker`

Sugerimos que você execute o servidor do Notebook em uma [Máquina virtual de ciência de dados (DSVM) para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) padrão DS4_V2. Após o DSVM configurado, execute os comandos a seguir para preparar as imagens do Docker:

`az ml computetarget attach remotedocker --name [connection_name] --address [VM_IP_address] --username [VM_username] --password [VM_password]`

`az ml experiment prepare --target [connection_name] --run-configuration [connection_name]`

Com as imagens do Docker preparadas, abra o servidor do Notebook do Jupyter. Para abrir o servidor do Notebook do Jupyter, vá para o guia **Notebook** do Workbench do Microsoft Azure Machine Learning ou inicie um servidor baseado em navegador: `az ml notebook start`

Os Notebooks são armazenados no diretório de Código no ambiente do Jupyter. Execute esses Notebooks sequencialmente conforme numerados, iniciando com Code\1_data_ingestion_and_preparation.ipynb.

Selecione o kernel para corresponder ao seu [project_name]_Template [connection_name], e então selecione **Definir Kernel**.

## <a name="data-description"></a>Descrição dos dados

O modelo usa três conjuntos de dados como entradas nos arquivos PM_train.txt, PM_test.txt, e PM_truth.txt.

-  **Treinar dados**: dados de execução-para-falha do mecanismo de aeronave. Os dados de treinamento (PM_train.txt) consistem em várias séries temporais multivariadas, com *ciclo* como a unidade de tempo. Ela inclui 21 leituras do sensor para cada ciclo. 

    Cada série temporal pode ser considerada como gerada a partir de um mecanismo diferente do mesmo tipo. Cada mecanismo deve iniciar com diferentes níveis de desgaste inicial e variação de fabricação. Essa informação é desconhecida ao usuário. 

    Nesses dados simulados, supõe-se que o mecanismo opere normalmente no início de cada série temporal. Ele começa a se degradar em algum momento durante a série de ciclos operacionais. A degradação progride e aumenta em magnitude. 

    Quando um limite predefinido é atingido, o mecanismo é considerado inseguro para outras operações. O último ciclo de cada série temporal pode ser considerado o ponto de falha do mecanismo correspondente.

-   **Dados de teste**: Os dados operacionais do mecanismo de aeronave, sem eventos de falhas registrados. Os dados de teste (PM_test.txt) têm o mesmo esquema de dados que os dados de treinamento. A única diferença é que os dados não indicam quando a falha ocorre (o último período de tempo *não* representa o ponto de falha). Não é conhecido quantos ciclos mais esse mecanismo pode durar antes de começar a falhar.

- **Dados da verdade**: as informações dos ciclos verdadeiros restantes para cada mecanismo nos dados de teste. Os dados da verdade fornecem o número de ciclos de trabalho restantes para os mecanismos nos dados de teste.

## <a name="scenario-structure"></a>Estrutura do cenário

O conteúdo para o cenário está disponível no [repositório do GitHub] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance). 

No repositório, um arquivo [Leiame] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/blob/master/README.md) descreve os processos, desde a preparação dos dados à criação e operacionalização do modelo. Os três Notebooks do Jupyter estão disponíveis na pasta [código] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/tree/master/Code) no repositório. 

A seguir, descrevemos o passo a passo do fluxo de trabalho do cenário.

### <a name="task-1-data-ingestion-and-preparation"></a>Tarefa 1: Preparação e ingestão de dados

O Notebook do Jupyter de ingestão de dados em Code/1_data_ingestion_and_preparation.ipnyb carrega os três conjuntos de dados de entrada no formato de dataframe Pandas. Em seguida, ele prepara os dados para modelagem e faz a visualização preliminar de alguns dados. Os dados, então, são transformados no formato PySpark e armazenados em um contêiner de armazenamento de BLOBs do Azure em sua assinatura do Azure para uso na próxima tarefa de modelagem.

### <a name="task-2-model-building-and-evaluation"></a>Tarefa 2: Criação de modelo e avaliação

O Notebook do Jupyter de criação de modelo em Code/2_model_building_and_evaluation.ipnyb lê o PySpark e treina e testa os conjuntos de dados do armazenamento de blobs. Em seguida, uma rede LSTM é construída com os conjuntos de dados de treinamento. O desempenho do modelo é medido no conjunto de teste. O modelo resultante é serializado e armazenado no contexto de computação local para uso na tarefa de operacionalização.

### <a name="task-3-operationalization"></a>Tarefa 3: Operacionalização

O Notebook do Jupyter de operacionalização em Code/3_operationalization.ipnyb utiliza o modelo armazenado para construir funções e esquemas necessários para chamar o modelo em um serviço Web hospedado no Azure. O Notebook testa as funções e, em seguida, compacta os ativos de operacionalização para um arquivo .zip.

O arquivo compactado contém esses arquivos:

- **modellstm.json**: O arquivo da definição de esquema para implantação. 
- **lstmscore.py**: As funções **init()** e **run()** exigidas pelo o serviço Web do Azure.
- **lstm.model**: O diretório de definição de modelo.

O Notebook testa as funções usando a definição de modelo antes de compactar os ativos de operacionalização para implantação. As instruções de implantação são incluídas no final do notebook.


## <a name="conclusion"></a>Conclusão

Este tutorial usa um cenário simples onde somente uma fonte de dados (valores de sensor) é usada para fazer previsões. Em cenários de manutenção preditiva mais avançados, como o [ Notebook Guia R de Modelagem de Manutenção Preditiva](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1), diversas fontes de dados podem ser usadas. Outras fontes de dados podem incluir registros históricos de manutenção, logs de erros e os recursos de máquina e operador. Fontes de dados adicionais podem exigir diferentes tipos de tratamentos a serem usados em redes de aprendizado profundo. Também é importante ajustar os modelos nos parâmetros certos, como o tamanho da janela. 

Você pode editar as partes relevantes deste cenário e tentar cenários de problemas diferentes, como os descritas no [Guia de modelagem de manutenção preditiva](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1), que envolve várias outras fontes de dados.


## <a name="references"></a>Referências

- [Modelo da solução de manutenção preditiva](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)
- [Guia de modelagem de manutenção preditiva](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
- [Bloco de anotações do Python do guia de modelagem de manutenção preditiva](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
- [Manutenção preditiva usando o PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

