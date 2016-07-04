<properties
	pageTitle="O que é o R no HDInsight? Introdução ao Servidor R no HDInsight (preview) | Microsoft Azure"
	description="O que é o Servidor R no HDInsight (preview) e como usá-lo na criação de aplicativos para análise de big data."
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


# Visão geral do Servidor R no HDInsight (visualização)

Com o Microsoft Azure HDInsight Premium, o Servidor R da Microsoft agora está disponível como uma opção quando você cria clusters HDInsight no Azure. Essa nova funcionalidade fornece aos cientistas de dados, estatísticos e programadores de R o acesso sob demanda a métodos escalonáveis e distribuídos de análise no HDInsight.

Os clusters podem ser dimensionados para os projetos e as tarefas em questão e interrompidos quando não forem mais necessários. Já que fazem parte do Azure HDInsight, esses clusters recebem suporte de nível empresarial 24 horas por dia e sete dias por semana, apresentam um SLA de 99,9% de tempo de ativação e a flexibilidade para se integrar a outros componentes no ecossistema do Azure.

>[AZURE.NOTE] O Servidor R está disponível somente com o HDInsight Premium. No momento, o HDInsight Premium só está disponível para clusters Hadoop e Spark. Por isso, atualmente só é possível usar o Servidor R com clusters Hadoop e Spark no HDInsight. Para saber mais, confira [Quais são os diferentes componentes do Hadoop disponíveis com o HDInsight?](hdinsight-component-versioning.md).

O Servidor R no HDInsight fornece os recursos mais recentes para análise de R em grandes conjuntos de dados carregados no Armazenamento de Blobs do Azure. Como o Servidor R é criado em R de software livre, os aplicativos baseados em R que você criar poderão aproveitar qualquer um dos mais de 8000 pacotes de software livre R, bem como as rotinas em ScaleR, o pacote de análise de big data da Microsoft incluído no Servidor R.

O nó de borda de clusters Premium fornece um local conveniente para se conectar ao cluster e executar scripts R. Com um nó do borda, você tem a opção de executar funções distribuídas paralelizadas do ScaleR nos núcleos do servidor do nó de borda. Você também tem a opção de executá-las em todos os nós do cluster usando o Hadoop Map Reduce do ScaleR ou os contextos de computação do Spark.

