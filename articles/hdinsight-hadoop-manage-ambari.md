<properties
   pageTitle="Gerenciar clusters HDInsight usando Ambari | Azure"
   description="Aprenda a usar o Ambari para monitorar e gerenciar clusters HDInsight baseados em Linux."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Gerenciar clusters HDInsight usando Ambari (Preview)

Aprenda a usar o Ambari para gerenciar e monitorar clusters HDInsight baseados em Linux.

> [AZURE.NOTE] Grande parte das informações neste artigo só se aplica a clusters HDInsight baseados em Linux. Para clusters de HDInsight baseados no Windows, apenas o monitoramento pela API REST do Ambari está disponível. Consulte [Monitorar Hadoop baseado no Windows no HDInsight usando a API do Ambari](../hdinsight-monitor-use-ambari-api/).

##<a id="whatis"></a>O que é Ambari?

<a href="http://ambari.apache.org" target="_blank">O Apache Ambari</a> torna o gerenciamento do Hadoop mais simples, fornecendo uma interface fácil de utilizar que pode ser usada para provisionar, gerenciar e monitorar clusters Hadoop. Os desenvolvedores podem integrar esses recursos em seus aplicativos usando as <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">APIs REST do Ambari</a>. 

Ambari é fornecido por padrão com os clusters HDInsight baseados em Linux. Clusters HDInsight baseados no Windows fornecem funcionalidade de monitoramento através das APIs REST do Ambari.

##Proxy SSH

> [AZURE.NOTE] Enquanto o Ambari para seu cluster possa ser acessado diretamente pela Internet, algumas funcionalidades se baseiam no acesso de nós pelo nome de domínio interno usado pelo cluster. Como esse é um nome de domínio interno e não público, você receberá erros de servidor não encontrado ao tentar acessar alguns recursos pela Internet.

Para resolver esse problema, use um túnel SSH para tráfego da Web do proxy para o nó principal do cluster, que pode resolver os nomes de domínio interno com êxito. Use os artigos a seguir para criar um túnel SSH de uma porta no computador local para o cluster.

* <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Use SSH com Hadoop baseado em Linux no HDInsight dos Linux, Unix ou OS X</a> - etapas sobre como criar um túnel SSH usando o comando  `ssh`

* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Usar SSH com Hadoop baseado em Linux no HDInsight do Windows</a> - etapas sobre como usar o PuTTy para criar um túnel SSH

##Interface da Web do Ambari

A interface da Web do Ambari está disponível em cada cluster HDInsight baseado em Linux que você cria em **https://&lt;clustername>.azurehdinsight.net**. Você também poderá acessar essa página usando o botão **Ambari Web** na parte inferior do painel do cluster no portal do Azure.

![ambari web icon](./media/hdinsight-hadoop-manage-ambari/ambari-web.png)

Você será solicitado a autenticar a página duas vezes; o primeiro prompt é para autenticar no cluster HDInsight, enquanto o segundo é para autenticar no Ambari.

* **Autenticação de cluster** - use o nome de usuário do administrador de cluster (o padrão é **admin**) e a senha

* **Autenticação do Ambari** - o padrão para o nome de usuário e senha é **admin**

	> [AZURE.NOTE] Se você tiver alterado a senha para o usuário **admin**, você deve inserir a nova senha.

Quando a página é aberta, observe a barra na parte superior - isso contém as seguintes informações e controles:

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Logotipo do Ambari** - abre o **Painel**

* **Número de operações do nome de cluster** - exibe o número de operações contínuas do Ambari. Selecionando o nome do cluster ou o **número de operações** exibirá uma lista de operações em segundo plano

* **número de alertas** - alertas críticos ou de aviso, se houver, para o cluster. Selecionar isso exibirá uma lista de alertas

* **Painel** - exibe o painel, que pode ser usado para monitorar o cluster

* **Serviços** - definições de configuração e informações para os serviços no cluster

* **Hosts** - definições de configuração e informações para os nós no cluster

* **Alertas** - uma longa de informações, aviso e alertas críticos

* **Admin** - pilha/serviços de software que estão instalados ou podem ser adicionados para o cluster, informações de conta de serviço e a segurança Kerberos

* **Botão Admin** - logout, configurações de usuário e gerenciamento do Ambari

###Monitoramento

####Alertas

O Ambari fornece vários alertas, que terão um dos seguintes como o status:

* **OK**

* **Aviso**

* **CRÍTICO**

* **DESCONHECIDO**

