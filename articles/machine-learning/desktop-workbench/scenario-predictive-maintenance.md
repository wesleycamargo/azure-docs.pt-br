---
title: Manutenção preditiva para cenários reais | Microsoft Docs
description: Manutenção preditiva para cenários reais usando PySpark
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ms.openlocfilehash: 791c34785fa817fd68d0bec8111bf23e606c9b64
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="predictive-maintenance-for-real-world-scenarios"></a>Manutenção preditiva para cenários reais

O impacto do tempo de inatividade não agendado de um equipamento pode ser prejudicial para qualquer negócio. É essencial manter os equipamentos de campo em execução para maximizar o uso e o desempenho e minimizar o tempo de inatividade não agendado e dispendioso. A identificação antecipada de problemas pode ajudar a alocar recursos de manutenção limitados de maneira mais econômica e a melhorar a qualidade e os processos da cadeia de fornecimento. 

Esse cenário explora um [conjunto de dados simulados em escala relativamente grande](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) para explicar um projeto de ciência de dados de manutenção preditiva a partir da ingestão de dados, engenharia de recursos, criação de modelos, e operacionalização e implantação de modelos. O código para todo o processo é escrito no Notebook Jupyter usando o PySpark no Azure Machine Learning Workbench. O modelo final é implantado usando o Gerenciamento de Modelos do Machine Learning do Azure para fazer previsões de falhas de equipamentos em tempo real.   

### <a name="cortana-intelligence-gallery-github-repository"></a>Repositório GitHub da Galeria do Cortana Intelligence

A Galeria do Cortana Intelligence para o tutorial PM é um repositório GitHub público ([https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)) onde você pode relatar problemas e fazer contribuições.


## <a name="use-case-overview"></a>Visão geral de casos de uso

Um grande problema enfrentado pelos negócios nos setores de ativos pesados são os custos significativos associados a atrasos devido a problemas mecânicos. A maioria dos negócios está interessada em prever quando esses problemas podem surgir para evitá-los proativamente antes que eles ocorram. A meta é reduzir os custos diminuindo o tempo de inatividade e possivelmente aumentar a segurança. 

Esse cenário utiliza ideias do [Guia estratégico de manutenção preditiva](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance) para demonstrar como criar um modelo preditivo para um conjunto de dados simulados. Os dados de exemplo são derivados de elementos comuns observados em muitos casos de uso de manutenção preditiva.

O problema de negócios para esses dados simulados é prever problemas causados por falhas em componentes. A pergunta de negócios é "*Qual é a probabilidade de um computador ficar inoperante devido à falha de um componente?*" Esse problema é formatado como um problema de classificação multiclasse (vários componentes por computador). Um algoritmo de aprendizado de máquina é usado para criar o modelo preditivo. O modelo é treinado em dados históricos coletados dos computadores. Nesse cenário, o usuário passa por várias etapas de implementação do modelo no ambiente do Machine Learning Workbench.

## <a name="prerequisites"></a>pré-requisitos

