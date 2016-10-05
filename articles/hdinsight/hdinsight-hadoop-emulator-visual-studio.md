<properties
pageTitle="Usar as ferramentas do Microsoft Azure Data Lake para Visual Studio com a Hortonworks Sandbox | Microsoft Azure"
description="Saiba como usar as ferramentas do Azure Data Lake para Visual Studio com a Hortonworks Sandbox (em execução em uma VM local). Com essas ferramentas, você pode criar e executar trabalhos Hive e Pig na área restrita e exibir a saída e o histórico do trabalho."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/26/2016"
ms.author="larryfr"/>

# Usar as ferramentas do Azure Data Lake para Visual Studio com a Hortonworks Sandbox

As ferramentas do Azure Data Lake para Visual Studio incluem ferramentas para trabalhar com clusters Hadoop genéricos, além de ferramentas para trabalhar com o Azure Data Lake e o HDInsight. Este documento fornece as etapas necessárias para usar as ferramentas do Azure Data Lake com a Hortonworks Sandbox em execução em uma máquina virtual local.

O uso da Hortonworks Sandbox permite que você trabalhe com o Hadoop localmente em seu ambiente de desenvolvimento. Após você desenvolver uma solução e querer implantá-la em escala, mude para um cluster HDInsight.

## Pré-requisitos

* A Hortonworks Sandbox em execução em uma máquina virtual em seu ambiente de desenvolvimento. Este documento foi escrito e testado com a área restrita em execução no Oracle VirtualBox, que foi configurado usando as informações no documento [Introdução ao ecossistema Hadoop](hdinsight-hadoop-emulator-get-started.md).

* Visual Studio 2013 ou 2015, qualquer edição.

