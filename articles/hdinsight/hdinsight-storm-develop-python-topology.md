<properties
   pageTitle="Usar componentes do Python em uma topologia do Storm no HDinsight | Microsoft Azure"
   description="Saiba como você pode usar componentes do Python com o Apache Storm no Azure HDInsight. Você aprenderá a usar componentes do Python de uma topologia do Storm baseada em Java e de uma baseada em Clojure."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/19/2016"
   ms.author="larryfr"/>

#Desenvolver topologias do Apache Storm usando o Python no HDInsight

O Apache Storm dá suporte a várias linguagens, permitindo até a combinação de componentes de várias linguagens em uma topologia. Neste documento, você aprenderá a usar componentes do Python em suas topologias do Java e do Storm com base em Clojure no HDInsight.

##Pré-requisitos

* Python 2.7 ou posterior

* Java JDK 1.7 ou posterior

* [Leiningen](http://leiningen.org/)

##Suporte a várias linguagens no Storm

O Storm foi desenvolvido para trabalhar com componentes escritos usando qualquer linguagem de programação. No entanto, isso requer que os componentes entendam como trabalhar com a [definição Thrift para Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Para o Python, é fornecido um módulo como parte do projeto do Apache Storm que permite interagir facilmente com o Storm. Você pode encontrar esse módulo em [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Como o Apache Storm é um processo do Java que é executado na JVM (Máquina Virtual Java), componentes escritos em outras linguagens são executados como subprocessos. Os bits do Storm em execução na JVM se comunicam com esses subprocessos usando mensagens JSON enviadas por stdin/stdout. Mais detalhes sobre a comunicação entre os componentes podem ser encontrados na documentação sobre [Protocolo de várias linguagens](https://storm.apache.org/documentation/Multilang-protocol.html).

###O módulo do Storm

O módulo do Storm (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py,) fornece os bits necessários para criar componentes do Python que funcionam com o Storm.

Isso fornece itens como `storm.emit` para emitir tuplas e `storm.logInfo` para gravar nos logs. Recomendo que você leia esse arquivo e entenda o que ele oferece.

##Desafios

Usando o módulo __storm.py__, você pode criar spouts do Python que consomem dados e bolts que processam dados. No entanto, a definição de topologia geral do Storm que conecta a comunicação entre os componentes ainda é gravada usando Java ou Clojure. Adicionalmente, se usar o Java, você também deverá criar componentes do Java que atuem como uma interface para os componentes do Python.

Além disso, como os clusters do Storm são executados em modo distribuído, você deve garantir que os módulos necessários para os componentes do Python estejam disponíveis em todos os nós de trabalhador no cluster. O Storm não fornece uma forma fácil de fazer isso para recursos de várias linguagens. Você precisa incluir todas as dependências como parte do arquivo jar para a topologia ou instalar manualmente as dependências em cada nó de trabalhador no cluster.

Há alguns projetos que tentam superar essas limitações, como [Pyleus](https://github.com/Yelp/pyleus) e [Streamparse](https://github.com/Parsely/streamparse). Embora ambos possam ser executados em clusters HDInsight baseados em Linux, eles não são o foco principal deste documento, pois exigem personalizações durante a configuração do cluster e não são totalmente testados em clusters HDInsight. Observações sobre como usar essas estruturas com o HDInsight são incluídas ao fim deste documento.

###Java vs. Definição de topologia Clojure

Dos dois métodos de definição de uma topologia, o Clojure é, de longe, o mais fácil/claro, pois você pode referenciar diretamente os componentes do Python na definição da topologia. Para definições de topologia baseadas em Java, você também deve definir componentes do Java que lidam com itens como declarar os campos nas tuplas retornadas dos componentes do Python.

Ambos os métodos são descritos neste documento, juntamente com projetos de exemplo.

##Componentes do Python com uma topologia do Java

> [AZURE.NOTE] Este exemplo está disponível em [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount), no diretório __JavaTopology__. Esse é um projeto com base em Maven. Se você não estiver familiarizado com o Maven, consulte [Desenvolver topologias baseadas em Java com o Apache Storm no HDInsight](hdinsight-storm-develop-java-topology.md) para obter mais informações sobre como criar um projeto do Maven para uma topologia do Storm.

Uma topologia baseada em Java que usa o Python (ou outros componentes de linguagem da JVM) inicialmente parece usar componentes do Java. Porém, se examinar cada um dos spouts/bolts do Java, você verá código semelhante ao seguinte:

    public SplitBolt() {
        super("python", "countbolt.py");
    }

Esse é o local em que o Java invoca o Python e executa o script que contém a lógica de bolt real. Os spouts/bolts do Java (para esse exemplo) simplesmente declaram os campos na tupla que será emitida pelo componente subjacente do Python.

Os arquivos reais do Python são armazenados no diretório `/multilang/resources` neste exemplo. O diretório `/multilang` é referenciado em __POM__:

<resources> <resource> <!-- Where the Python bits are kept --> <directory>${basedir}/multilang</directory> </resource> </resources>

Isso inclui todos os arquivos na pasta `/multilang` no jar que serão criados com base nesse projeto.

> [AZURE.IMPORTANT] Observe que isso só especifica o diretório `/multilang`, e não `/multilang/resources`. O Storm espera recursos não JVM em um diretório `resources`. Portanto, isso já é feito internamente. Colocar componentes nessa pasta permite referenciar apenas por nome no código Java. Por exemplo: `super("python", "countbolt.py");`. Outra maneira de entender isso é que o Storm vê o diretório `resources` como a raiz (/) ao acessar recursos de várias linguagens.
>
> Para este projeto de exemplo, o módulo `storm.py` está incluído no diretório `/multilang/resources`.

###Compilar e executar o projeto

Para executar esse projeto localmente, use o seguinte comando do Maven para compilar e executar em modo local:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Use ctrl+c para interromper o processo.

Para implantar o projeto em um cluster do HDInsight executando o Apache Storm, use as seguintes etapas:

1. Compile um grande jar:

        mvn package

    Isso criará um arquivo chamado __WordCount--1.0-SNAPSHOT.jar__ no diretório `/target` para esse projeto.

2. Carregue o arquivo jar no cluster do Hadoop usando um dos seguintes métodos:

    * Para clusters do HDInsight __baseados em Linux__: use `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` para copiar o arquivo jar para o cluster, substituindo USERNAME por seu nome de usuário do SSH e CLUSTERNAME pelo nome do cluster do HDInsight.

        Quando o carregamento do arquivo terminar, conecte-se ao cluster usando o SSH e comece a usar a topologia `storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`

    * Para clusters do HDInsight __baseados no Windows__: conecte-se ao Painel do Storm indo para HTTPS://CLUSTERNAME.azurehdinsight.net/ em seu navegador. Substitua CLUSTERNAME pelo nome de seu cluster do HDInsight e forneça o nome do administrador e a senha, quando solicitado.

        Usando o formulário, execute as seguintes ações:

        * __Arquivo jar__: selecione __Procurar__ e, em seguida, selecione o arquivo __WordCount-1.0-SNAPSHOT.jar__
        * __Nome da Classe__: insira `com.microsoft.example.WordCount`
        * __Parâmetros Adicionais__: insira um nome amigável, como `wordcount`, para identificar a topologia

        Por fim, selecione __Enviar__ para iniciar a topologia.

> [AZURE.NOTE] Após ser iniciada, uma topologia do Storm é executada até ser interrompida (eliminada). Para interromper a topologia, use o comando `storm kill TOPOLOGYNAME` na linha de comando (sessão do SSH para um cluster do Linux, por exemplo) ou, usando a interface do usuário do Storm, selecione a topologia e, em seguida, selecione o botão __Interromper__.

##Componentes do Python com uma topologia do Clojure

> [AZURE.NOTE] Este exemplo está disponível em [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount), no diretório __ClojureTopology__.

Essa topologia foi criada usando [Leiningen](http://leiningen.org) para [criar um novo projeto do Clojure](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project). Depois disso, foram feitas as seguintes modificações no projeto gerado automaticamente:

* __project.clj__: foram adicionadas dependências para o Storm e exclusões para os itens que podem causar problemas quando implantados no servidor do HDInsight.
* __recursos/recursos__: o Leiningen cria um diretório `resources` padrão. No entanto, os arquivos armazenados nele parecem ser adicionados à raiz do arquivo jar criado com base nesse projeto, e o Storm espera arquivos em um subdiretório chamado `resources`. Portanto, foi adicionado um subdiretório, e os arquivos do Python são armazenados em `resources/resources`. Em tempo de execução, isso será tratado como a raiz (/) para acessar os componentes do Python.
* __src/wordcount/core.clj__: esse arquivo contém a definição de topologia e é referenciado do arquivo __project.clj__. Para obter mais informações sobre como usar o Clojure para definir uma topologia do Storm, consulte [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html).

###Compilar e executar o projeto

__Para compilar e executar o projeto localmente__, use o seguinte comando:

    lein do clean, run

Para interromper a topologia, use __Ctrl+C__.

__Para compilar um uberjar e implantá-lo no HDInsight__, use as seguintes etapas:

1. Crie um uberjar contendo a topologia e as dependências necessárias:

        lein uberjar

    Isso criará um novo arquivo chamado `wordcount-1.0-SNAPSHOT.jar` no diretório `target\uberjar+uberjar`.
    
2. Use um dos seguintes métodos para implantar e executar a topologia para um cluster do HDInsight:

    * __HDInsight baseado em Linux__
    
        1. Copie o arquivo para o nó principal do cluster do HDInsight usando `scp`. Por exemplo:
        
                scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
                
            Substitua USERNAME por um usuário do SSH para seu cluster e CLUSTERNAME pelo nome do cluster do HDInsight.
            
        2. Quando o arquivo for copiado para o cluster, use o SSH para se conectar ao cluster e enviar o trabalho. Para obter informações sobre como usar o SSH com o HDInsight, consulte um destes procedimentos:
        
            * [Usar SSH com HDInsight baseado em Linux no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
            * [Usar SSH com HDInsight baseado em Linux no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
            
        3. Uma vez conectado, use o seguinte para iniciar a topologia:
        
                storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
    
    * __HDInsight baseado em Windows__
    
        1. Conecte-se ao Painel do Storm indo para HTTPS://CLUSTERNAME.azurehdinsight.net/ no navegador. Substitua CLUSTERNAME pelo nome de seu cluster do HDInsight e forneça o nome do administrador e a senha, quando solicitado.

        2. Usando o formulário, execute as seguintes ações:

            * __Arquivo jar__: selecione __Procurar__ e, em seguida, selecione o arquivo __wordcount-1.0-SNAPSHOT.jar__
            * __Nome da Classe__: insira `wordcount.core`
            * __Parâmetros Adicionais__: insira um nome amigável, como `wordcount`, para identificar a topologia

            Por fim, selecione __Enviar__ para iniciar a topologia.

> [AZURE.NOTE] Após ser iniciada, uma topologia do Storm é executada até ser interrompida (eliminada). Para interromper a topologia, use o comando `storm kill TOPOLOGYNAME` na linha de comando (sessão do SSH para um cluster do Linux) ou, usando a interface do usuário do Storm, selecione a topologia e, em seguida, selecione o botão __Interromper__.

##Estrutura Pyleus

[Pyleus](https://github.com/Yelp/pyleus) é uma estrutura que tenta facilitar o uso do Python com o Storm, fornecendo o seguinte:

* __Definições de topologia baseada em YAML__: isso fornece uma maneira fácil de definir a topologia, que não requer conhecimento do Java ou do Clojure
* __Serializador com base em MessagePack__: o MessagePack é usado como a serialização padrão, em vez de JSON. Isso pode resultar em mensagens mais rápidas entre os componentes
* __Gerenciamento de dependência__: Virtualenv é usado para garantir que as dependências do Python sejam implantadas em todos os nós de trabalhador. Isso requer que o Virtualenv seja instalado em nós de trabalhador

> [AZURE.IMPORTANT] O Pyleus requer o Storm em seu ambiente de desenvolvimento. O uso da distribuição base do Apache Storm 0.9.3 parece resultar em jars são incompatíveis com a versão do Storm fornecida com o HDInsight. Portanto, as etapas a seguir usam o cluster do HDInsight como o ambiente de desenvolvimento.

Você pode compilar com êxito as topologias do Pyleus de exemplo, usando o nó principal do HDInsight como o ambiente de compilação:

1. Ao provisionar um novo cluster do Storm no HDInsight, você deve garantir que o Virtualenv do Python esteja presente nos nós de cluster. Ao criar um novo cluster do HDInsight baseado em Linux, use as seguintes configurações de Ação de Script com [personalização de Cluster](hdinsight-hadoop-customize-cluster.md):

    * __Nome__: basta fornecer um nome amigável aqui
    * \_ URI de Script \_\_: use `https://hditutorialdata.blob.core.windows.net/customizecluster/pythonvirtualenv.sh` como o valor. Esse script instalará o Virtualenv do Python nos nós.
    
        > [AZURE.NOTE] Ele também criará alguns diretórios que são usados pela estrutura Streamparse mais adiante neste documento.
        
    * __Nimbus__: verifique essa entrada para que o script seja aplicado aos nós Nimbus (principais).
    * __Supervisor__: verifique essa entrada para que o script seja aplicado aos nós de supervisor (trabalhador)
    
    Deixe as outras entradas em branco.

1. Quando o cluster tiver sido criado, conecte-se usando o SSH:

    * [Usar SSH com HDInsight baseado em Linux no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Usar SSH com HDInsight baseado em Linux no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Na conexão do SSH, use o seguinte para criar um novo ambiente virtual e instalar o Pyleus:

        virtualenv pyleus_venv
        source pyleus_venv
        pip install pyleus

3. Em seguida, baixe o repositório do git do Pyleus e compile o exemplo de WordCount:

        sudo apt-get install git
        git clone https://github.com/Yelp/pyleus.git
        pyleus build pyleus/examples/word_count/pyleus_topology.yaml
    
    Quando a compilação for concluída, você terá um novo arquivo chamado `word_count.jar` no diretório atual.
    
4. Para enviar a topologia ao cluster do Storm, use o seguinte comando:

        pyleus submit -n localhost word_count.jar
    
    O parâmetro `-n` especifica o host Nimbus. Como estamos no nó principal, podemos usar `localhost`.
    
    Você também pode usar o comando `pyleus` para executar outras ações do Storm. Use o seguinte para listar as topologias em execução e, em seguida, interrompa a topologia `word_count`:
    
        pyleus list -n localhost
        pyleus kill -n localhost word_count

##Estrutura Streamparse

[Streamparse](https://github.com/Parsely/streamparse) é uma estrutura que tenta facilitar o uso do Python com o Storm, fornecendo o seguinte:

* __Scaffolding__: isso permite que você crie facilmente o scaffolding para um projeto e modifique arquivos para adicionar sua lógica
* __Funções DSL do Clojure__: reduzem o nível de detalhes do uso de componentes do Python em uma definição de topologia do Clojure
* __Gerenciamento de dependência__: Virtualenv é usado para garantir que as dependências do Python sejam implantadas em todos os nós de trabalhador. Isso requer que o Virtualenv seja instalado em nós de trabalhador
* __Implantação remota__: Streamparse pode usar a automação do SSH para implantar componentes para nós de trabalhador e pode criará um túnel do SSH para se comunicar com o Nimbus. Portanto, você pode implantar facilmente de seu ambiente de desenvolvimento para um cluster baseado em Linux, como o HDInsight

> [AZURE.IMPORTANT] O Streamparse depende de componentes que esperam [sinais do Unix](https://en.wikipedia.org/wiki/Unix_signal), que não estão disponíveis no Windows. Seu ambiente de desenvolvimento deve ser Linux, Unix ou OS X, e o cluster do HDInsight deve ser baseado em Linux.

1. Ao provisionar um novo cluster do Storm no HDInsight, você deve garantir que o Virtualenv do Python esteja presente nos nós de cluster. Ao criar um novo cluster do HDInsight baseado em Linux, use as seguintes configurações de Ação de Script com [personalização de Cluster](hdinsight-hadoop-customize-cluster.md):

    * __Nome__: basta fornecer um nome amigável aqui
    * \_ URI de Script \_\_: use `https://hditutorialdata.blob.core.windows.net/customizecluster/pythonvirtualenv.sh` como o valor. Esse script instalará o Virtualenv do Python em nós, além de criar diretórios usados pelo Streamparse
    * __Nimbus__: verifique essa entrada para que o script seja aplicado aos nós Nimbus (principais).
    * __Supervisor__: verifique essa entrada para que o script seja aplicado aos nós de supervisor (trabalhador)
    
    Deixe as outras entradas em branco.
    
    > [AZURE.WARNING] Você também deve usar uma __chave pública__ para proteger o usuário do SSH para o cluster do HDInsight, de modo a implantar remotamente usando o Streamparse.
    >
    > Para obter mais informações sobre como usar chaves com o SSH no HDInsight, consulte um dos seguintes documentos:
    >
    > * [Usar SSH com HDInsight baseado em Linux no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [Usar SSH com HDInsight baseado em Linux no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Enquanto o cluster estiver provisionando, instale o Streamparse no ambiente de desenvolvimento usando o seguinte comando:

        pip install streamparse
        
3. Após instalar o Streamparse, use o seguinte comando para criar um projeto de exemplo:

        sparse quickstart wordcount
        
    Isso criará um novo diretório, chamado `wordcount`, e o populará com um projeto de Contagem de Palavras de exemplo.

4. Altere os diretórios para o diretório `wordcount` e inicie a topologia no modo local:

        cd wordcount
        sparse run

    Use Ctrl+C para interromper a topologia.

###Implantar a topologia

Quando o cluster do HDInsight baseado em Linux for criado, use as seguintes etapas para implantar a topologia no cluster:

1. Use o seguinte comando para localizar os nomes de domínio totalmente qualificados dos nós de trabalhador para o cluster:

        curl -u admin:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts | grep '"host_name" : "worker'
    
    Isso recuperará as informações de hosts do cluster, as enviará ao grep e retornará as entradas para os nós de trabalhador. Você deverá ver resultados semelhantes ao seguinte:
    
        "host_name" : "workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"
    
    Salve as informações de `"workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"`, que serão usadas na próxima etapa.

2. Abra o arquivo __config.json__ no diretório `wordcount` e altere as seguintes entradas:

    * __usuário__: defina-o como o nome da conta de usuário do SSH que você configurou para o cluster do HDInsight. Isso será usado para autenticação no cluster ao ser implantado o projeto
    * __nimbus__: defina-o como `CLUSTERNAME-ssh.azurehdinsight.net`. Substitua CLUSTERNAME com o nome do cluster. Isso é usado na comunicação com o nó do Nimbus, que é o nó principal do cluster
    * __trabalhadores__: popule a entrada de trabalhadores com os nomes de host dos nós de trabalhador recuperados usando curl. Por exemplo:
    
        ```
"workers": [
    "workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net",
    "workernode1.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"
    ]
        ```
    
    * __virtualenv\_root__: defina isso como "/virtualenv"
    
    Isso configura o projeto para o cluster do HDInsight, incluindo o diretório `/virtualenv` que foi criado durante o provisionamento pela ação de script.

4. Como o Streamparse implantando no HDInsight precisa encaminhar a autenticação por meio do nó principal para os trabalhadores, o `ssh-agent` deve ser iniciado em sua estação de trabalho. Na maioria dos sistemas operacionais, ele é iniciado automaticamente. Use o seguinte comando para verificar se ele está em execução:

        echo "$SSH_AUTH_SOCK"
    
    Isso retornará uma resposta semelhante à seguinte se o `ssh-agent` estiver em execução:
    
        /tmp/ssh-rfSUL1ldCldQ/agent.1792
    
    > [AZURE.NOTE] O caminho completo pode ser diferente, dependendo do sistema operacional. Por exemplo, no OS X, o caminho pode ser semelhante a `/private/tmp/com.apple.launchd.vq2rfuxaso/Listeners`. Porém, ele deverá retornar um caminho se o agente estiver em execução.
    
    Se nada for retornado, use o comando `ssh-agent` para iniciar o agente.
    
5. Verifique se que o agente sabe sobre a chave usada para autenticação no servidor do HDInsight. Use o seguinte comando para listar as chaves que estão disponíveis para o agente:

        ssh-add -L
    
    Isso retornará as chaves privadas que foram adicionadas ao agente. Você pode comparar os resultados ao conteúdo da chave privada gerada durante a criação de uma chave do SSH para autenticação no HDInsight.
    
    Se nenhuma informação for retornada ou se as informações retornadas não corresponderem à chave privada, use o seguinte para adicionar a chave privada ao agente:
    
        ssh-add /path/to/key/file
    
    Por exemplo: `ssh-add ~/.ssh/id_rsa`.

4. Você também deve configurar o SSH para que ele saiba que o encaminhamento deve ser usado para o cluster do HDInsight. Adicione o seguinte ao `~/.ssh/config`. Se esse arquivo não existir, crie-o e use o seguinte como conteúdo:

        Host *.azurehdinsight.net
          ForwardAgent yes
        
        Host *.internal.cloudapp.net
          ProxyCommand ssh CLUSTERNAME-ssh.azurehdinsight.net nc %h %p
    
    Substitua CLUSTERNAME com o nome do cluster HDInsight.
    
    Isso configura o agente do SSH em sua estação de trabalho para habilitar o encaminhamento de suas credenciais do SSH por meio de qualquer sistema *.azurehdinsight.net ao qual você se conecte. Nesse caso, trata-se do nó principal do cluster. Em seguida, ele configura o comando usado para funcionar como proxy do tráfego do SSH do nó principal para os nós de trabalhador individuais (internal.cloudapp.net). Isso permite que o Streamparse se conecte ao nó principal e, em seguida, a cada um dos nós de trabalhador, usando a autenticação de chave para sua conta do SSH.
    
5. Por fim, use o seguinte comando para enviar a topologia do ambiente de desenvolvimento local para o cluster do HDInsight:

        sparse submit
    
    Isso se conectará ao cluster do HDInsight, implantará a topologia e as dependências do Python e iniciará a topologia.

##Próximas etapas

Neste documento, você aprendeu a usar componentes do Python de uma topologia do Storm. Consulte os seguintes documentos para conhecer outras maneiras de usar o Python com o HDInsight:

* [Como usar o Python para trabalhos de MapReduce de streaming](hdinsight-hadoop-streaming-python.md)
* [Como usar UDF (Funções Definidas pelo Usuário) do Python no Pig e Hive](hdinsight-python.md)

<!---HONumber=AcomDC_0420_2016-->