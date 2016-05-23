<properties
   pageTitle="Implantar e gerenciar topologias do Apache Storm no HDInsight| Microsoft Azure"
   description="Aprenda a implantar, monitorar e gerenciar topologias do Apache Storm usando o Painel do Storm no HDInsight. Use as ferramentas do Hadoop para Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/10/2016"
   ms.author="larryfr"/>

#Implantar e gerenciar topologias Apache Storm no HDInsight baseado no Windows

O Painel Storm permite que você implante e execute facilmente topologias Apache Storm para seu cluster HDInsight usando o navegador da Web. Você também pode usar o painel para monitorar e gerenciar topologias em execução. Se você usar o Visual Studio, as ferramentas do HDInsight para Visual Studio fornecem recursos semelhantes no Visual Studio.

Os recursos do Storm e do Painel Storm das Ferramentas do HDInsight contam com API REST Storm, que pode ser usada para criar suas próprias soluções de monitoramento e gerenciamento.

> [AZURE.IMPORTANT] As etapas deste documento exigem um Storm baseado em Windows no cluster HDInsight. Para obter informações sobre como usar um cluster baseado em Linux, consulte [Implantar e gerenciar as topologias do Apache Storm no HDInsight baseado em Linux](hdinsight-storm-deploy-monitor-topology-linux.md)

##Pré-requisitos

* **Apache Storm no HDInsight** - consulte <a href="../hdinsight-storm-getting-started/" target="_blank">Introdução ao Apache Storm no HDInsight</a> para obter as etapas de criação de um cluster

* Para o **Painel Storm**: um navegador da Web moderno que oferece suporte a HTML5

* Para **Visual Studio** - SDK do Azure 2.5.1 ou mais recente e as Ferramentas do HDInsight para Visual Studio. Consulte <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Introdução ao uso das Ferramentas do HDInsight para Visual Studio</a> para instalar e configurar as ferramentas do HDInsight para Visual Studio.

	Uma das seguintes versões do Visual Studio:

	* Visual Studio 2012 com <a href="http://www.microsoft.com/download/details.aspx?id=39305" target="_blank">Atualização 4</a>

	* Visual Studio 2013 com <a href="http://www.microsoft.com/download/details.aspx?id=44921" target="_blank">Atualização 4</a> ou <a href="http://go.microsoft.com/fwlink/?LinkId=517284" target="_blank">Visual Studio 2013 Community</a>

	* <a href="http://visualstudio.com/downloads/visual-studio-2015-ctp-vs" target="_blank">Visual Studio 2015 CTP6</a>

	> [AZURE.NOTE] Atualmente, as Ferramentas do HDInsight para Visual Studio dão suporte ao Storm apenas nas versões 3.2 do cluster HDInsight.

##Painel de Controle do Storm

O Painel do Strom é uma página da Web disponível no cluster Storm. A URL é **https://&lt;clusternameazurehdinsight.net/**, onde **clustername** é o nome do seu Storm no cluster HDInsight.

Na parte superior do Painel Storm, selecione **Enviar Topologia**. Siga as instruções na página para executar uma topologia de amostra ou para carregar e executar uma topologia que você criou.

![a página da topologia de envio][storm-dashboard-submit]

###Interface do Usuário do Storm

No Painel Storm, selecione o link **Interface do Usuário do Storm**. Isso exibirá informações sobre o cluster, além de qualquer topologia em execução.

![a interface do usuário do storm][storm-dashboard-ui]

> [AZURE.NOTE] Com algumas versões do Internet Explorer, talvez você descubra que a interface do usuário do Storm não é atualizada depois da primeira vez que você a visita. Por exemplo, ela pode não mostrar as novas topologias enviadas ou pode mostrar uma topologia como ativa quando na verdade ela foi desativada anteriormente. A Microsoft está ciente desse problema e está trabalhando em uma solução.

####Página principal

A página principal da interface do usuário do Storm fornece as seguintes informações:

* **Resumo do cluster **: informações básicas sobre o cluster do Storm

* **Resumo da topologia**: uma lista das topologias em execução. Use os links desta seção para exibir mais informações sobre topologias específicas.

* **Resumo do Supervisor**: informações sobre o supervisor do Storm.

* **Configuração do Nimbus**: configuração do Nimbus para o cluster.

####Resumo da topologia

Selecionar um link na seção **Resumo da topologia** exibirá as seguintes informações sobre a topologia:

* **Resumo da topologia**: informações básicas sobre a topologia.

* **Ações da topologia**: ações de gerenciamento que podem ser executadas para a topologia.

	* **Ativar**: retoma o processamento de uma topologia de desativada.

	* **Desativar**: pausa uma topologia em execução.

	* **Reequilibrar**: ajusta o paralelismo da topologia. Você deve reequilibrar topologias em execução depois de alterar o número de nós no cluster. Isso permite que a topologia ajuste o paralelismo para compensar o aumento/diminuição do número de nós no cluster.

		Para obter mais informações, consulte <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Noções básicas sobre o paralelismo de uma topologia Storm</a>

	* **Eliminar**: encerra uma topologia Storm após o tempo limite especificado.

* **Estatísticas da topologia**: estatísticas sobre a topologia. Use os links na coluna **Janela** para definir o período de tempo para as entradas restantes na página.

* **Spouts**: os spouts usados pela topologia. Use os links desta seção para exibir mais informações sobre spouts específicos.

* **Bolts**: os bolts usados pela topologia. Use os links desta seção para exibir mais informações sobre bolts específicos.

