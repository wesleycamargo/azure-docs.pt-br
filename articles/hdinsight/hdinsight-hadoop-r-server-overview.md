<properties
	pageTitle="O que é o R no HDInsight? Introdução ao Servidor R no HDInsight (preview) | Microsoft Azure"
	description="O que é o Servidor R no HDInsight (preview) e como usar o R para criar aplicativos para a análise de big data."
	services="hdinsight"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/01/2016"
   ms.author="jeffstok"/>


#Visão geral: Servidor R no HDInsight (preview)

Com o HDInsight Premium, o Servidor R agora está disponível como uma opção durante a criação de clusters HDInsight no Azure. Essa nova funcionalidade fornece aos cientistas de dados, estatísticos e programadores de R o acesso sob demanda a métodos escalonáveis e distribuídos de análise no HDInsight. Os clusters podem ser dimensionados para os projetos e as tarefas em questão, e interrompidos quando não forem mais necessários. Como fazem parte do Azure HDInsight, esses clusters recebem suporte de nível empresarial 24 horas por dia e sete dias por semana, SLA de 99,9% de atividade e a flexibilidade da integração com outros componentes do ecossistema Azure.

>[AZURE.NOTE] O Servidor R está disponível somente com o HDInsight Premium. No momento, o HDInsight Premium só está disponível para clusters Hadoop e Spark. Por isso, atualmente só é possível usar o Servidor R com clusters Hadoop e Spark no HDInsight. Para obter mais informações, consulte [Quais são os diferentes níveis de serviço e componentes do Hadoop disponíveis no HDInsight](hdinsight-component-versioning.md).

