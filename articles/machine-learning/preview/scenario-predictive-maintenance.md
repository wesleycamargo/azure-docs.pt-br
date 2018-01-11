---
title: "Cenário do mundo real de manutenção preditiva | Microsoft Docs"
description: "Cenário do mundo real de manutenção preditiva usando o PySpark"
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
ms.openlocfilehash: d8e34924cb29e2e6469d009e40b04d5cee8930a6
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2017
---
# <a name="predictive-maintenance-real-world-scenario"></a>Cenário do mundo real de manutenção preditiva.

O impacto do tempo de inatividade não agendado de um equipamento pode ser prejudicial para qualquer negócio. É essencial, portanto, manter os equipamentos de campo em execução para maximizar o uso e o desempenho e minimizar o tempo de inatividade não agendado e dispendioso. A identificação antecipada de problemas pode ajudar a alocar recursos de manutenção limitados de maneira mais econômica e a melhorar a qualidade e os processos da cadeia de fornecimento. 

Esse cenário explora um [conjunto de dados simulados em escala relativamente grande](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) para explicar um projeto de ciência de dados de manutenção preditiva a partir da ingestão de dados, engenharia de recursos, criação de modelos, e operacionalização e implantação de modelos. O código para todo o processo é escrito nos Notebooks Jupyter usando o PySpark dentro do Azure ML Workbench. O modelo final é implantado usando o Gerenciamento de Modelos do Machine Learning do Azure para fazer previsões de falhas de equipamentos em tempo real.   

## <a name="link-to-the-gallery-github-repository"></a>Link para o repositório GitHub da galeria