* O [SDK do Azure para .NET](https://azure.microsoft.com/downloads/) 2.7.1 ou superior

* [Ferramentas do Azure Data Lake para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)

## Configurar senhas para a área restrita

Certifique-se de que a Hortonworks Sandbox está em execução e execute as etapas em [Introdução ao ecossistema Hadoop](hdinsight-hadoop-emulator-get-started.md#set-passwords) para configurar a senha para a conta `root` de SSH e conta `admin` do Ambari. Essas senhas serão usadas ao conectar-se à área restrita a partir do Visual Studio.

## Conectar as ferramentas à área restrita

1. Abra o Visual Studio e selecione __Exibir__, __Gerenciador de Servidores__.

2. No __Gerenciador de Servidores__, clique com o botão direito do mouse na entrada __HDInsight__ e selecione __Conectar ao Emulador do HDInsight__.

    ![Conectar-se ao Emulador do HDInsight](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. Na caixa de diálogo __Conectar-se ao Emulador do HDInsight__, digite a senha que você configurou para Ambari.

    ![Inserir a senha do Ambari](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Selecione __Avançar__ para continuar.

4. Use o campo __Senha__ para inserir a senha configurada para a conta `root`. Mantenha os outros campos com os valores padrão.

    ![Inserir a senha raiz](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Selecione __Avançar__ para continuar.

5. Aguarde a validação dos serviços para concluir. Em alguns casos, a validação pode falhar e solicitar a atualização da configuração. Quando isso acontece, selecione o botão __atualizar__ e aguarde a conclusão da configuração e da verificação do serviço.

    ![Erros e botão de atualização](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [AZURE.NOTE] O processo de atualização usa o Ambari para modificar a configuração da Hortonworks Sandbox esperada pelas ferramentas do Azure Data Lake para Visual Studio.

    Após a conclusão da validação, selecione __Concluir__ para concluir a configuração.

    ![Concluir a conexão](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

    > [AZURE.NOTE] Dependendo da velocidade de seu ambiente de desenvolvimento e da quantidade de memória alocada para a máquina virtual, talvez demore vários minutos para configurar e validar os serviços.

Após a execução dessas etapas, você terá uma entrada de "cluster local do HDInsight" no Gerenciador de Servidores na seção HDInsight.

## Escrever uma consulta do Hive

O Hive fornece uma linguagem de consulta do tipo SQL (HiveQL) para trabalhar com dados estruturados. Use as etapas a seguir para saber como executar consultas ad hoc no cluster local.

1. No __Gerenciador de Servidores__, clique com o botão direito do mouse na entrada do cluster local que você adicionou anteriormente e, em seguida, selecione __Escrever uma consulta do Hive__.

    ![Escrever uma consulta do hive](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    Isso abre uma nova janela de consulta para você digitar e enviar rapidamente uma consulta para o cluster local.

2. Na nova janela de consulta, insira o seguinte:

        select count(*) from sample_08;
    
    Na parte superior da janela de consulta, certifique-se de que a configuração para o cluster local esteja selecionada e, em seguida, selecione __Enviar__. Deixe os outros valores (__Lote__ e nome de servidor) com os valores padrão.

    ![janela de consulta e botão enviar](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Observe que também é possível usar o menu suspenso próximo a __Enviar__ para selecionar __Avançado__. Isso abre uma caixa de diálogo que permite a você fornecer opções adicionais ao enviar o trabalho.

    ![envio avançado](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. Depois de enviar a consulta, o status do trabalho será exibido. Isso fornece informações sobre o trabalho conforme ele é processado pelo Hadoop. A entrada __Estado do Trabalho__ fornece o status atual do trabalho. O estado será atualizado periodicamente, ou você pode usar o ícone de atualização para atualizar manualmente o estado.

    ![Estado do trabalho](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Quando o __Status do Trabalho__ muda para __Concluído__, um DAG (gráfico acíclico dirigido) é exibido. Isso descreve o caminho de execução determinado pelo Tez (o mecanismo de execução padrão para o Hive no cluster local.)
    
    > [AZURE.NOTE] Tez também é o padrão ao usar clusters HDInsight baseados em Linux. Ele não é o padrão no HDInsight baseado em Windows; para usá-lo, você deve adicionar a linha `set hive.execution.engine = tez;` ao início da consulta Hive.

    Use o link __Saída de Trabalho__ para exibir a saída. Nesse caso, é __823__; o número de linhas na tabela sample\_08. Você pode exibir informações de diagnóstico sobre o trabalho usando os links __Log do Trabalho__ e __Baixar Log do YARN__.

4. Você também pode executar trabalhos do Hive de forma interativa alterando o campo __Lote__ para __Interativo__ e, em seguida, selecionando __Executar__.

    ![Consulta interativa](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    Isso transfere o log de saída gerado durante o processamento para a janela __Saída do HiveServer2__.
    
    > [AZURE.NOTE] Essas são as mesmas informações disponíveis a partir do link __Log de Trabalho__ após a conclusão de um trabalho.

    ![Saída do HiveServer2](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## Criar um projeto do Hive

Você também pode criar um projeto que contém vários scripts do Hive. Isso será útil quando você tiver scripts relacionados aos quais você precisa manter juntos, ou manter usando sistemas de controle de versão.

1. No Visual Studio, selecione __Arquivo__, __Novo__ e \_Projeto\_.

2. Na lista de projetos, expanda __Modelos__, __Azure Data Lake__ e, em seguida, selecione __HIVE (HDInsight)__. Na lista de modelos, selecione __Amostra do Hive__. Insira um nome e um local e selecione __OK__.

    ![Modelo HIVE (HDInsight)](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

O projeto __Amostra do Hive__ contém dois scripts, __WebLogAnalysis.hql__ e __SensorDataAnalysis.hql__. Você pode enviá-los usando o mesmo botão __Enviar__ na parte superior da janela.

## Criar um projeto Pig

Enquanto o Hive fornece uma linguagem semelhante ao SQL para trabalhar com dados estruturados, o Pig fornece uma linguagem (Pig Latin) que permite o desenvolvimento de um pipeline de transformações que são aplicadas aos seus dados. Use as etapas a seguir para usar o Pig com o cluster local.

1. Abra o Visual Studio, selecione __Arquivo__, __Novo__ e __Projeto__. Na lista de projetos, expanda __Modelos__, __Azure Data Lake__ e, em seguida, selecione __Pig (HDInsight)__. Na lista de modelos, selecione __Aplicativo Pig__. Insira um nome e local e selecione __OK__.

    ![Projeto Pig (HDInsight)](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Insira o seguinte como conteúdo do arquivo __script.pig__ que foi criado com este projeto.

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

    Embora o Pig use uma linguagem diferente do Hive, o modo de execução dos trabalhos é consistente entre os dois idiomas por meio do botão __Enviar__. A seleção da lista suspensa ao lado de __Enviar__ exibe uma caixa de diálogo de envio avançado para Pig.

    ![Envio avançado de Pig](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)
    
3. O status e a saída do trabalho também são exibidos como uma consulta do Hive.

    ![imagem de um trabalho pig concluído](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## Exibir trabalhos

As ferramentas do Azure Data Lake também permitem que você veja facilmente as informações sobre os trabalhos que foram executados no Hadoop. Use as etapas a seguir para ver os trabalhos que foram executados no cluster local.

1. No __Gerenciador de Servidores__, clique com o botão direito do mouse no cluster local e selecione __Exibir Trabalhos__. Isso exibirá uma lista de trabalhos que foram enviados para o cluster.

    ![Exibir trabalhos](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. Na lista de trabalhos, selecione um para exibir os detalhes do trabalho.

    ![selecionar um trabalho](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    As informações exibidas são semelhantes as que você vê depois de executar uma consulta de Hive ou Pig, com links para exibir a saída e informações de log.

3. Você também pode modificar e reenviar o trabalho a partir daqui.

## Exibir bancos de dados do Hive

1. No __Gerenciador de Servidores__, expanda a entrada __Cluster local do HDInsight__ e, em seguida, expanda __Bancos de Dados Hive__. Isso revelará os bancos de dados __Padrão__ e __xademo__ no cluster local. A expansão de um banco de dados revela as tabelas no banco de dados.

    ![bancos de dados expandidos](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. A expansão de uma tabela exibe as colunas dessa tabela. Você pode clicar com o botão direito do mouse em uma tabela e selecionar __Exibir as 100 Primeiras Linhas__ para exibir rapidamente os dados.

    ![exibição de bancos de dados do hive](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### Propriedades do banco de dados e da tabela

Talvez você tenha percebido que pode selecionar a exibição de __Propriedades__ em um banco de dados ou tabela. Isso mostrará detalhes do item selecionado na janela de propriedades.

![Propriedades](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### Criar uma tabela

Para criar uma nova tabela, clique com o botão direito em um banco de dados e selecione __Criar Tabela__.

![Criar tabela](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Em seguida, você pode criar a tabela usando um formulário. Você pode ver o HiveQL bruto que será usado para criar a tabela na parte inferior desta página.

![criar tabela de](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## Próximas etapas

* [Learning the ropes of the Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/) (Caminho das pedras do Hortonworks Sandbox)
* [Hadoop tutorial - Getting started with HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/) (Tutorial Hadoop - Introdução ao HDP)

<!---HONumber=AcomDC_0921_2016-->