* **Configuração da topologia**: a configuração da topologia selecionada.

####Resumo de Spout e Bolt

Selecionar um spout nas seções **Spouts** ou **Bolts** exibirá as seguintes informações sobre o item selecionado:

* **Resumo do componente**: informações básicas sobre o spout ou o bolt.

* **Estatísticas de Spout/Bolt**: estatísticas sobre o spout ou o bolt. Use os links na coluna **Janela** para definir o período de tempo para as entradas restantes na página.

* **Estatísticas de entrada** (somente bolt): informações sobre os streams de entrada consumidos pelo bolt.

* **Estatísticas de saída**: informações sobre os streams emitidos por esse spout ou bolt.

* **Executores**: informações sobre as instâncias do spout ou bolt. Selecione a entrada **Porta** gerada por um executor específico para exibir um log de informações de diagnóstico produzido para esta instância.

* **Erros**: qualquer informação de erro para este spout ou bolt.

##Ferramentas do HDInsight para Visual Studio

As Ferramentas do HDInsight podem ser usadas para enviar topologias C# ou híbridas para seu cluster do Storm. As etapas a seguir usam um aplicativo de exemplo. Para obter informações sobre como criar suas próprias topologias usando as Ferramentas do HDInsight, consulte [Desenvolver topologias em C# usando as Ferramentas do HDInsight para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Use as etapas a seguir para implantar um exemplo para seu Storm no cluster HDInsight e exibir e gerenciar a topologia.

1. Se você ainda não instalou a versão mais recente das Ferramentas do HDInsight para Visual Studio, consulte <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Introdução ao uso das Ferramentas do HDInsight para Visual Studio</a>.

2. Abra o Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**.

3. Na caixa de diálogo **Novo Projeto**, expanda **Instalados** > **Modelos** e selecione **HDInsight**. Na lista de modelos, selecione **Amostra do Storm**. Na parte inferior da caixa de diálogo, digite um nome para o aplicativo.

	![imagem](./media/hdinsight-storm-deploy-monitor-topology/sample.png)

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Enviar para o Storm no HDInsight**.

	> [AZURE.NOTE] Se solicitado, insira as credenciais de logon para sua assinatura do Azure. Se você tiver mais de uma assinatura, faça o logon naquela que contém seu Storm no cluster HDInsight.

2. Selecione seu Storm no cluster HDInsight na lista suspensa **Cluster Storm** e selecione **Enviar**. Você pode monitorar se o envio teve êxito ou não usando a janela **Saída**.

3. Depois que a topologia tiver sido enviada com êxito, as **Topologias Storm** para o cluster deverão aparecer. Selecione a topologia da lista para exibir informações sobre a topologia em execução.

	![monitor do visual studio](./media/hdinsight-storm-deploy-monitor-topology/vsmonitor.png)

	> [AZURE.NOTE] Você também pode exibir **Topologias Storm** no **Gerenciador de Servidores** expandindo **Azure**, **HDInsight** e clicando com o botão direito do mouse em um Storm no cluster do HDInsight e selecionando **Exibir Topologias Storm**.

	Escolha a forma dos spouts ou bolts para exibir informações sobre esses componentes. Uma nova janela será aberta para cada item selecionado.
    
    > [AZURE.NOTE] O nome da topologia é o nome de classe da topologia (nesse caso, `HelloWord`), com um carimbo de data e hora anexado.

4. Na exibição **Resumo da Topologia**, selecione **Eliminar** para parar a topologia.

	> [AZURE.NOTE] As topologias Storm continuarão em execução até serem paradas ou até que o cluster seja excluído.

##API REST

A interface do usuário do Storm é criada sobre a API REST e, portanto, você pode realizar gerenciamento semelhante e monitorar a funcionalidade usando a API REST. Você pode usar a API REST para criar ferramentas personalizadas para o gerenciamento e o monitoramento de topologias Storm.

Para obter mais informações, veja [API REST da interface do usuário do Storm](https://github.com/apache/storm/blob/0.9.3-branch/STORM-UI-REST-API.md). As informações a seguir são específicas para o uso da API REST com Apache Storm no HDInsight.

###URI de base

A URI base para a API REST em clusters HDInsight é **https://&lt;clustername>.azurehdinsight.net/stormui/api/v1/**, onde **clustername** é o nome do seu Storm no cluster HDInsight.

###Autenticação

As solicitações para a API REST devem usar a **autenticação básica** e, portanto, você usará o nome do administrador e a senha do cluster HDInsight.

> [AZURE.NOTE] Como a autenticação básica é enviada usando texto não criptografado, você **sempre** deverá usar HTTPS para proteger as comunicações com o cluster.

###Valores de retorno

As informações retornadas da API REST só poderão ser usadas dentro do cluster ou de máquinas virtuais na mesma Rede Virtual do Azure que o cluster. Por exemplo, o nome de domínio totalmente qualificado (FQDN) retornado para servidores Zookeeper não será acessível pela Internet.

##Próximas etapas

Agora que você aprendeu como implantar e monitorar topologias usando o Painel Storm, saiba como:

* [Desenvolver topologias C# usando as Ferramentas do HDInsight para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Desenvolver topologias baseadas em Java usando Maven](hdinsight-storm-develop-java-topology.md)

Para obter mais topologias de exemplo, consulte [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md).

[hdinsight-dashboard]: ./media/hdinsight-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/hdinsight-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/hdinsight-storm-deploy-monitor-topology/storm-ui-summary.png

<!---HONumber=AcomDC_0511_2016-->