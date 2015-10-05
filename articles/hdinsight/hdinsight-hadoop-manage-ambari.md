<properties
   pageTitle="Gerenciar clusters HDInsight usando o Ambari | Microsoft Azure"
   description="Aprenda a usar o Ambari para monitorar e gerenciar clusters HDInsight baseados em Linux."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/23/2015"
   ms.author="larryfr"/>

#Gerenciar clusters HDInsight usando o Ambari (visualização)

Aprenda a usar o Ambari para gerenciar e monitorar clusters Azure HDInsight baseados em Linux.

> [AZURE.NOTE]Grande parte das informações neste artigo só se aplica a clusters HDInsight baseados em Linux. Para clusters de HDInsight baseados no Windows, apenas o monitoramento pela API REST do Ambari está disponível. Consulte [Monitorar Hadoop baseado em Windows no HDInsight usando a API do Ambari](hdinsight-monitor-use-ambari-api.md).

##<a id="whatis"></a>O que é o Ambari?

O <a href="http://ambari.apache.org" target="_blank">Apache Ambari</a> simplifica o gerenciamento do Hadoop, fornecendo uma IU da Web fácil de usar que pode ser utilizada para provisionar, gerenciar e monitorar clusters Hadoop. Os desenvolvedores podem integrar esses recursos em seus aplicativos usando as <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">APIs REST do Ambari</a>.

Ambari é fornecido por padrão com os clusters HDInsight baseados em Linux. Clusters HDInsight baseados no Windows fornecem funcionalidade de monitoramento através das APIs REST do Ambari.

##Proxy SSH

> [AZURE.NOTE]Enquanto o Ambari para o cluster é acessível diretamente pela Internet, alguns links da interface do Ambari (como para o JobTracker) não são expostos na internet. Portanto, você receberá erros de "servidor não encontrado" ao tentar acessar esses recursos, a menos que use um túnel Secure Shell (SSH) para o tráfego de proxy da web para o nó principal do cluster.

Use os artigos a seguir para criar um túnel SSH de uma porta no computador local até o cluster:

* <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Usar SSH com Hadoop baseado em Linux no HDInsight por meio de Linux, Unix ou OS X</a>: etapas para criar um túnel SSH usando o comando `ssh`.

* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Usar SSH com Hadoop baseado em Linux no HDInsight no Windows</a>: etapas para usar PuTTY para criar um túnel SSH.

##Interface da Web do Ambari

A IU da Web do Ambari está disponível em cada cluster HDInsight baseado em Linux que você cria em **https://&lt;clustername>.azurehdinsight.net**.

Você deverá autenticar duas vezes na página. O primeiro prompt é para autenticação no cluster HDInsight e o segundo é para autenticação no Ambari.

* **Autenticação do cluster**: use o nome de usuário do administrador do cluster (o padrão é **admin**) e a senha correspondente.

* **Autenticação no Ambari**: o padrão tanto para o nome de usuário, quanto para a senha é **admin**.

	> [AZURE.NOTE]Se você alterou a senha do usuário **admin**, insira a nova senha.

Quando a página se abrir, observe a barra na parte superior. Ela contém as seguintes informações e controles:

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Logotipo do Ambari**: abre o painel, que pode ser usado para monitorar o cluster.

* **Nº operações do nome de cluster**: exibe o número de operações do Ambari em andamento. Selecionar o nome do cluster ou **Nº operações** exibirá uma lista de operações em segundo plano.

* **Nº alertas**: avisos ou alertas críticos, se houver, para o cluster. Selecionar esta opção exibirá uma lista de alertas.

* **Painel**: exibe o painel.

* **Serviços**: informações e definições de configuração dos serviços do cluster.

* **Hosts**: informações e definições de configuração dos nós no cluster.

* **Alertas**: um log de informações, avisos e alertas críticos.

* **Admin**: pilha/serviços de software que estão instalados no cluster, informações de conta de serviço e segurança Kerberos.

* **Botão Admin**: gerenciamento, configurações de usuário e logoff do Ambari.

##Monitoramento

###Alertas

O Ambari fornece vários alertas, que terão um dos seguintes como o status:

* **OK**

* **Aviso**

* **CRÍTICO**

* **DESCONHECIDO**

