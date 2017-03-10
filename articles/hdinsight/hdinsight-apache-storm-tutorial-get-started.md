---
title: "Tutorial do Apache Storm: Introdução ao Storm | Microsoft Docs"
description: "Introdução à análise de big data usando o Apache Storm e os exemplos do Storm Starter no HDInsight. Saiba como usar o Storm para processar dados em tempo real."
keywords: "apache storm, tutorial do apache storm, análise de big data, storm starter"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6f93f7ff-0736-4708-80ef-4289dae532a9
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/28/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfaade8249a643b77f3d7fdf466eb5ba38143f18
ms.openlocfilehash: e505d02895abd011661b3e4f66c7f4f7ea042358
ms.lasthandoff: 03/01/2017

---
# <a name="get-started-with-the-storm-starter-samples-for-big-data-analytics-on-hdinsight"></a>Introdução aos exemplos do Storm Starter para análise de Big Data no HDInsight

O Apache Storm é um sistema de computação escalável, tolerante a falhas, distribuído e em tempo real para o processamento de fluxos de dados. Com o Storm no Microsoft Azure HDInsight, você pode criar um cluster Storm baseado em nuvem que execute análise de big data em tempo real. 

> [!IMPORTANT]
> As etapas deste artigo criam um cluster HDInsight baseado no Windows. O HDInsight está disponível somente no Windows para versões inferiores ao HDInsight 3.4. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, veja [Substituição do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).
>
> Para as etapas criarem um Storm baseado em Linux no cluster HDInsight, confira [Tutorial do Apache Storm: Introdução ao exemplo do Storm Starter usando a análise de dados no HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md)

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Você deve ter o seguinte para concluir com êxito este tutorial do Apache Storm:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

### <a name="access-control-requirements"></a>Requisitos de controle de acesso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>Criar um cluster Storm

Use as seguintes etapas para criar um cluster Storm no HDInsight:

