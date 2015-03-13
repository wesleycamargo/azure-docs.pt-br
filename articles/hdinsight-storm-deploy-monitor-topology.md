<properties 
   pageTitle="Implantando e gerenciando topologias Storm no HDInsight | Azure" 
   description="Aprenda a implantar, monitorar e gerenciar topologias Storm usando o Painel Storm incluído no Apache Storm no HDInsight." 
   services="hdinsight" 
   documentationCenter="" 
   authors="Blackmist" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Implantar e gerenciar topologias Apache Storm no HDInsight

O Painel Storm permite que você implante e execute facilmente topologias Apache Storm para seu cluster HDInsight usando o navegador da Web. Você também pode usar o painel para monitorar e gerenciar topologias em execução. Se você usar o Visual Studio, as ferramentas do HDInsight para Visual Studio fornecem recursos semelhantes no Visual Studio.

Os recursos do Storm e Painel do Storm das Ferramentas do HDInsight contam com API de REST Storm, que pode ser usadas para criar suas próprias soluções de monitoramento e gerenciamento.

## Pré-requisitos

* **Apache Storm no HDInsight** -consulte <a href="../hdinsight-storm-getting-started/" target="_blank">Introdução ao Apache Storm no HDInsight</a> para obter as etapas sobre como criar um cluster

* Para o **Painel Storm** - um navegador da Web moderno que oferece suporte a HTML5

* Para **Visual Studio** - SDK do Azure 2.5.1 ou mais recente e as Ferramentas do HDInsight para Visual Studio. Consulte <a href="../hdinsight-hadoop-visual-studio-tools-getting-started/" target="_blank">Comece a usar as Ferramentas do HDInsight para o Visual Studio</a> para instalar e configurar as ferramentas de HDInsight para Visual Studio.

	Uma das seguintes versões do Visual Studio:

	* Visual Studio 2012 com <a href="http://www.microsoft.com/pt-br/download/details.aspx?id=39305" target="_blank">Atualização 4</a>

	* Visual Studio 2013 com <a href="http://www.microsoft.com/pt-br/download/details.aspx?id=44921" target="_blank">Atualização 4</a> ou <a href="http://go.microsoft.com/fwlink/?LinkId=517284" target="_blank">Comunidade do Visual Studio 2013</a>

	* <a href="http://visualstudio.com/pt-br/downloads/visual-studio-2015-ctp-vs" target="_blank">Visual Studio 2015 CTP6</a>

	> [AZURE.NOTE] Atualmente as Ferramentas do HDInsight para Visual Studio suportam apenas Storm em versões 3.2 do cluster HDInsight.

## Painel de Controle do Storm

O Painel Strom está disponível no cluster Storm. A URL é **https://&lt;clustername>.azurehdinsight.net/**, onde **clustername** é o nome do seu Storm no cluster HDInsight. Você também pode acessar o painel de controle usando o link **Painel Storm** no seu painel de cluster no Portal do Azure.

![the portal with storm dashboard highlighted][hdinsight-dashboard]

Na parte superior do Painel Storm, selecione **Enviar Topologia**. Siga as instruções na página para executar uma topologia de exemplo ou carregar e executar uma topologia que você criou.

![the submit topology page][storm-dashboard-submit]

### Interface do Usuário do Storm

No Painel Storm, selecione o link **Interface do Usuário do Storm**. Isso exibirá informações sobre o cluster, bem como qualquer topologia em execução.

![the storm ui][storm-dashboard-ui] 

#### Página principal

A página principal da interface do usuário do Storm fornece as seguintes informações.

* **Resumo do cluster** - informações básicas sobre o cluster do Storm

* **Resumo da topologia** - uma lista das topologias em execução. Use os links dessa seção para exibir mais informações sobre topologias específicas

* **Resumo de Supervisor** - informações sobre o supervisor do Storm

* **Configuração do Nimbus** - configuração do Nimbus para o cluster

#### Resumo da topologia

Selecionar um link na seção **Resumo da topologia** exibirá as seguintes informações sobre a topologia.

* **Resumo da topologia** - informações básicas sobre a topologia

* **Ações de topologia** - ações de gerenciamento que podem ser executadas para a topologia

	* **Ativar** - retoma o processamento de uma topologia de desativada
	
	* **Desativar** - pausa uma topologia em execução
	
	* **Reequilibrar** - ajusta o paralelismo da topologia. Você deve reequilibrar topologias em execução depois de alterar o número de nós no cluster. Isso permite que a topologia ajuste o paralelismo para compensar o aumento/diminuição do número de nós no cluster
	
		Para obter mais informações, consulte <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Noções básicas sobre o paralelismo de uma topologia do Storm</a>
	
	* **Eliminar** - encerra uma topologia do Storm após o tempo limite especificado

* **Estatísticas de topologia** - estatísticas sobre a topologia. Use os links na coluna **Janela** para definir o período de tempo para as entradas restantes na página.

* **Spouts** - os spouts usados pela topologia. Use os links dessa seção para exibir mais informações sobre spouts específicos

* **Bolts** - os bolts usados pela topologia. Use os links dessa seção para exibir mais informações sobre bolts específicos

* **Configuração da topologia** - a configuração da topologia selecionada

#### Resumo de Spout e Bolt

Selecionar um spout na seção **Spouts** ou **Bolts** exibirá as seguintes informações sobre o item selecionado.

* **Resumo dos componentes** - informações básicas sobre o spout ou bolt

