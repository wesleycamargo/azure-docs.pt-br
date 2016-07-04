<properties
pageTitle="Usar o Armazenamento do Azure Data Lake com o Apache Storm no Azure HDInsight"
description="Aprenda a gravar dados no Armazenamento do Azure Data Lake a partir de uma topologia do Apache Storm no HDInsight. Este documento e o exemplo associado demonstram como o componente HdfsBolt pode ser usado para gravar dados no Armazenamento do Data Lake."
services="hdinsight"
documentationCenter="na"
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="06/17/2016"
ms.author="larryfr"/>

#Usar o Armazenamento do Azure Data Lake com o Apache Storm com HDInsight

O Armazenamento do Azure Data Lake é um serviço de armazenamento de nuvem compatível com HDFS que fornece confiabilidade, disponibilidade, durabilidade e alta taxa de transferência de dados. Neste documento, você aprenderá como usar uma topologia Storm baseada Java para gravar dados no Armazenamento do Azure Data Lake usando o componente [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html), que é fornecido como parte do Apache Storm.

> [AZURE.IMPORTANT] A topologia de exemplo usada neste documento depende dos componentes incluídos no Storm em clusters HDInsight e pode exigir modificações para funcionar com o Armazenamento do Azure Data Lake quando usado com outros clusters do Apache Storm.

##Pré-requisitos

