<properties title="Get started using HDInsight Tools for Visual Studio" pageTitle="Começe a usar as ferramentas do HDInsight para o Visual Studio | Azure" description="Saiba como instalar e usar as ferramentas do HDInsight para o Visual Studio para conectar ao HDInsight e executar consultas Hive." metaKeywords="" services="HDInsight" solutions="big-data" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="big-data" ms.date="11/12/2014" ms.author="jgao" editor="cgronlun"/>

# Começe a usar as ferramentas do Hadoop do HDInsight para o Visual Studio

Saiba como instalar e usar as ferramentas do HDInsight para o Visual Studio para conectar ao HDInsight e executar consultas Hive. Para obter mais informações sobre como usar o HDInsight, consulte [Introdução ao HDInsight][hdinsight.introduction] e [Começar a usar o HDInsight][hdinsight.get.started].

+ [Instalação] 
+ [Conecte-se à sua assinatura do Azure]
+ [Navegue nos recursos vinculados]
+ [Execute consultas Hive]
+ [Próximas etapas]


##Pré-requisitos

- Uma estação de trabalho com o seguinte

	- Windows 7 ou Windows 8.
	- Visual Studio 2012 com a [atualização 4](http://www.microsoft.com/pt-br/download/details.aspx?id=39305), ou o Visual Studio 2013 com a [atualização 3](http://www.microsoft.com/pt-br/download/details.aspx?id=43721), ou o [Visual Studio Express 2013](http://www.microsoft.com/pt-br/download/details.aspx?id=43722).

	>[WACOM.NOTE] Atualmente, as ferramentas são fornecidas apenas com a versão em inglês. 


## Instalação

As ferramentas do HDInsight para o Visual Studio são fornecidas com o SDK do Microsoft Azure. Você pode usar o [Web Platform Installer](http://go.microsoft.com/fwlink/?LinkId=255386) para instalá-lo.

![HDinsight Tools for Visual Studio Web Platform installer][1]


## Conecte-se à sua assinatura do Azure
As ferramentas do HDInsight para o Visual Studio permitem que você se conecte a seus clusters de HDInsight, execute algumas operações básicas de gerenciamento e execute consultas Hive.

**Para se conectar à assinatura do Azure**

1.	Execute o Visual Studio.
2.	No menu **exibição**, clique em **Gerenciador de Servidores** para abrir a janela Gerenciador de Servidores.
3.	Expanda **Azure**e, em seguida, clique em Clusters do **HDInsight**. 

	>[WACOM.NOTE]Observe se a janela **lista de tarefas do HDInsight** está aberta. Se não estiver, ela pode ser aberta ao clicar em **Outras Janelas** do menu **Exibição** e, em seguida, clique na **janela da Lista de Tarefas do HDInsight**.  
4.	Insira suas credenciais de assinatura do azure e, em seguida, clique em **Entrar**. Isso só será necessário se você nunca tiver conectado à assinatura do Azure do Visual Studio nesta estação de trabalho.
5.	No Gerenciador de Servidores, você verá uma lista de clusters do HDInsight existentes. Se você não tiver nenhum clusters, você pode provisionar um usando o portal de gerenciamento, PowerShell do Azure ou o SDK do HDInsight.  Para obter mais informações, consulte [Provisionar clusters do HDInsight][hdinsight-provision].

	![HDInsight Tools for visual studio server explorer cluster list][5]
6.	Expanda um cluster do HDInsight. Você verá os **bancos de dados do Hive**, a conta de armazenamento padrão e as contas de armazenamento vinculadas. Você pode expandir mais as entidades. 

Depois de se conectar à sua assinatura do Azure, é possível executar o seguinte:

**Para se conectar ao portal de Gerenciamento do Visual Studio**

- No Gerenciador de Servidores, expanda **Azure**, **Clusters do HDInsight**, clique com o botão direito do mouse em um cluster do HDInsight e, em seguida, clique em **Gerenciar Cluster no Portal do Azure**.

**Para perguntas e comentários sobre o Visual Studio**

- No menu**FERRAMENTAS**, clique em **HDInsight** e, em seguida, clique em **Fórum do MSDN** para fazer perguntas, ou **dar Feedback**.

## Navegue nos recursos vinculados 

No Gerenciador de Servidores, pode-se visualizar a conta de armazenamento padrão e quaisquer contas de armazenamento vinculadas. Expanda a conta de armazenamento padrão, e visualize os contêineres na conta de armazenamento. Tanto a conta de armazenamento padrão como o contêiner padrão estão marcados. Também é possível clicar com botão direito do mouse em qualquer um dos contêineres para visualizá-lo.

![HDInsight Tools for visual studio server explorer cluster list][2]

## Execute consultas Hive
[Apache Hive][apache.hive] é uma infra-estrutura de armazenamento de dados com base no Hadoop para fornecer resumo de dados, consultas e análise. As ferramentas do HDInsight para o Visual Studio dão suporte às consultas Hive em execução do Visual Studio. Para obter mais informações sobre o Hive, consulte [Usar o Hive com o HDInsight][hdinsight.hive].

###Exibir a tabela Hive hivesampletable
Todos os clusters HDInsight são fornecidos com uma tabela Hive de exemplo chamada *hivesampletable*. Usaremos esta tabela para mostrar como listar tabelas Hive, exibir os esquemas de tabela e listar as linhas da tabela Hive.



**Listar tabelas Hive e exibir o esquema da tabela Hive**

1.	No**Gerenciador de Servidores**, expanda **Azure**, expanda **Cluster HDInsight**, expanda o cluster, expanda **Bancos de dados de Hive**, expanda **Padrão**e, em seguida, expanda **hivesampletable** para ver o esquema da tabela.
4.	Clique com o boão direito do mouse em**hivesampletable**e, em seguida, clique em **Exibir as 100 primeiras linhas** para listá-las. É o equivalente a executar a seguinte consulta Hive usando o driver ODBC do Hive:

		SELECT * FROM hivesampletable LIMIT 100

	É possível personalizar a contagem de linhas. 
 
	![HDinsight Hive Visual Studio schema query][6]

###Criar tabelas Hive

É possível usar a GUI para criar uma tabela Hive ou usar consultas Hive. Para usar consultas Hive, consulte [Executar consultas Hive](#run.queries).

**Para criar uma tabela Hive**

1. No **Gerenciador de Servidores**, expanda **Azure**, **Clusters HDInsight**, um cluster do HDInsight **bancos de dados Hive**, clique com o botão direito do mouse em**padrão**e, em seguida, clique em **Criar Tabela**.
2. Configure a tabela.
3. Clique em **Criar Tabela** para enviar o trabalho para a criação de uma nova tabela Hive.

	![hdinsight visual studio tools create hive table][7]

###<a name="run.queries"></a>Execute consultas Hive
Há duas maneiras de criar e executar consultas Hive:

- Criar consultas ad-hoc
- Criar um aplicativo Hive

**Para criar/executar consultas ad-hoc**

1. No **Gerenciador de Tarefas**, expanda **Azure**e, em seguida, expanda **Clusters HDInsight**.
2. Clique com o botão direito do mouse no cluster em que você deseja executar a consulta e, em seguida, clique em **Escrever uma consulta Hive**. 
3. Digite as consultas Hive.
4. Clique em **Enviar** ou **Enviar (Avançado)**. Com o envio avançado, você irá configurar o **Nome do trabalho**, **Argumentos**, **Configurações adicionais** e o **Diretório de Status** para o script:

	![hdinsight hadoop hive query][9]

	Depois de ter enviado o trabalho, é possível visualizar uma janela**Reumo da tarefa Hive**.

	![hdinsight hadoop hive query][8]
5. Use o botão**Atualizar** para ter um novo status até que o status do trabalho seja alterado para **Concluído**.
6. Clique nos links na parte inferior para ver **Consulta de trabalho**, **Saída de trabalho** e **Log de trabalho**.



**Para criar/executar uma solução Hive**

1. No menu **Arquivo**, clique em **Novo** e, em seguida, clique em **Projeto**.
2. Selecione **HDInsight** no painel esquerdo, selecione **Aplicativo Hive** no painel do meio, insira as propriedades e, em seguida, clique em **OK**.
3. No **Gerenciador de Soluções**, clique duas vezes em **Script.hql** para abri-lo.

 
###Exibir trabalhos Hive
É possível exibir o log de trabalhos de todos os trabalhos Hive, saída do trabalho e consulta de trabalho.

**Para exibir os trabalhos Hive**

1. No **Gerenciador de Servidores**, expanda **Azure**e, em seguida, expanda **HDInsight**. 
2. Clique com o botão direito do mouse em um cluster HDInsight e, em seguida, clique em **Exibir trabalhos Hive**. Você verá que uma lista dos trabalhos de Hive executados no cluster. 
3. Clique em um trabalho na lista de trabalhos para selecioná-lo e, em seguida, use a janela **Resumo do trabalho Hive** para abrir **Consulta de trabalho**, **Saída de trabalho** ou **Log de trabalho**.

## Próximas etapas
Este artigo, ensinou como conectar aos clusters HDInsight do Visual Studio e executar consultas Hive. Para obter mais informações, consulte:

- [Use o Hive com Hadoop no HDInsight][hdinsight.hive]
- [Comece a usar o Hadoop no HDInsight][hdinsight.get.started]
- [Enviar trabalhos Hadoop no HDInsight][hdinsight.submit.jobs]
- [Analisar dados do Twitter com Hadoop no HDInsight][hdinsight.analyze.twitter.data]


<!--Anchors-->
[Instalação] : #installation
[Conecte-se à sua assinatura do Azure]: #connect-to-your-azure-subscription
[Navegue nos recursos vinculados]: #navigate-the-linked-resources
[Execute consultas Hive]: #run-hive-queries
[Próximas etapas]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png


<!--Link references-->
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight.introduction]: ../hdinsight-introduction/
[hdinsight.get.started]: ../hdinsight-get-started/
[hdinsight.hive]: ../hdinsight-use-hive/
[hdinsight.submit.jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight.analyze.twitter.data]: ../hdinsight-analyze-twitter-data/


[apache.hive]: http://hive.apache.org

<!--HONumber=35.2-->
