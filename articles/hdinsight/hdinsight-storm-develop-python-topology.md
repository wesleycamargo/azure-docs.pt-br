---
title: Usar componentes do Python em uma topologia do Storm no HDInsight | Microsoft Docs
description: "Saiba como você pode usar componentes do Python com o Apache Storm no Azure HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: edd0ec4f-664d-4266-910c-6ecc94172ad8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 39a8eb9ce6a5363f541f02c33cd46d56fdabcae8
ms.lasthandoff: 04/12/2017


---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Desenvolver topologias do Apache Storm usando o Python no HDInsight

Saiba como usar componentes do Python com o Storm no Azure HDInsight. O Apache Storm dá suporte a várias linguagens, permitindo até a combinação de componentes de várias linguagens em uma topologia. A estrutura de fluxo (introduzida com o Storm 0.10.0) permite que você crie facilmente soluções que usam componentes do Python.

> [!IMPORTANT]
> As informações neste documento foram testadas usando o Storm no HDInsight 3.5. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, consulte [Substituição do HDInsight versão 3.3 e 3.4](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

O código para esse projeto está disponível em [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

## <a name="prerequisites"></a>Pré-requisitos

* Python 2.7 ou posterior

* Java JDK 1.8 ou superior

* Maven 3

* (Opcional) Um ambiente de desenvolvimento local do Storm. Um ambiente local do Storm só será necessário se você quiser executar a topologia localmente. Para obter mais informações, consulte [Configurar um ambiente de desenvolvimento](http://storm.apache.org/releases/1.0.1/Setting-up-development-environment.html).

## <a name="storm-multi-language-support"></a>Suporte a várias linguagens no Storm

O Storm foi projetado para trabalhar com componentes escritos usando qualquer linguagem de programação. Os componentes devem entender como trabalhar com a [definição do Thrift para Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Para o Python, é fornecido um módulo como parte do projeto do Apache Storm que permite interagir facilmente com o Storm. Você pode encontrar esse módulo em [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

O Apache Storm é um processo Java que é executado na JVM (Máquina Virtual Java). Componentes escritos em outras linguagens são executados como subprocessos. O Storm comunica-se com esses subprocessos usando mensagens JSON enviadas por stdin/stdout. Mais detalhes sobre a comunicação entre os componentes podem ser encontrados na documentação sobre [Protocolo de várias linguagens](https://storm.apache.org/documentation/Multilang-protocol.html) .

## <a name="python-and-the-flux-framework"></a>Python e a estrutura do Flux

A estrutura do Flux permite que você defina topologias Storm separadamente dos componentes. Enquanto os componentes neste exemplo são escritos usando o Python, a topologia é definida usando YAML. O texto a seguir é um exemplo de como um componente do Python é referenciado no documento YAML:

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

A classe `FluxShellSpout` é usada para iniciar o script `sentencespout.py` que implementa o spout.

O Flux espera que os scripts de Python estejam no diretório `/resources`, dentro do arquivo jar que contém a topologia. Assim, este exemplo armazena os scripts do Python no diretório `/multilang/resources`. O `pom.xml` inclui esse arquivo usando o XML a seguir:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Conforme mencionado anteriormente, há um arquivo `storm.py` que implementa a definição do Thrift para o Storm. A estrutura do Flux inclui isso automaticamente quando o projeto é compilado, portanto, você não precisa se preocupar em incluí-lo.

## <a name="build-the-project"></a>Compilar o projeto

Da raiz do projeto, use o seguinte comando:

```bash
mvn clean compile package
```

Esse comando cria um arquivo `target/WordCount-1.0-SNAPSHOT.jar` que contém a topologia compilada.

## <a name="run-the-topology-locally"></a>Executar a topologia localmente

Para executar a topologia localmente, use o seguinte comando:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]
> Esse comando requer um ambiente de desenvolvimento local do Storm. Para obter mais informações, consulte [Configurar um ambiente de desenvolvimento](http://storm.apache.org/releases/1.0.1/Setting-up-development-environment.html)

Uma vez que a topologia é iniciada, ela emite informações para o console local semelhantes ao texto a seguir:

```
24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
^C24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}
```

Para interromper a topologia, use Ctrl+c.

## <a name="run-the-topology-on-hdinsight"></a>Executar a topologia no HDInsight

1. Use o comando a seguir para copiar o arquivo `WordCount-1.0-SNAPSHOT.jar` para o Storm no cluster HDInsight:

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Substitua `sshuser` pelo usuário SSH para seu cluster. Substitua `mycluster` pelo nome do cluster. Pode ser solicitado que você insira a senha do usuário SSH.

    Para obter mais informações sobre como usar o SSH e o SCP, confira [Usar o SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Depois que o arquivo for carregado, conecte-se ao cluster usando SSH:

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Na sessão de SSH, use o seguinte comando para iniciar a topologia no cluster:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. Você pode usar a interface do usuário do Storm para exibir a topologia no cluster. Essa interface do usuário do Storm está localizada em https://mycluster.azurehdinsight.net/stormui. Substitua `mycluster` pelo nome do cluster.

> [!NOTE]
> Após ser iniciada, uma topologia do Storm é executada até ser interrompida. Para interromper a topologia, é possível usar um dos métodos a seguir:
>
> * O comando `storm kill TOPOLOGYNAME` da linha de comando
> * O botão **Encerrar** na interface do usuário Storm.


## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes documentos para conhecer outras maneiras de usar o Python com o HDInsight:

* [Como usar o Python para trabalhos de MapReduce de streaming](hdinsight-hadoop-streaming-python.md)
* [Como usar UDF (Funções Definidas pelo Usuário) do Python no Pig e Hive](hdinsight-python.md)

