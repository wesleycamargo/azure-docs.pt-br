---
title: Instalar o R GNU no HDInsight baseado em Linux | Microsoft Docs
description: Saiba como instalar e usar R para personalizar os clusters do Hadoop baseados em Linux.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 7b758492-87bf-4d82-8b8c-1664e7d177bd
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfaade8249a643b77f3d7fdf466eb5ba38143f18
ms.openlocfilehash: 9dd06f04344c1ea8e4c1511c8ea05b7c17b3329b
ms.lasthandoff: 03/01/2017

---
# <a name="install-and-use-gnu-r-on-linux-based-hdinsight-hadoop-clusters"></a>Instalar e usar o R GNU em clusters Hadoop do HDInsight baseados em Linux

Você pode instalar o R GNU em qualquer tipo de cluster no Hadoop do HDInsight usando a personalização de cluster **Ação de Script**. Os analistas e cientistas de dados podem usar R para processar grandes quantidades de dados em clusters Hadoop implantados no HDInsight.

> [!WARNING]
> Este documento fornece instruções de instalação do R GNU no HDInsight como uma ação personalizada. Ele __não__ fornece os mesmos recursos que o __Microsoft R Server no HDInsight__. É recomendável usar o Servidor R no HDInsight em vez de seguir as etapas neste documento.
>
> Para obter informações sobre como usar o Servidor R no HDInsight, consulte [Aprenda a usar o Servidor R no HDInsight](hdinsight-hadoop-r-server-get-started.md). 

## <a name="what-is-r"></a>O que é R?

O <a href="http://www.r-project.org/" target="_blank">Projeto R para computação de estatísticas</a> é uma linguagem de software livre e um ambiente de computação de estatísticas. O R fornece centenas de funções estatísticas de compilação e uma linguagem própria de programação que combina aspectos da programação funcional e orientada a objeto. Ele também fornece recursos abrangentes de gráficos. O R é o ambiente de programação preferencial para a maioria dos profissionais estatísticos e cientistas em uma ampla variedade de campos.

Os scripts R podem ser executados em clusters Hadoop no HDInsight que foram personalizados usando a ação de Script quando criado para instalar o ambiente de R. O R é compatível com o armazenamento de Blob do Azure (WASB) para que os dados armazenados nele possam ser processados usando R no HDInsight.

## <a name="what-the-script-does"></a>O que o script faz

A ação de script usada para instalar o R em seu cluster HDInsight instala os seguintes pacotes Ubuntu, que fornecem uma instalação básica do R:

