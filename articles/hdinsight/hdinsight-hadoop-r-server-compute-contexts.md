<properties
   pageTitle="Opções de contexto de computação para o Servidor R no HDI Premium | Azure"
   description="Conheça as diferentes opções de contexto de computação disponíveis para usuários com o Servidor R no HDInsight Premium"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="03/28/2016"
   ms.author="jeffstok"
/>

# Opções de contexto de computação para o Servidor R no HDInsight Premium

O Servidor R no HDI Spark fornece os recursos mais recentes para análise com base em R usando dados armazenados em HDFS em um contêiner em sua conta de armazenamento de [Blobs do Azure](../storage/storage-introduction.md "Armazenamento do Blob do Azure") ou no sistema de arquivos local do Linux. Como o Servidor R é criado em R de software livre, os aplicativos baseados em R que você criar poderão aproveitar qualquer um dos mais de oito mil pacotes de software livre R, bem como as rotinas em [ScaleR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "Revolution Analytics ScaleR"), o pacote de análise de big data da Microsoft incluído no Servidor R. O nó de borda de clusters Premium fornece uma conveniente zona de aterrissagem para conexão com o cluster e para executar seus scripts em R. Com um nó de borda, você tem a opção de executar funções paralelizadas e distribuídas do ScaleR entre os núcleos do servidor de nó de borda ou entre todos os nós do cluster por meio do uso do Hadoop Map Reduce do ScaleR ou dos contextos de computação do Spark.

## Contextos de computação para um nó de extremidade

Em geral, um script R executado no Servidor R no nó de extremidade será executado dentro do interpretador de R nesse nó, exceto para as etapas que chamam uma função ScaleR. As chamadas de ScaleR serão executadas em um ambiente de computação determinado pela configuração do contexto de computação do ScaleR. Valores possíveis de contexto da computação ao executar seu script R de um nó de extremidade são local sequencial ('local'), local paralelo ('localpar'), Map Reduce e Spark, em que:

| Contexto de computação | Como definir | Contexto de execução |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| Local sequencial | rxSetComputeContext(‘local’) | Execução sequencial (não paralela) no servidor de nó de extremidade |
| Local paralelo | rxSetComputeContext(‘localpar’) | Em paralelo entre os núcleos do servidor de nó de extremidade |
| Spark | RxSpark() | Execução distribuída em paralelo por meio do Spark em todos os nós do cluster do HDI |
| Map Reduce | RxHadoopMR() | Execução distribuída em paralelo por meio do Map Reduce em todos os nós do cluster do HDI |


Supondo que você preferiria a execução em paralelo para fins de desempenho, há três opções. Qual delas você escolherá depende da natureza de seu trabalho de análise e do tamanho e local dos dados.

## Decidindo por um contexto de computação

No momento, não há uma fórmula para levá-lo à conclusão de qual contexto de computação deverá ser usado. Mas há alguns princípios importantes que apontarão para a escolha certa ou pelo menos ajudarão a reduzir as opções antes da execução de um parâmetro de comparação se uma opção ideal for necessária. Esses princípios básicos incluem:

1.	O sistema de arquivos Linux local será mais rápido do que o HDFS
2.	Análises repetidas serão mais rápidas se os dados forem locais e em XDF 
3.	Fazer a transmissão de uma fonte de dados de texto para pequenos dados ou converter para XDF antes da análise 
4.	A sobrecarga de cópia/transmissão de dados para o nó de extremidade para análise se tornará inviável para dados muito grandes 
5.	O Spark será mais rápido do o Map Reduce para análises em Hadoop até que os dados se tornem grandes demais e não caibam mais na memória distribuída

Com esses princípios, algumas regras gerais para selecionar um contexto de computação são:

### Local paralelo

- Se os dados a serem analisados forem pequenos e não demandarem análise repetida, transmita-os diretamente para a rotina de análise e use 'localpar'. 
- Se os dados a serem analisados forem pequenos e demandarem análises repetidas, copie-os para o sistema de arquivos local, importe para XDF e analise via 'localpar'. 

### Hadoop Spark

- Se os dados a serem analisados forem grandes, importe para XDF no HDFS, a menos que o armazenamento seja um problema, e analise via 'Spark'. 

### Hadoop Map Reduce

- Se os dados a serem analisados forem muito grandes e o desempenho do Spark começar a apresentar queda, tente uma análise via 'Map Reduce'.

## Ajuda embutida em rxSetComputeContext

Para obter mais informações e exemplos de contextos de computação de ScaleR, consulte a ajuda embutida sobre R no método rxSetComputeContext, por exemplo,

    > ?rxSetComputeContext 

ou consulte o "ScaleR Distributed Computing Guide" disponível na biblioteca do [Servidor R no MSDN](https://msdn.microsoft.com/library/mt674634.aspx "R Server no MSDN").


## Próximas etapas

Agora que você entende como criar um novo cluster HDInsight que inclui o Servidor R e as noções básicas sobre como usar o console R em uma sessão SSH, use o seguinte para descobrir outras maneiras de trabalhar com o Servidor R no HDInsight.

- [Overview of R Server on Hadoop (Visão geral do servidor R no Hadoop)](hdinsight-hadoop-r-server-overview.md)
- [Get started with R server on Hadoop (Introdução ao servidor R no Hadoop)](hdinsight-hadoop-r-server-get-started.md)
- [Adicionar RStudio Server ao HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [Opções de armazenamento do Azure para o Servidor R no HDInsight Premium](hdinsight-hadoop-r-server-storage.md)

<!-----------HONumber=AcomDC_0330_2016-->