O Servidor R no HDInsight fornece os recursos mais recentes para análise de R em grandes conjuntos de dados carregados para o Blob do Azure (WASB). Como o Servidor R é criado em R de software livre, os aplicativos baseados em R que você criar poderão aproveitar qualquer um dos mais de oito mil pacotes de software livre R, bem como as rotinas em ScaleR, o pacote de análise de big data da Microsoft incluído no Servidor R. O nó de borda de clusters Premium fornece uma conveniente zona de aterrissagem para conexão com o cluster e para executar seus scripts em R. Com um nó de borda, você tem a opção de executar funções paralelizadas e distribuídas do ScaleR entre os núcleos do servidor de nó de borda ou entre todos os nós do cluster por meio do uso do Hadoop Map Reduce do ScaleR ou dos contextos de computação do Spark. Os modelos ou previsões resultantes das análises que podem ser baixadas para uso local ou operacionalizadas em outro local no Azure, tal como por meio de um [serviço Web](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md) [AML (Estúdio de Aprendizado de Máquina do Azure)](http://studio.azureml.net).

## Introdução ao R no HDInsight

Para incluir o Servidor R em um cluster HDInsight, você deve criar um cluster Hadoop ou Spark com a opção Premium ao criar um cluster usando o Portal do Azure. Ambos os tipos de cluster usam a mesma configuração que inclui o Servidor R nos nós de dados de um cluster e um nó de borda como uma zona de aterrissagem para análise no Servidor R. Consulte [Introdução ao Servidor R no HDInsight](hdinsight-hadoop-r-server-get-started.md) para ver um passo a passo detalhado sobre como criar um cluster.

## Opções de armazenamento de dados

O armazenamento padrão para clusters HDInsight fica nos Blobs do Azure (WASB) com o sistema de arquivos HDFS mapeado para um contêiner de blob. Isso assegura que os dados que são carregados para o armazenamento de cluster ou gravados no armazenamento do cluster durante a análise sejam persistentes. Um método conveniente de copiar dados do blob e para ele é usar o utilitário [AzCopy](../storage/storage-use-azcopy.md).

Além do Blob, há a opção de adicionar o uso do ADLS ([Armazenamento do Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/)) ao seu cluster. Se você adicionar o uso do ADLS, você poderá combinar o uso do Blob e do ADLS para armazenamento HDFS.

Você também pode usar os [Arquivos do Azure](../storage/storage-how-to-use-files-linux.md) como uma opção de armazenamento para uso no nó de borda. Os Arquivos do Azure permitem montar um compartilhamento de arquivos criado em uma conta de Armazenamento do Azure para o sistema de arquivos do Linux. Para obter mais informações sobre as opções de armazenamento de dados para o Servidor R no cluster HDInsight, consulte [Opções de armazenamento para o Servidor R em clusters HDInsight](hdinsight-hadoop-r-server-storage.md).
  
## Acessar o Servidor R no Cluster

Depois de criar um cluster com o Servidor R, você poderá se conectar ao Console R no nó de borda do cluster por meio de SSH/Putty ou de um navegador, se você optar por instalar o IDE do RStudio Server no nó de borda. Para obter mais informações sobre como instalar o RStudio Server, consulte [Instalar o RStudio Server em clusters HDInsight](hdinsight-hadoop-r-server-install-r-studio.md).

## Desenvolver e Executar Scripts R

Os scripts R que você criar e executar podem usar qualquer um dos mais de oito mil pacotes de software livre, além das rotinas paralelizadas e distribuídas na biblioteca do ScaleR. Um script executado no Servidor R no nó de borda será executado usando o interpretador R, exceto para as etapas que chamarem uma das funções do ScaleR com um conjunto de contexto de computação para o Hadoop Map Reduce (RxHadoopMR) ou Spark (RxSpark). Nesses casos, a função será executada de forma distribuída entre os nós de dados (tarefas) do cluster associado aos dados referenciados. Para obter mais informações sobre as diferentes opções de contexto de computação, consulte a seção [Opções de contexto de computação para o Servidor R no HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md).

## Operacionalizar um Modelo

Quando sua modelagem de dados estiver concluída, você poderá operacionalizar o modelo para fazer previsões sobre novos dados, o que também é conhecido como pontuação, no Azure e local. Veja alguns exemplos.

### Pontuação no HDInsight

Para pontuar no HDInsight, você poderia criar uma função R que chama o modelo para fazer previsões sobre um novo arquivo de dados carregado na sua conta do Armazenamento do Azure e salvar as previsões na conta de Armazenamento. Você poderia executar a rotina sob demanda no nó de borda do seu cluster ou usando um trabalho agendado.

### Pontuação no Aprendizado de Máquina do Azure 

Para pontuar usando um serviço Web de Aprendizado de Máquina do Azure, você poderia usar o [pacote R de Aprendizado de Máquina do Azure de software livre](http://www.inside-r.org/blogs/2015/11/18/enhancements-azureml-package-connect-r-azureml-studio) para [publicar seu modelo como um serviço Web do Azure](http://www.r-bloggers.com/deploying-a-car-price-model-using-r-and-azureml/), use os recursos no Aprendizado de Máquina do Azure para criar uma interface do usuário para o serviço Web e depois chamar o serviço Web conforme necessário para a pontuação. Se você escolher essa opção, será necessário converter os objetos de modelo do ScaleR para um objeto de modelo de software livre equivalente para usar com o serviço Web. Isso pode ser feito usando as funções de coerção do ScaleR, tal como `as.randomForest()`, para modelos baseados em união.
  
### Pontuação local

Para pontuar localmente, depois de criar seu modelo, você pode serializar o modelo em R, baixá-lo localmente, desserializá-lo e usá-lo para pontuar novos dados. Você pode fazer isso usando a abordagem listada acima para [Pontuar no HDInsight](#scoring-in-hdinsight) ou usando o [DeployR](https://deployr.revolutionanalytics.com/).

## Manter o cluster 

### Instalar e Manter Pacotes R

A maioria dos pacotes R que você usar será necessária para o nó de borda, pois a maior parte dos seus scripts R será executada nele. Para instalar pacotes R adicionais no nó de borda, você pode usar o método `install.packages()` usual em R.
  
Na maioria dos casos, você não precisará instalar pacotes R adicionais em nós de dados se estiver usando apenas rotinas da biblioteca do ScaleR para execução no cluster. No entanto, talvez você precise de pacotes adicionais para dar suporte ao uso da execução **rxExec** ou **RxDataStep** em nós de dados. Nesses casos, os pacotes adicionais devem ser especificados por meio do uso de uma ação de script depois da criação do cluster. Para obter mais informações, consulte [Criar um cluster HDInsight com o Servidor R](hdinsight-hadoop-r-server-get-started.md).
  
### Alterar as configurações de memória do Hadoop Map Reduce 

Um cluster pode ser modificado para alterar a quantidade de memória disponível para o Servidor R ao executar um trabalho do Map Reduce. Para fazer isso, use a interface do usuário do Ambari disponível por meio da folha do Portal do Azure para seu cluster. Para obter instruções sobre como acessar a interface de usuário do Ambari para seu cluster, consulte [Gerenciar clusters HDInsight usando a interface de usuário do Ambari Web](hdinsight-hadoop-manage-ambari.md).

Também é possível alterar a quantidade de memória disponível para o Servidor R usando opções do Hadoop na chamada para o **RxHadoopMR**, conforme mostrado abaixo.
 
	hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### Dimensionando o cluster

Um cluster existente pode ser dimensionado verticalmente por meio do Portal do Azure. Isso pode ser usado para obter capacidade adicional necessária para um maior processamento ou para reduzir um cluster quando ele estiver ocioso. Para obter instruções sobre como dimensionar um cluster, consulte [Gerenciar clusters HDInsight](hdinsight-administer-use-portal-linux.md).

### Manutenção do Sistema 

A manutenção é executada nas VMs do Linux subjacentes em um cluster HDInsight fora do horário comercial para aplicar patches do sistema operacional, por exemplo. Normalmente, isso é feito às 3:30 AM (com base na hora local da VM) segunda-feira e quinta-feira de cada semana. As atualizações são realizadas de maneira a não afetar mais de ¼ do cluster por vez. Como os nós principais são redundantes e nem todos os nós de dados são afetados, os trabalhos em execução nesse momento podem ficar mais lentos, mas deverão ser executados até serem concluídos. Dados locais ou softwares personalizados que você tiver instalado são preservados nesses eventos de manutenção, a menos que ocorra uma falha catastrófica que exija a recriação do cluster.

## Opções de IDE para o Servidor R no cluster HDInsight

O nó de borda do Linux de um cluster HDInsight Premium é a zona de aterrissagem para análises baseadas em R. Depois de se conectar ao cluster, você poderá iniciar a interface do console para o Servidor R, digite “R” no prompt de comando do Linux. O uso da interface do console será aprimorado se você executar um editor de texto para o desenvolvimento de scripts R em outra janela, e recortar e colar seções do seu script no console R, conforme necessário.
  
Uma etapa mais avançada do que um simples editor de texto para o desenvolvimento do seu script R é usar um IDE com base em R na área de trabalho, tal como o recém-anunciado [RTVS (R Tools para Visual Studio)](https://www.visualstudio.com/pt-BR/features/rtvs-vs.aspx) da Microsoft, uma família de ferramentas de área de trabalho e servidor do [RStudio](https://www.rstudio.com/products/rstudio-server/) ou o [StatET](http://www.walware.de/goto/statet) baseado em Eclipse da Walware.
  
Outra opção é instalar um IDE no próprio nó de borda do Linux. Uma opção popular nesses casos é o [RStudio Server](https://www.rstudio.com/products/rstudio-server/), que fornece um IDE baseado em navegador para o uso por clientes remotos. Instalar o RStudio Server no nó de borda de um cluster HDInsight Premium fornece uma experiência completa de IDE para o desenvolvimento e a execução de scripts R com o Servidor R no cluster, além de poder ser consideravelmente mais produtivo do que o uso padrão do Console R. Se desejar usar o RStudio Server, consulte [Instalar o RStudio Server em clusters HDInsight](hdinsight-hadoop-r-server-install-r-studio.md).

## Preços
 
As taxas associadas a um cluster HDInsight Premium com o Servidor R são estruturadas de forma semelhante aos clusters HDInsight padrão e baseiam-se no dimensionamento das VMs subjacentes entre os nós de nome, dados e borda, com a adição de uma elevação do núcleo-hora para o Premium. Para obter mais informações sobre os preços do HDInsight Premium, incluindo os preços durante a Preview Pública e a disponibilidade de uma avaliação gratuita de 30 dias, consulte os [Preço do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## Próximas etapas

Siga os links abaixo para saber mais sobre como usar o Servidor R com clusters HDInsight.

- [Introdução ao Servidor R no HDInsight](hdinsight-hadoop-r-server-get-started.md)

- [Adicionar RStudio Server ao HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)

- [Opções de contexto de computação para o Servidor R no HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md)

- [Opções de Armazenamento do Azure para o Servidor R no HDInsight Premium](hdinsight-hadoop-r-server-storage.md)

 

<!---HONumber=AcomDC_0608_2016-->