Alertas diferentes de **OK** farão com que a entrada do **número de alertas** na parte superior da página exiba o número de alertas. Selecionar essa entrada exibirá os alertas e seus status.

Os alertas são organizados em vários grupos padrão, que podem ser exibidos na página **Alertas**. 

![alerts page](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Você pode gerenciar os grupos usando o menu **Ações** e selecionando **Gerenciar Grupos de Alertas**. Isso permite que você modifique os grupos existentes ou crie novos grupos.

![manage alert groups dialog](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

Você também pode criar **Notificações de Alerta** no menu **Ações**. Isso permite que você crie disparos que enviam notificações via **Email** ou **SNMP** quando ocorrem combinações específicas de severidade/alerta. Por exemplo, você pode enviar um alerta quando qualquer um dos alertas do grupo **YARN Padrão** é definido como **Crítico**.

![Create alert dialog](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

####Cluster

A guia **Métricas** do **Painel** contém uma série de widgets que tornam mais fácil monitorar o status do cluster rapidamente. Vários widgets como **Uso de CPU** fornecem informações adicionais quando clicado.

![dashboard with metrics](./media/hdinsight-hadoop-manage-ambari/metrics.png)

A guia **Heatmaps** exibe as métricas como heatmaps coloridos, indo de verde para vermelho.

![dashboard with heatmaps](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Para obter mais informações sobre os nós no cluster, selecione **Hosts** e selecione o nó específico que lhe interessa.

![host details](./media/hdinsight-hadoop-manage-ambari/host-details.png)

####Acesso

A barra lateral **Serviços** no painel fornece uma visão rápida do status dos serviços executados no cluster. Vários ícones são usados para indicar o status ou as ações que devem ser realizadas, como um símbolo amarelo de reciclagem se um serviço precisa ser reciclado.

![services side-bar](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

Selecionar um serviço exibirá informações mais detalhadas sobre o serviço

![service summary information](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#####Links rápidos

Alguns serviços exibem um link **Links Rápidos** na parte superior da página. Isso pode ser usado para acessar a interface da Web de serviço específica, como:

* **Histórico de Trabalho** - histórico do trabalho do MapReduce

* **Gerenciador de Recursos** - interface do usuário do Gerenciador de recursos do YARN

* **NameNode** - interface do usuário do NameNode HDFS

* **Interface da Web Oozie** - interface do usuário do Oozie

A seleção de qualquer um desses links abrirá uma nova guia em seu navegador, que exibirá a página selecionada.

> [AZURE.NOTE] Selecionando um link em **Links rápidos** para qualquer serviço resultará em um erro de servidor não encontrado, a menos que você esteja usando um túnel SSL para tráfego da Web de proxy para o cluster. Isso ocorre porque o Ambari usa o nome de domínio interno para esses links.
> 
> Para obter informações sobre como usar um túnel SSL com o HDInsight, consulte um desses procedimentos.
> 
> * <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Use SSH com Hadoop baseado em Linux no HDInsight dos Linux, Unix ou OS X</a> - etapas sobre como criar um túnel SSH usando o comando  `ssh`
>
>* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Usar SSH com Hadoop baseado em Linux no HDInsight do Windows</a> - etapas sobre como usar o Putty para criar um túnel SSH

###Gerenciamento

####Usuários, grupos e permissões do Ambari

O gerenciamento de usuários, grupos e permissões não deve ser usado durante a visualização do HDInsight baseado em Linux.

####Host

A página **Hosts** lista todos os hosts no cluster. Para gerenciar hosts, siga estas etapas.

![hosts page](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [AZURE.NOTE] Adicionar, encerrar ou recomissionar um host não deve ser usado com os clusters HDInsight

1. Selecione o(s) host(s) que você deseja gerenciar.

2. Use o menu **Ações** para selecionar a ação que você deseja executar.

	* **Iniciar todos os componentes** - iniciar todos os componentes no host

	* **Parar todos os componentes** - parar todos os componentes no host

	* **Reiniciar todos os componentes** - parar e iniciar todos os componentes no host

	* **Ativar o modo de manutenção** - suprime alertas para o host. Isso deve ser habilitado se você estiver realizando ações que geram alertas. Por exemplo, reiniciar um serviço que depende de serviços em execução

	* **Desativar o modo de manutenção** - retorna o host para o alerta normal

	* **Parar** - interrompe DataNode ou NodeManagers no host

	* **Iniciar** - inicia DataNode ou NodeManagers no host

	* **Reiniciar** - para e inicia o DataNode ou NodeManagers no host

	* **Encerrar** - remove um host do cluster.

		> [AZURE.NOTE] Não use essa ação em clusters HDInsight

	* **Recomissionar** - adiciona um host anteriormente encerrado para o cluster.

		> [AZURE.NOTE] Não use essa ação em clusters HDInsight

####<a id="service"></a>Acesso

Na página **Painel** ou **Serviços**, use o botão **Ações** na parte inferior da lista de serviços para **adicionar** novos serviços ou **parar** e **iniciar** todos os serviços.

![service actions](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

A seguir estão as etapas gerais para **Adicionar um serviço**:

1. Na página **Painel** ou **Serviços**, use o botão **Ações** e selecione **Adicionar Serviço**.

2. No **Assistente para adicionar serviço**, selecione o serviço para adicionar e clique em **Próximo**.

	![add service](./media/hdinsight-hadoop-manage-ambari/add-service.png)

3. Prossiga com o assistente, fornecendo informações de configuração para o serviço. Consulte a documentação sobre o serviço específico que você está adicionando para obter mais informações sobre os requisitos de configuração.

4. Na página **Revisão**, você pode **Imprimir** as informações de configuração ou **Implantar** o serviço de cluster.

5. Depois que o serviço for implantado, a página **Instalar, Iniciar e Testar** exibirá informações de progresso conforme o serviço está instalado e testado. Quando o **Status** ficar verde, selecione **Próximo**.

	![image of install, start, and test page](./media/hdinsight-hadoop-manage-ambari/install-start-test.png)

6. A página **Resumo** exibe um resumo do processo de instalação, bem como quaisquer ações possíveis que você precisa tomar. Por exemplo, reiniciar outros serviços. Selecione **Concluir** para sair do assistente.

Embora o botão **Ações** possa reiniciar todos os serviços, geralmente você deseja iniciar, parar ou reiniciar um serviço específico. Use as seguintes etapas para **executar ações em um serviço individual**:

1. Na página **Painel** ou **Serviços**, selecione um serviço.

2. Na parte superior da guia **Resumo**, use o botão **Ações de Serviço** e selecione a ação a ser tomada. Isso reiniciará o serviço em todos os nós.

	![service action](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

	> [AZURE.NOTE] Reiniciar alguns serviços enquanto o cluster estiver em execução pode gerar alertas. Para evitar isso, você pode usar o botão **Ações de Serviço** para habilitar o **Modo de Manutenção** para o serviço antes de executar a reinicialização.

3. Depois que uma ação for selecionada, o **número de operações** na parte superior da página será aumentado para mostrar que uma operação em segundo plano está ocorrendo. Se configurado para exibir, a lista de operações em segundo plano será exibida.

	> [AZURE.NOTE] Se você habilitou o **Modo de manutenção** para o serviço, lembre-se de desabilitar usando o botão **Ações de Serviço** quando a operação for concluída.

Para **Configurar um serviço**, use as seguintes etapas:

1. Na página **Painel** ou **Serviços**, selecione um serviço.

2. Selecione a guia **Configurações**. A configuração atual será exibida. Uma lista de configurações anteriores também é exibida.

	![configurations](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Use os campos exibidos para modificar a configuração e selecione **Salvar**. Ou selecione uma configuração anterior e selecione **Tornar atual** para reverter para as configurações anteriores.

##API REST

Ambari Web se baseia em uma API REST subjacente, que você pode aproveitar para criar suas próprias ferramentas de gerenciamento e monitoramento. Embora a API seja relativamente direta para usar, há alguns pontos específicos do Azure para estar atento.

* **Autenticação** - o nome de usuário do administrador de cluster (padrão **admin**,)
*  e a senha devem ser usados para autenticar o serviço.

* **Segurança** - o Ambari usa autenticação básica, portanto, você deve sempre usar HTTPS ao se comunicar com a API

* **Endereços IP** - o endereço retornado para hosts em um cluster não acessíveis de fora do cluster, a menos que o cluster seja um membro de uma Rede Virtual do Azure. Em seguida, o endereço IP poderá ser acessado por outros membros da Rede Virtual, mas não de fora da rede.

* **Algumas funcionalidades não estejam habilitadas** - algumas funcionalidades Ambari estão desabilitadas, porque elas são gerenciadas pelo serviço de nuvem do HDInsight. Por exemplo, adicionar ou remover hosts do cluster. Outras funcionalidades podem não ser totalmente implementadas durante a visualização do HDInsight baseado em Linux.

Para obter uma referência completa da API REST, consulte [V1 de referência da API do Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).


<!--HONumber=45--> 
