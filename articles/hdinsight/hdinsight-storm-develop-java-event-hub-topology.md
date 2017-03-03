---
title: Processar eventos de Hubs de Eventos com o Storm no HDInsight usando o Java | Microsoft Docs
description: Saiba como processar dados de Hubs de Eventos com uma topologia Storm Java criada com o Maven.
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 453fa7b0-c8a6-413e-8747-3ac3b71bed86
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 110f3aa9ce4848c9350ea2e560205aa762decf7a
ms.openlocfilehash: 85f56e223210f14615f7e4e1c87e35111b238aac
ms.lasthandoff: 02/21/2017


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight (Java)

Saiba como usar Hubs de Eventos do Azure com o Storm no HDInsight. Este exemplo usa componentes baseados em Java para ler e gravar dados em Hubs de Eventos do Azure.

Os Hubs de Eventos do Azure permitem processar grandes quantidades de dados de sites, aplicativos e dispositivos. O spout de Hub de Eventos facilita o uso do Apache Storm no HDInsight para analisar esses dados em tempo real. Você pode também gravar dados no Hub de Eventos usando o bolt dos Hubs de Eventos.

## <a name="prerequisites"></a>Pré-requisitos

* Um Apache Storm no cluster HDInsight versão 3.5. Para obter mais informações, confira [Introdução ao Storm no cluster HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).
    
    > [!IMPORTANT]
    > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, veja [Substituição do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Um [Hub de Eventos do Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* [Oracle Java Developer Kit (JDK) versão 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou equivalente, como [OpenJDK](http://openjdk.java.net/).

* [Maven](https://maven.apache.org/download.cgi): o Maven é um sistema de criação de projetos para projetos Java.

* Um editor de texto ou um IDE (ambiente de desenvolvimento integrado).
  
  > [!NOTE]
  > Seu editor ou IDE pode ter uma funcionalidade específica para trabalhar com o Maven que não é abordada neste documento. Para obter informações sobre os recursos do seu ambiente de edição, consulte a documentação do produto que você está usando.
  
  * Um cliente SSH. Para obter mais informações, consulte um dos documentos a seguir:
    
    * [Use SSH com Hadoop baseado em Linux no HDInsight do Linux, Unix, OS X e Bash no Windows 10](hdinsight-hadoop-linux-use-ssh-unix.md).

    * [Usar SSH (PuTTY) com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

* Um cliente SCP. O comando `scp` é fornecido com todos os sistemas Linux, Unix e OS X (incluindo Bash no Windows 10). Para versões do Windows que não têm o comando `scp`, recomendamos PSCP. O PSCP está disponível na [página de download do PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a name="understanding-the-example"></a>Compreendendo o exemplo

O exemplo [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) contém duas topologias:

**com.microsoft.example.EventHubWriter** grava dados aleatórios em um Hub de Eventos do Azure. Os dados são gerados por um spout e são compostos por uma ID de dispositivo aleatório e por um valor de dispositivo. Dessa forma, ele está simulando um hardware que emite uma ID de cadeia de  caracteres e um valor numérico.

**com.microsoft.example.EventHubReader** lê dados do Hub de Eventos e os armazena no armazenamento padrão dos clusters no diretório /devicedata.

Os dados são formatados como um documento JSON antes de serem gravados no Hub de Eventos. Quando eles forem lidos pelo leitor, serão analisados pelo JSON em tuplas. O formato JSON é o seguinte:

    { "deviceId": "unique identifier", "deviceValue": some value }

### <a name="project-configuration"></a>Configuração do projeto

O arquivo **POM.xml** contém informações de configuração deste projeto Maven. As partes interessantes são:

#### <a name="the-eventhubs-storm-spout-dependency"></a>A dependência do Spout Storm dos Hubs de Eventos

```xml
<dependency>
    <groupId>com.microsoft</groupId>
    <artifactId>eventhubs</artifactId>
    <version>1.0.2</version>
</dependency>
```

Esse xml define uma dependência ao pacote eventhubs, que contém um spout para leitura dos Hubs de Eventos e um bolt para a gravação neles.

> [!NOTE]
> A instalação desse pacote é descrita posteriormente neste documento.

#### <a name="the-hdfsbolt-and-wasb-components"></a>Os componentes HdfsBolt e WASB

Normalmente, o HdfsBolt é usado para armazenar dados para o HDFS (Sistema de Arquivos Distribuído do Hadoop). No entanto, os clusters HDInsight usam o armazenamento do Azure (WASB) como o repositório de dados padrão, portanto precisamos carregar vários componentes que permitam ao HdfsBolt entender o sistema de arquivos WASB.

```xml
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
    <version>${storm.version}</version>
</dependency>
<!--So HdfsBolt knows how to talk to WASB -->
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-client</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-hdfs</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-azure</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>${hadoop.version}</version>
    <exclusions>
    <exclusion>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
    </exclusion>
    </exclusions>
</dependency>
```

> [!NOTE]
> Ao trabalhar com uma versão anterior do HDInsight, como a versão 3.2, você precisará registrar esses componentes manualmente. Para obter um exemplo, consulte a ramificação [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) do repositório de exemplo.

#### <a name="the-maven-compiler-plugin"></a>O maven-compiler-plugin

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>2.3.2</version>
    <configuration>
    <source>1.8</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

Isso configura o projeto para gerar uma saída para Java 8, que é usado pelo HDInsight 3.5.

#### <a name="the-maven-shade-plugin"></a>O maven-shade-plugin

```xml
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
```

Isso configura a solução para empacotar a saída em um uber jar. O jar contém o código do projeto e as dependências necessárias. Também é usado para:

* Renomeie os arquivos de licença para as dependências.
* Exclua a segurança/as assinaturas.
* Verifique se as várias implementações da mesma interface estão mescladas em uma entrada.

Essas definições de configuração evitam erros em tempo de execução.

#### <a name="the-exec-maven-plugin"></a>O exec-maven-plugin

```xml
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
```

A configuração facilita a execução da topologia localmente em seu ambiente de desenvolvimento. Você pode executar a topologia localmente usando a sintaxe a seguir:

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

Por exemplo: `mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`.

#### <a name="the-resources-section"></a>A seção de recursos

```xml
<resources>
    <resource>
    <directory>${basedir}/conf</directory>
    <filtering>false</filtering>
    <includes>
        <include>EventHubs.properties</include>
    </includes>
    </resource>
</resources>
```

Essa configuração define recursos não Java que estão incluídos no projeto. Por exemplo, o arquivo **EventHubs.properties** contém informações usadas para a conexão a um Hub de Eventos do Azure.

## <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

As seguintes variáveis de ambiente podem ser definidas quando você instala o Java e o JDK em sua estação de trabalho de desenvolvimento. No entanto, você deve verificar se elas existem e se contêm os valores corretos para o seu sistema.

* **JAVA_HOME** - deve apontar para o diretório onde o JRE (Java runtime environment) está instalado. Por exemplo, em uma distribuição Unix ou Linux, ele deve ter um valor semelhante a `/usr/lib/jvm/java-7-oracle`. No Windows, ele teria um valor semelhante a `c:\Program Files (x86)\Java\jre1.7`
* **PATH** - deve conter os seguintes caminhos:
  
  * **JAVA_HOME** (ou o caminho equivalente)
  * **JAVA_HOME\bin** (ou o caminho equivalente)
  * O diretório onde o Maven está instalado

## <a name="download-and-register-the-eventhub-components"></a>Baixar e registrar os componentes EventHub

1. Baixe o `storm-eventhubs-1.0.2-jar-with-dependencies.jar` de [https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar](https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar). Esse arquivo contém um componente spout e bolt para leitura e gravação de EventHubs.

2. Use o comando a seguir para registrar os componentes no repositório maven local:
    
        mvn install:install-file -Dfile=storm-eventhubs-1.0.2-jar-with-dependencies.jar -DgroupId=com.microsoft -DartifactId=eventhubs -Dversion=1.0.2 -Dpackaging=jar
    
    Modifique o parâmetro `-Dfile=` para apontar para o local do arquivo baixado.

    Este comando instala o arquivo no repositório Maven local, no qual ele pode ser encontrado em tempo de compilação pelo Maven.

## <a name="configure-event-hub"></a>Configurar o Hub de Eventos

Hubs de Eventos é a fonte de dados para este exemplo. Use as etapas a seguir para criar um novo hub de eventos.

1. No [Portal Clássico do Azure](https://manage.windowsazure.com), selecione **NOVO** > **Barramento de Serviço** > **Hub de Eventos** > **Criação Personalizada**.

2. Na tela **Adicionar um novo Hub de Eventos**, insira um **Nome do Hub de Eventos**. Selecione a **Região** para criar o hub e, em seguida, crie um namespace ou selecione um existente. Por fim, clique na **Seta** para continuar.
   
    ![página 1 do assistente](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)
   
   > [!NOTE]
   > Selecione o mesmo **Local** do seu Storm no servidor HDInsight para reduzir a latência e os custos.

3. Na tela **Configurar o Hub de Eventos**, insira os valores de **Contagem de partições** e **Retenção de Mensagem**. Para este exemplo, use uma contagem de partições de 10 e uma retenção de mensagens de 1. Anote a contagem de partições, pois você precisará desse valor posteriormente.
   
    ![página 2 do assistente](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

4. Depois que o hub de eventos tiver sido criado, selecione o namespace, selecione **Hubs de Eventos**e, em seguida, selecione o hub de eventos criado anteriormente.
5. Selecione **Configurar**e crie duas novas políticas de acesso usando as informações a seguir:
   
    <table>
    <tr><th>Nome</th><th>Permissões</th></tr>
    <tr><td>Gravador</td><td>Enviar</td></tr>
    <tr><td>Leitor</td><td>Escutar</td></tr>
    </table>
   
    Depois de criar permissões, selecione o ícone **Salvar** na parte inferior da página. Essas políticas de acesso compartilhado são usadas para ler e gravar no Hub de Eventos.
   
    ![políticas](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

6. Depois de salvar as políticas, use o **Gerador de chave de acesso compartilhada** na parte inferior da página para recuperar a chave para as políticas **gravador** e **leitor**. Salve essas chaves.

## <a name="download-and-build-the-project"></a>Baixar e compilar o projeto

1. Baixe o projeto do GitHub: [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub). Você pode baixar o pacote como um arquivo zip, ou usar o [git](https://git-scm.com/) para clonar o projeto localmente.

2. Use o seguinte para criar e empacotar o projeto:
   
        mvn package
   
    Esse comando baixa as dependências exigidas, as compila e então empacota o projeto. A saída é armazenada no diretório **/target** como **EventHubExample-1.0-SNAPSHOT.jar**.

## <a name="deploy-the-topologies"></a>Implantar as topologias

O jar criado por este projeto contém duas topologias; **com.microsoft.example.EventHubWriter** e **com.microsoft.example.EventHubReader**. A topologia EventHubWriter deve ser iniciada primeiro, já que grava eventos no Hub de Eventos que são lidos pelo EventHubReader.

1. Use o SCP para copiar o pacote jar para seu cluster HDInsight. Substitua USERNAME pelo usuário SSH para seu cluster. Substitua CLUSTERNAME pelo nome do seu cluster HDInsight:
   
        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.
   
    Se você tiver usado uma senha para sua conta SSH, será necessário inserir a senha. Se você tiver usado uma chave SSH com a conta, talvez seja necessário usar o parâmetro `-i` para especificar o caminho para o arquivo de chave. Por exemplo: `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`.
   
   > [!NOTE]
   > Se o cliente for uma estação de trabalho do Windows, talvez você não tenha um comando SCP instalado. Recomendamos o PSCP, que pode ser baixado da [página de download do PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
   
    Esse comando copia o arquivo para o diretório base do usuário SSH no cluster.

2. Após a conclusão do carregamento do arquivo, use o SSH para se conectar ao cluster HDInsight. Substitua o **USERNAME** pelo nome do seu logon SSH. Substitua o **CLUSTERNAME** pelo nome do seu cluster HDInsight:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
   > [!NOTE]
   > Se você tiver usado uma senha para sua conta SSH, será necessário inserir a senha. Se você tiver usado uma chave SSH com a conta, talvez seja necessário usar o parâmetro `-i` para especificar o caminho para o arquivo de chave. O exemplo a seguir carregará a chave privada de `~/.ssh/id_rsa`:
   > 
   > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Se você estiver usando o PuTTY, insira `CLUSTERNAME-ssh.azurehdinsight.net` no campo **Nome do Host (ou endereço IP)** e clique em **Abrir** para se conectar. Você recebe uma solicitação para inserir seu nome de conta SSH.
   
   > [!NOTE]
   > Se você tiver usado uma senha para sua conta SSH, será necessário inserir a senha. Se você tiver usado uma chave SSH com a conta, talvez seja necessário usar as etapas a seguir para selecionar a chave:
   > 
   > 1. Em **Categoria**, expanda **Conexão**, expanda **SSH** e selecione **Autenticação**.
   > 2. Clique em **Procurar** e selecione o arquivo .ppk que contém a chave privada.
   > 3. Clique em **Abrir** para se conectar.

3. Use o comando a seguir para iniciar as topologias:
   
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader
   
    Esses comandos iniciam as topologias usando os nomes amigáveis de "reader" e "writer".

4. Aguarde um minuto para as topologias gerarem dados. Use o comando a seguir para verificar que os dados sejam gravados no armazenamento do HDInsight:
   
        hadoop fs -ls /devicedata
   
    Esse comando retorna informações semelhantes ao texto a seguir:
   
        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt
   
   > [!NOTE]
   > Alguns arquivos podem mostrar um tamanho 0, já que foram criados pelo EventHubReader, mas os dados ainda não foram armazenados neles.
   
    Você pode exibir o conteúdo desses arquivos usando o comando a seguir:
   
        hadoop fs -text /devicedata/*.txt
   
    Isso retorna dados semelhantes ao texto a seguir:
   
        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574
   
    A primeira coluna contém o valor da ID do dispositivo e a segunda coluna é o valor do dispositivo.

5. Use os comandos a seguir para parar as topologias:
   
        storm kill reader
        storm kill writer

## <a name="delete-your-cluster"></a>Excluir o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshooting"></a>Solucionar problemas

Se você não vir arquivos no diretório /devicedata, use a interface do usuário do Storm para procurar erros retornados pelas topologias.

Para saber mais sobre como usar a interface do usuário Storm, consulte estes tópicos:

* Se você estiver usando um Storm **baseado em Linux** no cluster HDInsight, consulte [Implantar e gerenciar as topologias do Apache Storm no HDInsight baseado em Linux](hdinsight-storm-deploy-monitor-topology-linux.md)

* Se você estiver usando um Storm **baseado no Windows** no cluster HDInsight, consulte [Implantar e gerenciar as topologias do Apache Storm no HDInsight baseado no Windows](hdinsight-storm-deploy-monitor-topology-linux.md)

## <a name="next-steps"></a>Próximas etapas

* [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md)