* [r-base](http://packages.ubuntu.com/precise/r-base): pacote R GNU base
* [r-base-dev](http://packages.ubuntu.com/precise/r-base-dev): pacotes R GNU auxiliares

Os seguintes pacotes RHadoop também são instalados, os quais fornecem integração com o MapReduce e o HDFS:

* [rmr2](https://github.com/RevolutionAnalytics/rmr2): permite que os desenvolvedores do R usem o MapReduce do Hadoop
* [rhdfs](https://github.com/RevolutionAnalytics/rhdfs): permite que desenvolvedores do R usem HDFS do Hadoop (WASB para HDInsight)

Além disso, os seguintes pacotes R são instalados:

| Pacote R | O que ele oferece |
| --- | --- |
| [rJava](https://cran.r-project.org/web/packages/rJava/index.html) |R de nível baixo para interface Java. |
| [Rcpp](https://cran.r-project.org/web/packages/Rcpp/index.html) |Integração entre R e C++. |
| [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html) |Serializar/desserializar objetos do R para JSON |
| [bitops](https://cran.r-project.org/web/packages/bitops/index.html) |Funções para operações bit a bit em vetores de número inteiro. |
| [digest](https://cran.r-project.org/web/packages/digest/index.html) |Crie resumos de hash criptográfico de objetos em R. |
| [funcional](https://cran.r-project.org/web/packages/functional/index.html) |Curry, Compose e outras funções de ordem superior |
| [reshape2](https://cran.r-project.org/web/packages/reshape2/index.html) |Reestrutura de flexibilidade e dados agregados. |
| [stringr](https://cran.r-project.org/web/packages/stringr/index.html) |Invólucros simples e consistentes para operações comuns de cadeia de caracteres. |
| [plyr](https://cran.r-project.org/web/packages/plyr/index.html) |Ferramentas de divisão, aplicação e combinação de dados. |
| [caTools](https://cran.r-project.org/web/packages/caTools/index.html) |Ferramentas de estatísticas de janela em movimento, GIF, Base64, ROC AUC etc. |
| [stringdist](https://cran.r-project.org/web/packages/stringdist/index.html) |Aproximar a correspondência de cadeia de caracteres e funções de distância de cadeia de caracteres. |

## <a name="install-r-using-script-actions"></a>Instalar o R usando ações de script

A ação de script a seguir é usada para instalar o R em um cluster HDInsight. https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh

Esta seção fornece instruções sobre como utilizar o script ao criar um cluster usando o Portal do Azure. 

> [!NOTE]
> Modelos do Azure PowerShell, da CLI do Azure, do SDK do .NET do HDInsight ou do Azure Resource Manager também podem ser usados para aplicar ações de script. Também é possível aplicar ações de script a clusters que já estão em execução. Para saber mais, veja [Personalizar clusters HDInsight com as Ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Inicie o provisionamento de um cluster usando as etapas em [Provisionar clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md), mas não conclua o provisionamento.

2. Na folha **Configuração Opcional**, selecione **Ações de Script** e use as seguintes informações para preencher o formulário:
   
   * **NOME**: insira um nome amigável para a ação de script.
   * **URI DO SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
   * **CABEÇALHO**: marque esta opção
   * **TRABALHO**: marque esta opção
   * **ZOOKEEPER**: marque esta opção para instalar no nó Zookeeper.
   * **PARÂMETROS**: deixe este campo em branco

3. Na parte inferior das **Ações de Script**, use o botão **Selecionar** para salvar a configuração. Por fim, use o botão **Selecionar** na parte inferior da folha **Configuração opcional** para salvar as informações de configuração opcional.

4. Continue o provisionamento do cluster conforme descrito em [Provisionar clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="run-r-scripts"></a>Executar scripts de R

Após a conclusão do provisionamento do cluster, use as seguintes etapas para usar o R para executar uma operação de MapReduce no cluster.

1. Conecte-se ao cluster HDInsight usando SSH:
   
        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
   
    Para saber mais sobre como usar SSH com o HDInsight, veja os seguintes documentos:
   
   * [Usar SSH com Hadoop baseado em Linux no HDInsight do Bash no Windows 10, Linux, Unix ou no OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [Usar SSH (PuTTY) com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Do prompt `username@hn0-CLUSTERNAME:~$` , digite o seguinte comando para iniciar uma sessão interativa de R:
   
        R

3. Digite o seguinte programa R. Esse código gera os números de 1 a 100 e os multiplica por 2.
   
        library(rmr2)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
   
    A primeira linha chama o rmr2 da biblioteca do RHadoop, que é usado para operações de MapReduce.
   
    A segunda linha gera valores de 1 a 100 e armazena-os no sistema de arquivos do Hadoop usando o `to.dfs`.
   
    A terceira linha cria um processo MapReduce usando a funcionalidade fornecida pelo rmr2 e começa o processamento. Você verá várias linhas rolarem à medida que o processamento começar.

4. Em seguida, use o seguinte para ver o caminho temporário no qual a saída do MapReduce foi adicionada:
   
        print(calc())
   
    Esse comando retorna um valor semelhante a `/tmp/file5f615d870ad2`. Para exibir a saída real, use o seguinte comando:
   
        print(from.dfs(calc))
   
    O texto a seguir é um exemplo de saída:
   
        [1,]  1 2
        [2,]  2 4
        .
        .
        .
        [98,]  98 196
        [99,]  99 198
        [100,] 100 200

5. Para sair do R, insira o seguinte comando:
   
        q()

## <a name="next-steps"></a>Próximas etapas

* [Instalar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install.md). Use a personalização do cluster para instalar o Giraph em clusters de Hadoop do HDInsight. O Giraph permite que você realize processamento de tabelas usando o Hadoop, além de poder ser utilizado com o HDInsight do Azure.
* [Instalar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install.md). Use a personalização do cluster para instalar o Solr em clusters de Hadoop do HDInsight. O Solr permite que você execute operações de pesquisa poderosas nos dados armazenados.
* [Instalar a Matiz em clusters HDInsight](hdinsight-hadoop-hue-linux.md). Use a personalização do cluster para instalar o Hue em clusters de Hadoop do HDInsight. A Matiz é um conjunto de aplicativos da Web usado para interagir com um cluster Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