Os modelos ou previsões que resultam de análises podem ser baixados para uso no local. Eles também podem ser operacionalizados em qualquer lugar no Azure, como por meio de um [serviço Web](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md) do [Estúdio de Aprendizado de Máquina do Azure](http://studio.azureml.net).

## Introdução ao R no HDInsight

Para incluir o Servidor R em um cluster HDInsight, você deve criar um cluster Hadoop ou Spark com a opção Premium quando cria um cluster usando o Portal do Azure. Ambos os tipos de cluster usam a mesma configuração, que inclui o Servidor R nos nós de dados de um cluster e um nó de borda como uma zona de aterrissagem para análise no Servidor R. Veja [Introdução ao uso do Servidor R no HDInsight](hdinsight-hadoop-r-server-get-started.md) para ver um passo a passo detalhado sobre como criar um cluster.

## Saiba mais sobre as opções de armazenamento de dados

O armazenamento padrão para clusters HDInsight é o Armazenamento de Blobs com o sistema de arquivos HDFS mapeado para um contêiner de blob. Isso assegura que os dados carregados para o armazenamento de cluster ou gravados no armazenamento do cluster durante a análise sejam persistentes. Você pode usar o utilitário [AzCopy](../storage/storage-use-azcopy.md) para copiar dados bidirecionalmente no blob.

Além de usar o Armazenamento de Blobs, você tem a opção de usar o [Armazenamento do Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) com o cluster. Se você usar o Data Lake, será possível usar o Armazenamento de Blobs e o Data Lake para armazenamento HDFS.

Também é possível usar os [Arquivos do Azure](../storage/storage-how-to-use-files-linux.md) como uma opção de armazenamento para uso no nó de borda. Os Arquivos do Azure permitem montar um compartilhamento de arquivos que foi criado em um Armazenamento do Azure para o sistema de arquivos do Linux. Para saber mais sobre opções de armazenamento de dados para Servidor R no cluster HDInsight, confira [Opções de armazenamento do Servidor R em clusters HDInsight](hdinsight-hadoop-r-server-storage.md).

## Acessar o Servidor R no cluster

Depois de criar um cluster com o Servidor R, você poderá se conectar ao console do R no nó de borda do cluster por meio de SSH/PuTTY. Também é possível se conectar por meio de um navegador se você optar por instalar o IDE opcional do RStudio Server no nó de borda. Para saber mais sobre como instalar o RStudio Server, consulte [Instalando o RStudio Server em clusters HDInsight](hdinsight-hadoop-r-server-install-r-studio.md).

## Desenvolver e executar scripts R

Os scripts R que você criar e executar podem usar qualquer um dos mais de 8000 pacotes de software livre do R, além das rotinas paralelizadas e distribuídas na biblioteca do ScaleR. Em geral, o script que é executado no Servidor R no nó de borda é executado no interpretador de R nesse nó. A exceção é que essas etapas chamam uma função do ScaleR com um contexto de computação que é definido para o Map Reduze do Hadoop (RxHadoopMR) ou Spark (RxSpark).

Nesses casos, a função é executada de maneira distribuída por esses nós de dados (tarefa) do cluster que são associados aos dados referenciados. Para saber mais sobre as diferentes opções de contexto de computação, confira [Opções de contexto de computação para o Servidor R no HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md).

## Operacionalizar um modelo

Quando sua modelagem de dados estiver concluída, você poderá operacionalizar o modelo para fazer previsões sobre novos dados, no Azure e no local. Esse processo é conhecido como pontuação. Veja alguns exemplos.

### Pontuação no HDInsight

Para pontuar no HDInsight, escreva uma função R que chame seu modelo para fazer previsões para um novo arquivo de dados que você carregou na conta de armazenamento. Em seguida, salve as previsões na conta de armazenamento. Você pode executar a rotina sob demanda no nó de borda do seu cluster ou usando um trabalho agendado.

### Pontuação no Aprendizado de Máquina do Azure

Para pontuar usando um serviço Web do Aprendizado de Máquina do Azure, use o [pacote R do Aprendizado de Máquina do Azure de software livre](http://www.inside-r.org/blogs/2015/11/18/enhancements-azureml-package-connect-r-azureml-studio) para [publicar seu modelo como um serviço Web do Azure](http://www.r-bloggers.com/deploying-a-car-price-model-using-r-and-azureml/). Em seguida, use os recursos no Aprendizado de Máquina para criar uma interface de usuário para o serviço Web e chame o serviço Web conforme necessidade para pontuação.

Se você escolher essa opção, será preciso converter qualquer objeto de modelo do ScaleR em objeto de modelo de software livre equivalente para uso com o serviço Web. Isso pode ser feito usando as funções de coerção do ScaleR, como `as.randomForest()` para modelos baseados em união.

### Pontuação local

Para pontuar no local após criar o modelo, você pode serializar o modelo em R, baixá-lo, desserializá-lo e usá-lo para pontuação de novos dados. Você pode pontuar novos dados usando a abordagem descrita anteriormente em [Pontuação no HDInsight](#scoring-in-hdinsight) ou usando [DeployR](https://deployr.revolutionanalytics.com/).

## Manter o cluster

### Instalar e manter pacotes R

A maioria dos pacotes R que você usa será exigida no nó de borda, uma vez que a maioria dos scripts R será executada nele. Para instalar pacotes R adicionais no nó de borda, você pode usar o método `install.packages()` usual em R.

Na maioria dos casos, você não precisa instalar pacotes R adicionais em nós de dados se estiver usando apenas rotinas da biblioteca do ScaleR pelo cluster. No entanto, talvez você precise de pacotes adicionais para permitir o uso da execução de **rxExec** ou de **RxDataStep** em nós de dados.

Nesses casos, os pacotes adicionais devem ser especificados por meio do uso de uma ação de script depois que você cria o cluster. Para saber mais, confira [Criando um cluster HDInsight com o Servidor R](hdinsight-hadoop-r-server-get-started.md).

### Alterar as configurações de memória do Hadoop Map Reduce

Um cluster pode ser modificado para alterar a quantidade de memória disponível para o Servidor R quando ele está executando um trabalho do Map Reduce. Para modificar um cluster, use a interface de usuário do Apache Ambari que está disponível por meio da folha Portal do Azure do seu cluster. Para obter instruções sobre como acessar a interface de usuário do Ambari para seu cluster, confira [Gerenciar clusters HDInsight usando a interface de usuário do Ambari Web](hdinsight-hadoop-manage-ambari.md).

Também é possível alterar a quantidade de memória disponível para o Servidor R usando opções do Hadoop na chamada para **RxHadoopMR**, como se segue:

	hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### Ajustar escala do cluster

Um cluster existente pode ser escalado e reduzido verticalmente por meio do portal. Ao ajustar a escala, você pode ganhar a capacidade adicional que pode precisar para tarefas de processamento mais amplo, ou pode escalar um cluster de volta quando ele estiver ocioso. Para obter instruções sobre como escalar um cluster, confira [Gerenciar clusters HDInsight](hdinsight-administer-use-portal-linux.md).

### Manter o sistema

A manutenção é executada nas VMs do Linux subjacentes em um cluster HDInsight fora do horário comercial para aplicar patches do sistema operacional e outras atualizações. Normalmente, a manutenção é feita às 3:30 (com base na hora local da VM) todas as segundas e quintas-feiras. As atualizações são executadas de forma que não afetem mais de um quarto do cluster por vez.

Como os nós de cabeçalho são redundantes e nem todos os nós de dados são afetados, todos os trabalhos que estiverem em execução durante esse tempo podem ficar lentos. No entanto, todos eles devem continuar em execução até a conclusão. Todos os dados locais ou de software personalizados que você tem são preservados nesses eventos de manutenção, a menos que uma falha catastrófica ocorra e exija uma recriação do cluster.

## Saiba mais sobre opções de IDE para o Servidor R em um cluster HDInsight

O nó de borda do Linux de um cluster HDInsight Premium é a zona de aterrissagem para análises baseadas em R. Depois de se conectar ao cluster, você poderá iniciar a interface do console para o Servidor R, digite **R** no prompt de comando do Linux. O uso da interface do console será aprimorado se você executar um editor de texto para o desenvolvimento de scripts R em outra janela, e recortar e colar seções do seu script no console R, conforme necessário.

Uma ferramenta mais sofisticada para o desenvolvimento do script R é o IDE baseado em R para uso no desktop, como as [Ferramentas R para Visual Studio](https://www.visualstudio.com/pt-BR/features/rtvs-vs.aspx) (RTVS) recentemente anunciadas pela Microsoft. Essa é uma família de ferramentas de desktop e servidor do [RStudio](https://www.rstudio.com/products/rstudio-server/). Você também pode usar o [StatET](http://www.walware.de/goto/statet) baseado no Eclipse da Walware.

Outra opção é instalar um IDE no próprio nó de borda do Linux. Uma escolha popular é o [RStudio Server](https://www.rstudio.com/products/rstudio-server/), que fornece um IDE baseado em navegador para uso por clientes remotos. Instalar o RStudio Server no nó de borda de um cluster HDInsight Premium proporciona uma experiência completa de IDE para o desenvolvimento e a execução de scripts R com o Servidor R no cluster. Ele pode ser consideravelmente mais produtivo do que o console R. Se desejar usar o RStudio Server, confira [Instalando o RStudio em Server em clusters HDInsight](hdinsight-hadoop-r-server-install-r-studio.md).

## Saiba mais sobre preços

As taxas associadas a um cluster HDInsight Premium com servidor R são estruturadas de forma semelhante às taxas para os clusters HDInsight padrão. Elas são baseadas no dimensionamento das VMs subjacentes por nome, data e nós de borda com adição de uma elevação de hora por núcleo para Premium. Para saber mais sobre os preços do HDInsight Premium, incluindo os preços durante a Preview Pública e a disponibilidade de uma avaliação gratuita de 30 dias, confira o [Preço do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## Próximas etapas

Siga os links abaixo para ler mais sobre como usar o Servidor R com clusters HDInsight.

- [Introdução ao uso do Servidor R no HDInsight](hdinsight-hadoop-r-server-get-started.md)

- [Adicionar RStudio Server ao HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)

- [Opções de contexto de computação para o Servidor R no HDInsight (preview)](hdinsight-hadoop-r-server-compute-contexts.md)

- [Opções de Armazenamento do Azure para o Servidor R no HDInsight Premium](hdinsight-hadoop-r-server-storage.md)

<!---HONumber=AcomDC_0622_2016-->