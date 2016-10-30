<properties
   pageTitle="Processar eventos de Hubs de Eventos com o Storm no HDInsight usando o Java | Microsoft Azure"
   description="Saiba como processar dados de Hubs de Eventos com uma topologia Storm Java criada com o Maven."
   services="hdinsight,notification hubs"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/11/2016"
   ms.author="larryfr"/>


# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-(java)"></a>Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight (Java)

Os Hubs de Eventos do Azure permitem processar grandes quantidades de dados de sites, aplicativos e dispositivos. O spout dos Hubs de Eventos facilita o uso do Apache Storm no HDInsight para analisar esses dados em tempo real. Você pode também gravar dados no Hub de Eventos usando o bolt dos Hubs de Eventos.

Neste tutorial, você aprenderá a usar o spout e bolt dos Hubs de Eventos para ler e gravar dados em uma topologia Storm baseada em Java.

## <a name="prerequisites"></a>Pré-requisitos

* Um Apache Storm no cluster HDInsight. Use um dos seguintes artigos de introdução para criar um cluster:

    - Um [Storm baseado em Linux em cluster HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md): selecione esta opção se quiser utilizar o SSH para trabalhar com o cluster de clientes Linux, Unix, OS X ou Windows

    - Um [Storm baseado em Windows em cluster HDInsight](hdinsight-apache-storm-tutorial-get-started.md): selecione esta opção se quiser utilizar o PowerShell para trabalhar com o cluster de um cliente Windows

    > [AZURE.NOTE] As etapas neste documento baseiam-se no uso de um Storm em cluster HDInsight 3.3 ou 3.4. Esses clusters fornecem Storm 0.10.0 e Hadoop 2.7, o que reduz o número de etapas necessárias para que esse exemplo funcione.
    >
    > Para obter uma versão desse exemplo que funcione com o Storm 0.9.3 no HDInsight 3.2, confira a ramificação [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) do repositório de exemplo.

