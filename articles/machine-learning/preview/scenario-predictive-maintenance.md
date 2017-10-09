--- 
title: "Cenário do mundo real de manutenção preditiva | Microsoft Docs"
description: "Cenário do mundo real de manutenção preditiva usando o PySpark"
services: machine-learning
author: jaymathe
ms.author: jaymathe
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 7bb5672a2b9de0a62af8b6155bfdead6a0105eb5
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

--- 

# <a name="predictive-maintenance-real-world-scenario"></a>Cenário do mundo real de manutenção preditiva


O impacto do tempo de inatividade não agendado de um equipamento pode ser prejudicial para qualquer negócio. É essencial, portanto, manter os equipamentos de campo em execução para maximizar o uso e o desempenho e minimizar o tempo de inatividade não agendado e dispendioso. A identificação antecipada de problemas pode ajudar a alocar recursos de manutenção limitados de maneira mais econômica e a melhorar a qualidade e os processos da cadeia de fornecimento. 

Para este cenário, usamos [dados em escala relativamente grande](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) para explicar ao usuário as principais etapas da ingestão de dados, engenharia de recursos, criação de modelos e, finalmente, a operacionalização e implantação de modelos. O código para todo o processo é escrito no PySpark e implementado usando os blocos de anotações do Jupyter dentro do Azure ML Workbench. O melhor modelo é finalmente operacionalizado usando o Gerenciamento de Modelos do Machine Learning do Azure para uso em um ambiente de produção para fazer previsões de falha em tempo real.   

## <a name="link-to-the-gallery-github-repository"></a>Link para o repositório GitHub da galeria

