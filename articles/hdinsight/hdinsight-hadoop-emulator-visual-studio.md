---
title: Ferramentas do Azure Data Lake para Visual Studio com a Hortonworks Sandbox | Microsoft Docs
description: "Saiba como usar as ferramentas do Azure Data Lake para Visual Studio com a Hortonworks Sandbox (em execução em uma VM local). Com essas ferramentas, você pode criar e executar trabalhos Hive e Pig na área restrita e exibir a saída e o histórico do trabalho."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e3434c45-95d1-4b96-ad4c-fb59870e2ff0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/28/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 599aeb1f38c804c2edf6590140e739c9705ab1ab
ms.lasthandoff: 03/07/2017


---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Usar as ferramentas do Azure Data Lake para Visual Studio com a Hortonworks Sandbox

As ferramentas do Azure Data Lake para Visual Studio incluem ferramentas para trabalhar com clusters Hadoop genéricos, além de ferramentas para trabalhar com o Azure Data Lake e o HDInsight. Este documento fornece as etapas necessárias para usar as ferramentas do Azure Data Lake com a Hortonworks Sandbox em execução em uma máquina virtual local.

O uso da Hortonworks Sandbox permite que você trabalhe com o Hadoop localmente em seu ambiente de desenvolvimento. Após você desenvolver uma solução e querer implantá-la em escala, mude para um cluster HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* A Hortonworks Sandbox em execução em uma máquina virtual em seu ambiente de desenvolvimento. Este documento foi escrito e testado com a área restrita em execução no Oracle VirtualBox, que foi configurado usando as informações no documento [Introdução ao ecossistema Hadoop](hdinsight-hadoop-emulator-get-started.md) .

* Visual Studio 2013, 2015 ou 2017, qualquer edição.

