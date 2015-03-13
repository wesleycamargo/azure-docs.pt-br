<properties 
	pageTitle="Começe a usar as ferramentas do HDInsight para o Visual Studio | Azure" 
	description="Saiba como instalar e usar as ferramentas do HDInsight para o Visual Studio para conectar ao HDInsight e executar consultas Hive." 
	services="HDInsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="2/17/2015" 
	ms.author="jgao"/>

# Comece a usar as ferramentas do Hadoop do HDInsight para o Visual Studio

Saiba como usar ferramentas o HDInsight para o Visual Studio para se conectar ao clusters HDInsight e enviar consultas Hive. Para obter mais informações sobre como usar o HDInsight, consulte [Introdução ao HDInsight][hdinsight.introduction] e [Começar a usar o HDInsight][hdinsight.get.started]. Para obter mais informações sobre como se conectar ao cluster Storm, consulte [Desenvolver topologias C# para Apache Storm no HDInsight usando o Visual Studio][hdinsight.storm.visual.studio.tools]. 

>[WACOM.NOTE] Todos os recursos de Ferramentas do HDInsight que estão disponíveis para clusters do Windows também funcionam em clusters do Linux, exceto alguns recursos que não são suportados no Humboldt hoje, como a exibição de logs YARN de um determinado trabalho do Hive. 

>A versão mais recente introduziu alguns novos recursos, como suporte Intellisense do editor Hive, validação local de script do Hive e acesso aos logs do Yarn.

##Neste artigo
+ [Instalação]  
+ [Conecte-se à sua assinatura do Azure]
+ [Navegue nos recursos vinculados]
+ [Execute consultas Hive]
+ [Próximas etapas]


##Pré-requisitos

- Uma estação de trabalho com o seguinte

	- Windows 7, Windows 8 ou Windows 8.1
	- O Visual Studio com as seguintes versões:
		- O Visual Studio 2012 Professional/Premium/Ultimate com [Atualização 4](http://www.microsoft.com/pt-br/download/details.aspx?id=39305)
		- Visual Studio 2013 Community/Professional/Premium/Ultimate com [Atualização 4](https://www.microsoft.com/pt-br/download/details.aspx?id=44921)
		- Visual Studio 2015 Preview.

	>[WACOM.NOTE] Atualmente, as ferramentas são fornecidas apenas com a versão em inglês. 


## Instalação

As Ferramentas de HDInsight para Visual Studio pode ser instaladas usando o [Web Platform Installer](http://go.microsoft.com/fwlink/?LinkId=255386). Você pode consultar as ferramentas usando a palavra-chave "hdinsight". Há um pacote para cada uma das versões do Visual Studio. Você deve escolher aquele que coincide com o Visual Studio.  O pacote também instalará o Microsoft Hive ODBC Driver 32 bits e 64 bits.

![HDinsight Tools for Visual Studio Web Platform installer][1]


>[WACOM.NOTE] Se você tiver o Visual Studio 2012 ou 2015 e tem a versão mais antiga do SDK do Azure instalado, deve remover manualmente a versão mais antiga antes de instalar a versão mais recente. Visual Studio 2013 dá suporte à atualização direta.

## Conecte-se à sua assinatura do Azure
As ferramentas do HDInsight para o Visual Studio permitem que você se conecte a seus clusters de HDInsight, execute algumas operações básicas de gerenciamento e execute consultas Hive.

**Para se conectar à assinatura do Azure**

1.	Execute o Visual Studio.
2.	No menu **exibição**, clique em **Gerenciador de Servidores** para abrir a janela Gerenciador de Servidores.
3.	Expanda **Azure** e expanda **HDInsight**. 

	>[WACOM.NOTE]Observe se a janela **lista de tarefas do HDInsight** está aberta. Se não estiver, ela pode ser aberta ao clicar em **Outras Janelas** do menu **Exibição** e, em seguida, clique na **janela da Lista de Tarefas do HDInsight**.  
4.	Insira suas credenciais de assinatura do azure e, em seguida, clique em **Entrar**. Isso só será necessário se você nunca tiver conectado à assinatura do Azure do Visual Studio nesta estação de trabalho.
5.	No Gerenciador de Servidores, você verá uma lista de clusters do HDInsight existentes. Se você não tiver nenhum clusters, você pode provisionar um usando o portal de gerenciamento, PowerShell do Azure ou o SDK do HDInsight.  Para obter mais informações, consulte [Provisionar clusters do HDInsight][hdinsight-provision].

	![HDInsight Tools for visual studio server explorer cluster list][5]
6.	Expanda um cluster do HDInsight. Você verá **Bancos de Dados do Hive**, conta de armazenamento padrão, contas de armazenamento vinculadas e **log do Hadoop Service**. Você pode expandir mais as entidades. 

Depois de se conectar à sua assinatura do Azure, é possível executar o seguinte:

**Para se conectar ao portal de Gerenciamento do Visual Studio**

- No Gerenciador de Servidores, expanda **Azure**, **HDInsight**, clique com o botão direito do mouse em um cluster do HDInsight e, em seguida, clique em **Gerenciar Cluster no Portal do Azure**.

**Para perguntas e comentários sobre o Visual Studio**

- No menu**FERRAMENTAS**, clique em **HDInsight** e, em seguida, clique em **Fórum do MSDN** para fazer perguntas, ou **dar Feedback**.

## Navegue nos recursos vinculados 

No Gerenciador de Servidores, pode-se visualizar a conta de armazenamento padrão e quaisquer contas de armazenamento vinculadas. Expanda a conta de armazenamento padrão, e visualize os contêineres na conta de armazenamento. Tanto a conta de armazenamento padrão como o contêiner padrão estão marcados. Também é possível clicar com botão direito do mouse em qualquer um dos contêineres para visualizá-lo.

![HDInsight Tools for visual studio server explorer cluster list][2]

## Execute consultas Hive
[O Apache Hive][apache.hive] é uma infra-estrutura de armazenamento de dados com base no Hadoop para fornecer resumo de dados, consultas e análise. As ferramentas do HDInsight para o Visual Studio dão suporte às consultas Hive em execução do Visual Studio. Para obter mais informações sobre o Hive, consulte [Usar o Hive com o HDInsight][hdinsight.hive].

Leva tempo testar o script do Hive em um cluster HDInsight. Pode levar vários minutos ou mais.  As Ferramentas do HDInsight para Visual Studio podem validar a gramática de script do Hive localmente sem se conectar a um cluster ativo.

As Ferramentas do HDInsight para Visual Studio também permite que os usuários vejam o que há dentro do trabalho do Hive coletando e identificando os logs do Yarn de determinados trabalhos do Hive.

###Exibir a tabela Hive hivesampletable
Todos os clusters HDInsight são fornecidos com uma tabela Hive de exemplo chamada *hivesampletable*. Usaremos esta tabela para mostrar como listar tabelas Hive, exibir os esquemas de tabela e listar as linhas da tabela Hive.



**Listar tabelas Hive e exibir o esquema da tabela Hive**

1.	No **Gerenciador de Servidores**, expanda **Azure**, expanda **HDInsight**, expanda um cluster, expanda **Bancos de Dados do Hive**, expanda **Padrão** e expanda **hivesampletable** para ver o esquema da tabela.
4.	Clique com o botão direito do mouse em**hivesampletable**e, em seguida, clique em **Exibir as 100 primeiras linhas** para listá-las. É o equivalente a executar a seguinte consulta Hive usando o driver ODBC do Hive:

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

###<a name="run.queries"></a>Validar e executar consultas do Hive
Há duas maneiras de criar e executar consultas Hive:

- Criar consultas locais
- Criar um aplicativo Hive

**Para criar, validar e executar consultas locais**

1. No **Gerenciador de Tarefas**, expanda **Azure**e, em seguida, expanda **Clusters HDInsight**.
2. Clique com o botão direito do mouse no cluster em que você deseja executar a consulta e, em seguida, clique em **Escrever uma consulta Hive**. 
3. Digite as consultas Hive. Observe que o editor Hive suporta Intellisense.
4. (opcional) Clique em **Validar Script** para verificar os erros de sintaxe do script.

	![hdinsight tools for Visual Studio local validation][10]

4. Clique em **Enviar** ou **Enviar (Avançado)**. Com o envio avançado, você irá configurar o **Nome do trabalho**, **Argumentos**, **Configurações adicionais** e o **Diretório de Status** para o script:

	![hdinsight hadoop hive query][9]

	Depois de ter enviado o trabalho, é possível visualizar uma janela**Resumo da tarefa Hive**.

	![hdinsight hadoop hive query][8]
5. Use o botão**Atualizar** para ter um novo status até que o status do trabalho seja alterado para **Concluído**.
6. Clique nos links na parte inferior para ver **Consulta de Trabalho**, **Saída de Trabalho**, **Log do Trabalho** ou **Log do Yarn**.



**Para criar/executar uma solução Hive**

1. No menu **Arquivo**, clique em **Novo** e, em seguida, clique em **Projeto**.
2. Selecione **HDInsight** no painel esquerdo, selecione **Aplicativo Hive** no painel do meio, insira as propriedades e, em seguida, clique em **OK**.
3. No **Gerenciador de Soluções**, clique duas vezes em **Script.hql** para abri-lo. 
4. Para validar o script do Hive, você pode clicar no botão Validar Script ou clicar com o botão direito do mouse no editor do Hive e clicar em "Validar Script" do menu de contexto.

 
###Exibir trabalhos Hive
Você pode exibir a consulta de trabalho, saída do trabalho, log de trabalho e log do Yarn para trabalhos do Hive.  Consulte a captura de tela anterior.

A versão mais recente da ferramenta permite que você veja o que está dentro de seus trabalhos do Hive coletando e adicionar logs do Yarn. O log do yarn pode ajudar você investigar problemas de desempenho. Para obter mais informações sobre como o HDInsight coleta logs do YARN, consulte [Acesso a Logs de Aplicativo do HDInsight Programaticamente][hdinsight.access.application.logs].

**Para exibir os trabalhos Hive**

1. No **Gerenciador de Servidores**, expanda **Azure**e, em seguida, expanda **HDInsight**. 
2. Clique com o botão direito do mouse em um cluster HDInsight e, em seguida, clique em **Exibir trabalhos Hive**. Você verá que uma lista dos trabalhos de Hive executados no cluster. 
3. Clique em um trabalho na lista de trabalhos para selecioná-lo e use a janela **Resumo do Trabalho do Hive** para abrir **Consulta do Trabalho**, **Saída do Trabalho**, **Log do Trabalho** ou **Log do Yarn**.

## Próximas etapas
Este artigo, ensinou como conectar aos clusters HDInsight do Visual Studio e executar consultas Hive. Para obter mais informações, consulte:

- [Use o Hadoop Hive no HDInsight][hdinsight.hive]
- [Introdução ao uso do Hadoop no HDInsight][hdinsight.get.started]
- [Enviar trabalhos Hadoop no HDInsight][hdinsight.submit.jobs]
- [Analisar dados do Twitter com Hadoop no HDInsight][hdinsight.analyze.twitter.data]


<!--Anchors-->
[Instalação]: #installation
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
[10]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png


<!--Link references-->
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight.introduction]: ../hdinsight-introduction/
[hdinsight.get.started]: ../hdinsight-get-started/
[hdinsight.hive]: ../hdinsight-use-hive/
[hdinsight.submit.jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight.analyze.twitter.data]: ../hdinsight-analyze-twitter-data/
[hdinsight.storm.visual.studio.tools]: ../hdinsight-storm-develop-csharp-visual-studio-topology/
[hdinsight.access.application.logs]: ../hdinsight-hadoop-access-yarn-app-logs/

[apache.hive]: http://hive.apache.org
<!--HONumber=45--> 