A seguir está o link para o repositório GitHub público: [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>Visão geral de casos de uso

Um grande problema enfrentado pelos negócios nos setores de ativos pesados são os custos significativos associados a atrasos para problemas mecânicos. A maioria dos negócios está interessada em prever quando esses problemas ocorrem para evitá-los proativamente antes que eles ocorram. Isso reduz os custos, reduzindo o tempo de inatividade e, em alguns casos, aumentando a segurança. Consulte o [guia estratégico para manutenção preditiva](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance) para obter uma explicação detalhada dos casos de uso comuns e a abordagem de modelagem para manutenção preditiva.

Este cenário usa as ideias do guia estratégico com o objetivo de fornecer as etapas para implementar um modelo preditivo para um cenário, com base em uma síntese de vários problemas de negócios do mundo real. Este exemplo reúne elementos de dados comuns observados entre muitos casos de uso de manutenção preditiva.

O problema de negócios para esses dados simulados é prever problemas causados por falhas em componentes. A pergunta de negócios, portanto, é “*qual é a probabilidade de um computador ficar inoperante devido à falha de um componente*?” Esse problema é formatado como um problema de classificação multiclasse (vários componentes por computador) e um algoritmo de aprendizado de máquina é usado para criar o modelo preditivo. O modelo é treinado em dados históricos coletados dos computadores. Nesse cenário, o usuário passa por várias etapas de implementação desse modelo no ambiente do Azure Machine Learning Workbench.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta do Azure](https://azure.microsoft.com/en-us/free/) (avaliações gratuitas estão disponíveis).
* Uma cópia instalada do [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) seguindo o [guia de instalação de início rápido](./quickstart-installation.md) para instalar o programa e criar um espaço de trabalho.
* Os resultados intermediários para uso em blocos de anotações do Jupyter neste cenário são armazenados em um contêiner do Armazenamento de Blobs do Azure. As instruções para configurar uma conta de Armazenamento do Microsoft Azure estão disponíveis neste [link](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-python-how-to-use-blob-storage). 
* Para a [operacionalização](https://github.com/Azure/Machine-Learning-Operationalization) do modelo, será melhor se o usuário executar um [mecanismo do Docker](https://www.docker.com/) instalado e em execução localmente. Caso contrário, será possível usar a opção de cluster, mas lembre-se de que executar um [ACS (Serviço de Contêiner do Azure)](https://azure.microsoft.com/en-us/services/container-service/) geralmente pode ser caro.
* Este cenário pressupõe que o usuário está executando o Azure ML Workbench em um computador Windows 10 com o mecanismo do Docker instalado localmente. 
* O cenário foi criado e testado em um computador Windows 10 com a seguinte especificação: Intel Core i7-4600U CPU a 2,10 GHz, 8 GB RAM, sistema operacional de 64 bits, processador baseado em x64 com o Docker versão 17.06.0-ce-win19 (12801). 
* A operacionalização do modelo foi feita usando esta versão da CLI do Azure ML: azure-cli-ml==0.1.0a22

## <a name="create-a-new-workbench-project"></a>Criar um novo projeto de Workbench

Crie um novo projeto usando este exemplo como modelo:
1.  Abra o Azure Machine Learning Workbench
2.  Na página **Projetos**, clique no sinal **+** e selecione **Novo Projeto**
3.  No painel **Criar Novo Projeto**, preencha as informações do seu novo projeto
4.  Na caixa de pesquisa **Pesquisar modelos de projeto**, digite "Manutenção preditiva" e selecione o modelo
5.  Clique em **Criar**

## <a name="data-description"></a>Descrição dos dados

Os [dados simulados](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) consistem em cinco arquivos .csv (valores separados por vírgula). 

* [Computadores](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): recursos que diferenciam cada computador. Por exemplo, idade e modelo.
* [Erro](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): o log de erros contém erros contínuos gerados enquanto o computador ainda está em operação. Esses erros não são considerados falhas, embora possam ser preditivos de um evento de falha futuro. A data-hora do erro é arredondada para a hora mais próxima, porque os dados telemétricos são coletados por hora.
* [Manutenção](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): o log de manutenção contém os registros de manutenção agendados e não agendados. A manutenção agendada corresponde à inspeção regular de componentes, a manutenção não agendada pode surgir da falha mecânica ou de outra degradação do desempenho. A data-hora da manutenção é arredondada para a hora mais próxima, porque os dados telemétricos são coletados por hora.
* [Telemetria](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): os dados de série temporal de telemetria consistem de medições do sensor de tensão, rotação, pressão e vibração coletadas de cada computador em tempo real. Os dados são a média ao longo de uma hora e armazenados nos logs de telemetria
* [Falhas](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): as falhas correspondem às substituições de componentes dentro do log de manutenção. Cada registro contém a ID do computador, o tipo de componente e a data e hora da substituição. Esses registros são usados para criar os rótulos de aprendizado de maquina que o modelo está tentando prever.

Consulte o cenário [Ingestão de dados](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) do Jupyter Notebook na seção Código para baixar os conjuntos de dados brutos do repositório GitHub e para criar os conjuntos de dados PySpark para esta análise.

## <a name="scenario-structure"></a>Estrutura do cenário
O conteúdo para o cenário está disponível no [repositório GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

No repositório, há um arquivo [Leiame](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md), que descreve os processos de preparação dos dados até a criação de alguns modelos e, por fim, a operacionalização de um dos melhores modelos. Os quatro blocos de anotações do Jupyter estão disponíveis na pasta [Código](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) dentro do repositório.   

A seguir, descrevemos o fluxo de trabalho do cenário passo a passo. O cenário de ponta a ponta é escrito no PySpark e dividido em quatro blocos de anotações conforme descrito abaixo:

* [Ingestão de dados](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): este bloco de anotações manipula a ingestão de dados dos cinco arquivos .csv de entrada, faz a limpeza preliminar, cria alguns gráficos de resumo para verificar os dads de download e, por fim, armazena os conjuntos de dados resultantes em um contêiner de blobs do Azure para uso no próximo bloco de anotações.

* [Engenharia de recursos](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): usando o conjunto de dados limpos da etapa anterior, os recursos de retardo são criados para os sensores de telemetria, juntamente com a engenharia de recursos adicional para criar variáveis como dias desde a última substituição. Por fim, as falhas são marcadas para os registros relevantes a fim de criar um conjunto de dados definitivo, salvo em um blob do Azure para a próxima etapa. 

* [Criação de modelos](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): o conjunto de dados de engenharia de recursos definitivo é, então, dividido em dois, ou seja, um conjunto de dados de treinamento e de teste baseado em um carimbo de data/hora. Em seguida, dois modelos, ou seja, um Classificador Random Forest e um Classificador de árvore de decisão, são criados no conjunto de dados de treinamento e, em seguida, classificados no conjunto de dados de teste. 

* [Operacionalização e implantação de modelos](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): o melhor modelo criado na etapa anterior é, então, salvo como um arquivo .model juntamente com o respectivo arquivo de esquema .json para a implantação. As funções init() e run() são testados primeiro localmente antes da operacionalização do modelo usando o ambiente do Gerenciamento de Modelos do Azure Machine Learning para uso em um ambiente de produção para fazer previsões de falha em tempo real.  

## <a name="conclusion"></a>Conclusão

Este cenário fornece ao leitor uma visão geral de como criar uma solução de manutenção preditiva de ponta a ponta usando o PySpark dentro do ambiente de bloco de anotações do Jupyter no Azure ML Workbench. O cenário também orienta o leitor sobre como o melhor modelo pode ser facilmente operacionalizado e implantado usando o ambiente do Gerenciamento de Modelos do Azure Machine Learning para uso em um ambiente de produção para fazer previsões de falha em tempo real. Em seguida, o leitor pode editar as partes relevantes do cenário para estreitá-lo às suas necessidades de negócios.  

## <a name="references"></a>Referências

Esse caso de uso foi desenvolvido anteriormente em várias plataformas, conforme listado abaixo:

* [Modelo da solução de manutenção preditiva](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guia de modelagem de manutenção preditiva](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Guia de modelagem de manutenção preditiva usando os Serviços R do SQL](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Bloco de anotações do Python do guia de modelagem de manutenção preditiva](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Manutenção preditiva usando o PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)