* Uma [conta do Azure](https://azure.microsoft.com/free/) (avaliações gratuitas estão disponíveis).
* Uma cópia instalada do [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md). Siga o [Guia de instalação de Início Rápido](../service/quickstart-installation.md) para instalar o programa e criar um espaço de trabalho.
* A Operacionalização do Azure Machine Learning exige um ambiente de implantação local e uma [conta de Gerenciamento de Modelos do Azure Machine Learning](model-management-overview.md).

Este exemplo é executado em qualquer contexto de computação do Machine Learning Workbench. No entanto, é recomendado executar o exemplo com pelo menos 16 GB de memória. Esse cenário foi criado e testado em um computador Windows 10 que executa remotamente uma [Máquina Virtual de Ciência de Dados (DSVM) para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) de padrão DS4_V2.

A operacionalização do modelo foi feita usando a versão 0.1.0a22 da CLI do Azure Machine Learning.

## <a name="create-a-new-workbench-project"></a>Criar um novo projeto de Workbench

Crie um novo projeto usando este exemplo como modelo:
1.  Abra o Machine Learning Workbench.
2.  Na página **Projetos**, selecione **+** e **Novo Projeto**.
3.  No painel **Criar Novo Projeto**, preencha as informações do seu novo projeto.
4.  Na caixa de pesquisa **Pesquisar Modelos de Projeto**, digite "Manutenção Preditiva" e selecione o modelo **Manutenção Preditiva**.
5.  Selecione **Criar**.

## <a name="prepare-the-notebook-server-computation-target"></a>Preparar o destino de computação do servidor de notebook

Para executar no seu computador local, no menu **Arquivo** do Machine Learning Workbench, selecione **Abrir Prompt de Comando** ou **Abrir CLI do PowerShell**. A interface CLI permite que você acesse os seus serviços do Azure usando os comandos `az`. Primeiro, faça logon em sua conta do Azure com o comando:

```
az login
``` 

Esse comando fornece uma chave de autenticação para usar com a URL https:\\aka.ms\devicelogin. A CLI aguarda até que a operação de logon de dispositivo retorne e forneça algumas informações de conexão. Em seguida, se você tiver uma instalação de [Docker](https://www.docker.com/get-docker) local, prepare o ambiente de computação local com o comando:

```
az ml experiment prepare --target docker --run-configuration docker
```

É preferível executar em uma [DSVM para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) por causa dos requisitos de memória e disco. Depois que a DSVM estiver configurada, prepare o ambiente do Docker remoto com os dois comandos a seguir:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Uma vez conectado ao contêiner do Docker remoto, prepare o ambiente de computação do Docker de DSVM com o comando: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Com o ambiente de computação do Docker preparado, abra o servidor do Notebook Jupyter na guia **Notebooks** do Azure Machine Learning Workbench ou inicie um servidor baseado em navegador com o comando: 

```
az ml notebook start
```

Os notebooks de exemplo são armazenados no diretório Código. Os notebooks são configurados para serem executados em sequência, começando no primeiro notebook (Code\1_data_ingestion.ipynb). Ao abrir cada notebook, uma solicitação pedirá que você selecione o kernel de computação. Escolha o kernel [Project_Name]_Template [Connection_Name] a ser executado na DSVM configurado anteriormente.

## <a name="data-description"></a>Descrição dos dados

Os [dados simulados](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) consistem em cinco arquivos .csv (valores separados por vírgula). Use os links a seguir para obter descrições detalhadas sobre os conjuntos de dados.

* [Máquinas](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): recursos que diferenciam cada máquina, como idade e modelo.
* [Erro](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): o log de erros contém erros contínuos gerados enquanto o computador ainda está em operação. Esses erros não são considerados falhas, embora possam ser sinais de um evento de falha futuro. Os valores de data-hora dos erros são arredondados para a hora mais próxima, porque os dados telemétricos são coletados por hora.
* [Manutenção](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): o log de manutenção contém os registros de manutenção agendados e não agendados. A manutenção agendada corresponde à inspeção regular dos componentes. A manutenção agendada pode surgir devido a uma falha mecânica ou outra degradação do desempenho. Os valores de data-hora de manutenção são arredondados para a hora mais próxima, porque os dados telemétricos são coletados por hora.
* [Telemetria](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): os dados telemétricos consistem em medidas temporais de vários sensores dentro de cada computador. Os dados são registrados por média dos valores de sensor em cada intervalo de uma hora.
* [Falhas](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): as falhas correspondem às substituições de componentes dentro do log de manutenção. Cada registro contém a ID do computador, o tipo de componente e a data e hora da substituição. Esses registros são usados para criar os rótulos de aprendizado de maquina que o modelo está tentando prever.

Para baixar os conjuntos de dados brutos do repositório GitHub e para criar os conjuntos de dados PySpark para esta análise, consulte o cenário do Notebook Jupyter de [Ingestão de Dados](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) na pasta Código.

## <a name="scenario-structure"></a>Estrutura do cenário
O conteúdo para o cenário está disponível no [repositório GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

O arquivo [Readme](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) descreve o fluxo de trabalho a partir do preparo dos dados, da compilação de um modelo e, em seguida, da implantação de uma solução para produção. Cada etapa do fluxo de trabalho é encapsulada em um Notebook Jupyter na pasta [Código](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) dentro do repositório.   

[Code\1_data_ingestion.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): este notebook baixa os cinco arquivos de entrada .csv e executa a limpeza preliminar dos dados e a visualização. O notebook converte cada conjunto de dados no formato do PySpark e armazena-os em um contêiner de blob do Azure para uso no Notebook de Engenharia de Recursos.

[Code\2_feature_engineering.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): os recursos de modelo são construídos a partir do conjunto de dados brutos do armazenamento de Blob do Azure usando uma abordagem de série de tempo padrão para telemetria, erros e dados de manutenção. As substituições de componente relacionadas a falhas são usadas para construir os rótulos de modelos que descrevem qual componente falhou. Os dados do recurso rotulado são salvos em um blob do Azure para o Notebook de Criação de Modelo.

[Code\3_model_building.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): o Notebook de Construção de Modelo usa o conjunto de dados de recurso rotulado e divide os dados em conjuntos de dados de treinamento e desenvolvimento ao longo do carimbo de data e hora. O notebook é configurado como uma experiência com modelos pyspark.ml.classification. Os dados de treinamento são vetorizados. O usuário pode experimentar um **DecisionTreeClassifier** ou **RandomForestClassifier** para manipular hiperparâmetros para encontrar o melhor modelo de desempenho. O desempenho é determinado pela avaliação de estatísticas de medida no conjunto de dados de desenvolvimento. Essas estatísticas são registradas na tela de tempo de execução do Machine Learning Workbench para acompanhamento. Em cada execução, o notebook salva o modelo resultante no disco local que executa o kernel do notebook Jupyter. 

[Code\4_operationalization.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): este notebook usa o último modelo salvo em disco local (kernel de notebook Jupyter) para criar os componentes para a implantação do modelo em um serviço Web do Azure. Os ativos completamente operacionais são compactados no arquivo o16n.zip armazenado em outro contêiner de blob do Azure. O arquivo compactado contém:

* **service_schema.json**: o arquivo da definição de esquema para a implantação. 
* **pdmscore.py**: as funções **init()** e **run()** exigidas pelo o serviço Web do Azure.
* **pdmrfull.model**: o diretório de definição de modelo.
    
O notebook testa as funções com a definição de modelo antes de compactar os ativos de operacionalização para implantação. As instruções de implantação são incluídas no final do notebook.

## <a name="conclusion"></a>Conclusão

Este cenário fornece uma visão geral da criação de uma solução de manutenção preditiva de ponta a ponta usando PySpark dentro do ambiente do Notebook Jupyter no Machine Learning Workbench. Este cenário de exemplo também fornece detalhes sobre a implantação de modelo através do ambiente de Gerenciamento de Modelos do Machine Learning para fazer previsões de falha de equipamentos em tempo real.

## <a name="references"></a>Referências

As referências a seguir fornecem exemplos de outros casos de uso de manutenção preditiva de várias plataformas:

* [Modelo da solução de manutenção preditiva](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guia de modelagem de manutenção preditiva](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Guia de modelagem de manutenção preditiva usando os Serviços R do SQL](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Bloco de anotações do Python do guia de modelagem de manutenção preditiva](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Manutenção preditiva usando o PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Aprendizado profundo para manutenção preditiva](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-deep-learning-for-predictive-maintenance)

## <a name="next-steps"></a>Próximas etapas

Outros cenários de exemplo estão disponíveis no Machine Learning Workbench que demonstram os recursos adicionais do produto. 
