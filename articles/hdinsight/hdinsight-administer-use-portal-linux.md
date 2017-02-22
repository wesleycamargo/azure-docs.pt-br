---
title: Gerenciar clusters Hadoop no HDInsight usando o portal do Azure | Microsoft Docs
description: Aprenda a criar e gerenciar clusters HDInsight usando o portal do Azure.
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5a76f897-02e8-4437-8f2b-4fb12225854a
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 8c439591dae0bd561ba4f079aba9f2073d1e954b
ms.openlocfilehash: dc94c3f428200f107f7e953a57cf4e0e83bc7de1


---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Gerenciar clusters Hadoop no HDInsight Usando o portal do Azure
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Com o [Portal do Azure][azure-portal], você pode gerenciar clusters no Azure HDInsight. Use o seletor de guias para saber mais sobre como criar clusters Hadoop no HDInsight usando outras ferramentas. 

**Pré-requisitos**

Antes de começar este artigo, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="open-the-portal"></a>Abra o Portal
1. Entre em [https://portal.azure.com](https://portal.azure.com).
2. Depois de abrir o portal, você poderá:
   
   * Clique em **Novo** no menu esquerdo para criar um novo cluster:
     
       ![novo botão do cluster HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)
   * Clique em **Clusters HDInsight** no menu à esquerda para listar os clusters existentes
     
       ![Botão do cluster HDInsight do Portal do Azure](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

       Se você não vir o cluster HDInsight, clique em **Mais serviços** na parte inferior da lista e clique em **clusters HDInsight** na seção **Inteligência + Análises**.

## <a name="create-clusters"></a>Criar clusters
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

O HDInsight trabalha com uma ampla variedade de componentes do Hadoop. Para obter a lista dos componentes que foram verificados e suportados, consulte [Qual versão do Hadoop está no Azure HDInsight](hdinsight-component-versioning.md). Para obter informações gerais sobre a criação do cluster, consulte [Criar clusters Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="list-and-show-clusters"></a>Listar e mostrar clusters
1. Entre em [https://portal.azure.com](https://portal.azure.com).
2. Clique em **Clusters HDInsight** no menu à esquerda para listar os clusters existentes.
3. Clique no nome do cluster. Se a lista de clusters for longa, você poderá usar o filtro na parte superior da página.
4. Clique em um cluster da lista para ver a página de visão geral:
   
    ![Fundamentos do cluster HDInsight do portal do Azure](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png)
   
    **Menu de visão geral:**

   * **Painel**: abre o painel do cluster, que é o Ambari Web para clusters baseados em Linux.
   * **Secure Shell**: mostra as instruções para se conectar ao cluster usando uma conexão Secure Shell (SSH).
   * **Escalar Cluster**: permite alterar o número de nós de trabalho deste cluster.
   * **Excluir**: exclui o cluster.

    **Menu à esquerda:**
   
   * **Logs de Atividade**: mostrar e consultar logs de atividade.
   * **Controle de Acesso (IAM)**: usar atribuições de função.  Consulte [Usar as atribuições de função para gerenciar o acesso aos recursos de assinatura do Azure](../active-directory/role-based-access-control-configure.md).
   * **Marcas**: permitem estabelecer pares de chave/valor para definir uma taxonomia personalizada dos serviços de nuvem. Por exemplo, você pode criar uma chave chamada **projeto**e usar um valor comum para todos os serviços associados a um projeto específico.
   * **Diagnosticar e resolver problemas**: exibir informações de solução de problemas.
   * **Bloqueios**: adicionar bloqueio para impedir que o cluster seja modificado ou excluído.
   * **Script de automação**: exibir e exportar o modelo do Azure Resource Manager para o cluster. No momento, você pode exportar apenas a conta de armazenamento do Azure dependente. Consulte [Criar clusters Hadoop baseados em Linux no HDInsight usando modelos do Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
   * **Início Rápido**: exibe informações que o ajudarão a começar a usar o HDInsight.
   * **Ferramentas para HDInsight**: informações de ajuda para ferramentas relacionadas ao HDInsight.
   * **Logon do Cluster**: exibir as informações de logon do cluster.
   * **Uso de núcleo de assinatura**: exibir os núcleos usados e disponíveis para sua assinatura. 
   * **Dimensionar o Cluster**: aumente e diminua o número de nós de trabalho do cluster. Consulte [Dimensionar clusters](hdinsight-administer-use-management-portal.md#scale-clusters).
   * **Secure Shell**: mostra as instruções para se conectar ao cluster usando uma conexão Secure Shell (SSH). Confira [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows com PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md) e [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
   * **Parceiro HDInsight**: adicione/remova o Parceiro HDInsight atual.
   * **Metastores Externos**: exiba os Hive e Oozie metastores. Os metastores só podem ser configurados durante o processo de criação do cluster. Consulte [usar metastore do Hive/Oozie](hdinsight-hadoop-provision-linux-clusters.md#use-hiveoozie-metastore).
   * **Ações de Script**: execute scripts Bash no cluster. Confira [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).
   * **Aplicativos**: adicionar ou remover aplicativos do HDInsight.  Consulte [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md).
   * **Propriedades**: exiba as propriedades do cluster.
   * **Contas de armazenamento**: exibir as contas de armazenamento e as chaves. As contas de armazenamento são configuradas durante o processo de criação do cluster.
   * **Identidade do AAD do Cluster**: 
   * **Nova solicitação de suporte**: permite que você crie um tíquete de suporte com o suporte da Microsoft.

6. Clique em **Propriedades**:
   
    As propriedades são:
   
   * **Nome do host**: nome do cluster.
   * **URL do Cluster**. A URL para a interface da Web do Ambari.
   * **Status**: incluindo Aborted, Accepted, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operational, Running, Error, Deleting, Deleted, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, ClusterCustomization
   * **Região**: local do Azure. Para ter acesso à lista de locais do Azure com suporte, consulte a caixa de listagem suspensa **Região** em [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Data de criação**.
   * **Sistema operacional**: **Windows** ou **Linux**.
   * **Tipo**: Hadoop, HBase, Storm, Spark. 
   * **Versão**. Consulte [versões do HDInsight](hdinsight-component-versioning.md)
   * **Assinatura**: nome da assinatura.
   * **Fonte de dados padrão**: o sistema de arquivos padrão do cluster.
   * **Tamanho dos nós de trabalho**.
   * **Tamanho do nó de cabeçalho**.

## <a name="delete-clusters"></a>Excluir clusters
Excluir um cluster não excluirá a conta de armazenamento padrão, nem nenhuma conta de armazenamento vinculada. Você pode recriar o cluster usando as mesmas contas de armazenamento e as mesmas metastores. É recomendável usar um novo contêiner de Blobs padrão ao recriar o cluster.

1. Conecte-se no [Portal][azure-portal].
2. Clique em **Clusters HDInsight** no menu esquerdo. Se você não vir **Clusters HDInsight**, clique primeiro em **Mais serviços**.
3. Clique no cluster que você deseja excluir.
4. Clique em **Excluir** no menu superior e siga as instruções.

Consulte também [Pausar/desligar clusters](#pauseshut-down-clusters).

## <a name="scale-clusters"></a>Dimensionar clusters
O recurso de dimensionamento de clusters permite que você altere o número de nós de trabalhador usados por um cluster em execução no Azure HDInsight sem precisar recriar o cluster.

> [!NOTE]
> Somente clusters HDInsight versão 3.1.3 ou superior são compatíveis. Se não tiver certeza quanto à versão de seu cluster, você poderá verificar a página Propriedades.  Confira [Listar e mostrar clusters](#list-and-show-clusters).
> 
> 

O impacto da alteração do número de nós de dados em cada tipo de cluster com suporte do HDInsight:

* O Hadoop
  
    Você pode aumentar continuamente o número de nós de trabalhador em um cluster Hadoop em execução sem afetar os trabalhos pendentes ou em execução. Novos trabalhos também podem ser enviados enquanto a operação está em andamento. Falhas em uma operação de dimensionamento são normalmente manipuladas para que o cluster sempre seja deixado em um estado funcional.
  
    Quando um cluster Hadoop é reduzido verticalmente pela diminuição do número de nós de dados, alguns dos serviços no cluster são reiniciados. Isso faz com que todos os trabalhos em execução e pendentes falhem após a conclusão da operação de dimensionamento. Você pode, no entanto, reenviar os trabalhos quando a operação for concluída.
* HBase
  
    Você pode adicionar ou remover diretamente nós do cluster HBase enquanto ele é executado. Servidores Regionais são equilibrados automaticamente em alguns minutos após o término da operação de dimensionamento. No entanto, você pode equilibrar manualmente os servidores regionais fazendo logon no nó de cabeçalho do cluster e executando os seguintes comandos em uma janela de prompt de comando:
  
        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer
  
    Para obter mais informações sobre como usar o shell do HBase, confira []
* Storm
  
    Você pode adicionar ou remover nós de dados continuamente para seu cluster Strom enquanto ele é executado. Mas, após a conclusão bem-sucedida da operação de dimensionamento, você precisará redistribuir a topologia.
  
    A redistribuição pode ser feita de duas maneiras:
  
  * Interface da Web Storm
  * Ferramenta CLI (interface de linha de comando)
    
    Consulte a [documentação do Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.
    
    A IU da Web do Storm está disponível no cluster HDInsight:
    
    ![Redistribuir escala do Storm do HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.storm.rebalance.png)
    
    Aqui está um exemplo de como usar o comando CLI para reequilibrar a topologia do Storm:
    
        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors
        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

**Para dimensionar clusters**

1. Conecte-se no [Portal][azure-portal].
2. Clique em **Clusters HDInsight** no menu esquerdo.
3. Clique no cluster que você deseja dimensionar.
3. Clique em **Dimensionar Cluster**.
4. Insira o **Número de Nós de Trabalho**. O limite no número de nós do cluster varia entre as assinaturas do Azure. Contate o suporte de cobrança para aumentar o limite.  As informações de custo refletirão as alterações feitas no número de nós.
   
    ![HDInsight hadoop hbase storm spark dimensionar](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.png)

## <a name="pauseshut-down-clusters"></a>Pausar/desligar clusters

A maioria dos trabalhos do Hadoop é composta por trabalhos em lotes que só são executados ocasionalmente. Para a maioria dos clusters do Hadoop, há grandes períodos de tempo em que o cluster não está sendo usado para processamento. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso.
Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso.

Há várias maneiras de programar o processo:

* Use o Azure Data Factory. Veja [Criar clusters Hadoop baseados em Linux sob demanda usando o Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) para criar serviços vinculados do HDInsight sob demanda.
* Use o Azure PowerShell.  Consulte [Analisar dados de atraso de voo](hdinsight-analyze-flight-delay-data.md).
* Use a CLI do Azure. Consulte [Gerenciar clusters HDInsight usando a CLI do Azure](hdinsight-administer-use-command-line.md)
* Use o SDK .NET do HDInsight. Consulte [Enviar trabalhos do Hadoop](hdinsight-submit-hadoop-jobs-programmatically.md).

Para saber mais sobre preços, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Para excluir um cluster do Portal, veja [Excluir clusters](#delete-clusters)

## <a name="change-passwords"></a>Alterar senhas
Um cluster HDInsight pode ter duas contas de usuário. A conta de usuário do cluster HDInsight (também conhecido como conta de usuário HTTP) e a conta de usuário SSH são criadas durante o processo de criação. Você pode usar a interface do usuário do Ambari Web para alterar o nome de usuário e a senha da conta de usuário do cluster e ações de script para alterar a conta de usuário do SSH

### <a name="change-the-cluster-user-password"></a>Alterar a senha de usuário do cluster
Você pode usar a interface do usuário do Ambari Web para alterar a senha do usuário do cluster. Para fazer logon no Ambari, você precisa usar o nome de usuário e a senha existente do cluster.

> [!NOTE]
> Se você alterar a senha de usuário (admin) do cluster, isso poderá fazer com que ações de script executadas em relação a esse cluster falhem. Se você tiver ações de script persistente direcionadas para nós de trabalho, elas poderão falhar quando você adicionar nós ao cluster por meio de operações de redimensionamento. Para saber mais sobre as Ações de Script, confira [Personalizar clusters HDInsight usando ações de Script](hdinsight-hadoop-customize-cluster-linux.md).
> 
> 

1. Entre na interface do usuário do Ambari Web usando as credenciais de usuário do cluster HDInsight. O nome de usuário padrão é **admin**. A URL é **https://&lt;Nome do Cluster HDInsight>azurehdinsight.net**.
2. Clique em **Administrador** no menu superior e depois clique em "Gerenciar Ambari". 
3. No menu à esquerda, clique em **Usuários**.
4. Clique em **Administrador**.
5. Clique em **Alterar Senha**.

Em seguida, o Ambari altera a senha em todos os nós no cluster.

### <a name="change-the-ssh-user-password"></a>Alterar a senha de usuário de SSH
1. Usando um editor de texto, salve o seguinte texto como um arquivo denominado **changepassword.sh**.
   
   > [!IMPORTANT]
   > Você deve usar um editor que usa LF como o fim da linha. Se o editor usar CRLF, o script não funcionará.
   > 
   > 
   
        #! /bin/bash
        USER=$1
        PASS=$2
   
        usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
2. Carregue o arquivo para um local de armazenamento que possa ser acessado no HDInsight usando um endereço HTTP ou HTTPS. Por exemplo, um repositório de arquivos público como o OneDrive ou o Armazenamento de Blobs do Azure. Salve o URI (endereço HTTP ou HTTPS) no arquivo, pois isso é necessário na próxima etapa.
3. No Portal do Azure, clique em **Clusters HDInsight**.
4. Clique em seu cluster HDInsight.
4. Clique em **Ações de Script**.
4. Na folha **Ações de Script**, selecione **Enviar Novo**. Quando a folha **Enviar ação de script** aparecer, insira as informações a seguir.
   
   | Campo | Valor |
   | --- | --- |
   | Nome |Alterar senha SSH |
   | URI do script Bash |O URI do arquivo changepassword.sh |
   | Nós (Principal, Trabalho, Nimbus, Supervisor, Zookeeper etc.) |✓ para todos os tipos de nós listados |
   | Parâmetros |Insira o nome de usuário SSH e a nova senha. Deve haver um espaço entre o nome de usuário e a senha. |
   | Persistir esta ação de script... |Deixe este campo desmarcado. |
5. Selecione **Criar** para aplicar o script. Quando o script for concluído, você poderá se conectar ao cluster usando SSH com a nova senha.

## <a name="grantrevoke-access"></a>Conceder/revogar acesso
Os clusters HDInsight têm os seguintes serviços Web HTTP (todos esses serviços têm pontos de extremidade RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Por padrão, esses serviços são concedidos para acesso. Você pode revogar/conceder o acesso usando a [CLI do Azure](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) e o [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Localizar a ID da assinatura

**Para localizar suas IDs da assinatura do Azure**

1. Conecte-se no [Portal][azure-portal].
2. Clique em **Assinaturas**. Cada assinatura tem um nome e uma ID.

Cada cluster é vinculado a uma assinatura do Azure. A ID da assinatura é mostrada no bloco **Fundamentos** do cluster. Consulte [Listar e mostrar clusters](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Encontrar o grupo de recursos
No modo Azure Resource Manager, cada cluster HDInsight é criado com um grupo do Azure Resource Manager. O grupo do Resource Manager ao qual um cluster pertence aparece em:

* A lista de clusters tem uma coluna **Grupo de Recursos** .
* Bloco **Fundamentos** do cluster.  

Consulte [Listar e mostrar clusters](#list-and-show-clusters).

## <a name="find-the-default-storage-account"></a>Encontrar a conta de armazenamento padrão
Cada cluster HDInsight tem uma conta de armazenamento padrão. A conta de armazenamento padrão e as chaves para um cluster aparecem em **Contas de Armazenamento**. Consulte [Listar e mostrar clusters](#list-and-show-clusters).

## <a name="run-hive-queries"></a>Execute consultas Hive
Você não pode executar o trabalho do Hive diretamente do portal do Azure, mas pode usar a Exibição do Hive na interface do usuário do Ambari Web.

**Para executar consultas do Hive usando a Exibição do Hive do Ambari**

1. Entre na interface do usuário do Ambari Web usando as credenciais de usuário do cluster HDInsight. O nome de usuário padrão é **admin**. A URL é **https://&lt;Nome do Cluster HDInsight>azurehdinsight.net**.
2. Abra a Exibição do Hive como mostrado nesta captura de tela:  
   
    ![Exibição do Hive do HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)
3. Clique em **Consulta** no menu superior.
4. Insira uma consulta Hive no **Editor de Consultas**, em seguida, clique em **Executar**.

## <a name="monitor-jobs"></a>Monitorar trabalhos
Confira [Gerenciar clusters HDInsight usando a interface do usuário do Ambari Web](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="browse-files"></a>Procurar arquivos
Usando o portal do Azure, você pode procurar conteúdos do contêiner padrão.

1. Entre em [https://portal.azure.com](https://portal.azure.com).
2. Clique em **Clusters HDInsight** no menu à esquerda para listar os clusters existentes.
3. Clique no nome do cluster. Se a lista de clusters for longa, você poderá usar o filtro na parte superior da página.
4. Clique em **Contas de Armazenamento** no menu esquerdo do cluster.
5. Clique em uma conta de armazenamento.
7. Clique no bloco **Blobs** .
8. Clique no nome de contêiner padrão.

## <a name="monitor-cluster-usage"></a>Monitorar o uso do cluster
A seção **Uso** da folha do cluster do HDInsight exibe informações sobre o número de núcleos disponíveis para sua assinatura para uso com o HDInsight, bem como o número de núcleos alocados para esse cluster e como eles são alocados para os nós presentes no cluster. Consulte [Listar e mostrar clusters](#list-and-show-clusters).

> [!IMPORTANT]
> Para monitorar os serviços fornecidos pelo cluster HDInsight, você deve usar o Ambari Web ou a API REST do Ambari. Para saber mais sobre como usar o Ambari, consulte [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md)
> 
> 

## <a name="connect-to-a-cluster"></a>Conectar a um cluster
Confira [Usar o Hive com Hadoop no HDInsight com SSH](hdinsight-hadoop-use-hive-ssh.md#ssh).

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como criar um cluster HDInsight usando o Portal e como abrir a ferramenta de linha de comando do Hadoop. Para saber mais, consulte os seguintes artigos:

* [Administrar o HDInsight usando o PowerShell do Azure](hdinsight-administer-use-powershell.md)
* [Administrar o HDInsight usando a CLI do Azure](hdinsight-administer-use-command-line.md)
* [Criar clusters HDInsight](hdinsight-provision-clusters.md)
* [Usar o Hive no HDInsight](hdinsight-use-hive.md)
* [Usar o Pig no HDInsight](hdinsight-use-pig.md)
* [Usar o Sqoop no HDInsight](hdinsight-use-sqoop.md)
* [Introdução ao Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Qual versão do Hadoop está no Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Linha de comando do Hadoop"



<!--HONumber=Feb17_HO1-->


