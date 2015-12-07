<properties 
	pageTitle="Como identificar cenários e planejar o processamento de dados analíticos avançados | Microsoft Azure" 
	description="Planeje a análise avançada considerando uma série de perguntas importantes." 
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
	ms.author="bradsev" />


# Como identificar cenários e planejar o processamento de dados analíticos avançados

Quais recursos você deve planejar incluir ao configurar um ambiente para o processamento de análise avançada em um conjunto de dados? Este artigo sugere uma série de perguntas a serem feitas e que ajudarão a identificar as tarefas e os recursos relevantes ao seu cenário. A ordem das etapas de alto nível para análise preditiva é descrita no documento [O que é o CAP (Processo de Análise da Cortana)?](machine-learning-data-science-the-cortana-analytics-process.md). Cada uma dessas etapas exigirá recursos específicos para as tarefas relevantes ao seu cenário específico. As principais perguntas para identificar seu cenário dizem respeito à logística de dados, às características, à qualidade dos conjuntos de dados e às ferramentas e linguagens que você prefere para fazer a análise.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Perguntas sobre logística: locais e movimentação de dados
As perguntas sobre logística dizem respeito à localização da **fonte de dados**, ao **destino** no Azure e aos requisitos para mover os dados, inclusive o cronograma, a quantidade e os recursos envolvidos. Pode ser necessário mover os dados várias vezes durante o processo de análise. Um cenário comum é mover os dados locais para alguma forma de armazenamento no Azure e então para o Estúdio de Aprendizado de Máquina.

1. **Qual é a sua fonte de dados?** Ela é local ou fica na nuvem? Por exemplo:
	- Os dados estão disponíveis publicamente em um endereço HTTP.
	- Os dados residem em um local de arquivo local/na rede.
	- Os dados estão em um banco de dados do SQL Server.
	- Os dados são armazenados em um contêiner de armazenamento do Azure.

2. **Qual é o destino do Azure?** Onde ele precisa estar para processamento ou modelagem? Por exemplo:
	- Armazenamento do Blobs do Azure
	- Bancos de dados do SQL Azure
	- SQL Server em VM do Azure
	- Tabelas do HDInsight (Hadoop no Azure) ou do Hive
	- Aprendizado de Máquina do Azure
	- Discos rígidos virtuais montáveis do Azure.

3. **Como você moverá os dados?** Os procedimentos e os recursos disponíveis para ingestão ou carregamento de dados em uma variedade de ambientes de armazenamento e de processamento diferentes são descritos nos tópicos a seguir.

	-  [Carregar dados em ambientes de armazenamento para análise](machine-learning-data-science-ingest-data.md) 
	-  [Importar os dados de treinamento para o Estúdio de Aprendizado de Máquina do Azure de diferentes fontes de dados](machine-learning-data-science-import-data,md).

4. **Os dados precisam ser movidos em um cronograma regular ou ser modificados durante a migração?** Considere o uso do ADF (Azure Data Factory) quando os dados precisarem ser migrados de forma contínua, particularmente se um cenário híbrido que acesse os recursos locais e de nuvem estiver envolvido ou quando os dados forem transacionados ou quando for necessário modificá-los ou adicionar lógica de negócios a eles durante a migração. Para saber mais, confira [Mover dados de um servidor SQL local para o SQL Azure com o Azure Data Factory](machine-learning-data-science-move-sql-azure-adf.md)

5. **Quanto dos dados será movido para o Azure?** Os conjuntos de dados muito grandes podem exceder a capacidade de armazenamento de determinados ambientes. Para obter um exemplo, confira a discussão sobre os limites de tamanho do Estúdio de Aprendizado de Máquina na próxima seção. Nesses casos, uma amostra dos dados poderá ser usada durante a análise. Para obter detalhes sobre como reduzir a amostra de um conjunto de dados em vários ambientes do Azure, confira [Dados de exemplo no Processo de Análise da Cortana](machine-learning-data-science-sample-data.md).


## Perguntas sobre características de dados: tipo, formato e tamanho
Essas perguntas são essenciais para o planejamento de seus ambientes de armazenamento e de processamento, cada um apropriado aos diversos tipos de dados e cada um com determinadas restrições.