* Um [Hub de Eventos do Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* [Oracle Java Developer Kit (JDK) versão 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou equivalente, como [OpenJDK](http://openjdk.java.net/)

* [Maven](https://maven.apache.org/download.cgi): o Maven é um sistema de criação de projetos para projetos Java

* Um editor de texto ou um ambiente de desenvolvimento integrado (IDE) Java

    > [AZURE.NOTE] Seu editor ou IDE pode ter uma funcionalidade específica para trabalhar com o Maven que não é abordada neste documento. Para obter informações sobre os recursos do seu ambiente de edição, consulte a documentação do produto que você está usando.

 * Um cliente SSH. Consulte um dos artigos a seguir para obter mais informações sobre a utilização do SSH com o HDInsight:

    - [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

* Um cliente SCP. É fornecido com todos os sistemas Linux, Unix e OS X. Para clientes Windows, é recomendável o PSCP, que está disponível na [página de download do PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a name="understanding-the-example"></a>Compreendendo o exemplo

O exemplo [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) contém duas topologias:

__com.microsoft.example.EventHubWriter__ grava dados aleatórios em um Hub de Eventos do Azure. Os dados são gerados por um spout e são compostos por uma ID de dispositivo aleatório e por um valor de dispositivo. Dessa forma, ele está simulando um hardware que emite uma ID de cadeia de  caracteres e um valor numérico.

__com.microsoft.example.EventHubReader__ lê dados do Hub de eventos (os dados gravados pelo EventHubWriter) e os armazena no HDFS (WASB nesse caso, uma vez que isso foi gravado e testado com o Azure HDInsight) no diretório /devicedata.

Os dados são formatados como um documento JSON antes de serem gravados no Hub de Eventos. Quando eles forem lidos pelo leitor, serão analisados pelo JSON em tuplas. O formato JSON é o seguinte:

    { "deviceId": "unique identifier", "deviceValue": some value }

O motivo para usar um documento JSON para armazenar os dados no Hub de Eventos é que, dessa forma, sabemos qual é o formato em vez de confiarmos na mecânica de formatação interna do Spout e Bolt do Hub de Eventos.

###<a name="project-configuration"></a>Configuração do projeto

O arquivo **POM.xml** contém informações de configuração deste projeto Maven. As partes interessantes são:

####<a name="the-eventhubs-storm-spout-dependency"></a>A dependência do Spout Storm dos Hubs de Eventos

    <dependency>
      <groupId>org.apache.storm</groupId>
      <artifactId>storm-eventhubs</artifactId>
      <version>0.10.0</version>
    </dependency>

Isso adiciona uma dependência ao pacote storm-eventhubs, que contém um spout para leitura dos Hubs de Eventos e um bolt para a gravação neles.

> [AZURE.NOTE] Esse pacote só está disponível para a versão do Storm 0.10.0 e superior. Ao usar o Storm 0.9.3, você deve instalar manualmente o pacote spout fornecido pela Microsoft. Para obter um exemplo de como trabalhar com o Storm 0.9.3, confira a ramificação [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) do repositório de exemplo.

####<a name="the-hdfsbolt-and-wasb-components"></a>Os componentes HdfsBolt e WASB

Normalmente, o HdfsBolt é usado para armazenar dados para o HDFS (Sistema de Arquivos Distribuído do Hadoop). No entanto, os clusters HDInsight usam o armazenamento do Azure (WASB) como o repositório de dados padrão, portanto precisamos carregar vários componentes que permitam ao HdfsBolt entender o sistema de arquivos WASB.

      <!--HdfsBolt stuff -->
        <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-hdfs</artifactId>
        <exclusions>
            <exclusion>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            </exclusion>
            <exclusion>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-hdfs</artifactId>
            </exclusion>
        </exclusions>
        <version>0.10.0</version>
        </dependency>
    <!--So HdfsBolt knows how to talk to WASB -->
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-client</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-hdfs</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-azure</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>2.7.1</version>
        <exclusions>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
        </exclusions>
    </dependency>

> [AZURE.NOTE] Ao trabalhar com uma versão anterior do HDInsight, como a versão 3.2, você precisará registrar esses componentes manualmente. Para obter um exemplo desse processo, junto com os bits personalizados necessários para clusters do HDInsight mais antigos, confira a ramificação [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) do repositório de exemplo.

####<a name="the-maven-compiler-plugin"></a>O maven-compiler-plugin

    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>2.3.2</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

Isso informa ao Maven que o projeto deve ser compilado com compatibilidade para Java 7, que é usado pelos clusters HDInsight.

####<a name="the-maven-shade-plugin"></a>O maven-shade-plugin

      <!-- build an uber jar -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
          <transformers>
            <!-- Keep us from getting a can't overwrite file error -->
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer"/>
            <!-- Keep us from getting errors when trying to use WASB from the storm-hdfs bolt -->
            <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer"/>
          </transformers>
          <!-- Keep us from getting a bad signature error -->
          <filters>
            <filter>
                <artifact>*:*</artifact>
                <excludes>
                    <exclude>META-INF/*.SF</exclude>
                    <exclude>META-INF/*.DSA</exclude>
                    <exclude>META-INF/*.RSA</exclude>
                </excludes>
            </filter>
          </filters>
        </configuration>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
          </execution>
        </executions>
      </plugin>

Usado para empacotar a solução para um jar uber com o código do projeto e as dependências necessárias. Também é usado para:

* Renomear os arquivos de licença para as dependências: se isso não for feito, poderá resultar em um erro em tempo de execução em clusters HDInsight baseados no Windows.

* Excluir segurança/assinaturas: se isso não for feito, poderá resultar em um erro em tempo de execução no cluster HDInsight.

* Verifique se as várias implementações da mesma interface estão mescladas em uma entrada. Se isso não for feito, você receberá erros informando que o bolt Storm-HDFS não sabe como se comunicar com o sistema de arquivos do WASB.

####<a name="the-exec-maven-plugin"></a>O exec-maven-plugin

    <plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <version>1.2.1</version>
      <executions>
        <execution>
        <goals>
          <goal>exec</goal>
        </goals>
        </execution>
      </executions>
      <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
      </configuration>
    </plugin>

Permite que você execute a topologia localmente em seu ambiente de desenvolvimento usando o seguinte comando:

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

Por exemplo: `mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`.

####<a name="the-resources-section"></a>A seção de recursos

    <resources>
      <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
          <include>EventHubs.properties</include>
          <include>core-site.xml</include>
        </includes>
      </resource>
    </resources>

Isso define os recursos exigidos pelo projeto:

- **EventHubs.properties**: contém informações usadas para a conexão a um Hub de Eventos do Azure
- **core-site**: contém informações sobre o Armazenamento do Azure usado pelo cluster HDInsight.

Você deve preencher essas informações sobre seu Hub de Eventos e o cluster HDInsight.

##<a name="configure-environment-variables"></a>Configurar variáveis de ambiente

As seguintes variáveis de ambiente podem ser definidas quando você instala o Java e o JDK em sua estação de trabalho de desenvolvimento. No entanto, você deve verificar se elas existem e se contêm os valores corretos para o seu sistema.

* **JAVA_HOME** - deve apontar para o diretório onde o JRE (Java runtime environment) está instalado. Por exemplo, em uma distribuição Unix ou Linux, ele deve ter um valor semelhante a `/usr/lib/jvm/java-7-oracle`. No Windows, ele teria um valor semelhante a `c:\Program Files (x86)\Java\jre1.7`

* **PATH** - deve conter os seguintes caminhos:

    * **JAVA_HOME** (ou o caminho equivalente)

    * **JAVA_HOME\bin** (ou o caminho equivalente)

    * O diretório onde o Maven está instalado

## <a name="configure-event-hub"></a>Configurar o Hub de Eventos

Hubs de Eventos é a fonte de dados para este exemplo. Use as seguintes etapas para criar um novo Hub de Eventos.

1. No [Portal Clássico do Azure](https://manage.windowsazure.com), selecione **NOVO** > **Barramento de Serviço** > **Hub de Eventos** > **Criação Personalizada**.

2. Na tela **Adicionar um novo Hub de Eventos**, insira um **Nome do Hub de Eventos**, selecione a **Região** na qual criar o hub e crie um novo namespace ou selecione um existente. Clique na **Seta** para continuar.

    ![página 1 do assistente](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

    > [AZURE.NOTE] Você deve selecionar o mesmo **Local** como seu Storm no servidor HDInsight para reduzir a latência e os custos.

2. Na tela **Configurar o Hub de Eventos**, insira os valores de **Contagem de partições** e **Retenção de Mensagem**. Para este exemplo, use uma contagem de partições de 10 e uma retenção de mensagens de 1. Observe a contagem de partições, pois você precisará desse valor posteriormente.

    ![página 2 do assistente](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. Depois que o hub de eventos tiver sido criado, selecione o namespace, selecione **Hubs de Eventos**e, em seguida, selecione o hub de eventos criado anteriormente.

4. Selecione **Configurar**e crie duas novas políticas de acesso usando as informações a seguir.

    <table>
    <tr><th>Nome</th><th>Permissões</th></tr>
    <tr><td>Gravador</td><td>Enviar</td></tr>
    <tr><td>Leitor</td><td>Escutar</td></tr>
    </table>

    Depois de criar permissões, selecione o ícone **Salvar** na parte inferior da página. Isso cria políticas de acesso compartilhado que serão usadas para enviar (gravador) e escutar (leitor) esse Hub de Eventos.

    ![políticas](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. Depois de salvar as políticas, use o **Gerador de chave de acesso compartilhada** na parte inferior da página para recuperar a chave para as políticas **gravador** e **leitor**. Salve-as, pois serão usadas mais tarde.

## <a name="download-and-build-the-project"></a>Baixar e compilar o projeto

1. Baixe o projeto do GitHub: [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub). Você pode baixar o pacote como um arquivo zip, ou usar o [git](https://git-scm.com/) para clonar o projeto localmente.

2. Use o seguinte para criar e empacotar o projeto:

        mvn package

    Isso baixará as dependências exigidas, compilará e então empacotará o projeto. A saída será armazenada no diretório __/target__ como __EventHubExample-1.0-SNAPSHOT.jar__.

## <a name="deploy-the-topologies"></a>Implantar as topologias

O jar criado por este projeto contém duas topologias; __com.microsoft.example.EventHubWriter__ e __com.microsoft.example.EventHubReader__. A topologia EventHubWriter deve ser iniciada primeiro, já que grava eventos no Hub de Eventos que são lidos pelo EventHubReader.

###<a name="if-using-a-linux-based-cluster"></a>Se um cluster baseado em Linux estiver sendo usado

1. Use o SCP para copiar o pacote jar para seu cluster HDInsight. Substitua USERNAME pelo usuário SSH para seu cluster. Substitua CLUSTERNAME pelo nome do seu cluster HDInsight:

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Se você tiver usado uma senha para sua conta SSH, a inserção da senha poderá ser solicitada. Se você tiver usado uma chave SSH com a conta, talvez seja necessário usar o parâmetro `-i` para especificar o caminho para o arquivo de chave. Por exemplo: `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`.

    > [AZURE.NOTE] Se o cliente for uma estação de trabalho do Windows, talvez você não tenha um comando SCP instalado. Recomendamos o PSCP, que pode ser baixado da [página de download do PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    Esse comando copiará o arquivo para o diretório base do usuário SSH no cluster.

1. Após a conclusão do carregamento do arquivo, use o SSH para se conectar ao cluster HDInsight. Substitua o **USERNAME** pelo nome do seu logon SSH. Substitua o **CLUSTERNAME** pelo nome do seu cluster HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [AZURE.NOTE] Se você tiver usado uma senha para sua conta SSH, a inserção da senha poderá ser solicitada. Se você tiver usado uma chave SSH com a conta, talvez seja necessário usar o parâmetro `-i` para especificar o caminho para o arquivo de chave. O exemplo a seguir carregará a chave privada de `~/.ssh/id_rsa`:
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

    Se você estiver usando o PuTTY, insira `CLUSTERNAME-ssh.azurehdinsight.net` no campo __Nome do Host (ou endereço IP)__ e clique em __Abrir__ para se conectar. Será solicitado que você insira seu nome de conta SSH.

    > [AZURE.NOTE] Se você tiver usado uma senha para sua conta SSH, a inserção da senha poderá ser solicitada. Se você tiver usado uma chave SSH com a conta, talvez seja necessário usar as etapas a seguir para selecionar a chave:
    >
    > 1. Em **Categoria**, expanda **Conexão**, expanda **SSH** e selecione **Autenticação**.
    > 2. Clique em **Procurar** e selecione o arquivo .ppk que contém a chave privada.
    > 3. Clique em __Abrir__ para se conectar.

2. Use o comando a seguir para iniciar as topologias:

        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader

    Isso iniciará as topologias e dará a elas os nomes amigáveis “leitor” e “gravador”.

3. Aguarde um minuto ou dois para permitir que as topologias gravem ou leiam eventos do hub de eventos, use o comando a seguir para verificar se o EventHubReader está armazenando dados para o armazenamento do HDInsight:

        hadoop fs -ls /devicedata

    Isso deverá retornar uma lista de arquivos semelhante à seguinte:

        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt

    > [AZURE.NOTE] Alguns arquivos podem mostrar um tamanho 0, já que foram criados pelo EventHubReader, mas os dados ainda não foram armazenados neles.

    Você pode exibir o conteúdo desses arquivos usando o comando a seguir:

        hadoop fs -text /devicedata/*.txt

    Isso retornará dados semelhantes aos seguintes:

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

    A primeira coluna contém o valor da ID do dispositivo e a segunda coluna é o valor do dispositivo.

4. Use os comandos a seguir para parar as topologias:

        storm kill reader
        storm kill writer

###<a name="if-using-a-windows-based-cluster"></a>Se você estiver usando um cluster baseado no Windows

1. Abra seu navegador para https://CLUSTERNAME.azurehdinsight.net. Quando solicitado, insira as credenciais do administrador do cluster HDInsight. Você chegará ao Painel do Storm.

2. Use a lista suspensa __Jar File__ para procurar e selecionar o arquivo EventHubExample-1.0-SNAPSHOT.jar do seu ambiente de compilação.

3. Em __Nome da Classe__, insira `com.mirosoft.example.EventHubWriter`.

4. Em __Parâmetros Adicionais__, insira `writer`. Por fim, clique em __Enviar__ para carregar o jar e iniciar a topologia do EventHubWriter.

5. Assim que a topologia tiver sido iniciada, use o formulário para iniciar o EventHubReader:

    * __Arquivo Jar__: selecione o EventHubExample-1.0-SNAPSHOT.jar que foi carregado anteriormente
    * __Nome da Classe__: insira `com.microsoft.example.EventHubReader`
    * __Parâmetros Adicionais__, insira `reader`

    Clique em enviar para iniciar a topologia do EventHubReader.

6. Aguarde alguns minutos para permitir que as topologias gerem eventos e os armazenem no Armazenamento do Azure, selecione a guia __Console do Hadoop Query__ na parte superior da página __Painel do Storm__.

7. No __Console de Consulta__, selecione __Editor do Hive__ e substitua o padrão `select * from hivesampletable` pelo seguinte:

        create external table devicedata (deviceid string, devicevalue int) row format delimited fields terminated by ',' stored as textfile location 'wasbs:///devicedata/';
        select * from devicedata limit 10;

    Clique em __Selecionar__ para executar a consulta. Isso retornará 10 linhas dos dados gravados no Armazenamento do Azure (WASB) pelo EventHubReader. Assim que a consulta for concluída, você deverá ver dados semelhantes aos seguintes:

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

8. Selecione o __Painel do Storm__ na parte superior da página e, em seguida, selecione __Interface do usuário Storm__. Na __Interface do usuário Storm__, selecione o link para a topologia do __leitor__ e use o botão __Eliminar__ para parar a topologia. Repita o processo para a topologia do __gravador__ .



### <a name="checkpointing"></a>Ponto de verificação

O EventHubSpout cria periodicamente pontos de verificação para seu estado para o nó Zookeeper, que salva o deslocamento atual para mensagens lidas da fila. Isso permite que o componente comece a receber mensagens no deslocamento salvo nos seguintes cenários:

* A instância de componente falha e é reiniciada.

* Você aumenta ou reduz o cluster ao adicionar ou remover nós.

* A topologia é interrompida e reiniciada **com o mesmo nome**.

####<a name="on-windows-based-hdinsight-clusters"></a>Em clusters HDInsight baseado no Windows

Você pode exportar e importar os pontos de verificação persistentes para WASB (Armazenamento do Azure usado pelo seu cluster HDInsight). Os scripts para fazer isso estão localizados no Storm no cluster HDInsight, em **c:\apps\dist\storm-0.9.3.2.2.1.0-2340\zkdatatool-1.0\bin**.

>[AZURE.NOTE] O número de versão no caminho pode ser diferente, já que a versão do Storm instalada no cluster pode ser alterada no futuro.

Os scripts que estão nesse diretório são:

* **stormmeta_import.cmd**: importe todos os metadados do Storm do contêiner de armazenamento de cluster padrão para o Zookeeper.

* **stormmeta_export.cmd**: exporte todos os metadados do Storm do Zookeeper para o contêiner de armazenamento do cluster padrão.

* **stormmeta_delete.cmd**: exclua todos os metadados do Storm do Zookeeper.

Exportar uma importação permite que você mantenha os dados de ponto de verificação quando precisar excluir o cluster, mas quiser retomar o processamento do deslocamento atual no hub quando colocar um novo cluster novamente online.

> [AZURE.NOTE] Como os dados são mantidos para o contêiner de armazenamento padrão, o novo cluster **deverá** usar a mesma conta de armazenamento e o contêiner do cluster anterior.

## <a name="delete-your-cluster"></a>Excluir o cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="troubleshooting"></a>Solucionar problemas

Se você não vir os arquivos armazenados no local /devicedata (usando o comando `hadoop fs -ls /devicedata` ou o comando do Hive no Console da Consulta), use a interface do usuário Storm para procurar os erros retornados pelas topologias.

Para saber mais sobre como usar a interface do usuário Storm, consulte estes tópicos:

* Se você estiver usando um Storm __baseado em Linux__ no cluster HDInsight, consulte [Implantar e gerenciar as topologias do Apache Storm no HDInsight baseado em Linux](hdinsight-storm-deploy-monitor-topology-linux.md)

* Se você estiver usando um Storm __baseado no Windows__ no cluster HDInsight, consulte [Implantar e gerenciar as topologias do Apache Storm no HDInsight baseado no Windows](hdinsight-storm-deploy-monitor-topology-linux.md)

##<a name="next-steps"></a>Próximas etapas

* [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md)



<!--HONumber=Oct16_HO2-->


