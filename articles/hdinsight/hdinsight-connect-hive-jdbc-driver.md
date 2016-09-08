<properties
 pageTitle="Usar JDBC para consultar Hive no Azure HDInsight"
 description="Saiba como usar JDBC para se conectar ao Hive no Azure HDInsight e executar remotamente as consultas nos dados armazenados na nuvem."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="java"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="08/23/2016"
 ms.author="larryfr"/>

#Conectar-se ao Hive no Azure HDInsight usando o driver JDBC do Hive

[AZURE.INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

Neste documento, você aprenderá a usar JDBC por meio de um aplicativo Java para enviar remotamente consultas de Hive para um cluster do HDInsight. Você aprenderá a se conectar do cliente do SQuirreL SQL e a se conectar programaticamente do Java.

Para obter mais informações sobre a Interface JDBC do Hive, consulte [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

##Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um Hadoop no cluster do HDInsight. Um cluster baseado em Linux ou em Windows funcionará.

* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). O SQuirreL é um aplicativo cliente JDBC.

Para compilar e executar o aplicativo Java de exemplo vinculado a este artigo, você precisará do seguinte.

* O [Java Developer Kit (JDK) versão 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou superior.

* [Apache Maven](https://maven.apache.org). Maven é um sistema de criação de projeto para projetos Java que é usado pelo projeto associado a este artigo.

##Cadeia de conexão

Conexões JDBC para um cluster HDInsight no Azure são feitas em 443, e o tráfego é protegido usando SSL. O gateway público atrás dos quais ficam os clusters redireciona o tráfego para a porta que o HiveServer2 de fato está escutando. Assim, uma cadeia de conexão típica seria semelhante à seguinte:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

Substitua __CLUSTERNAME__ pelo nome do seu cluster HDInsight.

##Autenticação

Ao estabelecer a conexão, você deverá usar o nome e a senha do administrador do cluster HDInsight para se autenticar no gateway do cluster. Durante a conexão de clientes JDBC como o SQuirreL SQL, você deve inserir o nome e a senha do administrador nas configurações do cliente.

De um aplicativo Java, use o nome e a senha ao estabelecer uma conexão. Por exemplo, o código Java a seguir abre uma nova conexão usando a cadeia de conexão, o nome do administrador e a senha:

    DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);

##Conectar-se ao cliente do SQuirreL SQL

O SQuirreL SQL é um cliente JDBC que pode ser usado para executar remotamente as consultas do Hive com o cluster HDInsight. As etapas a seguir pressupõem que você já tenha instalado o SQuirreL SQL e explicam como baixar e configurar os drivers para o Hive.

1. Copie os drivers JDBC do Hive do seu cluster HDInsight.

    * Para o __HDInsight baseado em Linux__, use as etapas a seguir para baixar os arquivos jar necessários.

        1. Crie uma nova pasta para colocar os arquivos. Por exemplo: `mkdir hivedriver`.

        2. Em um prompt de comando, Bash, PowerShell ou de outro prompt de linha de comando, mude para o novo diretório e use os seguintes comandos para copiar os arquivos do cluster HDInsight.

                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/hive-jdbc*standalone.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-common.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-auth.jar .

            Substitua __NOMEDOUSUÁRIO__ pelo nome da conta de usuário SSH para o cluster. Substitua __NOMEDOCLUSTER__ pelo nome do cluster HDInsight.

            > [AZURE.NOTE] Nos ambientes do Windows, será necessário usar o utilitário PSCP em vez do scp. Baixe-o de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    * Para o __HDInsight baseado no Windows__, use as etapas a seguir para baixar os arquivos jar necessários.

        1. No portal do Azure, selecione o cluster HDInsight e selecione o ícone da __Área de Trabalho Remota__.

            ![Ícone da Área de Trabalho Remota](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopicon.png)

        2. Na folha Área de Trabalho Remota, selecione __Conectar__ para se conectar ao cluster. Se a Área de Trabalho Remota não estiver habilitada, use o formulário para fornecer um nome de usuário e uma senha e selecione __Habilitar__ para habilitar a Área de Trabalho Remota para o cluster.

            ![Folha Área de Trabalho Remota](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopblade.png)

            Depois que você selecionar __Conectar__, um arquivo .rdp será baixado. Use esse arquivo para iniciar o cliente da Área de Trabalho Remota. Quando solicitado, use o nome de usuário e a senha inseridos para o acesso à Área de Trabalho Remota.

        3. Uma vez conectado, copie os seguintes arquivos da sessão da Área de Trabalho Remota para o seu computador local. Coloque-os em um diretório local chamado `hivedriver`.

            * C:\\apps\\dist\\hive-0.14.0.2.2.9.1-7\\lib\\hive-jdbc-0.14.0.2.2.9.1-7-standalone.jar
            * C:\\apps\\dist\\hadoop-2.6.0.2.2.9.1-7\\share\\hadoop\\common\\hadoop-common-2.6.0.2.2.9.1-7.jar
            * C:\\apps\\dist\\hadoop-2.6.0.2.2.9.1-7\\share\\hadoop\\common\\lib\\hadoop-auth-2.6.0.2.2.9.1-7.jar

            > [AZURE.NOTE] Os números da versão incluídos nos caminhos e os nomes de arquivo podem ser diferentes para o seu cluster.

        4. Desconecte a sessão da Área de Trabalho Remota quando terminar de copiar os arquivos.

3. Inicie o aplicativo SQuirreL SQL. Na parte esquerda da janela, selecione __Drivers__.

    ![Guia Drivers à esquerda da janela](./media/hdinsight-connect-hive-jdbc-driver/squirreldrivers.png)

4. Nos ícones na parte superior do diálogo __Drivers__, selecione o ícone __+__ ícone para criar um novo driver.

    ![Ícones de Drivers](./media/hdinsight-connect-hive-jdbc-driver/driversicons.png)

5. No diálogo Adicionar Driver, adicione as informações a seguir.

    * __Nome__: Hive
    * __URL de Exemplo__: jdbc:hive2://localhost:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2
    * __Caminho de Classe Adicional__: use o botão Adicionar para adicionar os arquivos jar baixados anteriormente
    * __Nome da Classe__: org.apache.hive.jdbc.HiveDriver

    ![diálogo adicionar driver](./media/hdinsight-connect-hive-jdbc-driver/adddriver.png)

    Clique em __OK__ para salvar as configurações.

6. À esquerda da janela do SQuirreL SQL, selecione __Aliases__. Em seguida, clique no ícone __+__ para criar um novo alias de conexão.

    ![adicionar novo alias](./media/hdinsight-connect-hive-jdbc-driver/aliases.png)

7. Use os valores a seguir para o diálogo __Adicionar Alias__.

    * __Nome__: Hive no HDInsight
    * __Driver__: use o menu suspenso para selecionar o driver do __Hive__
    * __URL__: jdbc:hive2://NOMEDOCLUSTER.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

        Substitua __CLUSTERNAME__ pelo nome do seu cluster HDInsight.

    * __Nome de Usuário__: o nome de conta de logon do cluster para o cluster HDInsight. O padrão é `admin`.
    * __Senha__: a senha da conta de logon do cluster. É uma senha que você forneceu ao criar o cluster HDInsight.

    ![diálogo adicionar alias](./media/hdinsight-connect-hive-jdbc-driver/addalias.png)

    Use o botão __Testar__ para verificar se a conexão funciona. Quando o diálogo __Conectar a: Hive no HDInsight__ for exibido, selecione __Conectar__ para executar o teste. Se o teste tiver êxito, você verá um diálogo __Conexão bem-sucedida__.

    Use o botão __Ok__ na parte inferior do diálogo __Adicionar Alias__ para salvar o alias de conexão.

8. Na lista suspensa __Conectar a__, na parte superior do SQuirreL SQL, selecione __Hive no HDInsight__. Quando solicitado, selecione __Conectar__.

    ![diálogo conexão](./media/hdinsight-connect-hive-jdbc-driver/connect.png)

9. Uma vez conectado, insira a consulta a seguir no diálogo Consulta SQL e selecione o ícone __Executar__. A área de resultados deve mostrar os resultados da consulta.

        select * from hivesampletable limit 10;

    ![diálogo consulta sql, incluindo os resultados](./media/hdinsight-connect-hive-jdbc-driver/sqlquery.png)

##Conectar-se de um aplicativo Java de exemplo

Um exemplo de como usar um cliente Java para consultar o Hive no HDInsight está disponível em [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Siga as instruções no repositório para compilar e executar o exemplo.

##Solucionar problemas

### Ocorreu um erro inesperado ao tentar abrir uma conexão SQL.

__Sintomas__: ao se conectar a um cluster HDInsight versão 3.3 ou 3.4, você poderá receber uma mensagem indicando que ocorreu um erro inesperado. O rastreamento de pilha para esse erro começará com as seguintes linhas:

    java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
    at java.util.concurrent.FutureTas...(FutureTask.java:122)
    at java.util.concurrent.FutureTask.get(FutureTask.java:206)

__Causa__: este erro é causado por uma incompatibilidade na versão do arquivo commons-codec.jar usada pelo SQuirreL e aquela exigida pelos componentes JDBC do Hive baixada por meio do cluster HDInsight.

__Resolução__: para corrigir esse erro, use as etapas a seguir.

1. Baixe o arquivo commons-codec.jar por meio do cluster HDInsight.

        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. Saia do SQuirreL e vá para o diretório em que o SQuirreL está instalado em seu sistema. No diretório do SquirreL, sob o diretório `lib`, substitua o commons-codec.jar existente pelo baixado por meio do cluster HDInsight.

3. Reinicie o SQuirreL. O erro não deverá ocorrer mais nas próximas conexões ao Hive no HDInsight.

##Próximas etapas

Agora que você aprendeu a usar o JDBC para trabalhar com Hive, use os links abaixo para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Carregar dados no HDInsight](hdinsight-upload-data.md)
* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar trabalhos do MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0824_2016-->