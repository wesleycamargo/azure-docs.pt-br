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
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/13/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: 2e8ebbdab2be7bed224a67facec798820615bb22
ms.contentlocale: pt-br
ms.lasthandoff: 07/14/2017

---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight (Java)

Saiba como usar Hubs de Eventos do Azure com o Storm no HDInsight. Este exemplo usa componentes baseados em Java para ler e gravar dados em Hubs de Eventos do Azure.

Os Hubs de Eventos do Azure permitem processar grandes quantidades de dados de sites, aplicativos e dispositivos. O spout de Hub de Eventos facilita o uso do Apache Storm no HDInsight para analisar esses dados em tempo real. Você pode também gravar dados no Hub de Eventos usando o bolt dos Hubs de Eventos.

## <a name="prerequisites"></a>Pré-requisitos

* Um Apache Storm no cluster HDInsight versão 3.6. Para obter mais informações, confira [Introdução ao Storm no cluster HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).

    > [!IMPORTANT]
    > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Um [Hub de Eventos do Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* [Oracle JDK (Java Developer Kit) versão 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) ou equivalente, por exemplo [OpenJDK](http://openjdk.java.net/).

* [Maven](https://maven.apache.org/download.cgi): o Maven é um sistema de criação de projetos para projetos Java.

* Um editor de texto ou um IDE (ambiente de desenvolvimento integrado).

    > [!NOTE]
    > Seu editor ou IDE pode ter uma funcionalidade específica para trabalhar com o Maven que não é abordada neste documento. Para obter informações sobre os recursos do seu ambiente de edição, consulte a documentação do produto que você está usando.

    * Um cliente SSH. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* Os comandos `ssh` e `scp`. Eles são usados para copiar arquivos para o cluster do HDInsight. No Windows, você pode obtê-los por meio do Bash no Windows 10.

## <a name="understanding-the-example"></a>Compreendendo o exemplo

O exemplo [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) contém duas topologias:

A topologia `resources/writer.yaml` grava dados aleatórios em um Hub de Eventos do Azure. Os dados são gerados pelo componente `DeviceSpout` e são um valor de dispositivo e ID de dispositivo aleatório. Dessa forma, ele está simulando um hardware que emite uma ID de cadeia de  caracteres e um valor numérico.

A topologia do `resources/reader.yaml` lê dados do Hub de Eventos (os dados gravados pelo EventHubWriter,) analisa os dados JSON e, em seguida, registra os dados `deviceId` e `deviceValue`.

Os dados são formatados como um documento JSON antes de serem gravados no Hub de Eventos. Quando eles forem lidos pelo leitor, serão analisados pelo JSON em tuplas. O formato JSON é o seguinte:

    { "deviceId": "unique identifier", "deviceValue": some value }

### <a name="project-configuration"></a>Configuração do projeto

O arquivo `POM.xml` contém informações de configuração deste projeto Maven. As partes interessantes são:

#### <a name="event-hub-components"></a>Componentes do Hub de Eventos

O componente que lê e grava os Hubs de Eventos do Azure está localizado no [repositório do HDInsight](https://github.com/hdinsight/mvn-rep). As seguintes seções no arquivo `POM.xml` carregam os componentes desse repositório

```xml
<repositories>
    <repository>
        <id>hdinsight-examples</id>
        <url>http://raw.github.com/hdinsight/mvn-repo/master</url>
    </repository>
</repositories>
```

#### <a name="the-eventhubs-storm-spout-dependency"></a>A dependência do Spout Storm dos Hubs de Eventos

```xml
<dependency>
    <groupId>com.microsoft</groupId>
    <artifactId>eventhubs</artifactId>
    <version>${storm.eventhub.version}</version>
</dependency>
```

Esse xml define uma dependência ao pacote eventhubs, que contém um spout para leitura dos Hubs de Eventos e um bolt para a gravação neles.

```xml
</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

Esse xml configura o projeto para gerar uma saída para Java 8, que é usada pelo HDInsight 3.5 ou superior.

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

Esse xml configura a solução para empacotar a saída em um uber jar. O jar contém o código do projeto e as dependências necessárias. Também é usado para:

* Renomeie os arquivos de licença para as dependências.
* Exclua a segurança/as assinaturas.
* Verifique se as várias implementações da mesma interface estão mescladas em uma entrada.

Essas definições de configuração evitam erros em tempo de execução.

#### <a name="topology-definitions"></a>Definições de topologia

Este exemplo usa a estrutura [Fluxo](https://storm.apache.org/releases/1.1.0/flux.html). Essa estrutura usa YAML para definir as topologias. O principal benefício é que você não embute a topologia em código Java. Como a definição é YAML, você pode alterá-la antes de enviar a topologia sem precisar recompilar tudo.

__writer.yaml__:

```yaml
---
# Topology that reads from Event Hubs
name: "eventhubwriter"

components:
  # Configure the Event Hub spout
  - id: "eventhubbolt-config"
    className: "org.apache.storm.eventhubs.bolt.EventHubBoltConfig"
    constructorArgs:
      # These are populated from the .properties file when the topology is started
      - "${eventhub.write.policy.name}"
      - "${eventhub.write.policy.key}"
      - "${eventhub.namespace}"
      - "servicebus.windows.net"
      - "${eventhub.name}"

spouts:
  - id: "device-emulator-spout"
    className: "com.microsoft.example.DeviceSpout"
    parallelism: ${eventhub.partitions}

bolts:
  - id: "eventhub-bolt"
    className: "org.apache.storm.eventhubs.bolt.EventHubBolt"
    constructorArgs:
      - ref: "eventhubbolt-config" # config declared in components section
    # parallelism hint. This should be the same as the number of partitions for your Event Hub, so we read it from the dev.properties file passed at run time.
    parallelism: ${eventhub.partitions}

  # Log information
  - id: "log-bolt"
    className: "org.apache.storm.flux.wrappers.bolts.LogInfoBolt"
    parallelism: 1

# How data flows through the components
streams:
  - name: "spout -> eventhub" # just a string used for logging
    from: "device-emulator-spout"
    to: "eventhub-bolt"
    grouping:
        type: SHUFFLE

  - name: "spout -> logger"
    from: "device-emulator-spout"
    to: "log-bolt"
    grouping:
        type: SHUFFLE
```

__reader.yaml__:

```yaml
---
# Topology that reads from Event Hubs
name: "eventhubreader"

components:
  # Configure the Event Hub spout
  - id: "eventhubspout-config"
    className: "org.apache.storm.eventhubs.spout.EventHubSpoutConfig"
    constructorArgs:
      # These are populated from the .properties file when the topology is started
      - "${eventhub.read.policy.name}"
      - "${eventhub.read.policy.key}"
      - "${eventhub.namespace}"
      - "${eventhub.name}"
      - ${eventhub.partitions}

spouts:
  - id: "eventhub-spout"
    className: "org.apache.storm.eventhubs.spout.EventHubSpout"
    constructorArgs:
      - ref: "eventhubspout-config" # config declared in components section
    # parallelism hint. This should be the same as the number of partitions for your Event Hub, so we read it from the dev.properties file passed at run time.
    parallelism: ${eventhub.partitions}

bolts:
  # Log information
  - id: "log-bolt"
    className: "org.apache.storm.flux.wrappers.bolts.LogInfoBolt"
    parallelism: 1

  # Parses from JSON into tuples
  - id: "parser-bolt"
    className: "com.microsoft.example.ParserBolt"
    parallelism: ${eventhub.partitions}

# How data flows through the components
streams:
  - name: "spout -> parser" # just a string used for logging
    from: "eventhub-spout"
    to: "parser-bolt"
    grouping:
        type: SHUFFLE

  - name: "parser -> log-bolt"
    from: "parser-bolt"
    to: "log-bolt"
    grouping:
        type: SHUFFLE
```

#### <a name="tell-the-topology-about-event-hub"></a>Informar a topologia sobre o Hub de Eventos

No tempo de execução, o arquivo `dev.properties` é usado para passar a configuração de Hub de Eventos à topologia. O exemplo a seguir é o conteúdo padrão do arquivo:

```yaml
eventhub.write.policy.name: writer
eventhub.write.policy.key: your_key_here
eventhub.read.policy.name: reader
eventhub.read.policy.key: your_key_here
eventhub.namespace: your_namespace_here
eventhub.name: storm
eventhub.partitions: 2
```

## <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

As seguintes variáveis de ambiente podem ser definidas quando você instala o Java e o JDK em sua estação de trabalho de desenvolvimento. No entanto, você deve verificar se elas existem e se contêm os valores corretos para o seu sistema.

* **JAVA_HOME** - deve apontar para o diretório onde o JRE (Java runtime environment) está instalado. Por exemplo, em uma distribuição Unix ou Linux, ele deve ter um valor semelhante a `/usr/lib/jvm/java-7-oracle`. No Windows, ele teria um valor semelhante a `c:\Program Files (x86)\Java\jre1.7`
* **PATH** - deve conter os seguintes caminhos:

  * **JAVA_HOME** (ou o caminho equivalente)
  * **JAVA_HOME\bin** (ou o caminho equivalente)
  * O diretório onde o Maven está instalado

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

2. Modifique o arquivo `dev.properties` com a configuração para o Hub de Eventos.

3. Use o seguinte para criar e empacotar o projeto:

        mvn package

    Esse comando baixa as dependências exigidas, as compila e então empacota o projeto. A saída é armazenada no diretório **/target** como **EventHubExample-1.0-SNAPSHOT.jar**.

## <a name="test-locally"></a>Testar localmente

Uma vez que essas topologias somente leem e gravam em Hubs de Eventos, você poderá testá-las localmente se tiver um [ambiente de desenvolvimento do Storm](http://storm.apache.org/releases/current/Setting-up-development-environment.html). Use as etapas a seguir execução local no ambiente de desenvolvimento:

1. Execute o gravador:

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /writer.yaml --filter dev.properties

2. Execute o leitor:

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /reader.yaml --filter dev.properties

> [!TIP]
> * `--local`: execute a topologia em modo local (não distribuído).
> * `-R /writer.yaml`: carregue a definição de topologia do `resources` empacotada no jar. Se a topologia for um arquivo no sistema de arquivos local, especifique o caminho para ela como o último parâmetro.
> * `--filter dev.properties`: use o conteúdo de `dev.properties` para preencher os valores nas definições de topologia. Por exemplo: `${eventhub.read.policy.name}`.

A saída é registrada no console quando ao executar localmente. Use __Ctrl+C__ para interromper a topologia.

## <a name="deploy-the-topologies"></a>Implantar as topologias

1. Use o SCP para copiar o pacote jar para seu cluster HDInsight. Substitua USERNAME pelo usuário SSH para seu cluster. Substitua CLUSTERNAME pelo nome do seu cluster HDInsight:

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Se você tiver usado uma senha para sua conta SSH, será necessário inserir a senha. Se você tiver usado uma chave SSH com a conta, talvez seja necessário usar o parâmetro `-i` para especificar o caminho para o arquivo de chave. Por exemplo, `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

    Esse comando copia o arquivo para o diretório base do usuário SSH no cluster.

2. Após a conclusão do carregamento do arquivo, use o SSH para se conectar ao cluster HDInsight. Substitua o **USERNAME** pelo nome do seu logon SSH. Substitua o **CLUSTERNAME** pelo nome do seu cluster HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [!NOTE]
    > Se você tiver usado uma senha para sua conta SSH, será necessário inserir a senha. Se você tiver usado uma chave SSH com a conta, talvez seja necessário usar o parâmetro `-i` para especificar o caminho para o arquivo de chave. O exemplo a seguir carregará a chave privada de `~/.ssh/id_rsa`:
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. Use o comando a seguir para iniciar as topologias:

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties

    > [!TIP]
    > * `--remote`: envia a topologia para o serviço Nimbus, que inicia nos nós de trabalho no cluster.

4. Para exibir os dados registrados, vá para https://CLUSTERNAME.azurehdinsight.net/stormui, em que __CLUSTERNAME__ é o nome do cluster do HDInsight. Selecione as topologias e faça drill down até os componentes. Selecione a entrada __porta__ para uma instância de um componente para exibir informações registradas.

5. Use os comandos a seguir para parar as topologias:

        storm kill reader
        storm kill writer

## <a name="delete-your-cluster"></a>Excluir o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Próximas etapas

* [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md)