1. **Quais são os tipos de dados?** Por exemplo: 
	- Numérico
	- Categóricos
	- Cadeias de caracteres
	- Binário

2. **Como seus dados estão formatados?** Por exemplo:
    - Arquivos simples separados por vírgulas (CSV) ou separados por tabulações (TSV)
    - Compactados ou descompactados
	- Blobs do Azure
	- Tabelas do Hive do Hadoop
	- Tabelas do SQL Server

2. **Qual o tamanho dos seus dados?**
    - Pequeno: menos de 2 GB
    - Médio: mais de 2 GB e menos de 10 GB
	- Grande: maior que 10 GB

Tome o ambiente do Estúdio de Aprendizado de Máquina do Azure como exemplo:

- Para obter uma lista dos formatos e dos tipos de dados com suporte no Estúdio de Aprendizado de Máquina do Azure, confira a seção [Formatos de dados e tipos de dados com suporte](machine-learning-data-science-import-data.md#data-formats-and-data-types-supported).
- Para obter informações sobre as limitações de dados para o Estúdio de Aprendizado de Máquina do Azure, confira a seção **De que tamanho deverá ser o conjunto de dados para os meus módulos?** de [Importando e exportando dados para o Aprendizado de Máquina](machine-learning-faq.md#machine-learning-studio-questions)

Para obter informações sobre as limitações de outros serviços do Azure usados no processo de análise, confira [Assinatura do Azure e Limites de Serviço, Cotas e Restrições](azure-subscription-service-limits.md).

## Perguntas sobre qualidade de dados: exploração e pré-processamento

1. **O que você sabe sobre seus dados?** Explore os dados quando precisar compreender suas características básicas. Quais padrões ou tendências eles exibem, quais exceções que possuem ou quantos valores estão ausentes. Esta etapa é importante para a determinação da extensão do pré-processamento necessário, para a formulação de hipóteses que poderiam sugerir os recursos mais apropriados ou o tipo de análise e para a formulação de planos para coleta de dados adicionais. O cálculo de estatísticas descritivas e a plotagem de visualizações são técnicas úteis para a inspeção de dados. Para obter detalhes sobre como explorar um conjunto de dados em vários ambientes do Azure, confira [Explorar dados no Processo de Análise da Cortana](machine-learning-data-science-explore-data.md).

2. **Os dados exigem pré-processamento ou limpeza?** O pré-processamento e a limpeza de dados são tarefas importantes e geralmente devem ser realizadas antes que o conjunto de dados possa ser usado com eficiência para o aprendizado de máquina. Dados brutos costumam conter ruídos e não são confiáveis, e pode haver valores ausentes. Usar esses dados para a modelagem pode produzir resultados incorretos. Para obter uma descrição, confira [Tarefas para preparar dados para o aprendizado de máquina avançado](machine-learning-data-science-prepare-data.md).

## Perguntas sobre ferramentas e linguagens
Há muitas opções, dependendo de quais linguagens e ambientes ou ferramentas de desenvolvimento você precisa ou com as quais se sente mais confortável.
 
1. **Quais linguagens você prefere usar para análise?**  
	- R
	- Python
	- SQL

2. **Quais ferramentas você deve usar para a análise de dados?**
	- [Microsoft Azure Powershell](powershell-install-configure.md) - uma linguagem de script usada para administrar os recursos do Azure em uma linguagem de script.
	- [Estúdio de Aprendizado de Máquina do Azure](machine-learning-what-is-ml-studio/)
	- [Revolution Analytics](http://www.revolutionanalytics.com/revolution-r-open)
	- [RStudio](http://www.rstudio.com)
	- [Python Tools para Visual Studio](http://microsoft.github.io/PTVS/)
	- [Anaconda](https://www.continuum.io/why-anaconda)
	- [Jupiter notebooks](http://jupyter.org/)
	- [Microsoft Power BI](http://powerbi.microsoft.com) 


## Identificar seu cenário de análise avançada
Depois de responder às perguntas na seção anterior, você está pronto para determinar qual cenário melhor se adapta a seu caso. Os cenários de exemplo são descritos em [Cenários para análise avançada no Aprendizado de Máquina do Azure](../machine-learning-data-science-plan-sample-scenarios.md).







 

<!---HONumber=AcomDC_1125_2015-->