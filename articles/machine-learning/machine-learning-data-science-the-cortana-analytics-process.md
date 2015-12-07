<properties 
	pageTitle="O que é o Processo de Análise da Cortana (CAP)? | Microsoft Azure" 
	description="O Processo de Análise da Cortana é um método de ciência de dados sistemático para a criação de aplicativos inteligentes que aproveitam a análise avançada." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev"
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/23/2015" 
	ms.author="bradsev;gopitk" />


# O que é o Processo de Análise da Cortana (CAP)?

O Processo de Análise da Cortana (CAP) é um método de ciência de dados sistemática que descreve uma sequência de etapas que aproveita a análise avançada para criar aplicativos inteligentes. As etapas do CAP oferecem **diretrizes** sobre como definir o problema, analisar os dados relevantes, criar e avaliar os modelos de previsão e implantar os modelos de aplicativos inteligentes.

Estas são as etapas do **Processo de Análise da Cortana**:

![Fluxo de trabalho do CAP](./media/machine-learning-data-science-the-cortana-analytics-process/CAP-workflow.png)

O processo é **iterativo**: a compreensão dos refinamentos novos e existentes no modelo evolui e exige o retrabalho em etapas anteriormente concluídas na sequência. Os processos de planejamento de projeto e de desenvolvimento organizacional existentes são **adaptados com facilidade** ao trabalho com a sequência de etapas definida pelo CAP.

As etapas no processo são diagramadas e vinculadas no [Roteiro de aprendizagem do CAP](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) e descritas abaixo.

## Etapas de preparação 

## P1. Planejar o projeto de análise 

Iniciar um projeto de análise, definindo suas metas de negócios e os problemas. Eles são especificados em termos de **requisitos de negócios**. Um objetivo central desta etapa é identificar as variáveis de negócios essenciais (previsão de vendas ou a probabilidade de um pedido ser fraudulento, por exemplo) que a análise precisa prever para atender a esses requisitos. Normalmente, o planejamento adicional é essencial para o desenvolvimento de uma compreensão das **fontes de dados** necessárias para atender aos objetivos do projeto de uma perspectiva analítica. Por exemplo, não é incomum descobrir que os sistemas existentes precisam coletar e registrar tipos de dados adicionais para solucionarem o problema e atingirem os objetivos do projeto. Para obter diretrizes, consulte [Planejar seu ambiente para o Processo de Análise da Cortana](machine-learning-data-science-plan-your-environment.md) e [Cenários para análises avançadas no Aprendizado de Máquina do Azure](machine-learning-data-science-plan-sample-scenarios.md).

## P2. Configurar o ambiente de análise 

Um ambiente de análise para o Processo de Análise da Cortana envolve vários componentes:

- **espaços de trabalho de dados**, em que os dados são preparados para análise e modelagem, 
- uma **infraestrutura de processamento** para pré-processamento, exploração e modelagem de dados
- uma **infraestrutura em tempo de execução** para operacionalizar os modelos analíticos e executar os aplicativos cliente inteligentes que consomem os modelos.  

A infraestrutura de análise que precisa ser configurada com frequência faz parte de um ambiente separado dos sistemas operacionais principais. Mas ela normalmente utiliza dados de vários sistemas dentro da empresa, bem como de fontes externas à empresa. A infraestrutura de análise pode ser puramente baseada em nuvem ou uma instalação local, ou ainda uma mistura de ambas. Para obter opções, consulte [Configure ambientes de ciência da dados para uso no Processo de Análise da Cortana](machine-learning-data-science-environment-setup.md).

## Etapas da análise:  

## 1\. Ingestão de dados para o ambiente analítico 

A primeira etapa é trazer os dados relevantes de várias fontes, seja de dentro ou de fora da empresa, para ambientes de análise em que os dados podem ser processados. O **formato** dos dados na fonte pode ser diferente do formato exigido pelo destino. Dessa forma, também pode ser necessário que as ferramentas de ingestão façam algumas transformações de dados. Para obter opções, consulte [Carregar dados em ambientes de armazenamento para análise](machine-learning-data-science-ingest-data.md)

Além da ingestão inicial dos dados, serão necessários muitos aplicativos inteligentes para a atualização regular dos dados como parte de um processo de aprendizado em andamento. Isso pode ser feito configurando um **pipeline de dados** ou um fluxo de trabalho. Isso faz parte da parte interativa do processo que inclui a recriação e a reavaliação dos modelos analíticos usados pelo aplicativo inteligente que esteja implantando a solução. Consulte, por exemplo, [Mover dados de um SQL Server local para o SQL Azure com o Azure Data Factory](machine-learning-data-science-move-sql-azure-adf.md).


## 2\. Explorar e pré-processar os dados 

