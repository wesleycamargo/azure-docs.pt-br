---
title: "Use o Kit de ferramentas do Azure para IntelliJ com a área restrita Hortonworks | Microsoft Docs"
description: "Saiba como usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ com a área restrita do Hortonworks."
keywords: "ferramentas do hadoop,consulta do hive,intellij,área restrita do hortonworks,kit de ferramentas do azure para intellij"
services: HDInsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: b587cc9b-a41a-49ac-998f-b54d6c0bdfe0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/25/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: d252a4a8b811f966098348866cc498e2be2924f2
ms.contentlocale: pt-br
ms.lasthandoff: 07/17/2017

---
#<a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Usar ferramentas de HDInsight para IntelliJ com a área restrita do Hortonworks

Saiba como usar as Ferramentas do HDInsight para IntelliJ para desenvolver aplicativos do Apache Scala e testar os aplicativos na [a área restrita do Hortonworks](http://hortonworks.com/products/sandbox/) em execução em sua estação de trabalho. [IntelliJ IDEA](https://www.jetbrains.com/idea/) é um ambiente de desenvolvimento integrado Java (IDE) para o desenvolvimento de software de computador. Depois que você desenvolveu e testou seus aplicativos na área restrita do Hortonworks, você pode movê-los para [Azure HDInsight](hdinsight-hadoop-introduction.md).

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deverá ter o seguinte:

- O HDP 2.4 na área restrita do Hortonworks em execução no seu ambiente local. Para configurar, veja [Introdução ao ecossistema Hadoop com uma área restrita do Hadoop em uma máquina virtual](hdinsight-hadoop-emulator-get-started.md). Observe que as ferramentas de HDInsight para IntelliJ foi testada somente com HDP 2.4. Do [site de download de área restrita do Hortonworks](http://hortonworks.com/downloads/#sandbox), expanda o **arquivamento da área restrita do Hortonworks** para obtê-la.
- [Java Developer Kit (JDK) versão 1.8 ou posterior](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). JDK é exigido pelo Kit de ferramentas do Azure para IntelliJ.
- [Edição de comunidade IntelliJ IDEA](https://www.jetbrains.com/idea/download) com o [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) plug-in e o [Kit de ferramentas do Azure para IntelliJ](../azure-toolkit-for-intellij.md) plug-in. As Ferramentas do HDInsight para IntelliJ estão disponíveis como uma parte do Kit de Ferramentas do Azure para IntelliJ. 

  **Para instalar os plug-ins:**

  1. Abra o IntelliJ IDEA.
  2. Na tela de boas-vindas, clique em **configurar**e, em seguida, clique em **plug-ins**.
  3. Clique em **Instalar o plugin JetBrains** no canto inferior esquerdo.
  4. Use a função de pesquisa para pesquisar **Scala**e, em seguida, clique em **instalar**.
  5. Clique em **reinicie IntelliJ IDEA** para concluir a instalação.
  6. Repita a etapa 4 e 5 para instalar o **Kit de ferramentas do Azure para IntelliJ**. Para saber mais, confira [Instalar o Kit de Ferramentas do Azure para IntelliJ](../azure-toolkit-for-intellij-installation.md).

## <a name="create-a-spark-scala-application"></a>Criar um aplicativo Spark Scala

Nesta seção, você deve criar um projeto de scala de exemplo usando IntelliJ IDEA. Na próxima seção, você vincular IntelliJ IDEA à área restrita Hortonworks (emulador) antes de enviar o projeto.

1. Abra IntelliJ IDEA de sua estação de trabalho.
2. Clique em **Criar Novo Projeto**.
3. Clique em **HDInsight** no painel esquerdo, clique em **Spark no HDInsight(Scala)** do painel direito e clique **próxima**.

    ![Criar projeto IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)
    - Ferramenta de build: o assistente de criação de projetos Scala dá suporte para o Maven ou para o SBT gerenciar as dependências e a criação de projetos do Scala. Selecione um de acordo com a necessidade.
4. Insira as seguintes informações:

    ![Criar IntelliJ Scala propriedades do projeto](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)

    - **Nome do projeto**: fornecer um nome de projeto.
    - **Local do projeto**: fornecer um local do projeto.
    - **SDK do projeto**: clique em **novo**, clique em **JDK**, e, em seguida, especifique a pasta de Java JDK 7 ou posterior. Use Java 1.8 para cluster do spark 2.x, Java 1.7 para cluster do spark 1.x. O local padrão é C:\Program Files\Java\jdk1.8.x_xxx.
    - **Versão do Spark**: o assistente de criação de projeto Scala integra a versão apropriada do SDK do Spark e do SDK do Scala. Se a versão de cluster do Spark for inferior a 2.0, escolha o Spark 1.x. Caso contrário, você deve selecionar o spark2.x. Este exemplo usa o Spark 1.6.2 (Scala 2.10.5). Também verifique se você está usando o repositório marcado como Scala 2.10.x. Não use o repositório marcado como Scala 2.11.x
5. Clique em **Concluir**.
6. Pressione **[ALT] + 1** para abrir o modo de exibição de projeto se ele não estiver aberto.
7. De **Explorador de projeto**, expanda o projeto e, em seguida, clique em **src**.
8. Clique com botão direito **src**, aponte para **novo**e, em seguida, clique em **Scala classe**.
9. Insira um nome, selecione **objeto** na **tipo**e, em seguida, clique em **OK**.

    ![Criar nova classe IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)
10. No arquivo .scala, cole este código:

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

11. No menu **Compilar**, clique em **Compilar projeto**. Verifique se que a compilação for concluída com êxito.


## <a name="link-to-the-hortonworks-sandbox"></a>Link para a área restrita do HortonWorks

Você precisa ter um aplicativo IntelliJ existente antes de você pode vincular a uma área restrita do Hortonworks (emulator).

**Para vincular a um emulador**

1. Abra o projeto no IntelliJ, caso ainda não esteja aberto.
2. No menu **Exibir**, clique em **Janelas de Ferramenta** e então clique em **Azure Explorer**.
3. Expanda **Azure**, clique com botão direito **HDInsight**e, em seguida, clique em **vincular um emulador**.
4. Digite a senha que você configurou para a conta raiz da área restrita do Hortonworks e o restante do valores similar à seguinte captura de tela e, em seguida, clique em **OK**. 

  ![Link de IntelliJ um emulador](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Clique em **Sim** para configurar o emulador.

  Quando conectado com êxito, você pode ver o emulador (área restrita do Hortonworks) listado sob o nó do HDInsight.

## <a name="submit-the-spark-scala-application-to-the-sandbox"></a>Enviar o aplicativo Spark Scala à área restrita

Depois de vincular IntelliJ IDEA no emulador, você pode enviar seu projeto.

**Para enviar um projeto em um emulador**

1. No **Explorador de projetos**, clique com o botão direito do mouse no projeto e clique em **Enviar aplicativo Spark para o HDInsight**.
2. Especifique os seguintes campos:

    - **Cluster Spark (somente no Linux)**: selecione a área restrita do Hortonworks.
    - **Nome da classe principal**: escolha ou digite o nome da classe principal.  Para este tutorial, é **GroupByTest**.
3. Clique em **Enviar**. Os logs de envio de trabalho são mostrados na janela da ferramenta de envio Spark.

## <a name="next-steps"></a>Próximas etapas:

- Para saber como criar aplicativos Spark para o HDInsight usando as Ferramentas do HDInsight para IntelliJ, veja [Usar Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ para criar aplicativos Spark para cluster Linux HDInsight Spark](hdinsight-apache-spark-intellij-tool-plugin.md).
- Para assistir a um vídeo sobre as Ferramentas do HDInsight para IntelliJ, veja [Introduzir as Ferramentas do HDInsight para IntelliJ para desenvolvimento Spark](https://www.youtube.com/watch?v=YTZzYVgut6c).
- Para saber como depurar aplicativos Spark usando o kit de ferramentas remotamente no HDInsight por meio de SSH, consulte [Depurar remotamente aplicativos Spark em um cluster do HDInsight com o Kit de Ferramentas do Azure para IntelliJ por meio de SSH](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md).
- Para saber como depurar aplicativos Spark usando o kit de ferramentas remotamente no HDInsight por meio da VPN, confira [Usar Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ para depurar aplicativos do Spark remotamente no cluster Linux do HDInsight Spark](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).
- Para saber como usar Ferramentas do HDInsight para Eclipse para criar aplicativo Spark, veja [Usar Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse para criar aplicativos Spark](hdinsight-apache-spark-eclipse-tool-plugin.md).
- Para assistir a um vídeo sobre as Ferramentas do HDInsight para Eclipse, veja [Usar a Ferramenta do HDInsight para Eclipse para criar aplicativos Spark](https://mix.office.com/watch/1rau2mopb6fha).
