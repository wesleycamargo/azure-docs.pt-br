---
title: Usar os fluxos de trabalho de Sqoop do Hadoop no HDInsight baseado em Linux | Microsoft Docs
description: Usar o Oozie do Hadoop no HDInsight baseado em Linux. Saiba como definir um fluxo de trabalho do Oozie e enviar um trabalho do Oozie.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: fc79b8017f2184091f2473a0ff9cdfbd0a4cbdf8
ms.openlocfilehash: 0d4da9d09bc5fb5cd08aa97dede506c4cac15dae


---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-on-linux-based-hdinsight"></a>Usar o Oozie com Hadoop para definir e executar um fluxo de trabalho no HDInsight baseado em Linux
[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Aprenda a usar o Apache Oozie para definir um fluxo de trabalho que usa o Hive e Sqoop e, em seguida, executar o fluxo de trabalho em um cluster HDInsight baseado em Linux.

O Apache Oozie é um sistema de fluxo de trabalho/coordenação que gerencia trabalhos do Hadoop. Ele é integrado com a pilha do Hadoop e oferece suporte a trabalhos do Hadoop para o Apache MapReduce, Apache Pig, Apache Hive e Apache Sqoop. Ele também pode ser usado para agendar trabalhos específicos para um sistema, como programas Java ou scripts de shell

> [!NOTE]
> Outra opção para definir fluxos de trabalho com o HDInsight é uma Azure Data Factory. Para conhecer mais o Azure Data Factory, confira [Usar o Pig e o Hive com o Data Factory][azure-data-factory-pig-hive].

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**: consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **CLI do Azure**: consulte [Instalar e Configurar a CLI do Azure](../xplat-cli-install.md)

    > [!IMPORTANT]
    > O suporte do Azure PowerShell para gerenciar os recursos do HDInsight usando o Gerenciador de Serviços do Azure está **preterido** e será removido em 1º de janeiro de 2017. As etapas neste documento usam os novos cmdlets do HDInsight que funcionam com o Azure Resource Manager.
    >
    > Siga as etapas em [Instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para instalar a versão mais recente do Azure PowerShell. Se você tiver scripts que precisam ser modificados para usar os novos cmdlets que funcionam com o Azure Resource Manager, confira [Migrando para as ferramentas de desenvolvimento baseadas no Azure Resource Manager dos clusters de HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md) para obter mais informações.

* **Um cluster hdinsight**: consulte [Introdução ao HDInsight no Linux](hdinsight-hadoop-linux-tutorial-get-started.md)
* **Um Banco de Dados SQL do Azure**: será criado usando as etapas neste documento

## <a name="example-workflow"></a>Fluxo de trabalho de exemplo
O fluxo de trabalho que você vai implementar seguindo as instruções neste documento contém duas ações. Ações são definições de tarefas, como Sqoop, Hive, MapReduce ou outro processo de execução:

![Diagrama de fluxo de trabalho][img-workflow-diagram]

1. Uma ação do Hive executa um script HiveQL para extrair os registros de **hivesampletable** incluídos com o HDInsight. Cada linha de dados descreve uma visita de um dispositivo móvel específico. O formato de registro é semelhante ao seguinte:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    O script do Hive usado neste documento conta o total de visitas para cada plataforma (como Android ou iPhone) e armazena as contagens em uma nova tabela Hive.

    Para obter mais informações sobre o Hive, consulte [Usar o Hive com o HDInsight][hdinsight-use-hive].
2. Uma ação do Sqoop exporta o conteúdo da nova tabela Hive para uma tabela em um Banco de Dados SQL do Azure. Para saber mais sobre o Sqoop, confira [Usar o Hadoop Sqoop com o HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Para obter as versões do Oozie com suporte em clusters HDInsight, confira [Novidades nas versões de clusters fornecidas pelo HDInsight][hdinsight-versions].

## <a name="create-the-working-directory"></a>Criar o diretório de trabalho
O Oozie espera que os recursos necessários para um trabalho sejam armazenados no mesmo diretório. Este exemplo usa **wasbs:///tutorials/useoozie**. Use o comando a seguir para criar esse diretório e o diretório de dados que armazenará a nova tabela do Hive criada por este fluxo de trabalho:

    hdfs dfs -mkdir -p /tutorials/useoozie/data

> [!NOTE]
> O diretório `-p` fez todos os diretórios no caminho serem criados se eles ainda não existiam. O diretório de **dados** será usado para armazenar dados usados pelo script **useooziewf.hql**.

Além disso, execute o seguinte comando, que garante que o Oozie possa representar a conta de usuário durante a execução de trabalhos de Hive e Sqoop. Substitua **NOME DE USUÁRIO** pelo seu nome de logon:

    sudo adduser USERNAME users

Se você receber um erro indicando que o usuário já é um membro de usuários, você pode simplesmente ignorá-lo.

## <a name="add-a-database-driver"></a>Adicionar um driver de banco de dados
Como esse fluxo de trabalho usa Sqoop para exportar dados para o banco de dados SQL, você deve fornecer uma cópia do driver JDBC usado para se comunicar com o Banco de Dados SQL. Use o seguinte comando para copiá-lo para o diretório de trabalho:

    hdfs dfs -copyFromLocal /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/

Se seu fluxo de trabalho tiver usado outros recursos, como um jar que contém um aplicativo MapReduce, você precisará adicioná-los também.

## <a name="define-the-hive-query"></a>Definir a consulta de Hive
Use as seguintes etapas para criar um script HiveQL que define uma consulta, que será usada em um fluxo de trabalho Oozie mais adiante neste documento.

1. Utilize SSH para se conectar ao cluster do HDInsight para Linux:

   * **Clientes Linux, Unix ou OS X**: consultem [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
   * **Clientes Windows**: consulte [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
2. Use o comando a seguir para criar um novo aplicativo Web:

        nano useooziewf.hql
3. Quando o editor for aberto, use o seguinte como conteúdo do arquivo:

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
        FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;

    Duas variáveis são usadas no script:

   * **${hiveTableName}**: conterá o nome da tabela a ser criada
   * **${hiveDataFolder}**: conterá o local para armazenar os arquivos de dados para a tabela

     O arquivo de definição do fluxo de trabalho (workflow.xml neste tutorial) irá passar esses valores para o script HiveQL em tempo de execução.
4. Pressione Ctrl-X para sair do editor. Quando solicitado, selecione **Y** para salvar o arquivo e, em seguida, use **Enter** para usar o nome de arquivo **useooziewf.hql**.
5. Use os seguintes comandos para copiar **useooziewf.hql** para **wasbs:///tutorials/useoozie/useooziewf.hql**:

        hdfs dfs -copyFromLocal useooziewf.hql /tutorials/useoozie/useooziewf.hql

    Esses comandos armazenam o arquivo **useooziewf.hql** na conta de Armazenamento do Azure associada a esse cluster, que preserva o arquivo mesmo se o cluster for excluído. Isso permite que você economize dinheiro, excluindo os clusters quando eles não estiverem em uso, mantendo seus trabalhos e fluxos de trabalho.

## <a name="define-the-workflow"></a>Definir o fluxo de trabalho
As definições de fluxos de trabalho do Oozie são escritas em hPDL (uma Linguagem de Definição de Processo XML). Use as etapas a seguir para definir o fluxo de trabalho:

1. Use a instrução a seguir para criar e editar um novo arquivo:

        nano workflow.xml
2. Quando o editor nano abrir, digite o seguinte como o conteúdo do arquivo:

        <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
            <start to = "RunHiveScript"/>
            <action name="RunHiveScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScript}</script>
                <param>hiveTableName=${hiveTableName}</param>
                <param>hiveDataFolder=${hiveDataFolder}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
            </action>
            <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.compress.map.output</name>
                    <value>true</value>
                </property>
                </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveDataFolder}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\t"</arg>
                <archive>sqljdbc41.jar</archive>
                </sqoop>
            <ok to="end"/>
            <error to="fail"/>
            </action>
            <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
            </kill>
            <end name="end"/>
        </workflow-app>

    Existem duas ações definidas no fluxo de trabalho:

   * **RunHiveScript**: essa é a ação inicial, e executa o script Hive **useooziewf.hql**
   * **RunSqoopExport**: exporta os dados criados por meio do script Hive para o banco de dados SQL usando o Sqoop. Isso será executado apenas se a ação **RunHiveScript** tiver sido bem-sucedida.

     > [!NOTE]
     > Para saber mais sobre o fluxo de trabalho do Oozie e sobre como usar ações de fluxo de trabalho, confira a [Documentação do Apache Oozie 4.0][apache-oozie-400] (para o HDInsight versão 3.0) ou a [Documentação do Oozie Apache 3.3.2][apache-oozie-332] (para o HDInsight versão 2.1).
     >
     >

     Observe que o fluxo de trabalho tem várias entradas, como `${jobTracker}`, que serão substituídas por valores usados na definição de tarefa neste documento.

     Observe também a entrada `<archive>sqljdbc4.jar</arcive>` na seção Sqoop. Isso instrui o Oozie a disponibilizar esse arquivo para Sqoop quando essa ação é executada.
3. Use Ctrl-X e, em seguida, **Y** e **Enter** para salvar o arquivo.
4. Use o comando a seguir para copiar o arquivo **workflow.xml** para **wasbs:///tutorials/useoozie/workflow.xml**:

        hdfs dfs -copyFromLocal workflow.xml /tutorials/useoozie/workflow.xml

## <a name="create-the-database"></a>Criar o banco de dados
Siga as etapas no documento [Create a SQL Database](../sql-database/sql-database-get-started.md) (Criar um banco de dados SQL) para criar um novo banco de dados. Ao criar o banco de dados, use **oozietest** como o nome do banco de dados. Anote o nome usado para o servidor de banco de dados, pois ele será necessário na próxima seção.

### <a name="create-the-table"></a>Criar a tabela.
> [!NOTE]
> Há várias maneiras para se conectar ao Banco de Dados SQL para criar uma tabela. As seguintes etapas usam [FreeTDS](http://www.freetds.org/) do cluster HDInsight.
>
>

1. Use o seguinte comando para instalar o FreeTDS no cluster do HDInsight:

        sudo apt-get --assume-yes install freetds-dev freetds-bin
2. Uma vez que o FreeTDS tiver sido instalado, use o seguinte comando para conectar-se ao Banco de Dados SQL Server criado anteriormente:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest

    Você receberá saídas semelhantes ao seguinte:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>
3. Ao prompt `1>` , insira o seguinte:

        CREATE TABLE [dbo].[mobiledata](
        [deviceplatform] [nvarchar](50),
        [count] [bigint])
        GO
        CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
        GO

    Quando a instrução `GO` for inserida, as instruções anteriores serão avaliadas. Isso criará uma nova tabela denominada **mobiledata** que será gravada pelo Sqoop.

    Use o seguinte para verificar se a tabela foi criada:

        SELECT * FROM information_schema.tables
        GO

    Você deverá ver uma saída semelhante ao seguinte:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo     mobiledata      BASE TABLE
4. Enter `exit` at the `1>` .

## <a name="create-the-job-definition"></a>Criar a definição de trabalho
A definição de trabalho descreve onde encontrar o workflow.xml, bem como outros arquivos usados pelo fluxo de trabalho (como useooziewf.hql). Isso também define os valores para as propriedades usadas no fluxo de trabalho e arquivos associados.

1. Use o seguinte comando para obter o endereço completo do WASB para o armazenamento padrão. Isto será usado no arquivo de configuração em breve:

        sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml

    Isso deve retornar informações semelhantes a estas:

        <name>fs.defaultFS</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>

    Salve o valor **wasbs://mycontainer@mystorageaccount.blob.core.windows.net**, pois ele será usado nas próximas etapas.
2. Use o seguinte comando para obter o FQDN do headnode do cluster. Isso será usado para o endereço do JobTracker para o cluster. Isto será usado no arquivo de configuração em breve:

        hostname -f

    Isso retornará informações similares às seguintes:

        hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net

    A porta usada para o JobTracker é a 8050, assim, o endereço completo a ser usado para o JobTracker será **hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8050**.
3. Use o seguinte para criar a configuração de definição de trabalho do Oozie:

        nano job.xml
4. Quando o editor for aberto, use o seguinte como conteúdo do arquivo:

        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>

          <property>
            <name>nameNode</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
          </property>

          <property>
            <name>jobTracker</name>
            <value>JOBTRACKERADDRESS</value>
          </property>

          <property>
            <name>queueName</name>
            <value>default</value>
          </property>

          <property>
            <name>oozie.use.system.libpath</name>
            <value>true</value>
          </property>

          <property>
            <name>hiveScript</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
          </property>

          <property>
            <name>hiveTableName</name>
            <value>mobilecount</value>
          </property>

          <property>
            <name>hiveDataFolder</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
          </property>

          <property>
            <name>sqlDatabaseConnectionString</name>
            <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
          </property>

          <property>
            <name>sqlDatabaseTableName</name>
            <value>mobiledata</value>
          </property>

          <property>
            <name>user.name</name>
            <value>YourName</value>
          </property>

          <property>
            <name>oozie.wf.application.path</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
          </property>
        </configuration>

   * Substitua todas as instâncias de **wasbs://mycontainer@mystorageaccount.blob.core.windows.net** pelo valor que você recebeu anteriormente.

     > [!WARNING]
     > Você deve usar o caminho completo do WASB, com a conta de armazenamento e contêiner como parte do caminho. Usar o formato curto (wasbs:///) fará com que a ação de RunHiveScript falhe quando o trabalho for iniciado.
     >
     >
   * Substitua **JOBTRACKERADDRESS** pelo endereço de JobTracker/ResourceManager recebido anteriormente.
   * Substitua **YourName** pelo seu nome de logon para o cluster do HDInsight.
   * Substitua **serverName**, **adminLogin** e **adminPassword** pelas informações de seu Banco de Dados SQL do Azure.

     A maioria das informações contidas nesse arquivo é usada para preencher os valores usados nos arquivos workflow.xml ou ooziewf.hql (por exemplo, ${nameNode}.)

     > [!NOTE]
     > A entrada **oozie.wf.application.path** define onde encontrar o arquivo workflow.xml, que contém o fluxo de trabalho executado por esse trabalho.
     >
     >
5. Use Ctrl-X e, em seguida, **Y** e **Enter** para salvar o arquivo.

## <a name="submit-and-manage-the-job"></a>Enviar e gerenciar o trabalho
As etapas a seguir usam o comando Oozie para enviar e gerenciar fluxos de trabalho do Oozie no cluster. O comando do Oozie é uma interface amigável sobre a [API REST do Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]
> Ao usar o comando Oozie, você deve usar o FQDN para headnode do HDInsight. Esse FQDN só está acessível no cluster ou, se o cluster estiver em uma Rede Virtual do Azure, de outros computadores na mesma rede.
>
>

1. Use o seguinte para obter a URL para o serviço do Oozie:

        sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml

    Isso retornará um valor semelhante ao seguinte:

        <name>oozie.base.url</name>
        <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>

    O trecho **http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie** é a URL a ser usada com o comando Oozie.
2. Use o seguinte para criar uma variável de ambiente para a URL para que você não precise digitá-la para cada comando:

        export OOZIE_URL=http://HOSTNAMEt:11000/oozie

    Substitua a URL pela recebida anteriormente.
3. Use o seguinte para enviar o trabalho:

        oozie job -config job.xml -submit

    Isso carrega as informações do trabalho de **job.xml** e as envia para Oozie, mas não o executa.

    Quando o comando for concluído, ele deverá retornar a ID do trabalho. Por exemplo: `0000005-150622124850154-oozie-oozi-W`. Isso será usado para gerenciar o trabalho.
4. Exiba o status do trabalho usando o comando a seguir. Insira a ID de trabalho retornada pelo comando anterior:

        oozie job -info <JOBID>

    Isso retornará informações semelhantes às seguintes:

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasbs:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    Este trabalho tem um status de `PREP`, que indica que ele foi enviado, mas ainda não foi iniciado.
5. Use o seguinte para iniciar o trabalho:

        oozie job -start JOBID

    Se você verificar o status após este comando, ele estará em um estado de execução e as informações serão retornadas para as ações do trabalho.
6. Depois que a tarefa for concluída com sucesso, você poderá verificar se os dados foram gerados e exportados para a tabela do Banco de Dados SQL usando os seguintes comandos:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest

    Ao prompt `1>` , insira o seguinte:

        SELECT * FROM mobiledata
        GO

    Você deve receber informações da ajuda semelhantes a:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Para obter mais informações sobre o comando Oozie, consulte [Ferramenta da linha de comando do Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>API REST do Oozie
A API REST do Oozie permite que você crie suas próprias ferramentas que funcionem com Oozie. A seguir estão informações específicas do HDInsight sobre como usar a API REST do Oozie:

* **URI**: a API REST pode ser acessada de fora do cluster em `https://CLUSTERNAME.azurehdinsight.net/oozie`
* **Autenticação**: você deve autenticar para a API usando a conta (admin) e a senha HTTP do cluster. Por exemplo:

        curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions

Para obter mais informações sobre como usar a API REST do Oozie, consulte [API de serviços Web do Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Interface da Web do Oozie
A IU da Web do Oozie fornece um modo de exibição baseado na web sobre o status dos trabalhos do Oozie no cluster. Ele permite que você exiba o status do trabalho, a definição de trabalho, a configuração, um gráfico de ações no trabalho e logs para o trabalho. Você também pode exibir detalhes de ações dentro de um trabalho.

Para acessar a interface do usuário do Oozie da Web, use as seguintes etapas:

1. Crie um túnel SSH para o cluster HDInsight. Para obter mais informações sobre como fazer isso, consulte [Usar túnel SSH para acessar a IU da Ambari Web, ResourceManager, JobHistory, NameNode, Oozie e outras IUs da Web](hdinsight-linux-ambari-ssh-tunnel.md).
2. Quando um túnel tiver sido criado, abra a interface do usuário da Web do Ambari no navegador da Web. O URI para o site Ambari é **https://CLUSTERNAME.azurehdinsight.net**. Substitua **CLUSTERNAME** pelo nome do seu cluster do HDInsight baseado em Linux.
3. No lado esquerdo da página, selecione **Oozie** e, em seguida, **Links rápidos** e, finalmente, **Interface do usuário da Web do Oozie**.

    ![imagem dos menus](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)
4. A interface do usuário da Web do Oozie assume como padrão a exibição de Trabalhos do fluxo de trabalho em execução. Para ver todos os trabalhos de fluxo de trabalho, selecione **Todos os trabalhos**.

    ![Todos os trabalhos exibidos](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)
5. Selecione um trabalho para exibir mais informações sobre ele.

    ![Informações do Trabalho](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)
6. Na guia Informações do Trabalho, você pode ver informações básicas sobre o trabalho, bem como as ações individuais dentro do trabalho. Usando as guias na parte superior, você pode ver a Definição de trabalho, a Configuração de trabalho, acessar o Log de trabalho ou ver um gráfico acíclico dirigido (DAG) do trabalho.

   * **Log de trabalho**: selecione o botão **GetLogs** para obter todos os logs do trabalho, ou use o campo **Inserir filtro de pesquisa** para filtrar os logs

       ![Log de trabalhos](./media/hdinsight-use-oozie-linux-mac/joblog.png)
   * **JobDAG**: o DAG é uma visão geral gráfica dos caminhos de dados percorridos pelo fluxo de trabalho

       ![DAG de trabalho](./media/hdinsight-use-oozie-linux-mac/jobdag.png)
7. Selecionar uma das ações na guia **Informações do Trabalho** exibirá informações para a ação. Por exemplo, selecione a ação **RunHiveScript** .

    ![Informações da ação](./media/hdinsight-use-oozie-linux-mac/action.png)
8. Você pode ver detalhes da ação, incluindo um link para a **URL do Console**, que pode ser usado para exibir informações de JobTracker para o trabalho.

## <a name="scheduling-jobs"></a>Agendamento de trabalhos
O coordenador permite que você especifique um início, um fim e a frequência de ocorrência para trabalhos de forma que eles possam ser agendados para determinados horários.

Para definir uma agenda para o fluxo de trabalho, use as seguintes etapas:

1. Use o seguinte para criar um novo arquivo chamado **coordinator.xml**:

        nano coordinator.xml

    Use o seguinte como conteúdo do arquivo:

        <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
          <action>
            <workflow>
              <app-path>${workflowPath}</app-path>
            </workflow>
          </action>
        </coordinator-app>

    Observe que isso usa variáveis `${...}` que serão substituídos pelos valores na definição de trabalho. As variáveis são:

   * **${coordFrequency}**: tempo entre instâncias de execução do trabalho
   * **${coordStart}**: a hora de início do trabalho
   * **${coordEnd}**: a hora de término do trabalho
   * **${coordTimezone}**: trabalhos do coordenador estão em um fuso horário fixo sem horário de verão (geralmente representado pelo UTC). Esse fuso horário é chamado de "Fuso horário de processamento do Oozie".
   * **${wfPath}**: o caminho para workflow.xml
2. Use Ctrl-X e, em seguida, **Y** e **Enter** para salvar o arquivo.
3. Use o seguinte para copiar para o diretório de trabalho para este trabalho:

        hadoop fs -copyFromLocal coordinator.xml /tutorials/useoozie/coordinator.xml
4. Use o seguinte para modificar o arquivo **job.xml** :

        nano job.xml

    Faça as seguintes alterações:

   * Alterar `<name>oozie.wf.application.path</name>` para `<name>oozie.coord.application.path</name>`. Isso instrui o Oozie a executar o arquivo de coordenador, em vez do arquivo de fluxo de trabalho
   * Adicione o seguinte, que definirá uma variável usada no coordinator.xml para apontar para o local do workflow.xml:

           <property>
             <name>workflowPath</name>
             <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
           </property>

       Substitua os valores de **mycontainer** e **mystorageaccount** pelos valores usados em outras entradas no arquivo job.xml.
   * Adicione o seguinte, que define o início, o fim e a frequência a usar para o arquivo coordinator.xml:

           <property>
             <name>coordStart</name>
             <value>2015-06-25T12:00Z</value>
           </property>

           <property>
             <name>coordEnd</name>
             <value>2015-06-27T12:00Z</value>
           </property>

           <property>
             <name>coordFrequency</name>
             <value>1440</value>
           </property>

           <property>
             <name>coordTimezone</name>
             <value>UTC</value>
           </property>

       Isso define a hora de início para as 12h em 25 de junho de 2015, a hora de término para 27 de junho de 2015 e o intervalo para executar esse trabalho como diário (a frequência é em minutos, até 24 horas x 60 minutos = 1440 minutos.) Por fim, o fuso horário é definido como UTC.
5. Use Ctrl-X e, em seguida, **Y** e **Enter** para salvar o arquivo.
6. Para executar o trabalho, use o seguinte comando:

        oozie job -config job.xml -run

    Isso vai enviar e iniciar o trabalho.
7. Se você visitar a interface do usuário da Web do Oozie e selecionar a guia **Trabalhos do Coordenador** , deverá obter informações semelhantes às seguintes:

    ![guia de trabalhos do coordenador](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    Observe a entrada **Próxima Materialização** ; isso é a próxima vez em que o trabalho será executado.
8. Semelhante ao fluxo de trabalho anterior, selecionar a entrada de trabalho na interface do usuário da Web exibirá informações sobre o trabalho:

    ![Informações de trabalho do coordenador](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    Observe que isso só mostra a execução bem-sucedida do trabalho, não ações individuais do fluxo de trabalho agendado. Para ver isso, selecione uma das entradas de **Ação** . Isso exibirá informações semelhantes àquelas recuperados para o fluxo de trabalho anterior.

    ![Informações da ação](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>Solucionar problemas
Ao solucionar problemas de trabalhos do Oozie, a UI Oozie é muito útil, pois permite exibir facilmente ambos os logs de Oozie, bem como links para logs de JobTracker para tarefas MapReduce como consultas Hive. Em geral, o padrão para solução de problemas deve ser:

1. Exiba o trabalho na interface do usuário da Web do Oozie.
2. Se houver um erro ou falha de uma ação específica, selecione a ação para ver se o campo **Mensagem de Erro** fornece mais informações sobre a falha.
3. Se estiver disponível, use a URL da ação para exibir mais detalhes (como logs de JobTracker) para a ação.

A seguir estão os erros específicos que podem ser encontrados e como resolvê-los.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: não é possível inicializar o cluster
**Sintomas**: o status do trabalho será alterado para **SUSPENSO**. Os detalhes do trabalho mostrarão o status de RunHiveScript como **START_MANUAL**. Selecionar a ação revelará a seguinte mensagem de erro:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Causa**: os endereços de WASB usados no arquivo **job.xml** não contêm o contêiner de armazenamento ou o nome da conta de armazenamento. O formato do endereço WASB deve ser `wasbs://containername@storageaccountname.blob.core.windows.net`.

**Resolução**: alterar os endereços WASB usados pelo trabalho.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltuser"></a>JA002: o Oozie não tem permissão para representar o &lt;USER>
**Sintomas**: o status do trabalho será alterado para **SUSPENSO**. Os detalhes do trabalho mostrarão o status de RunHiveScript como **START_MANUAL**. Selecionar a ação revelará a seguinte mensagem de erro:

    JA002: User: oozie is not allowed to impersonate <USER>

**Causa**: as configurações de permissão atuais não permitem ao Oozie representar a conta de usuário especificada.

**Resolução**: o Oozie tem permissão para representar os usuários no grupo **usuários**. Use o `groups USERNAME` para ver os grupos do qual a conta de usuário é membro. Se o usuário não for membro do grupo **usuários** , use o seguinte comando para adicionar o usuário ao grupo:

    sudo adduser USERNAME users

> [!NOTE]
> Pode levar vários minutos antes de o HDInsight reconhecer que o usuário foi adicionado ao grupo.
>
>

### <a name="launcher-error-sqoop"></a>ERRO do Iniciador (Sqoop)
**Sintomas**: o status do trabalho será alterado para **ENCERRADO**. Detalhes do trabalho mostrarão o status de RunSqoopExport como **ERRO**. Selecionar a ação revelará a seguinte mensagem de erro:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Causa**: o Sqoop não conseguiu carregar o driver do banco de dados necessário para acessar o banco de dados.

**Resolução**: ao usar o Sqoop de um trabalho do Oozie, você deve incluir o driver de banco de dados com outros recursos (como o workflow.xml) usado pelo trabalho.

Você também deve consultar o arquivo contendo o driver do banco de dados na seção `<sqoop>...</sqoop>` do workflow.xml.

Por exemplo, para o trabalho neste documento, você usaria o seguinte procedimento:

1. Copie o arquivo sqljdbc4.1.jar para o diretório /tutorials/useoozie:

         hadoop fs -copyFromLocal /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar
2. Modifique o workflow.xml para adicionar o seguinte em uma nova linha acima `</sqoop>`:

        <archive>sqljdbc41.jar</archive>

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a definir um fluxo de trabalho do Oozie e a executar um trabalho do Oozie. Para saber mais sobre como trabalhar com o HDInsight, consulte os seguintes artigos:

* [Usar o Coordenador do Oozie baseado em tempo com o HDInsight][hdinsight-oozie-coordinator-time]
* [Carregar dados para trabalhos do Hadoop no HDInsight][hdinsight-upload-data]
* [Usar Sqoop com o Hadoop no HDInsight][hdinsight-use-sqoop]
* [Usar o Hive com Hadoop no HDInsight][hdinsight-use-hive]
* [Usar o Pig com Hadoop no HDInsight][hdinsight-use-pig]
* [Desenvolver programas Java MapReduce para HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/data-factory-data-transformation-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]: storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/en-us/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx



<!--HONumber=Dec16_HO2-->