A próxima etapa será obter uma compreensão mais profunda dos dados investigando as **estatísticas de resumo**, os relacionamentos e usando técnicas como a **visualização**. O tratamento de problemas de **qualidade de dados** e de integridade, como valores ausentes, incompatibilidades de tipos de dados e relacionamentos de dados inconsistentes também é feito aqui. As transformações de pré-processamento são usadas para limpar os dados brutos antes que outra análise ou modelagem possa acontecer. Para obter uma descrição, consulte [Tarefas para preparar dados para o aprendizado de máquina avançado](machine-learning-data-science-prepare-data.md).


## 3\. Desenvolver recursos 

Os cientistas de dados, em colaboração com os especialistas de domínio, devem identificar os recursos que capturam as propriedades relevantes do conjunto de dados e que podem ser usados com mais eficiência para a previsão das principais variáveis de negócios identificadas durante o planejamento. Esses novos recursos podem ser derivados de dados existentes ou podem exigir a coleta de dados adicionais. Esse processo é conhecido como **engenharia de recursos** e é uma das principais etapas da criação de um sistema eficiente de análise preditiva. Esta etapa requer uma combinação criativa de experiência de domínio e das ideias obtidas na etapa de exploração de dados. Para obter diretrizes, consulte [Engenharia de recurso no Processo de Análise da Cortana](machine-learning-data-science-create-features.md).


## 4\. Criar modelos de previsão 

Os cientistas de dados criam modelos de análise para a previsão das principais variáveis identificadas pelos requisitos de negócios definidos na etapa de planejamento usando dados que foram limpos e destacados. Os sistemas de aprendizado de máquina dão suporte a vários **algoritmos de modelagem** aplicáveis a uma ampla variedade de casos. Para obter diretrizes, consulte [Como escolher algoritmos de Aprendizado de Máquina do Microsoft Azure](machine-learning-algorithm-choice,md).

Os cientistas de dados devem escolher o modelo mais adequado para sua tarefa de previsão e não é incomum que os resultados de vários modelos tenham de ser combinados para a obtenção dos melhores resultados. Os dados de entrada para a modelagem geralmente são divididos aleatoriamente em três partes:

- um conjunto de dados de treinamento, 
- um conjunto de dados de validação, 
- um conjunto de dados de testes 

Os modelos são criados usando o **conjunto de dados de treinamento**. A combinação ideal de modelos (com os parâmetros ajustados) é selecionada por meio da execução dos modelos e da medição dos erros de previsão para o **conjunto de dados de validação**. Por fim, o **conjunto de dados de testes** é usada para avaliar o desempenho do modelo escolhido em dados independentes que não foram usados para treinar ou validar o modelo. Para obter procedimentos, consulte [Como avaliar o desempenho do modelo no Aprendizado de Máquina do Azure](machine-learning-evaluate-model-performance.md).


## 5\. Implantar e consumir modelos 

Assim que tivermos um conjunto de modelos com um bom desempenho, eles poderão ser **operacionalizados** para o consumo de outros aplicativos. Dependendo dos requisitos de negócios, as previsões serão feitas em **tempo real** ou em **lotes**. Para ser operacionalizada, os modelos precisam ser expostos com uma **interface de API aberta**, que é facilmente consumida de diversos aplicativos, como sites, planilhas, painéis online, ou aplicativos de linha de negócios ou de back-end. Consulte [Implantar um serviço Web de Aprendizado de Máquina do Azure](machine-learning-publish-a-machine-learning-web-service.md).

## Resumo e próximas etapas

O [Processo do Cortana Analytics](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) é modelado como uma sequência de etapas repetidas que **oferecem diretrizes** sobre as tarefas necessárias ao uso de uma análise avançada para a criação de um aplicativo inteligente. Cada etapa também fornece detalhes sobre como usar diversas tecnologias da Microsoft para concluir as tarefas descritas.

Embora o CAP não prescreva tipos específicos de artefatos de **documentação**, é uma prática recomendada documentar os resultados de exploração de dados, a modelagem e a avaliação e salvar o código pertinente para que a análise possa ser iterada quando necessário. Isso também permite a reutilização do trabalho de análise enquanto você estiver trabalhando em outros aplicativos que envolvam dados e tarefas de previsão semelhantes.

Também serão fornecidos passo a passos completos que demonstram todas as etapas do processo para **cenários específicos**. Consulte [O Processo de Análise da Cortana em ação: usando SQL Server](machine-learning-data-science-process-sql-walkthrough.md) e [O Processo de Análise da Cortana em ação: usando clusters Hadoop do HDInsight](machine-learning-data-science-process-hive-walkthrough.md).

 

<!---HONumber=AcomDC_1125_2015-->