---
title: 'Tutorial: Criar um aplicativo Scala Maven para Spark no Azure HDInsight usando IntelliJ'
description: Crie um aplicativo do Spark escrito no Scala com o Apache Maven como o sistema de build e um arquétipo Maven existente para Scala fornecido pelo IntelliJ IDEA.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/07/2018
ms.openlocfilehash: d83c04946b67dd25bae306c2fa41a0864287bfc8
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52499318"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Tutorial: Criar um aplicativo Scala Maven para Apache Spark no HDInsight usando IntelliJ

Neste tutorial, você aprenderá a criar um aplicativo [Apache Spark](https://spark.apache.org/) escrito em [Scala](https://www.scala-lang.org/) usando o [Apache Maven](https://maven.apache.org/) com IntelliJ IDEA. O artigo usa o Apache Maven como o sistema de build e começa com um arquétipo Maven existente para Scala fornecido pelo IntelliJ IDEA.  Criar um aplicativo Scala no IntelliJ IDEA envolve as seguintes etapas:

* Usar o Maven como o sistema de compilação.
* Atualize o arquivo do POM (Modelo de Objeto do Projeto) para resolver as dependências do módulo Spark.
* Escreva seu aplicativo em Scala.
* Gere um arquivo jar que pode ser enviado aos clusters HDInsight Spark.
* Executar os aplicativos em um cluster do Spark usando Livy.

> [!NOTE]
> O HDInsight também fornece uma ferramenta de plug-in do IntelliJ IDEA para facilitar o processo de criação e envio de aplicativos para um cluster HDInsight Spark no Linux. Para saber mais, consulte [Usar o plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Apache Spark](apache-spark-intellij-tool-plugin.md).
> 

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Use IntelliJ para desenvolver um aplicativo Scala Maven

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Kit de desenvolvimento Oracle Java. Você pode instalá-lo clicando [aqui](https://aka.ms/azure-jdks).
* Um Java IDE. Este artigo usa IntelliJ IDEA 18.1.1. Você pode instalá-lo clicando [aqui](https://www.jetbrains.com/idea/download/).

## <a name="use-intellij-to-create-application"></a>Use o IntelliJ para criar o aplicativo

1. Inicie o IDEA do IntelliJ e crie um projeto. Na caixa de diálogo **Novo Projeto** , faça o seguinte: 

    a. Selecione **HDInsight** > **Spark no HDInsight (Scala)**.

   b. Na lista **Ferramenta de build**, selecione uma das seguintes opções, de acordo com suas necessidades:

      * **Maven**, para obter suporte ao assistente de criação de projetos Scala
      * **SBT**, para gerenciar as dependências e a compilação no projeto Scala

   ![A caixa de diálogo Novo Projeto](./media/apache-spark-create-standalone-application/create-hdi-scala-app.png)

1. Selecione **Avançar**.

1. O assistente de criação de projetos Scala detecta automaticamente se você instalou o plug-in Scala. Selecione **Instalar**.

   ![Verificação do plug-in Scala](./media/apache-spark-create-standalone-application/Scala-Plugin-check-Reminder.PNG) 

1. Para baixar o plug-in Scala, selecione **OK**. Siga as instruções para reiniciar o IntelliJ. 

   ![A caixa de diálogo Instalação do plug-in Scala](./media/apache-spark-create-standalone-application/Choose-Scala-Plugin.PNG)

1. Na janela **Novo Projeto**, faça o seguinte:  

    ![Selecionando o SDK do Spark](./media/apache-spark-create-standalone-application/hdi-new-project.png)

    a. Insira um nome e o local do projeto.

   b. Na lista suspensa **SDK do Projeto**, selecione **Java 1.8** para o cluster Spark 2.x ou selecione **Java 1.7** para o cluster Spark 1.x.

   c. Na lista suspensa **Versão do Spark**, o assistente de criação de projeto Scala integra a versão apropriada do SDK do Spark e do SDK do Scala. Se a versão do cluster do Spark for inferior a 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark 2.x**. Esse exemplo usa o **Spark 2.0.2 (Scala 2.11.8)**.

1. Selecione **Concluir**.

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalar o plug-in Scala para IntelliJ IDEA
Para instalar o plug-in Scala, use as seguintes etapas:

1. Abra o IntelliJ IDEA.
1. Na tela de boas-vindas, selecione **Configurar** e, em seguida, selecione **Plug-ins**.
   
    ![Habilitar plug-in do scala](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)
1. Selecione **Instalar o plugin JetBrains** no canto inferior esquerdo. 
1. Na caixa de diálogo **Procurar plug-ins da JetBrains**, pesquise por **Scala** e, em seguida, selecione **Instalar**.
   
    ![Instalar o plug-in do scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)
1. Depois que o plug-in foi instalado com êxito, você deve reiniciar o IDE.

## <a name="create-a-standalone-scala-project"></a>Criar um projeto de Scala autônomo
1. Abra o IntelliJ IDEA.
1. Do menu **Arquivo**, selecione **Novo > Projeto** para criar um novo projeto.
1. Na caixa de diálogo Novo Projeto, faça as opções a seguir:
   
    ![Criar projeto Maven](./media/apache-spark-create-standalone-application/create-maven-project.png)
   
   * Selecione **Maven** como o tipo de projeto.
   * Especifique um **SDK do projeto**. Selecione **Novo** e navegue até o diretório de instalação do Java, normalmente `C:\Program Files\Java\jdk1.8.0_66`.
   * Selecione a opção **Criar do Arquétipo** .
   * Na lista de arquétipos, selecione **org.scala-tools.archetypes:scala-archetype-simple**. Esse arquétipo cria a estrutura de diretório certa e baixará as dependências padrão necessárias para gravar o programa Scala.
1. Selecione **Avançar**.
1. Forneça os valores relevantes para **GroupId**, **ArtifactId** e **versão**. Os valores a seguir são usados neste tutorial:

    - GroupId: com.microsoft.spark.example
    - ArtifactId: SparkSimpleApp
1. Selecione **Avançar**.
1. Verifique as configurações e selecione **Avançar**.
1. Verifique o nome de projeto e local e, em seguida, selecione **Concluir**.
1. No painel esquerdo, selecione **src > test > scala > com > microsoft > spark > exemple**, clique com botão direito **MySpec** e, em seguida, selecione **Excluir**. Você não precisa desse arquivo para o aplicativo.
  
1. Nas etapas subsequentes, você atualiza o pom.xml para definir as dependências do aplicativo Spark Scala. Para que essas dependências sejam baixadas e resolvidas automaticamente, você deve configurar o Maven adequadamente.
   
    ![Configurar Maven para downloads automáticos](./media/apache-spark-create-standalone-application/configure-maven.png)
   
   1. No menu **Arquivo**, selecione **Configurações**.
   1. Na caixa de diálogo **Configurações**, navegue até **Build, Execução, Implantação** > **Ferramentas de Compilação** > **Maven** > **Importando**.
   1. Selecione a opção para **Importar projetos Maven automaticamente**.
   1. Selecione **Aplicar** e, depois, **OK**.
1. No painel esquerdo, selecione **src > principal > scala > com.microsoft.spark.example**e, em seguida, clique duas vezes em **Aplicativo** abrir App.scala.

1. Substitua o código de exemplo existente pelo código a seguir e salve as alterações. Esse código lê os dados no HVAC.csv (disponível em todos os clusters Spark HDInsight), recupera as linhas com apenas um dígito na sexta coluna e grava a saída em **/HVACOut** no contêiner padrão de armazenamento do cluster.

        package com.microsoft.spark.example
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACout")
          }
        }
1. No painel esquerdo, clique duas vezes em **pom.xml**.
   
   1. Dentro de `<project>\<properties>`, adicione os seguintes segmentos:
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   1. Dentro de `<project>\<dependencies>`, adicione os seguintes segmentos:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Salve as alterações a pom.xml.
1. Crie o arquivo .jar. O IntelliJ IDEA permite a criação de JAR como um artefato de um projeto. Execute as seguintes etapas:
    
    1. No menu **Arquivo**, escolha **Estrutura do Projeto**.
    1. Na caixa de diálogo **Estrutura do Projeto**, selecione **Artefatos** e, em seguida, selecione o símbolo de adição. Na caixa de diálogo pop-up, clique em **JAR** e em seguida, clique em **Dos módulos com dependências**.
       
        ![Criar JAR](./media/apache-spark-create-standalone-application/create-jar-1.png)
    1. Na caixa de diálogo **Criar JAR de Módulos** selecione o botão de reticências (![reticências](./media/apache-spark-create-standalone-application/ellipsis.png)) em relação à **Classe Principal**.
    1. Na caixa de diálogo **Selecionar Classe Principal**, selecione a classe que é exibida por padrão e, em seguida, selecione **OK**.
       
        ![Criar JAR](./media/apache-spark-create-standalone-application/create-jar-2.png)
    1. Na caixa de diálogo **Criar JAR de Módulos**, certifique-se de que a opção **extrair para o JAR de destino** esteja selecionada e, em seguida, selecione **OK**.  Essa configuração cria um JAR único com todas as dependências.
       
        ![Criar JAR](./media/apache-spark-create-standalone-application/create-jar-3.png)
    1. A guia layout de saída lista todos os jars incluídos como parte do projeto Maven. Você pode selecionar e excluir aqueles dos quais o aplicativo Scala não tem qualquer dependência direta. Para o aplicativo que estamos criando aqui, você pode remover tudo, exceto o último (**Saída de compilação SparkSimpleApp**). Selecione os jars para excluir e, em seguida, selecione o ícone **Excluir**.
       
        ![Criar JAR](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Certifique-se de que a caixa **Incluir na compilação do projeto** esteja selecionada, o que garante que o jar seja criado sempre que o projeto for criado ou atualizado. Selecione **Aplicar** e **OK**.
    1. Do menu **Criar**, selecione **Criar artefatos** para criar o jar. O jar de saída é criado em **\out\artifacts**.
       
        ![Criar JAR](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Executar o aplicativo no cluster do Apache Spark
Para executar o aplicativo no cluster, você pode usar as seguintes abordagens:

* **Copie o jar do aplicativo para o blob de armazenamento do Azure** associado ao cluster. Você pode usar [**AzCopy**](../../storage/common/storage-use-azcopy.md), um utilitário de linha de comando, para fazer isso. Há muitos outros clientes também que você pode usar para carregar dados. É possível saber mais sobre eles em [Carregar dados para trabalhos do Apache Hadoop no HDInsight](../hdinsight-upload-data.md).
* **Use o Apache Livy para enviar um trabalho de aplicativo remotamente** para o cluster Spark. Os clusters Spark no HDInsight incluem Livy, que expõe pontos de extremidade REST para enviar remotamente trabalhos do Spark. Para saber mais, consulte [Enviar trabalhos do Apache Spark remotamente usando o Apache Livy com clusters Spark no HDInsight](apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Próxima etapa

Neste artigo, você aprendeu a criar um aplicativo Scala do Apache Spark. Avance para o próximo artigo para saber como executar esse aplicativo em um cluster do HDInsight Spark usando Livy.

> [!div class="nextstepaction"]
>[Executar trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](./apache-spark-livy-rest-interface.md)

