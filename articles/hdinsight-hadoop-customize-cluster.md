<properties linkid="customize HDInsight clusters using Script Action" urlDisplayName="Customize HDInsight clusters using Script Action" pageTitle="Personalizar os Clusters HDInsight usando a ação de Script| Azure"metaKeywords ="" description="Saiba como personalizar os clusters HDInsight usando a ação do Script." metaCanonical="" services="hdinsight" documentationCenter="" title="Customize HDInsight clusters using Script Action" authors="nitinme" solutions="" manager="paulettm" editor="cgronlun" /> 

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/14/2014" ms.author="nitinme" />

# Personalizar os clusters HDInsight usando a ação de Script (visualização)

Você pode personalizar um cluster HDInsight do Azure para instalar software adicional em um cluster, ou para alterar a configuração de aplicativos no cluster. O HDInsight fornece uma opção de configuração chamada **ação de Script** que chama scripts personalizados, que definem a personalização para ser executada no cluster. Esses scripts podem ser usados para personalizar um cluster *enquanto ele está sendo implantado*.  

Os clusters HDInsight podem ser personalizados em diversas maneiras, como, incluindo contas de armazenamento adicional, alterando os arquivos de configuração do hadoop (core-site, hive-site, etc.) ou adicionando bibliotecas compartilhadas (p. ex.: Hive, Oozie) em locais comuns do cluster. Essas personalizações podem ser feitas usando o PowerShell do HDInsight, o SDK do .NET ou o Portal de Gerenciamento. Para obter mais informações, consulte [Provisionar clusters do Hadoop no HDInsight usando as opções personalizadas][hdinsight-provision-cluster].



> [WACOM.NOTE] Usar a ação de Script para personalizar um cluster tem suporte apenas no cluster HDInsight versão 3.1. Para obter mais informações sobre versões do cluster HDInsight, consulte [versões do cluster HDInsight](http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-component-versioning/).



## Neste artigo

- [Como o script é usado durante a criação do cluster?](#lifecycle)
- [Como escrever um script para personalização do cluster?](#writescript)
- [Exemplos de personalização do cluster](#example)


## <a name="lifecycle"></a>Como o script é usado durante a criação do cluster?

Usando a ação de Script, um cluster HDInsight pode ser personalizado somente enquanto ele está sendo criado. Enquanto um cluster HDInsight está sendo criado, ele passa pelos seguintes estágios:

![HDInsight cluster customization and stages during cluster provisioning][img-hdi-cluster-states] 

O script é chamado após a criação de cluster completar o estágio *HDInsightConfiguration* e antes do estágio *ClusterOperational*. Cada cluster pode aceitar várias ações de script que são chamadas na ordem em que elas são especificadas.

> [WACOM.NOTE] A opção de personalizar os clusters HDInsight está disponível como parte das assinaturas padrões do HDInsight do Azure sem nenhum custo adicional.

### Como o script funciona?

Você tem a opção de executar o script no headnode, nos nós de trabalho ou em ambos. Quando o script é executado, o cluster insere o estágio *ClusterCustomization*. Nesse estágio, o script é executado na conta de administrador do sistema, em paralelo com todos os nós especificados no cluster e fornece privilégios totais de administrador nos nós. 

> [WACOM.NOTE] Por ter privilégios administrativos nos nós de cluster durante o estágio *personalização de Cluster*, você pode usar o script para executar operações como parar e iniciar serviços, incluindo serviços relacionados ao Hadoop. Como parte do script, você deve verificar que os serviços de Ambari e outros serviços relacionados ao Hadoop estão em funcionamento antes do script terminar a execução. Esses serviços são necessários para verificar a integridade e o estado do cluster enquanto ele estiver sendo criado. Se você alterar qualquer configuração no cluster que afete esses serviços, é necessário usar as funções auxiliares que são fornecidas. Para obter mais informações sobre funções auxiliares, consulte [desenvolvimento de ação de Script com o HDInsight][hdinsight-write-script].

O resultado, bem como os logs de erro do script são armazenados na conta de armazenamento padrão que você especificou para o cluster. Os logs são armazenados em uma tabela com o nome *u<\cluster-name-fragment><\time-stamp>setuplog*. Esses são logs de agregação de scripts executados em todos os nós (nós headnode e de trabalho) no cluster.

## <a name="writescript"></a>Como escrever um script para personalização do cluster?

Para obter informações sobre como escrever um script de personalização do cluster, consulte [desenvolvimento de ação de Script com o HDInsight][hdinsight-write-script]. 

## <a name="example"></a>Exemplos de personalização do cluster?

Para começar, o HDInsight oferece scripts de exemplo para instalar o Spark e o R em um cluster HDInsight.

- Para obter instruções sobre como instalar o Spark, consulte [Instalar o Spark em clusters HDInsight][hdinsight-install-spark].
- Para obter instruções sobre como instalar o R, consulte [Instalar R em clusters HDInsight][hdinsight-install-r].


## Consulte também ##
[Provisionar os clusters de Hadoop no HDInsight usando opções personalizadas][hdinsight-provision-cluster] fornece instruções sobre como provisionar um cluster HDInsight usando outras opções personalizadas.

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-write-script]: ../hdinsight-hadoop-script-actions/
[hdinsight-provision-cluster]: ../hdinsight-provision-clusters/

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Stages during cluster provisioning"

<!--HONumber=35.2-->