A seguir está o link para o repositório GitHub público: [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>Visão geral de casos de uso

Um grande problema enfrentado pelos negócios nos setores de ativos pesados são os custos significativos associados a atrasos para problemas mecânicos. A maioria dos negócios está interessada em prever quando esses problemas ocorrem para evitá-los proativamente antes que eles ocorram. A meta é reduzir os custos diminuindo o tempo de inatividade e possivelmente aumentar a segurança. 

Esse cenário leva ideias do [guia estratégico de manutenção preditiva](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance) para demonstrar a criação de um modelo preditivo para um conjunto de dados simulados. Os dados de exemplo são derivados de elementos comuns observados em muitos casos de uso de manutenção preditiva.

O problema de negócios para esses dados simulados é prever problemas causados por falhas em componentes. A pergunta de negócios, portanto, é “*qual é a probabilidade de um computador ficar inoperante devido à falha de um componente*?” Esse problema é formatado como um problema de classificação multiclasse (vários componentes por computador) e um algoritmo de aprendizado de máquina é usado para criar o modelo preditivo. O modelo é treinado em dados históricos coletados dos computadores. Nesse cenário, o usuário passa por várias etapas de implementação desse modelo no ambiente do Azure Machine Learning Workbench.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta do Azure](https://azure.microsoft.com/en-us/free/) (avaliações gratuitas estão disponíveis).
* Uma cópia instalada do [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) seguindo o [guia de instalação de início rápido](./quickstart-installation.md) para instalar o programa e criar um espaço de trabalho.
* A Operacionalização do Azure Machine Learning exige um ambiente de implantação local e uma [conta de gerenciamento de modelos](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview)

Este exemplo pode ser executado em qualquer contexto de computação do AML Workbench. No entanto, é recomendável executá-lo com pelo menos 16 GB de memória. Esse cenário foi criado e testado em um computador Windows 10 que executa remotamente uma [Máquina Virtual de Ciência de Dados para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) de padrão DS4_V2.

A operacionalização do modelo foi feita usando a versão da CLI do Azure ML 0.1.0a22.

## <a name="create-a-new-workbench-project"></a>Criar um novo projeto de Workbench

Crie um novo projeto usando este exemplo como modelo:
1.  Abra o Azure Machine Learning Workbench
2.  Na página **Projetos**, clique no sinal **+** e selecione **Novo Projeto**
3.  No painel **Criar Novo Projeto**, preencha as informações do seu novo projeto
4.  Na caixa de pesquisa **Pesquisar modelos de projeto**, digite "Manutenção preditiva" e selecione o modelo
5.  Clique em **Criar**

## <a name="prepare-the-notebook-server-computation-target"></a>Preparar o destino de computação do servidor de notebook

Para executar no computador local, no menu `File` do AML Workbench, selecione `Open Command Prompt` ou `Open PowerShell CLI`. A interface CLI permite que você acesse os seus serviços do Azure usando os comandos `az`. Primeiro, faça logon em sua conta com o comando:

```
az login
``` 

Esse comando fornece uma chave de autenticação a ser usada com a URL `https:\\aka.ms\devicelogin`. A CLI aguarda até que a operação de logon de dispositivo retorne e forneça algumas informações de conexão. Em seguida, se você tiver uma instalação de [docker](https://www.docker.com/get-docker) local, prepare o ambiente de computação local com os seguintes comandos:

```
az ml experiment prepare --target docker --run-configuration docker
```

É preferível executar em uma [Máquina de Virtual de Ciência de Dados para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) devido aos requisitos de memória e disco. Depois que o DSVM estiver configurado, prepare o ambiente do docker remoto com os dois comandos a seguir:

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

Os [dados simulados](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) consistem em cinco arquivos .csv (valores separados por vírgula). Siga os links para obter uma descrição mais detalhada dos conjuntos de dados.

* [Computadores](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): recursos que diferenciam cada computador. Por exemplo, idade e modelo.
* [Erro](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): o log de erros contém erros contínuos gerados enquanto o computador ainda está em operação. Esses erros não são considerados falhas, embora possam ser preditivos de um evento de falha futuro. A data-hora do erro é arredondada para a hora mais próxima, porque os dados telemétricos são coletados por hora.
* [Manutenção](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): o log de manutenção contém os registros de manutenção agendados e não agendados. A manutenção agendada corresponde à inspeção regular de componentes, a manutenção não agendada pode surgir da falha mecânica ou de outra degradação do desempenho. A data-hora da manutenção é arredondada para a hora mais próxima, porque os dados telemétricos são coletados por hora.
* [Telemetria](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): Os dados telemétricos consistem em medidas temporais de vários sensores dentro de cada computador. Os dados são registrados por média dos valores de sensor em cada intervalo de uma hora.
* [Falhas](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): as falhas correspondem às substituições de componentes dentro do log de manutenção. Cada registro contém a ID do computador, o tipo de componente e a data e hora da substituição. Esses registros são usados para criar os rótulos de aprendizado de maquina que o modelo está tentando prever.

Consulte o cenário [Ingestão de dados](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) do Jupyter Notebook na seção Código para baixar os conjuntos de dados brutos do repositório GitHub e para criar os conjuntos de dados PySpark para esta análise.

## <a name="scenario-structure"></a>Estrutura do cenário
O conteúdo para o cenário está disponível no [repositório GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

O arquivo [Readme](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) descreve o fluxo de trabalho a partir do preparo os dados, compilação de um modelo e, em seguida, implantação de uma solução para produção. Cada etapa do fluxo de trabalho é encapsulada em um notebook do Jupyter na pasta do [código](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) dentro do repositório.   

[`Code\1_data_ingestion.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): este notebook baixa os cinco arquivos de entrada .csv, executa a limpeza preliminar dos dados e a visualização. O notebook converte cada conjunto de dados para o formato do PySpark e armazena os mesmos em um contêiner de blob do Azure para uso no notebook de engenharia de recursos.

[`Code\2_feature_engineering.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): Usando o conjunto de dados brutos de blob do Azure, os recursos do modelo são criados usando a abordagem de série de tempo padrão para dados de telemetria, erros e manutenção. As substituições de componente relacionadas com falhas são usadas para construir os rótulos de modelos que descrevem o componente que falhou. Os dados do recurso rotulado são salvos em um blob do Azure para o notebook de criação de modelo.

[`Code\3_model_building.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): usando o conjunto de dados do recurso rotulado, o notebook de modelagem divide os dados em conjuntos de dados de treinamento e desenvolvimento ao longo do carimbo de data e hora. O notebook é configurado com um conjunto de experimentos com modelos `pyspark.ml.classification`. Os dados de treinamento são vetorizados e o usuário pode realizar experimentos com um `DecisionTreeClassifier` ou um `RandomForestClassifier`, manipulando hiperparâmetros para localizar o modelo de melhor desempenho. O desempenho é determinado pela avaliação de estatísticas de medidas no conjunto de dados de desenvolvimento. Essas estatísticas são registradas na tela de tempo de execução do AML Workbench para acompanhamento. Em cada execução, o notebook salva o modelo resultante no disco local que executa o kernel do notebook do Jupyter. 

[`Code\4_operationalization.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): usando o último modelo salvo no disco local (kernel do notebook do Jupyter), esse notebook cria os componentes para a implantação do modelo em um serviço Web do Azure. Os ativos completamente operacionais são compactados no arquivo `o16n.zip` armazenado em outro contêiner de blob do Azure. O arquivo compactado contém:

* `service_schema.json` O arquivo da definição de esquema para implantação. 
* `pdmscore.py` As funções init() e run() exigidas pelo o serviço Web do Azure
* `pdmrfull.model` O diretório de definição de modelo.
    
 O notebook testa as funções com a definição de modelo antes de compactar os ativos de operacionalização para implantação. As instruções de implantação são incluídas no final do notebook.

## <a name="conclusion"></a>Conclusão

Este cenário fornece ao leitor uma visão geral de como criar uma solução de manutenção preditiva de ponta a ponta usando o PySpark dentro do ambiente de bloco de anotações do Jupyter no Azure ML Workbench. Este cenário de exemplo também fornece detalhes sobre a implantação de modelo através do ambiente de Gerenciamento de Modelos do Machine Learning do Azure para fazer previsões de falha de equipamentos em tempo real.

## <a name="references"></a>Referências

Esse caso de uso foi desenvolvido anteriormente em várias plataformas:

* [Modelo da solução de manutenção preditiva](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guia de modelagem de manutenção preditiva](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Guia de modelagem de manutenção preditiva usando os Serviços R do SQL](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Bloco de anotações do Python do guia de modelagem de manutenção preditiva](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Manutenção preditiva usando o PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

## <a name="next-steps"></a>Próximas etapas

Existem vários outros cenários de exemplo disponíveis no Azure Machine Learning Workbench que demonstram os recursos adicionais do produto. 