* **Estatísticas de Spout/Bolt** - estatísticas sobre o spout ou bolt. Use os links na coluna **Janela** para definir o período de tempo para as entradas restantes na página

* **Estatísticas de entrada** (somente bolt) - informações sobre o(s) stream(s) de entrada consumido(s) pelo bolt

* **Estatísticas de saída** - informações sobre os streams emitidos por esse spout ou bolt

* **Executores** - informações sobre as instâncias do spout ou bolt. Selecione a entrada **Porta** gerada por um executor específico para exibir um log de informações de diagnóstico produzido para essa instância

* **Erros** - qualquer informação de erro para este spout ou bolt.

## Ferramentas do HDInsight para Visual Studio

As ferramentas do HDInsight podem ser usadas para enviar topologias C# ou híbridas para seu Cluster do Storm. As etapas a seguir usam um aplicativo de exemplo. Para obter informações sobre como criar suas próprias topologias usando as Ferramentas do HDInsight, consulte [desenvolver topologias em C# usando as Ferramentas do HDInsight para Visual Studio](../hdinsight-storm-develop-csharp-visual-studio-topology/).

Use as etapas a seguir para implantar um exemplo para seu Storm no cluster HDInsight e exibir e gerenciar a topologia.

1. Se você ainda não instalou a versão mais recente das Ferramentas do HDInsight para Visual Studio, consulte <a href="../hdinsight-hadoop-visual-studio-tools-getting-started/" target="_blank">Comece a usar as Ferramentas do HDInsight para o Visual Studio</a>.

2. Abra o Visual Studio, selecione **Arquivo**, **Novo** e **Projeto**.

3. Na caixa de diálogo **Novo Projeto**, expanda **Instalados**, **Modelos** e selecione **HDInsight**. Na lista de modelos, selecione **Exemplo do Storm**. Na parte inferior da caixa de diálogo, digite um nome para o aplicativo.

	![image](./media/hdinsight-storm-deploy-monitor/sample.png)

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Enviar para o Storm no HDInsight**.

	> [AZURE.NOTE] Se solicitado, insira as credenciais de logon para sua assinatura do Azure. Se você tiver mais de uma assinatura, faça o logon naquela que contém seu Storm no cluster do HDInsight.

2. Selecione seu Storm no cluster do HDInsight no menu suspenso **Cluster do Storm** e selecione **Enviar**. Você pode monitorar se o envio teve êxito ou não usando a janela **Saída**.

3. Depois que a topologia tiver sido enviada com êxito, as **Topologias do Storm** para o cluster devem aparecer. Selecione a topologia da lista para exibir informações sobre a topologia em execução.

	![visual studio monitor](./media/hdinsight-storm-deploy-monitor/vsmonitor.png)

	> [AZURE.NOTE] Você também pode exibir **Topologias do Storm** no **Server Explorer** expandindo **Azure**, **HDInsight** e clicando com o botão direito do mouse em um Storm no cluster do HDInsight e selecionando **Exibir Topologias do Storm**.

	Use os links para os spouts ou bolts para exibir informações sobre esses componentes. Uma nova janela será aberta para cada item selecionado.

4. Na exibição **Resumo da Topologia**, selecione **Eliminar** para parar a topologia.

	> [AZURE.NOTE] As topologias do Storm continuam em execução até que sejam eliminadas ou o cluster seja excluído.

## API REST

A interface do usuário do Storm é criada sobre a API REST, portanto, você pode realizar funcionalidades de monitoramento e gerenciamento semelhantes usando a API. Usando a API REST, você pode criar ferramentas personalizadas para gerenciar e monitorar topologias do Storm.

A API REST do Storm está documentada em <a href="https://github.com/apache/storm/blob/master/STORM-UI-REST-API.md" target="_base">https://github.com/apache/storm/blob/master/STORM-UI-REST-API.md</a>. As informações a seguir são específicas para o uso da API REST com Apache Storm no HDInsight.

### URI de base

A URI de base para a API REST em clusters HDInsight é **https://&lt;clustername>.azurehdinsight.net/stormui/api/v1/**, onde **clustername** é o nome do seu Storm no cluster do HDInsight.

### Autenticação

As solicitações para a API REST devem usar **autenticação básica** com o nome do administrador e a senha de cluster do HDInsight. 

> [AZURE.NOTE] Como a autenticação básica é enviada usando texto não criptografado, você deve **sempre** usar HTTPS para proteger as comunicações com o cluster.

### Valores de retorno

Informações retornadas da API REST podem ser usadas somente dentro do cluster ou computadores na mesma Rede Virtual do Azure que o cluster. Por exemplo, o nome de domínio totalmente qualificado (FQDN) retornado para servidores Zookeeper não será acessível pela Internet.

## Próximas etapas

Agora que você aprendeu como implantar e monitorar topologias usando o Painel do Storm, saiba como [desenvolver topologias em C# usando as Ferramentas do HDInsight para Visual Studio](../hdinsight-storm-develop-csharp-visual-studio-topology/) ou como [desenvolver topologias baseados em Java usando Maven](../hdinsight-storm-develop-java-topology/).


[hdinsight-dashboard]: ./media/hdinsight-storm-deploy-monitor/dashboard-link.png
[storm-dashboard-submit]: ./media/hdinsight-storm-deploy-monitor/submit.png
[storm-dashboard-ui]: ./media/hdinsight-storm-deploy-monitor/storm-ui-summary.png

<!--HONumber=45--> 
