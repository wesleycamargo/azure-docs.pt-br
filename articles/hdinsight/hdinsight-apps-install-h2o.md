---
title: Instalar aplicativo publicado – H2O Sparkling Water – HDInsight do Azure
description: Instale e use o aplicativo de Hadoop de terceiros da H2O Sparkling Water.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 4be346163fd54c0c5f962d15bc2433c7fab49e0b
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2018
ms.locfileid: "53650936"
---
# <a name="install-published-application---h2o-sparkling-water"></a>Instalar aplicativo publicado – H2O Sparkling Water

Este artigo descreve como instalar e executar o aplicativo [H20 Sparkling Water](https://www.h2o.ai/) publicado [do Apache Hadoop](https://hadoop.apache.org/) no Azure HDInsight. Para obter uma visão geral da plataforma do aplicativo HDInsight e uma lista dos aplicativos publicados de Independent Software Vendor (ISV), consulte [Instalar aplicativos de Hadoop de terceiros](hdinsight-apps-install-applications.md). Para obter instruções sobre como instalar seu próprio aplicativo, confira [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md).

## <a name="about-h2o-sparkling-water"></a>Sobre a H2O Sparkling Water

A H2O Sparkling Water é uma plataforma de aprendizado de máquina na memória, totalmente distribuída e de software livre com escalabilidade linear. O H2O Sparkling Water permite combinar os algoritmos de aprendizado de máquina rápidos e dimensionáveis do H2O com os recursos do [Apache Spark](https://spark.apache.org/). Com o Sparkling Water, os usuários podem controlar o cálculo de [Scala](https://www.scala-lang.org/), R e Python usando a interface de usuário do H2O Flow.

A H2O Sparkling Water fornece:

* **Interfaces familiares e de WebUI fácil de usar** – configure se e comece rapidamente usando a GUI do Flow intuitiva baseada na Web do H2O ou ambientes de programação como R, Python, Java, Scala, JSON e as APIs do H2O.
* **Suporte a dados independentes para todos os tipos de bancos de dados e arquivos comuns** – explore e modele facilmente Big Data em Microsoft Excel, R Studio, Tableau e muito mais. Conecte-se aos dados de fontes de dados HDFS, S3, SQL e NoSQL.
* **Conversão e análise de Big Data altamente escalonável** – o H2O Big Joins pode executar 7 vezes mais rápido do que operações de R data.table e dimensiona linearmente para junções de linhas de 10 bilhões x 10 bilhões.
* **Pontuação de dados em tempo real** – implante modelos rapidamente para produção usando objetos de Java básicos (POJO), objetos Java com otimização de modelo (MOJO), ou a API REST do H2O.

### <a name="resource-links"></a>Link de recursos

* [Roteiro de engenharia do H2O.ai](http://jira.h2o.ai/)
* [Página inicial do H2O.ai](https://www.h2o.ai/)
* [Documentação do H2O.ai](http://docs.h2o.ai/)
* [Suporte do H2O.ai](https://support.h2o.ai/)
* [Base do código-fonte aberto do H2O.ai](https://github.com/h2oai/)

## <a name="prerequisites"></a>Pré-requisitos

Para instalar este aplicativo em um novo cluster do HDInsight ou em um cluster existente, você deve ter a seguinte configuração:

* Camada(s) de cluster: Standard ou Premium
* Tipo de cluster: Spark
* Versões do cluster: 3.5 ou 3.6

## <a name="install-the-h2o-sparkling-water-published-application"></a>Instalar o aplicativo publicado H2O Sparkling Water

Para obter instruções passo a passo sobre como instalar este e outros aplicativos ISV disponíveis, leia [Instalar aplicativos Apache Hadoop de terceiros](hdinsight-apps-install-applications.md).

## <a name="launch-h2o-sparkling-water"></a>Inicializar a H2O Sparkling Water

1. Após a instalação, você pode começar a usar o H2O Sparkling Water (água com gás de água doce) do seu cluster no portal do Azure abrindo [Notebooks Jupyter](https://jupyter.org/) (`https://<ClusterName>.azurehdinsight.net/jupyter`). Você também pode acessar o Jupyter selecionando **Painel do cluster** no painel do seu cluster no portal e selecionando **Jupyter Notebook**. Será solicitado que você insira suas credenciais. Insira as credenciais de Hadoop do cluster conforme especificado na criação do cluster.

2. No Jupyter, você verá três pastas: H2O-PySparkling-Examples, PySpark Examples e Scala Examples. Selecione a pasta **H2O-PySparkling-Examples**.

    ![Página inicial do Jupyter Notebooks](./media/hdinsight-apps-install-h2o/jupyter-home.png)

3. A primeira etapa ao criar um bloco de anotações é configurar o ambiente do Spark. Essas informações estão incluídas no exemplo **Sentiment_analysis_with_Sparkling_Water**. Ao configurar o ambiente do Spark, certifique-se de usar o jar correto e especifique o endereço IP fornecido pela saída da primeira célula.

    ![Página inicial do Jupyter Notebooks](./media/hdinsight-apps-install-h2o/spark-config.png)

4. Inicie o Cluster do H2O.

    ![Iniciar cluster](./media/hdinsight-apps-install-h2o/start-cluster.png)

5. Depois que o Cluster do H2O estiver em execução, abra o H2O Flow acessando **`https://<ClusterName>-h2o.apps.azurehdinsight.net:443`**.

    > [!NOTE]  
    > Se não for possível abrir H2O Flow, tente limpar o cache do navegador. Se ainda não for possível acessá-lo, você provavelmente não tem recursos suficientes no cluster. Tente aumentar o número de nós de trabalho na opção **Cluster de escala** no painel do cluster.

    ![Painel de controle do H2O Flow](./media/hdinsight-apps-install-h2o/h2o-flow.png)

6. Selecione o exemplo **Million_Songs.flow** no menu à direita. Quando receber um aviso, clique em **Carregar Notebook**. Esta demonstração foi projetada para ser executada em poucos minutos usando dados reais. O objetivo é prever dos dados se a música foi lançada antes ou depois de 2004 usando classificação binária.

    ![Selecionar Million_Songs.flow](./media/hdinsight-apps-install-h2o/million-songs.png)

7. Localize o caminho que contém **milsongs-cls-train.csv.gz** e substitua todo o caminho por **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-train.csv.gz**.

8. Localize o caminho que contém **milsongs-cls-test.csv.gz** e substitua-o por **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-test.csv.gz**.

9. Para executar todas as instruções dentro das células do bloco de anotações, selecione o botão **Executar Tudo** na barra de ferramentas.

    ![Executar Tudo](./media/hdinsight-apps-install-h2o/run-all.png)

10. Em alguns minutos você deverá ver um resultado semelhante ao seguinte.

    ![Saída](./media/hdinsight-apps-install-h2o/output.png)

É isso! Você controlou a inteligência artificial no Spark em questão de minutos. Agora pode explorar mais exemplos no H2O Flow que demonstram os diferentes tipos de algoritmos de aprendizado de máquina.

## <a name="next-steps"></a>Próximas etapas

* [Documentação do H2O](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/index.html)
* [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md): Saiba como implantar um aplicativo não publicado do HDInsight no HDInsight.
* [Publicar aplicativos HDInsight](hdinsight-apps-publish-applications.md): Saiba como publicar seus aplicativos personalizados do HDInsight no Azure Marketplace.
* [MSDN: Instalar um aplicativo HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Saiba como definir aplicativos do HDInsight.
* [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): Saiba como usar a ação de script para instalar aplicativos adicionais.
* [Usar nós de borda vazios no HDInsight](hdinsight-apps-use-edge-node.md): Saiba como usar um nó de borda vazio para acessar clusters HDInsight, além de testar e hospedar aplicativos do HDInsight.
