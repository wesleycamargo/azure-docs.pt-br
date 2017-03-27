---
title: Monitorar e gerenciar o Azure HDInsight usando a uusando a IU da Web do Ambari | Microsoft Docs
description: "Aprenda a usar o Ambari para monitorar e gerenciar clusters HDInsight baseados em Linux. Neste documento, você aprenderá a usar a interface de usuário do Ambari Web com clusters HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4787f3cc-a650-4dc3-9d96-a19a67aad046
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: e80bf82df28fbce8a1019c6eb07cfcae4cbba930
ms.openlocfilehash: 34f5c92dc565e644bd8c569bfbea65e92ace5a19
ms.lasthandoff: 02/09/2017


---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-web-ui"></a>Gerenciar clusters HDInsight usando a interface de usuário do Ambari Web

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

O Apache Ambari simplifica o gerenciamento e monitoramento de um cluster Hadoop, fornecendo uma forma fácil de usar a interface do usuário da Web e a API REST. O Ambari está incluído em clusters HDInsight baseados em Linux e é usado para monitorar o cluster e fazer alterações de configuração.

Neste documento, você aprenderá a usar a interface de usuário do Ambari Web com um cluster HDInsight.

## <a id="whatis"></a>O que é o Ambari?

O [Apache Ambari](http://ambari.apache.org) simplifica o gerenciamento do Hadoop, fornecendo uma IU da Web fácil de usar que pode ser utilizada para provisionar, gerenciar e monitorar clusters Hadoop. Os desenvolvedores podem integrar essas funcionalidades em seus aplicativos usando as [APIs REST do Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

A IU da Web do Ambari é fornecida por padrão com clusters HDInsight que usam o sistema operacional Linux.

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, veja [Substituição do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date). 

## <a name="connectivity"></a>Conectividade

A IU da Web do Ambari está disponível no seu cluster HDInsight em HTTPS://CLUSTERNAME.azurehdidnsight.net, onde **CLUSTERNAME** é o nome do cluster. 

> [!IMPORTANT]
> Conectar-se ao Ambari no HDInsight requer HTTPS. Você também deve autenticar-se no Ambari usando o nome da conta de administrador (o padrão é **admin**) e a senha fornecidos durante a criação do cluster. 

## <a name="ssh-tunnel-proxy"></a>Túnel SSH (proxy)

> [!NOTE]
> Enquanto o Ambari para o cluster é acessível diretamente pela Internet, alguns links da interface do Ambari (como para o JobTracker) não são expostos na internet. Portanto, você recebe erros de "servidor não encontrado" ao tentar acessar esses recursos, a menos que use um túnel Secure Shell (SSH) para o tráfego de proxy da web para o nó principal do cluster.

Para saber mais sobre como criar e usar um túnel SSH para funcionar com o Ambari, consulte [Usar túnel SSH para acessar a IU do Ambari Web, ResourceManager, JobHistory, NameNode, Oozie e outras interfaces de usuário na Web](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Interface do usuário da Ambari Web

Ao se conectar à interface do usuário do Ambari Web, você recebe uma solicitação para autenticar a página. Use o usuário do administrador de cluster (Admin padrão) e a senha usada durante a criação do cluster.

Quando a página se abrir, observe a barra na parte superior. Ela contém as seguintes informações e controles:

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Logotipo do Ambari** : abre o painel, que pode ser usado para monitorar o cluster.

* **Nº operações do nome de cluster** : exibe o número de operações do Ambari em andamento. A seleção do nome do cluster ou **Nº operações** exibe uma lista de operações em segundo plano.

* **Nº alertas** : avisos ou alertas críticos, se houver, para o cluster. Selecionar esta opção exibirá uma lista de alertas.

* **Painel** : exibe o painel.

* **Serviços** : informações e definições de configuração dos serviços do cluster.

* **Hosts** : informações e definições de configuração dos nós no cluster.

* **Alertas** : um log de informações, avisos e alertas críticos.

* **Admin** : pilha/serviços de software que estão instalados no cluster, informações de conta de serviço e segurança Kerberos.

* **Botão Admin** : gerenciamento, configurações de usuário e logoff do Ambari.

## <a name="monitoring"></a>Monitoramento

### <a name="alerts"></a>Alertas

O Ambari fornece vários alertas com uma das opções abaixo como o status:

* **OK**
* **Aviso**
* **CRÍTICO**
* **DESCONHECIDO**

Os alertas diferentes de **OK** fazem com que a entrada **nº alertas** na parte superior da página exiba o número de alertas. A seleção dessa entrada exibe os alertas e seus status.

Os alertas estão organizados em vários grupos padrão, que podem ser exibidos na página **Alertas** .

![página alertas](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Você pode gerenciar os grupos usando o menu **Ações** e selecionando **Gerenciar Grupos de Alerta**. Isso permite que você modifique os grupos existentes ou crie novos grupos.

![diálogo gerenciar grupos de alerta](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

Você também pode gerenciar os métodos de alerta e criar notificações de alerta no menu **Ações** selecionando __Gerenciar Notificações de Alerta__. Isso exibe quaisquer notificações atuais e permite que você crie novas notificações. As notificações podem ser enviadas por **EMAIL** ou **SNMP** quando ocorrerem combinações específicas de alerta/gravidade. Por exemplo, você pode enviar um alerta quando qualquer um dos alertas no grupo **YARN Padrão** está definido para **Crítico**.

![Diálogo Criar alerta](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Por fim, a seleção de __Gerenciar Definições de Alerta__ do menu __Ações__ permite que você defina o número de vezes que um alerta deve ocorrer antes do envio de uma notificação. Isso pode ser usado para impedir notificações para erros transitórios, por exemplo, quando um serviço falha em um nó principal e é reiniciado em outro nó principal.

### <a name="cluster"></a>HDInsight

A guia **Métricas** do painel contém uma série de widgets que facilitam monitorar o status do cluster em um relance. Vários widgets, tais como **Uso de CPU**, fornecem informações adicionais quando são clicados.

![painel com metrics](./media/hdinsight-hadoop-manage-ambari/metrics.png)

A guia **Mapa de Dados** exibe as métricas na forma de mapas de dados coloridos, que vão do verde ao vermelho.

![painel com heatmaps](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Para obter informações mais detalhadas sobre os nós no cluster, selecione **Hosts**e selecione o nó específico que de seu interesse.

![detalhes do host](./media/hdinsight-hadoop-manage-ambari/host-details.png)

### <a name="services"></a>Serviços

A barra lateral **Serviços** no painel oferece uma visão rápida do status dos serviços em execução no cluster. Vários ícones são usados para indicar o status ou as ações que devem ser realizadas, como um símbolo amarelo de reciclagem se um serviço precisa ser reciclado.

![barra lateral serviços](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

> [!NOTE]
> Os serviços exibidos variam de acordo com versões e tipos de cluster HDInsight. Os serviços exibidos aqui podem ser diferentes dos serviços exibidos para o cluster.

A seleção de um serviço exibirá informações mais detalhadas sobre o serviço.

![informações de resumo do serviço](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#### <a name="quick-links"></a>Links rápidos

Alguns serviços exibem um link **Links Rápidos** na parte superior da página. Ele pode ser usado para acessar IUs da Web específicas do serviço, como:

* **Histórico de Trabalhos** : histórico de trabalhos do MapReduce.
* **Gerenciador de Recursos** : IU do gerenciador de recursos YARN RM.
* **NameNode** : IU NameNode no HDFS (Sistema de Arquivos Distribuído do Hadoop).
* **IU da Web do Oozie** : IU do Oozie.

A seleção de qualquer um desses links abrirá uma nova guia em seu navegador, que exibirá a página selecionada.

> [!NOTE]
> A seleção de um link **Links Rápidos** de algum serviço resulta em um erro "servidor não encontrado", a não ser que você use um túnel SSL como proxy do tráfego da Web para o cluster. Isso ocorre porque os aplicativos Web usados para exibir essas informações não são expostos na Internet.
> 
> Para saber mais sobre como usar um túnel SSL com HDInsight, consulte [Usar túnel SSH para acessar a IU do Ambari Web, ResourceManager, JobHistory, NameNode, Oozie e outras IU da Web](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Gerenciamento

### <a name="ambari-users-groups-and-permissions"></a>Usuários, grupos e permissões do Ambari

O trabalho com usuários, grupos e permissões é suportado ao usar um cluster HDInsight [ingressado no domínio](hdinsight-domain-joined-introduction.md). Para obter informações sobre o uso da IU de Gerenciamento do Ambari em um cluster ingressado no domínio, consulte [Gerenciar clusters HDInsight ingressados no domínio](hdinsight-domain-joined-introduction.md).

> [!WARNING]
> Não altere a senha do watchdog Ambari (hdinsightwatchdog) no seu cluster HDInsight baseado em Linux. A alteração da senha interrompe a capacidade de usar as ações de script ou executar operações de dimensionamento com o cluster.

### <a name="hosts"></a>Hosts

A página **Hosts** lista todos os hosts no cluster. Para gerenciar hosts, siga estas etapas.

![página hosts](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [!NOTE]
> As ações de adicionar, encerrar ou reativar um host não devem ser usadas com clusters HDInsight.

1. Selecione o(s) host(s) que você deseja gerenciar.

2. Use o menu **Ações** para selecionar a ação que deseja executar:
   
   * **Iniciar todos os componentes** : iniciar todos os componentes no host.

   * **Parar todos os componentes** : parar todos os componentes no host.

   * **Reiniciar todos os componentes** : parar e iniciar todos os componentes no host.

   * **Ativar o modo de manutenção** : suprime os alertas sobre o host. Isso deve ser habilitado para que você execute ações que geram alertas, como reiniciar um serviço do qual dependem os serviços em execução.

   * **Desativar o modo de manutenção** : retorna o host para o modo de alerta normal.

   * **Parar** : Para o DataNode ou NodeManagers no host.

   * **Iniciar** : Inicia o DataNode ou NodeManagers no host.

   * **Reiniciar** : para e inicia o DataNode ou NodeManagers no host.

   * **Encerramento** : remove um host do cluster.
     
     > [!NOTE]
     > Não use esta ação em clusters HDInsight.

   * **Reativação** : adiciona ao cluster um host que foi encerrado.
     
     > [!NOTE]
     > Não use esta ação em clusters HDInsight.

### <a id="service"></a>Serviços

Na página **Painel** ou **Serviços**, use o botão **Ações** na parte inferior da lista de serviços para interromper e iniciar todos os serviços.

![Ações de Serviço](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [!WARNING]
> Enquanto **Adicionar Serviço** estiver listado nesse menu, ele não deverá ser usado para adicionar serviços ao cluster HDInsight. Novos serviços devem ser adicionados usando uma Ação de Script durante o provisionamento do cluster. Para saber mais sobre o uso de ações de script, consulte [Personalizar clusters HDInsight usando ações de script](hdinsight-hadoop-customize-cluster-linux.md).

Embora o botão **Ações** possa reiniciar todos os serviços, muitas vezes convém iniciar, parar ou reiniciar um serviço específico. Use as seguintes etapas para executar ações em um serviço individual:

1. Na página **Painel** ou **Serviços**, selecione um serviço.

2. Na parte superior da guia **Resumo**, use o botão **Ações de Serviço** e selecione a ação a tomar. Isso reinicia o serviço em todos os nós.
   
    ![ação de serviço](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)
   
   > [!NOTE]
   > Reiniciar alguns serviços enquanto o cluster estiver em execução pode gerar alertas. Para evitar isso, você pode usar o botão **Ações de Serviço** para habilitar o **modo Manutenção** para o serviço antes de executar a reinicialização.

3. Depois que uma ação é selecionada, a entrada **Nº operações** na parte superior da página aumenta para mostrar que está ocorrendo uma operação em segundo plano. Se a exibição estiver configurada, a lista de operações em segundo plano será exibida.
   
   > [!NOTE]
   > Se você habilitou o **modo Manutenção** para o serviço, lembre-se de desabilitá-lo usando o botão **Ações de Serviço** quando a operação for concluída.

Para configurar um serviço, use as seguintes etapas:

1. Na página **Painel** ou **Serviços**, selecione um serviço.

2. Selecione a guia **Configurações** . A configuração atual é exibida. Uma lista das configurações anteriores também é exibida.
   
    ![configurações](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Use os campos exibidos para modificar a configuração e selecione **Salvar**. Ou selecione uma configuração anterior e clique em **Tornar atual** para reverter para as configurações anteriores.

## <a name="ambari-views"></a>Exibições do Ambari

As Exibições do Ambari permitem que os desenvolvedores conectem elementos de interface do usuário à interface do usuário da Web do Ambari usando a [Estrutura de modos de exibição do Ambari](https://cwiki.apache.org/confluence/display/AMBARI/Views). O HDInsight fornece as seguintes exibições com tipos de cluster do Hadoop:

* Gerenciador de filas Yarn: o gerenciador de filas fornece uma interface do usuário simples para exibir e modificar filas YARN.

* Exibição do Hive: a Exibição do Hive permite executar consultas de Hive diretamente do seu navegador da Web. Você pode salvar consultas, exibir os resultados, salvar os resultados no armazenamento de cluster ou baixar os resultados no sistema local. Para obter mais informações sobre como usar Exibições do Hive, consulte [Usar Exibições do Hive com o HDInsight](hdinsight-hadoop-use-hive-ambari-view.md).

* Exibição do Tez: a Exibição do Tez permite que você entenda melhor e otimize trabalhos exibindo informações sobre como trabalhos do Tez são executados e quais recursos são usados pelo trabalho.


