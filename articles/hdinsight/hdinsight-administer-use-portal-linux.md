---
title: Gerenciar clusters do Apache Hadoop no HDInsight usando o portal do Azure
description: Aprenda a criar e gerenciar clusters HDInsight usando o portal do Azure.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: hrasheed
ms.openlocfilehash: ce30b752ecf1d5413ae534fa03907cbf11b1c694
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794474"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Gerenciar clusters do Apache Hadoop no HDInsight usando o portal do Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Usando o [portal do Microsoft Azure][azure-portal], você pode gerenciar clusters do [Apache Hadoop](https://hadoop.apache.org/) no Azure HDInsight. Use o seletor de guias acima para saber mais sobre como criar clusters Hadoop no HDInsight usando outras ferramentas.

**Pré-requisitos**
- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster existente do Apache Hadoop no HDInsight.  Consulte [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Introdução
Entre em [https://portal.azure.com](https://portal.azure.com).


## <a name="showClusters"></a> Listar e mostrar clusters
A página **Clusters do HDInsight** lista os clusters existentes.  Do portal:
1. Selecione **Todos os serviços** no menu esquerdo.
2. Selecione **Clusters do HDInsight** em **ANÁLISES**.

## <a name="homePage"></a> Página inicial do cluster 
Selecione o nome do cluster da página [ **Clusters HDInsight**](#showClusters).  Isso abrirá a exibição **Visão geral**, que é semelhante à seguinte imagem:

![Fundamentos do cluster HDInsight do portal do Azure](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Menu principal:**  
- **Mover**: Move o cluster para outro grupo de recursos ou outra assinatura.  
- **Excluir**: Exclui o cluster.  
- **Atualizar**:  Atualiza a exibição.

**Menu à esquerda:**  
 - **Menu do canto superior esquerdo**  
    - **Visão geral**:  Fornece informações gerais para o cluster.
    -  **Log de atividades**: Mostra e consulta logs de atividades.
    - **Controle de acesso (IAM)**: Utiliza as atribuições de função.  Consulte [Usar as atribuições de função para gerenciar o acesso aos recursos de assinatura do Azure](../role-based-access-control/role-assignments-portal.md).
    - **Marcas**: Permite estabelecer pares de chave/valor para definir uma taxonomia personalizada dos serviços de nuvem. Por exemplo, você pode criar uma chave chamada **projeto**e usar um valor comum para todos os serviços associados a um projeto específico.
    - **Diagnosticar e resolver problemas**: Exibe informações sobre solução de problemas.
    - **Início rápido**:  Exibe informações que ajudam você a começar a usar o HDInsight.
    - **Ferramentas**: Informações de ajuda para ferramentas relacionadas ao HDInsight.

- **Menu Configurações**  
  - **Tamanho do cluster**: Verifique, aumente e diminua o número de nós do trabalhador do cluster. Ver [Dimensionar clusters](hdinsight-administer-use-management-portal.md#scale-clusters).
  - **Limites de cota**: Exibe os núcleos usados e disponíveis para sua assinatura.
  - **SSH + logon do Cluster**: Mostra as instruções para se conectar ao cluster usando uma conexão Secure Shell (SSH). Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
  - **Data Lake Storage Gen1**: Configura o acesso ao Data Lake Storage Gen1.  Confira [Início Rápido: Configura clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).
  - **Contas de armazenamento**: Exibe as contas de armazenamento e as chaves. As contas de armazenamento são configuradas durante o processo de criação do cluster.
  - **Aplicativos**: Adicionar/remove aplicativos do HDInsight.  Consulte [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md).
  - **Ações de script**: Execute scripts Bash no cluster. Confira [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).
  - **Metastores externos**: Exiba os metastores [Apache Hive](https://hive.apache.org/) e [Apache Oozie](https://oozie.apache.org/). Os metastores só podem ser configurados durante o processo de criação do cluster.
  - **Parceiro HDInsight**: Adiciona/remove o parceiro do HDInsight atual.
  - **Propriedades**: Exibe as [propriedades do cluster](#properties).
  - **Bloqueios**: Adiciona um bloqueio para impedir que o cluster seja modificado ou excluído.
  -  **Script de automação**: Exibe e exporta o modelo do Azure Resource Manager para o cluster. No momento, você pode exportar apenas a conta de armazenamento do Azure dependente. Consulte [Criar clusters do Apache Hadoop baseados em Linux no HDInsight usando os modelos do Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

- **Menu Monitoramento**
  - **Alertas**: Gerencia os alertas e ações.
  - **Métricas**: Monitora as métricas do cluster no Azure Log Analytics.
  - **Configurações de diagnóstico**: Configurações sobre onde armazenar as métricas de diagnóstico.
  - **Operations Management Suite**:  monitore o seu cluster no Azure Operations Management Suite (OMS) e no Azure Log Analytics.

- **Menu Suporte + solução de problemas**
  - **Integridade de recursos**: Confira [Visão geral do Azure Resource Health](../service-health/resource-health-overview.md).
  - **Nova solicitação de suporte**: Permite criar um tíquete de suporte com o suporte da Microsoft.
    
## <a name="properties"></a> Propriedades do cluster
Na [página inicial do cluster](#homePage), em **Configurações**, selecione **Propriedades**.
* **Nome do host**: Nome do cluster.
* **URL do cluster**: A URL para a interface da Web do Ambari.
* **Secure Shell (SSH)**: O nome de usuário e o nome do host a ser usado para acessar o cluster por meio do SSH.
* **Status**: Um destes: Aborted, Accepted, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operational, Running, Error, Deleting, Deleted, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued ou ClusterCustomization.
* **Região**: Localização do Azure. Para ter acesso à lista de locais do Azure com suporte, veja a caixa de listagem suspensa **Região** em [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
* **Data de criação**: A data em que o cluster foi implantado.
* **Sistema operacional**: **Windows** ou **Linux**.
* **Tipo**: Hadoop, HBase, Storm, Spark.
* **Versão**. Consulte [HDInsight versions](hdinsight-component-versioning.md) (Versões do HDInsight).
* **Assinatura**: Nome da assinatura.
* **Fonte de dados padrão**: O sistema de arquivos padrão do cluster.
* **Tamanho dos nós de trabalho**: O tamanho selecionado da VM dos nós de trabalho.
* **Tamanho do nó de cabeçalho**: O tamanho selecionado da VM dos nós de cabeçalho.
* **Rede virtual**: O nome da Rede Virtual na qual o cluster é implantado, caso uma tenha sido selecionada durante a implantação.

## <a name="move-clusters"></a>Mover clusters

Você pode mover um cluster do HDInsight para outro grupo de recursos do Azure ou outra assinatura. 

Na [home page do cluster](#homePage):

1. Selecione **Mover** no menu superior.
2. Selecione **Mover para outro grupo de recursos** ou **Mover para outra assinatura**.
3. Siga as instruções da página nova.

## <a name="delete-clusters"></a>Excluir clusters
Excluir um cluster não excluirá a conta de armazenamento padrão, nem nenhuma conta de armazenamento vinculada. Você pode recriar o cluster usando as mesmas contas de armazenamento e as mesmas metastores. Recomendamos usar um novo contêiner de Blobs padrão ao recriar o cluster.

Na [home page do cluster](#homePage):

1. Selecione **Excluir** no menu superior.
2. Siga as instruções da página nova.

Consulte também [Pausar/desligar clusters](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Adicionar outras contas de armazenamento

Depois de criar um cluster, você pode adicionar outras contas do Armazenamento do Azure e contas do Azure Data Lake Storage. Para saber mais, confira [Adicionar outras contas de armazenamento ao HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Dimensionar clusters
O recurso de colocação em escala de clusters permite que você altere o número de nós de trabalhador usados por um cluster Azure HDInsight sem precisar recriar o cluster.

> [!NOTE]  
> Somente clusters HDInsight versão 3.1.3 ou superior são compatíveis. Se não tiver certeza quanto à versão de seu cluster, você poderá verificar a página Propriedades.  Confira [Listar e mostrar clusters](#list-and-show-clusters).

Na [home page do cluster](#homePage):

1. Em **Configurações**, selecione **Tamanho do cluster**.
2. Insira **Número de nós de trabalho** na caixa de texto numérica. O limite no número de nós de cluster varia entre as assinaturas do Azure. Contate o suporte de cobrança para aumentar o limite.  As informações de custo refletem as alterações feitas no número de nós.
3. Clique em **Salvar**.

    ![HDInsight hadoop hbase storm spark dimensionar](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster2.png)

O impacto da alteração do número de nós de dados varia em cada tipo de cluster com suporte do HDInsight:

* Apache Hadoop

    Você pode aumentar continuamente o número de nós de trabalhador em um cluster Hadoop em execução sem afetar os trabalhos pendentes ou em execução. Novos trabalhos também podem ser enviados enquanto a operação está em andamento. Falhas em uma operação de dimensionamento são normalmente manipuladas para que o cluster sempre seja deixado em um estado funcional.

    Quando um cluster Hadoop é reduzido verticalmente pela diminuição do número de nós de dados, alguns dos serviços no cluster são reiniciados. Esse comportamento faz com que todos os trabalhos em execução e pendentes falhem após a conclusão da operação de dimensionamento. Você pode, no entanto, reenviar os trabalhos quando a operação for concluída.
* HBase no Apache

    Você pode adicionar ou remover diretamente nós do cluster HBase enquanto ele é executado. Servidores Regionais são equilibrados automaticamente em alguns minutos após o término da operação de dimensionamento. No entanto, você pode equilibrar manualmente os servidores regionais fazendo logon no nó de cabeçalho do cluster e executando os seguintes comandos em uma janela de prompt de comando:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Para obter mais informações sobre como usar o shell do HBase, consulte [Introdução a um exemplo do Apache HBase em HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Você pode adicionar ou remover nós de dados continuamente para seu cluster Strom enquanto ele é executado. Contudo, após a conclusão bem-sucedida da operação de colocação em escala, será necessário redistribuir a topologia.

    A redistribuição pode ser feita de duas maneiras:

  * Interface da Web Storm
  * Ferramenta CLI (interface de linha de comando)

    Consulte a [documentação do Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.

    A IU da Web do Storm está disponível no cluster HDInsight:

    ![Redistribuir escala do Storm do HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Veja um exemplo do comando CLI para redistribuir a topologia do Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```


## <a name="pauseshut-down-clusters"></a>Pausar/desligar clusters

A maioria dos trabalhos do Hadoop é composta por trabalhos em lotes que só são executados ocasionalmente. Para a maioria dos clusters do Hadoop, há grandes períodos de tempo em que o cluster não está sendo usado para processamento. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso.
Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso.

Há várias maneiras de programar o processo:

* Use o Azure Data Factory. Consulte [Criar clusters Apache Hadoop baseados em Linux sob demanda no HDInsight usando o Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) para criar serviços vinculados HDInsight sob demanda.
* Use o Azure PowerShell.  Consulte [Analisar dados de atraso de voo](hdinsight-analyze-flight-delay-data.md).
* Use a CLI Clássica do Azure. Consulte [Gerenciar clusters HDInsight usando a CLI Clássica do Azure](hdinsight-administer-use-command-line.md).
* Use o SDK .NET do HDInsight. Ver [trabalhos de envio do Apache Hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Para saber mais sobre preços, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Para excluir um cluster do Portal, veja [Excluir clusters](#delete-clusters)



## <a name="upgrade-clusters"></a>Atualizar clusters

Consulte [Atualizar o cluster do HDInsight para uma versão mais recente](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Abra a interface do usuário do Apache Ambari web

O Ambari fornece uma interface do usuário da Web de gerenciamento do Hadoop intuitiva e fácil de usar com suporte de suas APIs RESTful. O Ambari permite aos administradores do sistema gerenciar e monitorar clusters Hadoop.

Na [home page do cluster](#homePage):

1. Selecione **Painéis do cluster**.

    ![Menu do cluster HDInsight Hadoop](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Selecione **Página inicial do Ambari** na página nova.
2. Insira o nome de usuário do cluster e a senha.  O nome de usuário do cluster padrão é _admin_. A interface do usuário da Web do Ambari tem esta aparência:

Para obter mais informações, consulte [Gerenciar clusters do HDInsight usando a interface da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Alterar senhas
Um cluster HDInsight pode ter duas contas de usuário. A conta de usuário do cluster HDInsight (também conhecido como conta de usuário HTTP) e a conta de usuário SSH são criadas durante o processo de criação. Você pode usar a interface do usuário do portal para alterar a senha da conta de usuário do cluster e ações de script para alterar a conta de usuário do SSH.

### <a name="change-the-cluster-user-password"></a>Alterar a senha de usuário do cluster

> [!NOTE]  
> Alterar a senha de usuário (admin) do cluster poderá fazer as ações de script executadas em relação a esse cluster falharem. Se você tiver ações de script persistente direcionadas para nós de trabalho, esses scripts poderão falhar quando você adicionar nós ao cluster por meio de operações de redimensionamento. Para saber mais sobre as Ações de Script, confira [Personalizar clusters HDInsight usando ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

Na [home page do cluster](#homePage):
1. Selecione **SSH + login do cluster** em **Configurações**.
2. Selecione **Redefinir credencial**.
3. Insira e confirme a nova senha nas caixas de texto.
4. Selecione **OK**.

A senha é alterada em todos os nós no cluster.

### <a name="change-the-ssh-user-password"></a>Alterar a senha de usuário de SSH
1. Usando um editor de texto, salve o seguinte texto como um arquivo denominado **changepassword.sh**.

    > [!IMPORTANT]  
    > Você deve usar um editor que usa LF como o fim da linha. Se o editor usar CRLF, então ele não funcionará.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Carregue o arquivo para um local de armazenamento que possa ser acessado no HDInsight usando um endereço HTTP ou HTTPS. Por exemplo, um repositório de arquivos público como o OneDrive ou o Armazenamento de Blobs do Azure. Salve o URI (endereço HTTP ou HTTPS) no arquivo, pois esse URI será necessário na próxima etapa.
3. Na [home page do cluster](#homePage), selecione **Ações do script**, em **Configurações**.
4. Na folha **Ações de Script**, selecione **Enviar Novo**. 
5. Na folha **Enviar ação do script**, insira as seguintes informações:

   | Campo | Valor |
   | --- | --- |
   | Tipo de script | Selecione **- Personalizar** na lista suspensa.|
   | NOME |"Alterar senha ssh" |
   | URI do script Bash |O URI do arquivo changepassword.sh |
   | Tipos de nó: (Principal, Trabalho, Nimbus, Supervisor, Zookeeper etc.) |✓ para todos os tipos de nó listados |
   | parâmetros |Insira o nome de usuário SSH e a nova senha. Deve haver um espaço entre o nome de usuário e a senha. |
   | Persistir esta ação de script... |Deixe este campo desmarcado. |
5. Selecione **Criar** para aplicar o script. Quando o script for concluído, você poderá se conectar ao cluster usando SSH com a nova senha.

## <a name="grantrevoke-access"></a>Conceder/revogar acesso
Os clusters HDInsight têm os seguintes serviços Web HTTP (todos esses serviços têm pontos de extremidade RESTful):

* ODBCODBC
* JDBC
* Ambari
* Oozie
* Templeton

Por padrão, esses serviços são concedidos para acesso. Você pode revogar/conceder o acesso usando [CLI do Azure Classic](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) e [do Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Localizar a ID da assinatura
Cada cluster é vinculado a uma assinatura do Azure.  A ID de assinatura do Azure está visível na [página inicial do cluster](#homePage).

## <a name="find-the-resource-group"></a>Encontrar o grupo de recursos
No modo Azure Resource Manager, cada cluster HDInsight é criado com um grupo do Azure Resource Manager. O grupo Resource Manager está visível na [página inicial do cluster](#homePage).

## <a name="find-the-storage-accounts"></a>Localizar contas de armazenamento
Os clusters do HDInsight usam uma conta de Armazenamento do Azure ou um Azure Data Lake Storage para armazenar dados. Cada cluster do HDInsight pode ter uma conta de armazenamento padrão e um número de contas de armazenamento vinculadas. Para listar as contas de armazenamento, na [página inicial do cluster](#homePage), em **Configurações**, selecione **Contas de armazenamento**.


## <a name="monitor-jobs"></a>Monitorar trabalhos
Consulte [Gerenciar clusters do HDInsight usando a interface da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).


## <a name="monitor-cluster-usage"></a>Monitorar o uso do cluster
A seção **Uso** da folha do cluster do HDInsight exibe informações sobre o número de núcleos disponíveis para sua assinatura para uso com o HDInsight, bem como o número de núcleos alocados para esse cluster e como eles são alocados para os nós presentes no cluster. Confira [Listar e mostrar clusters](#list-and-show-clusters).

> [!IMPORTANT]  
> Para monitorar os serviços fornecidos pelo cluster HDInsight, você deve usar o Ambari Web ou a API REST do Ambari. Para obter mais informações sobre o uso do Ambari, consulte [Gerenciar clusters do HDInsight usando o Apache Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Conectar a um cluster

* [Usar o Apache Hive com o HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Use o SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu algumas funções administrativas básicas. Para saber mais, confira os seguintes artigos:

* [Administrar o HDInsight usando o PowerShell do Azure](hdinsight-administer-use-powershell.md)
* [Administrar o HDInsight usando a CLI Clássica do Azure](hdinsight-administer-use-command-line.md)
* [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Leia mais sobre como usar a interface da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Detalhes sobre como usar a API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Usar o Apache Hive no HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o Apache Pig no HDInsight](hadoop/hdinsight-use-pig.md)
* [Usar o Apache Sqoop no HDInsight](hadoop/hdinsight-use-sqoop.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Qual versão do Apache Hadoop está no Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Linha de comando do Hadoop"
