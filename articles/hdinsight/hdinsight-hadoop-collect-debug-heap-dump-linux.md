<properties
	pageTitle="Habilitar despejos de heap para serviços do Hadoop no HDInsight | Microsoft Azure"
	description="Habilite despejos de heap para serviços do Hadoop por meio de clusters do HDInsight baseados em Linux para depuração e análise."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="larryfr"/>


#Habilitar despejos heap para serviços do Hadoop no HDInsight baseado em Linux (visualização)

[AZURE.INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Despejos de heap contêm um instantâneo da memória do aplicativo, incluindo os valores das variáveis no momento em que o despejo foi criado. Portanto, eles são muito úteis para diagnosticar problemas que ocorrem no tempo de execução.

> [AZURE.NOTE] As informações neste artigo aplicam-se apenas ao HDInsight baseado em Linux. Para obter informações sobre o HDInsight baseado no Windows, consulte [Habilitar despejos de heap para serviços do Hadoop no HDInsight baseado no Windows](hdinsight-hadoop-collect-debug-heap-dumps.md)

## <a name="whichServices"></a>Serviços

Você pode habilitar o despejo de heap para os seguintes serviços:

*  **hcatalog** - tempelton
*  **hive** - hiveserver2, metastore, derbyserver
*  **mapreduce** - jobhistoryserver
*  **yarn** - resourcemanager, nodemanager, timelineserver
*  **hdfs** - datanode, secondarynamenode, namenode

Você também pode habilitar despejos de heap para os processos de mapeamento e redução executados pelo HDInsight.

## <a name="configuration"></a>Compreendendo a configuração do despejo de heap

Despejos de heap são habilitados transmitindo opções (às vezes conhecidas como opts, ou parâmetros) para a JVM quando um serviço é iniciado. Para a maioria dos serviços do Hadoop, isso pode ser feito modificando o script de shell usado para iniciar o serviço.

Em cada script, há uma exportação para ***\_OPTS**, que contém as opções passadas para a JVM. Por exemplo, no script **hadoop-env.sh**, a linha que começa com `export HADOOP_NAMENODE_OPTS=` contém as opções para o serviço NameNode.

Processos de mapeamento e redução são ligeiramente diferentes, uma vez que são processos filho do serviço MapReduce. Cada processo de mapeamento ou redução é executado em um contêiner filho, e há duas entradas que contêm as opções de JVM para eles. Contidos em **mapred-site.xml**:

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [AZURE.NOTE] Nós recomendamos usar o Ambari para modificar os scripts e configurações de mapred-site.xml, pois ele processará a réplica de alterações nos nós do cluster. Consulte a seção [Usando o Ambari](#using-ambari) para ver as etapas específicas.

###Habilitar despejos de heap

A seguinte opção habilita os despejos de heap quando ocorre um OutOfMemoryError:

    -XX:+HeapDumpOnOutOfMemoryError

O **+** indica que a opção está habilitada. Por padrão, ela fica desabilitada.

> [AZURE.WARNING] Despejos de heap não são habilitados para serviços do Hadoop no HDInsight por padrão, pois os arquivos de despejo podem ser grandes. Se você habilitá-los para solução de problemas, lembre-se de desabilitá-los após ter reproduzido o problema e coletado os arquivos de despejo.

###Local do despejo

O local padrão para o arquivo de despejo é o diretório de trabalho atual. Você pode controlar onde o arquivo é armazenado usando a seguinte opção:

    -XX:HeapDumpPath=/path

Por exemplo, usar `-XX:HeapDumpPath=/tmp` fará com que os despejos sejam armazenados no diretório /tmp.

###Scripts

Você também pode disparar um script quando um **OutOfMemoryError** ocorrer. Por exemplo, disparar uma notificação para que você saiba que o erro ocorreu. Isso é controlado usando a seguinte opção:

    -XX:OnOutOfMemoryError=/path/to/script

> [AZURE.NOTE] Como o Hadoop é um sistema distribuído, qualquer script usado deve ser colocado em todos os nós no cluster em que o serviço é executado.
>
> O script deve também estar em um local que seja acessível pela conta em que o serviço é executado e deve fornecer permissões de execução. Por exemplo, você pode optar por armazenar scripts em `/usr/local/bin` e usar `chmod go+rx /usr/local/bin/filename.sh` para conceder permissões de leitura e execução.

##Usando o Ambari

Para modificar a configuração de um serviço, use as seguintes etapas:

1. Abra a UI da Web do Ambari para seu cluster. A URL será https://YOURCLUSTERNAME.azurehdinsight.net.

    Quando solicitado, autentique no site usando o nome da conta HTTP (padrão: administrador) e a senha de seu cluster.

    > [AZURE.NOTE] O Ambari poderá solicitar o nome de usuário e senha mais uma vez. Nesse caso, basta reinserir o mesmo nome de conta e senha

2. Usando a lista à esquerda, selecione a área de serviço que você deseja modificar. Por exemplo, **HDFS**. Na área central, selecione a guia **Configurações**.

    ![Imagem do Ambari Web com a guia de Configurações de HDFS selecionada](./media/hdinsight-hadoop-heap-dump-linux/serviceconfig.png)

3. Usando a entrada **Filtrar...**, digite **opts**. Isso filtra a lista de itens de configuração apenas para os itens que contêm esse texto, e é uma maneira rápida de localizar o script de shell ou o **modelo** que pode ser usado para definir essas opções.

    ![Lista filtrada](./media/hdinsight-hadoop-heap-dump-linux/filter.png)

4. Encontre a entrada ***\_OPTS** do serviço para o qual você deseja habilitar os despejos de heap e adicione as opções que deseja habilitar. Na imagem a seguir, adicionei `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` à entrada **HADOOP\_NAMENODE\_OPTS**:

    ![HADOOP\_NAMENODE\_OPTS com -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/](./media/hdinsight-hadoop-heap-dump-linux/opts.png)

	> [AZURE.NOTE] Ao habilitar despejos de heap para o processo filho de mapeamento ou redução, você procurará os campos rotulados **mapreduce.admin.map.child.java.opts** e **mapreduce.admin.reduce.child.java.opts**.

    Use o botão **Salvar** para salvar as alterações. Você pode inserir uma nota curta que descreve as alterações.

5. Após as alterações terem sido aplicadas, o ícone **Reinicialização necessária** aparecerá ao lado de um ou mais serviços.

    ![botão de reinicialização necessária e botão de reinicialização](./media/hdinsight-hadoop-heap-dump-linux/restartrequiredicon.png)

6. Selecione cada serviço que precisa ser reiniciado e use o botão **Ações de Serviço** para **Ativar o Modo de Manutenção**. Isso impede que alertas sejam gerados pelo serviço quando você reiniciá-lo.

    ![Ativar o menu do modo de manutenção](./media/hdinsight-hadoop-heap-dump-linux/maintenancemode.png)

7. Após ter habilitado o modo de manutenção, use o botão **Reiniciar** para o serviço **Reiniciar Todos os Afetados**

    ![Entrada Reiniciar todos os afetados](./media/hdinsight-hadoop-heap-dump-linux/restartbutton.png)

    > [AZURE.NOTE] as entradas do botão **Reiniciar** podem ser diferentes para outros serviços.

8. Após os serviços serem reiniciados, use o botão **Ações de Serviço** para **Desativar o Modo de Manutenção**. Use este Ambari para retomar o monitoramento dos alertas do serviço.

<!---HONumber=AcomDC_0713_2016-->