Alertas diferentes de **OK** farão com que a entrada **Nº alertas** na parte superior da página exiba o número de alertas. Selecionar essa entrada exibirá os alertas e seus status.

Os alertas estão organizados em vários grupos padrão, que podem ser exibidos na página **Alertas**.

![página alertas](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Você pode gerenciar os grupos usando o menu **Ações** e selecionando **Gerenciar Grupos de Alertas**. Isso permite que você modifique os grupos existentes ou crie novos grupos.

![diálogo gerenciar grupos de alerta](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

Você também pode criar notificações de alerta no menu **Ações**. Isso permite que você crie gatilhos que enviam notificações via **EMAIL** ou **SNMP** quando ocorrem combinações específicas de alerta/severidade. Por exemplo, você pode enviar um alerta quando qualquer um dos alertas no grupo **Padrão do YARN** estiver definido como **Crítico**.

![Diálogo Criar alerta](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

###HDInsight

A guia **Métricas** do painel contém uma série de widgets que facilitam monitorar o status do cluster em um relance. Vários widgets, tais como **Uso de CPU**, fornecem informações adicionais quando são clicados.

![painel com metrics](./media/hdinsight-hadoop-manage-ambari/metrics.png)

A guia **Mapa de Dados** exibe as métricas na forma de mapas de dados coloridos, que vão do verde ao vermelho.

![painel com heatmaps](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Para obter informações mais detalhadas sobre os nós no cluster, selecione **Hosts** e selecione o nó específico que de seu interesse.

![detalhes do host](./media/hdinsight-hadoop-manage-ambari/host-details.png)

###Serviços

A barra lateral **Serviços** no painel oferece uma visão rápida do status dos serviços em execução no cluster. Vários ícones são usados para indicar o status ou as ações que devem ser realizadas, como um símbolo amarelo de reciclagem se um serviço precisa ser reciclado.

![barra lateral serviços](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

Selecionar um serviço exibirá informações mais detalhadas sobre o serviço.

![informações de resumo do serviço](./media/hdinsight-hadoop-manage-ambari/service-details.png)

####Links rápidos

Alguns serviços exibem um link **Links Rápidos** na parte superior da página. Ele pode ser usado para acessar IUs da Web específicas do serviço, como:

* **Histórico de Trabalhos**: histórico de trabalhos do MapReduce.

* **Gerenciador de Recursos**: IU do gerenciador de recursos YARN RM.

* **NameNode**: IU NameNode no HDFS (Sistema de Arquivos Distribuído do Hadoop).

* **IU da Web do Oozie**: IU do Oozie.

A seleção de qualquer um desses links abrirá uma nova guia em seu navegador, que exibirá a página selecionada.

> [AZURE.NOTE]Selecionar um link **Links Rápidos** de algum serviço resultará em um erro "servidor não encontrado", a não ser que você use um túnel SSL como proxy do tráfego da Web para o cluster. Isso ocorre porque os aplicativos Web usados para exibir essas informações não são expostos na Internet.
>
> Para obter informações sobre como usar um túnel SSL com o HDInsight, consulte um destes tópicos:
>
> * <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Usar SSH com Hadoop baseado em Linux no HDInsight por meio de Linux, Unix ou OS X</a>: etapas para criar um túnel SSH usando o comando `ssh`.
>
>* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Usar SSH com Hadoop baseado em Linux no HDInsight no Windows</a>: etapas para usar PuTTY para criar um túnel SSH.

##Gerenciamento

###Usuários, grupos e permissões do Ambari

O gerenciamento de usuários, grupos e permissões não deve ser usado durante a visualização do HDInsight baseado em Linux.

###Hosts

A página **Hosts** lista todos os hosts no cluster. Para gerenciar hosts, siga estas etapas.

![página hosts](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [AZURE.NOTE]As ações de adicionar, encerrar ou reativar um host não devem ser usadas com clusters HDInsight.

1. Selecione o(s) host(s) que você deseja gerenciar.

2. Use o menu **Ações** para selecionar a ação que deseja executar:

	* **Iniciar todos os componentes**: iniciar todos os componentes no host.

	* **Parar todos os componentes**: parar todos os componentes no host.

	* **Reiniciar todos os componentes**: parar e iniciar todos os componentes no host.

	* **Ativar o modo de manutenção**: suprime os alertas sobre o host. Deve ser habilitado para que você execute ações que vão gerar alertas, como reiniciar um serviço do qual dependem os serviços em execução.

	* **Desativar o modo de manutenção**: retorna o host para o modo de alerta normal.

	* **Parar**: Para o DataNode ou NodeManagers no host.

	* **Iniciar**: Inicia o DataNode ou NodeManagers no host.

	* **Reiniciar**: para e inicia o DataNode ou NodeManagers no host.

	* **Encerramento**: remove um host do cluster.

		> [AZURE.NOTE]Não use esta ação em clusters HDInsight.

	* **Reativação**: adiciona ao cluster um host que foi encerrado.

		> [AZURE.NOTE]Não use esta ação em clusters HDInsight.

###<a id="service"></a>Serviços

Na página **Painel** ou **Serviços**, use o botão **Ações**, na parte inferior da lista de serviços, para interromper e iniciar todos os serviços.

![ações de serviço](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [AZURE.WARNING]Enquanto __Adicionar serviço__ estiver listado nesse menu, ele não deve ser usado para adicionar serviços ao cluster HDInsight. Novos serviços devem ser adicionados usando uma Ação de Script durante o provisionamento do cluster. Para saber mais sobre o uso de Ações de Script, consulte [Personalizar clusters HDInsight usando Ações de Script](hdinsight-hadoop-customize-cluster-linux.md).


Embora o botão **Ações** possa reiniciar todos os serviços, muitas vezes convém iniciar, parar ou reiniciar um serviço específico. Use as seguintes etapas para executar ações em um serviço individual:

1. Na página **Painel** ou **Serviços**, selecione um serviço.

2. Na parte superior da guia **Resumo**, use o botão **Ações de Serviço** e selecione a ação a ser tomada. Isso reiniciará o serviço em todos os nós.

	![ação de serviço](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

	> [AZURE.NOTE]Reiniciar alguns serviços enquanto o cluster estiver em execução pode gerar alertas. Para evitar isso, você pode usar o botão **Ações de Serviço** para habilitar o **Modo de manutenção** para o serviço antes de executar a reinicialização.

3. Depois que uma ação é selecionada, a entrada **Nº operações** na parte superior da página aumentará para mostrar que está ocorrendo uma operação em segundo plano. Se a exibição estiver configurada, a lista de operações em segundo plano será exibida.

	> [AZURE.NOTE]Se você habilitou o **Modo de manutenção** para o serviço, lembre-se de desabilitá-lo usando o botão **Ações de Serviço** quando a operação terminar.

Para configurar um serviço, use as seguintes etapas:

1. Na página **Painel** ou **Serviços**, selecione um serviço.

2. Selecione a guia **Configurações**. A configuração atual será exibida. Uma lista das configurações anteriores também é exibida.

	![configurações](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Use os campos exibidos para modificar a configuração e selecione **Salvar**. Ou selecione uma configuração anterior e clique em **Tornar atual** para reverter para as configurações anteriores.

##API REST

Ambari Web se baseia em uma API REST subjacente, que você pode aproveitar para criar suas próprias ferramentas de gerenciamento e monitoramento. Embora a API seja relativamente fácil de usar, você deve levar em consideração algumas especificidades do Azure:

* **Autenticação**: o nome de usuário do administrador do cluster (padrão: **admin**) e a senha correspondente devem ser usados para autenticação no serviço.

* **Segurança**: o Ambari usa autenticação básica; logo, sempre use HTTP seguro (HTTPS) ao se comunicar com a API.

* **Endereços IP**: o endereço retornado dos hosts em um cluster não são acessíveis de fora do cluster, a menos que o cluster seja um membro de uma rede virtual do Azure. Neste último caso, o endereço IP poderá ser acessado por outros membros da rede virtual, mas não de fora da rede.

* **Algumas funcionalidades não estão habilitadas**: parte da funcionalidade do Ambari está desabilitada, já que ele é gerenciado pelo serviço de nuvem HDInsight; por exemplo, adicionar ou remover hosts do cluster ou adicionar novos serviços. Outras funcionalidades podem não ser totalmente implementadas durante a visualização do HDInsight baseado em Linux.

Para obter uma referência completa da API REST, consulte [Referência de API do Ambari, V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

<!---HONumber=Sept15_HO4-->