1. No [portal do Azure](https://portal.azure.com), selecione **+ NOVO**, **Inteligência + Análises** e **HDInsight**.
   
    ![Criar um cluster HDInsight](./media/hdinsight-apache-storm-tutorial-get-started/create-hdinsight.png)

2. Na folha **Básico** , insira as seguintes informações:

    * **Nome do cluster**: o nome do cluster HDInsight.
    * **Assinatura**: selecione a assinatura a ser utilizada.
    * **Nome de usuário de logon do cluster** e **Senha de logon do cluster**: logon ao acessar o cluster por HTTPS. Você pode usar essas credenciais para acessar serviços como a interface do usuário da Web do Ambari ou a API REST.
    * **Nome de usuário do SSH (Secure Shell)**: deixe esses campos como padrão. Eles não são usados para clusters HDInsight baseados em Windows.
    * **Grupo de Recursos**: o grupo de recursos para criar o cluster.
    * **Local**: a região do Azure para criar o cluster.
   
    ![Escolha a assinatura](./media/hdinsight-apache-storm-tutorial-get-started/hdinsight-basic-configuration.png)

3. Selecione **Tipo de cluster** e defina os seguintes valores na folha **Configuração de Cluster**:
   
    * **Tipo de Cluster**: Storm

    * **Sistema operacional**: Windows

    * **Versão**: Storm 0.10.0 (HDI 3.3)

        > [!NOTE]
        > HDInsight versão 3.4 e superiores estão disponíveis apenas para o sistema operacional Linux.

    * **Camada de Cluster**: Padrão
     
    Por fim, use o botão **Selecionar** para salvar as configurações.
     
    ![Selecione o tipo de cluster](./media/hdinsight-apache-storm-tutorial-get-started/set-hdinsight-cluster-type.png)

4. Depois de selecionar o tipo de cluster, use o botão __Selecionar__ para definir o tipo de cluster. Em seguida, use o botão __Avançar__ para concluir a configuração básica.

5. Na folha **Armazenamento**, selecione ou crie uma Conta de armazenamento. Para as etapas neste documento, deixe os outros campos nessa folha com os valores padrão. Use o botão __Avançar__ para salvar a configuração de armazenamento.

    ![Definir as configurações de conta de armazenamento do HDInsight](./media/hdinsight-apache-storm-tutorial-get-started/set-hdinsight-storage-account.png)

6. Na folha **Resumo**, examine a configuração do cluster. Use os links __Editar__ para alterar as configurações que estão incorretas. Por fim, use o botão __Criar__ para criar o cluster.
   
    ![Resumo da configuração do cluster](./media/hdinsight-apache-storm-tutorial-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > Pode levar até 20 minutos para criar o cluster.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>Executar uma amostra do Starter Storm no HDInsight
Este tutorial Apache Storm apresenta análise de big data usando as amostras do Starter Storm no GitHub.

Cada Storm no cluster HDInsight é fornecido com o Painel Storm, que pode ser usado para carregar e executar topologias Storm no cluster. Cada cluster também vem com topologias de amostra que podem ser executadas diretamente no Painel Storm.

### <a id="connect"></a>Conectar-se ao painel
O painel está localizado em **https://&lt;nomedocluster>.azurehdinsight.net//**, onde **nomedocluster** é o nome do cluster. Você também encontrará um link para o painel selecionando o cluster no Quadro inicial e selecionando o link **Painel** na parte superior da folha.

![Link Portal do Azure com Painel do Storm](./media/hdinsight-apache-storm-tutorial-get-started/dashboard.png)

> [!NOTE]
> Ao conectar-se ao painel, é solicitado que você insira um nome de usuário e uma senha. Esse é o nome do administrador (**admin**) e a senha usada durante a criação do cluster.
> 
> 

Depois que o Painel Storm for carregado, você verá o formulário **Enviar Topologia** .

![Envie sua topologia do Storm Starter com o Painel do Storm.](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)

O formulário **Enviar Topologia** pode ser usado para carregar e executar arquivos. jar com topologias Storm. Ele também inclui várias amostras básicas fornecidas com o cluster.

### <a id="run"></a>Executar a amostra de contagem de palavras do projeto Starter Storm no GitHub
As amostras fornecidas com o cluster incluem diversas variações de uma topologia de contagem de palavras. Esses exemplos incluem um **spout** que emite frases aleatoriamente e **bolts** que dividem cada frase em palavras individuais e contam quantas vezes cada palavra ocorreu. Esses exemplos são obtidos das [amostras do Storm Starter](https://github.com/apache/storm/tree/master/examples/storm-starter), que fazem parte do Apache Storm.

Execute as seguintes etapas para executar uma amostra do Storm Starter:

1. Selecione **StormStarter - WordCount** na lista suspensa **Arquivo Jar**. Isso preenche os campos **Nome de Classe** e **Parâmetros Adicionais** com os parâmetros dessa amostra.
   
    ![Storm Starter WordCount selecionado no Painel do Storm.](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)
   
   * **Nome da Classe** : a classe no arquivo .jar que envia a topologia.
   * **Parâmetros Adicionais** : quaisquer parâmetros necessários para a topologia. Neste exemplo, o campo é usado para fornecer um nome amigável para a topologia enviada.
2. Clique em **Enviar**. Após alguns instantes, o campo **Resultado** exibe o comando usado para enviar o trabalho, bem como os resultados do comando. O campo **Erro** exibe todos os erros ocorridos no envio da topologia.
   
    ![Botão enviar e resultados de Storm Starter WordCount.](./media/hdinsight-apache-storm-tutorial-get-started/submit-results.png)
   
   > [!NOTE]
   > Os resultados não indicam que a topologia foi concluída - **uma topologia Storm, uma vez iniciada, será executada até que você a interrompa.** A topologia de contagem de palavras gera frases aleatórias e mantém uma contagem de quantas vezes ela encontra cada palavra até ser interrompida.
   > 
   > 

### <a id="monitor"></a>Monitorar a topologia
A interface do usuário do Storm pode ser usada para monitorar a topologia.

1. Selecione **Interface do Usuário do Storm** na parte superior do Painel Storm. Isso exibe informações de resumo para o cluster e todas as topologias em execução.
   
    ![Painel do Storm mostrando resumo da topologia do Storm Starter WordCount.](./media/hdinsight-apache-storm-tutorial-get-started/stormui.png)
   
    Na página acima, você pode ver o tempo que a topologia está ativa, além do número de trabalhadores, executores e tarefas em uso.
   
   > [!NOTE]
   > A coluna **Nome** contém o nome amigável fornecido anteriormente por meio do campo **Parâmetros Adicionais**.
   > 
   > 
2. Em **Resumo da topologia**, selecione a entrada **wordcount** na coluna **Nome**. Isso exibe mais informações sobre a topologia.
   
    ![Painel do Storm com informações da topologia do Storm Starter WordCount.](./media/hdinsight-apache-storm-tutorial-get-started/topology-summary.png)
   
    Esta página fornece as seguintes informações:
   
   * **Estatísticas de topologia** -informações básicas sobre o desempenho de topologia, organizadas em janelas de tempo.
     
     > [!NOTE]
     > A seleção de uma janela de tempo específica altera a janela de tempo das informações exibidas em outras seções da página.
     > 
     > 
   * **Spouts** -informações básicas sobre spouts, incluindo o último erro retornado por cada spout.
   * **Bolts** -informações básicas sobre bolts.
   * **Configuração de topologia** -informações detalhadas sobre a configuração de topologia.
     
     Esta página também fornece ações que podem ser executadas na topologia:
   * **Ativar** - retoma o processamento de uma topologia desativada.
   * **Desativar** - pausa uma topologia em execução.
   * **Reequilibrar** - ajusta o paralelismo da topologia. Você deve reequilibrar topologias em execução depois de alterar o número de nós no cluster. Isso permite que a topologia ajuste o paralelismo para compensar o aumento/diminuição do número de nós no cluster. Para saber mais, consulte [Noções básicas sobre o paralelismo de uma topologia do Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).
   * **Eliminar** - encerra uma topologia do Storm após o tempo limite especificado.
3. Nessa página, selecione uma entrada da seção **Spouts** ou **Bolts**. Isso exibe informações sobre o componente selecionado.
   
    ![Painel do Storm com informações sobre os componentes selecionados.](./media/hdinsight-apache-storm-tutorial-get-started/component-summary.png)
   
    Esta página exibe as seguintes informações:
   
   * **Estatísticas de spout/bolt** -informações básicas sobre o desempenho de componente, organizadas em janelas de tempo.
     
     > [!NOTE]
     > A seleção de uma janela de tempo específica altera a janela de tempo das informações exibidas em outras seções da página.
     > 
     > 
   * **Estatísticas de entrada** (somente bolt) - informações sobre componentes que geram dados consumidos pelo bolt.
   * **Estatísticas de saída** -informações sobre dados emitidos por este bolt.
   * **Executores** -informações sobre instâncias deste componente.
   * **Erros** -erros produzidos por este componente.
4. Ao exibir os detalhes de um spout ou bolt, selecione uma entrada da coluna **Porta** na seção **Executores** para exibir detalhes de uma instância específica do componente.
   
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]
   
    Por meio desses dados, você pode ver que a palavra **seven** ocorreu 1,493,957 vezes. Essa é a quantidade de vezes em que ela foi encontrada desde que a topologia foi iniciada.

### <a name="stop-the-topology"></a>Parar a topologia
Volte para a página **Resumo da topologia** para a topologia de contagem de palavras e, em seguida, selecione **Eliminar** da seção **Ações de topologia**. Quando solicitado, insira 10 para os segundos a aguardar antes da interrupção da topologia. Após o período de tempo limite, a topologia não será mais exibida quando você visitar a seção **Interface do usuário do Storm** do painel.

## <a name="delete-the-cluster"></a>Excluir o cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="summary"></a>Resumo
Neste tutorial sobre o Storm Apache, você usou o Storm Starter para aprender a criar um Storm no cluster HDInsight e a usar o Painel Storm para implantar, monitorar e gerenciar topologias Storm.

## <a id="next"></a>Próximas etapas
* **Ferramentas do HDInsight para Visual Studio** - as Ferramentas do HDInsight permitem que você use o Visual Studio para enviar, monitorar e gerenciar topologias Storm semelhantes ao Painel Storm mencionado anteriormente. As Ferramentas do HDInsight também oferecem a capacidade de criar topologias Storm C# e incluem topologias de amostra que você pode implantar e executar no cluster.
  
    Para saber mais, confira [Introdução ao uso das Ferramentas do HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).
* **Arquivos de exemplo** - o cluster Storm do HDInsight oferece vários exemplos no diretório **%STORM_HOME%\contrib**. Cada exemplo deve conter o seguinte:
  
  * O código-fonte - por exemplo, storm-starter-0.9.1.2.1.5.0-2057-sources.jar
  * Os documentos Java - por exemplo, storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar
  * O exemplo - por exemplo, storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar
    
    Use o comando 'jar' para extrair o código-fonte ou os documentos Java. Por exemplo, 'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar'.
    
    > [!NOTE]
    > Os documentos Java consistem em páginas da Web. Após a extração, use um navegador para exibir o arquivo **index.html** .
    > 
    > 
    
    Para acessar essas amostras, você deve habilitar a Área de Trabalho Remota para o Storm no cluster HDInsight e, em seguida, copiar os arquivos de **%STORM_HOME%\contrib**.
* O documento a seguir contém uma lista de outros exemplos que podem ser usados com o Storm no HDInsight:
  
  * [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