* [Java JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou superior
* [Maven 3.x](https://maven.apache.org/download.cgi)
* Uma assinatura do Azure
* Um Storm no cluster HDInsight versão 3.2. Para criar um novo Storm no cluster HDInsight, use as etapas descritas no documento [Usar o HDInsight com o Repositório Data Lake usando o Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). As etapas neste documento irão orientá-lo na criação de um novo cluster HDInsight e Armazenamento do Azure Data Lake.  

    > [AZURE.IMPORTANT] Ao criar o cluster HDInsight, é necessário selecionar __Storm__ como o tipo de cluster e __3.2__ como a versão. O SO pode ser Windows ou Linux.

###Configurar variáveis de ambiente

As seguintes variáveis de ambiente podem ser definidas quando você instala o Java e o JDK em sua estação de trabalho de desenvolvimento. No entanto, você deve verificar se elas existem e se contêm os valores corretos para o seu sistema.

* __JAVA\_HOME__ - deve apontar para o diretório onde o Java runtime environment (JRE) está instalado. Por exemplo, em uma distribuição Unix ou Linux, ele deve ter um valor semelhante a `/usr/lib/jvm/java-7-oracle`. No Windows, ele teria um valor semelhante a `c:\Program Files (x86)\Java\jre1.7`.

* __PATH__ - deve conter os seguintes caminhos:

    * __JAVA\_HOME__ (ou o caminho equivalente)
    
    * __JAVA\_HOME\\bin__ (ou o caminho equivalente)
    
    * O diretório onde o Maven está instalado

##Implementação da topologia

O exemplo usado neste documento é escrito em Java e usa os seguintes componentes:

* __TickSpout__: gera os dados usados por outros componentes na topologia.

* __PartialCount__: conta os eventos gerados por TickSpout.

* __FinalCount__: agrega os dados de contagem de PartialCount.

* __ADLStoreBolt__: grava os dados no Repositório Azure Data Lake usando o componente [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html).

O projeto que contém essa topologia está disponível como um download em [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

###Noções básicas sobre ADLStoreBolt

O ADLStoreBolt é o nome usado para a instância de HdfsBolt na topologia que grava no Azure Data Lake. Isso não é uma versão especial do HdfsBolt criada pela Microsoft. No entanto, depende dos valores de configuração do site principal, além dos componentes do Hadoop incluídos com o Azure HDInsight para a comunicação com o Data Lake.

Especificamente, quando você cria um cluster HDInsight, pode associá-lo a um Armazenamento do Azure Data Lake. Isso grava as entradas no site principal para o Armazenamento do Data Lake selecionado, que são usadas por componentes, como, cliente do hadoop e hdfs do hadoop para permitir a comunicação com o Armazenamento do Data Lake.

> [AZURE.NOTE] A Microsoft contribuiu com o código para os projetos de Storm e Apache Hadoop que permite a comunicação com o Armazenamento de Blobs do Azure e do Azure Data Lake, mas essa funcionalidade não pode ser incluída por padrão nas outras distribuições de Hadoop e Storm.

A configuração para HdfsBolt na topologia é a seguinte:

    // 1. Create sync and rotation policies to control when data is synched
    //    (written) to the file system and when to roll over into a new file.
    SyncPolicy syncPolicy = new CountSyncPolicy(1000);
    FileRotationPolicy rotationPolicy = new FileSizeRotationPolicy(0.5f, Units.KB);
    // 2. Set the format. In this case, comma delimited
    RecordFormat recordFormat = new DelimitedRecordFormat().withFieldDelimiter(",");
    // 3. Set the directory name. In this case, '/stormdata/'
    FileNameFormat fileNameFormat = new DefaultFileNameFormat().withPath("/stormdata/");
    // 4. Create the bolt using the previously created settings,
    //    and also tell it the base URL to your Data Lake Store.
    // NOTE! Replace 'MYDATALAKE' below with the name of your data lake store.
    HdfsBolt adlsBolt = new HdfsBolt()
		.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")
      	.withRecordFormat(recordFormat)
      	.withFileNameFormat(fileNameFormat)
      	.withRotationPolicy(rotationPolicy)
      	.withSyncPolicy(syncPolicy);
    // 4. Give it a name and wire it up to the bolt it accepts data
    //    from. NOTE: The name used here is also used as part of the
    //    file name for the files written to Data Lake Store.
    builder.setBolt("ADLStoreBolt", adlsBolt, 1)
      .globalGrouping("finalcount");
      
Se você estiver familiarizado com o uso de HdfsBolt, notará que se trata de todas as configurações padrão, exceto para a URL. A URL fornece o caminho para a raiz do seu Armazenamento do Azure Data Lake.

Como a gravação no Armazenamento do Data Lake usa o HdfsBolt e é apenas uma alteração na URL, você deve ser capaz de pegar qualquer topologia existente que grava no HDFS ou no WASB usando o HdfsBolt e alterá-la facilmente para usar o Armazenamento do Azure Data Lake.

##Compilar e criar o pacote da topologia

1. Baixe o projeto de exemplo em [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) em seu ambiente de desenvolvimento.

2. Abra o arquivo `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` em um editor e encontre a linha que contém `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")`. Altere __MYDATALAKE__ para o nome do Repositório Azure Data Lake usado durante a criação de seu servidor HDInsight.

3. Em um prompt de comando, terminal ou sessão do shell, altere os diretórios para a raiz do projeto baixado e execute os seguintes comandos para compilar e criar o pacote da topologia.

        mvn compile
        mvn package
    
    Após a conclusão do build e do empacotamento, haverá um novo diretório chamado `target`, que contém um arquivo denominado `StormToDataLakeStore-1.0-SNAPSHOT.jar`. Isto contém a topologia compilada.

##Implantar e executar no HDInsight baseado em Linux

Se você criou um Storm baseado em Linux no cluster HDInsight, use as etapas a seguir para implantar e executar a topologia.

1. Use o seguinte comando para copiar a topologia para o cluster HDInsight. Substitua __USER__ pelo nome de usuário SSH usado ao criar o cluster. Substitua o __CLUSTERNAME__ pelo nome do cluster.

        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
    
    Quando solicitado, digite a senha usada ao criar o usuário SSH para o cluster. Se você usou uma chave pública em vez de uma senha, talvez precise usar o parâmetro `-i` para especificar o caminho para a chave privada correspondente.
    
    > [AZURE.NOTE] Caso esteja usando um cliente Windows para o desenvolvimento, talvez não haja um comando `scp`. Se estiver, você poderá usar o `pscp`, que está disponível em [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

2. Quando o carregamento for concluído, use o seguinte para se conectar ao cluster HDInsight usando o SSH. Substitua __USER__ pelo nome de usuário SSH usado ao criar o cluster. Substitua o __CLUSTERNAME__ pelo nome do cluster.

        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net

    Quando solicitado, digite a senha usada ao criar o usuário SSH para o cluster. Se você usou uma chave pública em vez de uma senha, talvez precise usar o parâmetro `-i` para especificar o caminho para a chave privada correspondente.
    
    > [AZURE.NOTE] Se estiver usando um cliente Windows para o desenvolvimento, siga as informações em [Connect to Linux-based HDInsight with SSH from Windows](hdinsight-hadoop-linux-use-ssh-windows.md) (Conectar ao HDInsight baseado em Linux com o SSH do Windows) para obter informações sobre como usar o cliente PuTTY para se conectar ao cluster.
    
3. Uma vez conectado, use o seguinte para iniciar a topologia:

        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar com.microsoft.example.StormToDataLakeStore datalakewriter
    
    Isso iniciará a topologia com o nome amigável `datalakewriter`.

##Implantar e executar no HDInsight baseado em Windows

1. Abra um navegador da Web e vá para HTTPS://CLUSTERNAME.azurehdinsight.net, em que __CLUSTERNAME__ é o nome de seu cluster HDInsight. Quando solicitado, forneça o nome de usuário do administrador (`admin`) e a senha usada para essa conta quando o cluster foi criado.

2. No Painel do Storm, escolha __Procurar__ na lista suspensa __Arquivo Jar__ e, em seguida, escolha arquivo StormToDataLakeStore-1.0-SNAPSHOT.jar no diretório `target`. Use os seguintes valores para as outras entradas no formulário:

    * Nome da Classe: com.microsoft.example.StormToDataLakeStore
    * Parâmetros Adicionais: datalakewriter
    
    ![imagem do painel do storm](./media/hdinsight-storm-write-data-lake-store/submit.png)

3. Escolha o botão __Enviar__ para carregar e iniciar a topologia. O campo de resultado abaixo do botão __Enviar__ deverá exibir informações semelhantes às seguintes quando a topologia for iniciada:

        Process exit code: 0
        Currently running topologies:
        Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
        -------------------------------------------------------------------
        datalakewriter       ACTIVE     68         8            10        

##Exibir dados de saída

Há várias maneiras de exibir os dados. Nesta seção, usamos o Portal do Azure e o comando `hdfs` para exibir os dados.

> [AZURE.NOTE] Você deve permitir que as topologias sejam executadas por vários minutos antes de verificar os dados de saída para que os dados sejam sincronizados em vários arquivos no Armazenamento do Azure Data Lake.

* __No [Portal do Azure](https://portal.azure.com)__: no portal, escolha o Repositório Azure Data Lake usado com o HDInsight.

    > [AZURE.NOTE] Se você não fixou o Repositório Data Lake no painel do Portal do Azure, será possível encontrá-lo escolhendo __Procurar__ na parte inferior da lista à esquerda, em seguida, __Repositório Data Lake__ e, por fim, escolhendo o repositório.
    
    Nos ícones localizados na parte superior do Repositório Data Lake, escolha __Explorador de Dados__.
    
    ![ícone do gerenciador de dados](./media/hdinsight-storm-write-data-lake-store/dataexplorer.png)
    
    Em seguida, escolha a pasta __stormdata__. Uma lista de arquivos de texto deverá ser exibida.
    
    ![arquivos de texto](./media/hdinsight-storm-write-data-lake-store/stormoutput.png)
    
    Escolha um dos arquivos para exibir seu conteúdo.

* __No cluster__: se você se conectou ao cluster HDInsight usando o SSH (cluster do Linux) ou a Área de Trabalho Remota (cluster do Windows), será possível usar o seguinte para exibir os dados. Substitua __DATALAKE__ pelo nome do Repositório Data Lake

        hdfs dfs -cat adl://DATALAKE.azuredatalakestore.net/stormdata/*.txt

    Isso irá concatenar os arquivos de texto armazenados no diretório e exibir informações semelhantes às seguintes:
    
        406000000
        407000000
        408000000
        409000000
        410000000
        411000000
        412000000
        413000000
        414000000
        415000000
        
##Parar a topologia

As topologias do Storm serão executadas até ser interrompidas ou o cluster ser excluído. Para interromper as topologias, use as informações a seguir.

__Para o HDInsight baseado em Linux__:

Em uma sessão SSH para o cluster, use o seguinte comando:

    storm kill datalakewriter

__Para o HDInsight baseado em Windows__:

1. No Painel do Storm (https://CLUSTERNAME.azurehdinsight.net,), escolha o link __Interface do usuário do Storm__ na parte superior da página.

2. Depois que a interface do usuário do Storm for carregada, escolha o link __datalakewriter__.

    ![link para o datalakewriter](./media/hdinsight-storm-write-data-lake-store/selecttopology.png)

3. Na seção __Ações da Topologia__, escolha __Encerrar__ e OK na caixa de diálogo exibida.

    ![ações da topologia](./media/hdinsight-storm-write-data-lake-store/topologyactions.png)

## Excluir o cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##Próximas etapas

Agora que você aprendeu a usar o Storm para gravar no Repositório Azure Data Lake, descubra outros [exemplos do Storm para HDInsight](hdinsight-storm-example-topology.md).

<!---HONumber=AcomDC_0622_2016-->