* O [SDK do Azure para .NET](https://azure.microsoft.com/downloads/) 2.7.1 ou superior.

* [Ferramentas do Azure Data Lake para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Configurar senhas para a área restrita

Certifique-se de que a Hortonworks Sandbox está em execução e siga as etapas em [Introdução ao ecossistema Hadoop](hdinsight-hadoop-emulator-get-started.md#set-sandbox-passwords). Essas etapas configuram a senha para a conta `root` de SSH e a conta `admin` do Ambari. Essas senhas serão usadas ao conectar-se do Visual Studio à área restrita.

## <a name="connect-the-tools-to-the-sandbox"></a>Conectar as ferramentas à área restrita

1. Abra o Visual Studio e selecione **Exibir**, **Gerenciador de Servidores**.

2. No **Gerenciador de Servidores**, clique com o botão direito do mouse na entrada **HDInsight** e selecione **Conectar ao Emulador do HDInsight**.

    ![Conectar ao Emulador do HDInsight](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. NO diálogo **Conectar-se ao Emulador do HDInsight**, digite a senha que você configurou para Ambari.

    ![Inserir a senha do Ambari](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Selecione **Avançar** para continuar.

4. Use o campo **Senha** para inserir a senha configurada para a conta `root`. Mantenha os outros campos com os valores padrão.

    ![Inserir a senha raiz](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Selecione **Avançar** para continuar.

5. Aguarde a validação dos serviços para concluir. Em alguns casos, a validação pode falhar e solicitar a atualização da configuração. Se a validação falhar, selecione o botão **atualizar** e aguarde a conclusão da configuração e da verificação do serviço.

    ![Erros e botão de atualização](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]
    > O processo de atualização usa o Ambari para modificar a configuração da Hortonworks Sandbox esperada pelas ferramentas do Azure Data Lake para Visual Studio.

    Após a conclusão da validação, selecione **Concluir** para concluir a configuração.

    ![Concluir a conexão](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

    > [!NOTE]
    > Dependendo da velocidade de seu ambiente de desenvolvimento e da quantidade de memória alocada para a máquina virtual, talvez demore vários minutos para configurar e validar os serviços.

Após a execução dessas etapas, você terá uma entrada de "cluster local do HDInsight" no Gerenciador de Servidores na seção HDInsight.

## <a name="write-a-hive-query"></a>Escrever uma consulta do Hive

O Hive fornece uma linguagem de consulta do tipo SQL (HiveQL) para trabalhar com os dados estruturados. Use as etapas a seguir para saber como executar consultas ad hoc no cluster local.

1. No **Gerenciador de Servidores**, clique com o botão direito do mouse na entrada do cluster local que você adicionou anteriormente e, em seguida, selecione **Escrever uma consulta do Hive**.

    ![Escrever uma consulta do Hive](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    Isso abre uma nova janela de consulta para você digitar e enviar rapidamente uma consulta para o cluster local.

2. Na nova janela de consulta, insira o seguinte comando:

        select count(*) from sample_08;

    Na parte superior da janela de consulta, certifique-se de que a configuração para o cluster local esteja selecionada e, em seguida, selecione **Enviar**. Deixe os outros valores (**Lote** e nome do servidor) com os valores padrão.

    ![janela de consulta e botão enviar](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Você também pode usar o menu suspenso próximo a **Enviar** para selecionar **Avançado**. Opções avançadas permitem que você forneça opções adicionais ao enviar o trabalho.

    ![envio avançado](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. Depois de enviar a consulta, o status do trabalho será exibido. Isso fornece informações sobre o trabalho conforme ele é processado pelo Hadoop. A entrada **Estado do Trabalho** fornece o status do trabalho. O estado é atualizado periodicamente ou você pode usar o ícone de atualização para atualizar o estado manualmente.

    ![Estado do Trabalho](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Quando o **Status do Trabalho** muda para **Concluído**, um DAG (gráfico acíclico dirigido) é exibido. Este diagrama descreve o caminho de execução determinado pelo Tez (o mecanismo de execução padrão para o Hive no cluster local.)

    > [!NOTE]
    > Tez também é o padrão ao usar clusters HDInsight baseados em Linux. Ele não é o padrão no HDInsight baseado em Windows; para usá-lo, você deve adicionar a linha `set hive.execution.engine = tez;` ao início da consulta Hive.

    Use o link **Saída de Trabalho** para exibir a saída. Nesse caso, é **823**; o número de linhas na tabela sample_08. Você pode exibir informações de diagnóstico sobre o trabalho usando os links **Log do Trabalho** e **Baixar Log do YARN**.

4. Você também pode executar trabalhos do Hive de forma interativa alterando o campo **Lote** para **Interativo** e, em seguida, selecionando **Executar**.

    ![Consulta interativa](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    Uma consulta interativa transfere o log de saída gerado durante o processamento para a janela **Saída do HiveServer2**.

    > [!NOTE]
    > Essas são as mesmas informações disponíveis pelo link **Log de Trabalho** após a conclusão de um trabalho.

    ![Saída do HiveServer2](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Criar um projeto do Hive

Você também pode criar um projeto que contém vários scripts do Hive. Um projeto será útil quando você tiver scripts relacionados aos quais você precisa manter juntos ou manter usando sistemas de controle de versão.

1. No Visual Studio, selecione **Arquivo**, **Novo** e _Projeto_.

2. Na lista de projetos, expanda **Modelos**, **Azure Data Lake** e selecione **HIVE (HDInsight)**. Na lista de modelos, selecione **Amostra do Hive**. Insira um nome e um local e selecione **OK**.

    ![Modelo HIVE (HDInsight)](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

O projeto **Amostra do Hive** contém dois scripts, **WebLogAnalysis.hql** e **SensorDataAnalysis.hql**. Você pode enviá-los usando o mesmo botão **Enviar** na parte superior da janela.

## <a name="create-a-pig-project"></a>Criar um projeto Pig

Enquanto o Hive fornece uma linguagem semelhante ao SQL para trabalhar com os dados estruturados, o Pig funciona executando transformações nos dados. O Pig fornece uma linguagem (Pig Latin) que permite que você desenvolva um pipeline de transformações. Use as etapas a seguir para usar o Pig com o cluster local:

1. Abra o Visual Studio, selecione **Arquivo**, **Novo** e **Projeto**. Na lista de projetos, expanda **Modelos**, **Azure Data Lake** e selecione **Pig (HDInsight)**. Na lista de modelos, selecione **Aplicativo Pig**. Insira um nome e local e selecione **OK**.

    ![Projeto Pig (HDInsight)](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Insira o texto a seguir como conteúdo do arquivo **script.pig** que foi criado com este projeto.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Embora o Pig use uma linguagem diferente do Hive, o modo de execução dos trabalhos é consistente entre os dois idiomas por meio do botão **Enviar** . A seleção da lista suspensa ao lado de **Enviar** exibe uma caixa de diálogo de envio avançado para o Pig.

    ![Envio avançado de Pig](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. O status e a saída do trabalho também são exibidos como uma consulta do Hive.

    ![imagem de um trabalho pig concluído](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>Exibir trabalhos

As ferramentas do Azure Data Lake também permitem que você veja facilmente as informações sobre os trabalhos que foram executados no Hadoop. Use as etapas a seguir para ver os trabalhos que foram executados no cluster local.

1. No **Gerenciador de Servidores**, clique com o botão direito do mouse no cluster local e selecione **Exibir Trabalhos**. Uma lista de trabalhos que foram enviados para o cluster será exibida.

    ![Exibir trabalhos](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. Na lista de trabalhos, selecione um para exibir os detalhes do trabalho.

    ![selecionar um trabalho](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    As informações exibidas são semelhantes as que você vê depois de executar uma consulta de Hive ou Pig, com links para exibir a saída e informações de log.

3. Você também pode modificar e reenviar o trabalho a partir daqui.

## <a name="view-hive-databases"></a>Exibir bancos de dados do Hive

1. No **Gerenciador de Servidores**, expanda a entrada **Cluster local do HDInsight** e, em seguida, expanda **Bancos de Dados Hive**. Os bancos de dados **Padrão** e **xademo** no cluster local serão exibidos. A expansão de um banco de dados revela as tabelas no banco de dados.

    ![bancos de dados expandidos](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. A expansão de uma tabela exibe as colunas dessa tabela. Você pode clicar com o botão direito do mouse em uma tabela e selecionar **Exibir as 100 Primeiras Linhas** para exibir rapidamente os dados.

    ![exibição de bancos de dados do hive](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>Propriedades do banco de dados e da tabela

Talvez você tenha percebido que pode selecionar a exibição de **Propriedades** em um banco de dados ou tabela. Selecionar **Propriedades** mostra detalhes do item selecionado na janela Propriedades.

![Propriedades](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>Criar uma tabela

Para criar uma nova tabela, clique com o botão direito em um banco de dados e selecione **Criar Tabela**.

![Criar Tabela](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Em seguida, você pode criar a tabela usando um formulário. Você pode ver o HiveQL bruto que será usado para criar a tabela na parte inferior desta página.

![criar tabela de](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>Próximas etapas

* [Learning the ropes of the Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop tutorial - Getting started with HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

