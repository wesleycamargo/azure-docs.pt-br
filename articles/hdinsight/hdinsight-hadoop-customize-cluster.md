<properties
	pageTitle="Personalizar os clusters HDInsight usando ações de script | Microsoft Azure"
	description="Saiba como personalizar os clusters HDInsight usando a ação de Script."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/07/2015"
	ms.author="nitinme"/>

# Personalizar os clusters HDInsight usando a Ação de Script

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-v1.md)

O HDInsight oferece uma opção de configuração chamada **Ação de Script**; ela invoca scripts personalizados que definem a personalização a ser executada no cluster durante o processo de provisionamento. Esses scripts podem ser usados para instalar software adicional em um cluster ou para alterar a configuração de aplicativos em um cluster.


> [AZURE.NOTE]As informações contidas neste artigo são específicas aos clusters HDInsight baseados no Windows. Para obter uma versão deste artigo específica aos clusters baseados no Linux, veja [Personalizar clusters HDInsight usando a Ação de Script (Linux)](hdinsight-hadoop-customize-cluster-linux.md)

Clusters HDInsight também podem ser personalizados de várias outras formas, como incluir contas adicionais do Armazenamento do Azure, alterar os arquivos de configuração do Hadoop (core-site.xml, hive-site.xml, etc.) ou adicionar bibliotecas compartilhadas (p.ex., Hive, Oozie) em locais comuns no cluster. Essas personalizações podem ser feitas por meio do Azure PowerShell, do SDK do Azure HDInsight .NET ou do Portal de Visualização do Azure. Para obter mais informações, consulte [Provisionar clusters Hadoop no HDInsight usando opções personalizadas][hdinsight-provision-cluster].

## Ação de script no processo de provisionamento do cluster

A Ação de Script só é usada enquanto um cluster está sendo criado. O diagrama a seguir ilustra quando a Ação de Script é executada durante o processo de provisionamento:

![Personalização do cluster HDInsight e estágios durante o provisionamento de cluster][img-hdi-cluster-states]

Quando o script é executado, o cluster entra no estágio **ClusterCustomization**. Nesse estágio, o script é executado na conta de administrador do sistema, em paralelo com todos os nós especificados no cluster e fornece privilégios totais de administrador nos nós.

> [AZURE.NOTE]Por ter privilégios administrativos nos nós de cluster durante o estágio **ClusterCustomization**, você pode usar o script para executar operações como parar e iniciar serviços, inclusive serviços relacionados ao Hadoop. Logo, como parte do script, você deve garantir que os serviços Ambari e outros serviços relacionados ao Hadoop estejam funcionando antes do script terminar a execução. Esses serviços são necessários para verificar a integridade e o estado do cluster enquanto ele estiver sendo criado. Se você alterar qualquer configuração no cluster que afete esses serviços, é necessário usar as funções auxiliares que são fornecidas. Para obter mais informações sobre funções auxiliares, confira [Desenvolver scripts de Ação de Script para o HDInsight][hdinsight-write-script].

Os logs de saída e de erros do script são armazenados na conta padrão do Armazenamento que você especificou para o cluster. Os logs são armazenados em uma tabela de nome **u<\\fragmento-do-nome-do-cluster><\\carimbo-de-data-e-hora>setuplog**. São logs agregados dos scripts executados em todos os nós (nó de cabeçalho e nós de trabalho) no cluster.


Cada cluster pode aceitar várias ações de script, que são invocadas na ordem em que elas são especificadas. Um script pode ser executado no nó principal, nos nós de trabalho ou em ambos.

## Scripts de Ação de Chamar Script

Scripts de Ação de Script podem ser usados no Portal de Visualização do Azure, no Azure PowerShell ou no SDK do .NET HDInsight. Este artigo mostra como usar a Ação de Script a partir do portal. Para saber como usar o PowerShell e o SDK do .NET para usar a Ação de Script, veja os exemplos listados na tabela a seguir.

O HDInsight fornece vários scripts para instalar os seguintes componentes em clusters do HDInsight:

Nome | Script
----- | -----
**Instalar Spark** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Consulte [Instalar e usar o Spark em clusters HDInsight][hdinsight-install-spark].
**Instalar R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Consulte [Instalar e usar o R em clusters HDInsight][hdinsight-install-r].
**Instalar Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Consulte [Instalar e usar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install.md).
- **Instalar o Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Consulte [Instalar e usar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install.md).



**No Portal de Visualização do Azure**

1. Inicie o provisionamento de um cluster como descrito em [Provisionando um cluster usando opções personalizadas](hdinsight-provision-clusters.md#portal).
2. Em Configuração Opcional, para a folha **Ações de Script**, clique em **adicionar ação de script** para fornecer detalhes sobre a ação de script, como mostrado abaixo:

	![Usar Ação de Script para personalizar um cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Usar Ação de Script para personalizar um cluster")

	<table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Nome</td>
		<td>Especifique um nome para a ação de script.</td></tr>
	<tr><td>URI do script</td>
		<td>Especifique o URI para o script que é chamado para personalizar o cluster. s</td></tr>
	<tr><td>Cabeçalho/Trabalho</td>
		<td>Especificar os nós (**Cabeçalho** ou **Trabalho**) nos quais o script de personalização é executado.</b>.
	<tr><td>Parâmetros</td>
		<td>Especifique os parâmetros, se exigido pelo script.</td></tr>
</table>Pressione ENTER para adicionar mais de uma ação de script para instalar vários componentes no cluster.

3. Clique em **Selecionar** para salvar a configuração da ação de script e continuar com o provisionamento do cluster.


## Suporte para software livre utilizado em clusters do HDInsight
O serviço Microsoft Azure HDInsight é uma plataforma flexível que permite compilar aplicativos Big Data na nuvem usando um ecossistema de tecnologias de software livre baseado no Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de software livre, conforme discutimos na seção **Escopo do suporte** do <a href="http://azure.microsoft.com/support/faq/" target="_blank">site Perguntas frequentes de suporte do Azure</a>. O serviço HDInsight fornece um nível adicional de suporte a alguns dos componentes, como descrito abaixo.

Há dois tipos de componentes de software livre disponíveis no serviço HDInsight:

- **Componentes internos**: estes componentes estão pré-instalado em clusters HDInsight e fornecem a funcionalidade básica do cluster. Por exemplo, o gerenciador de recursos YARN RM, o HiveQL (linguagem de consulta do Hive) e a biblioteca Mahout pertencem a esta categoria. Uma lista completa dos componentes de cluster está disponível em <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">O que há de novo nas versões de cluster Hadoop fornecidas pelo HDInsight?</a>
- **Componentes personalizados**: como usuário do cluster, você pode instalar ou usar em sua carga de trabalho qualquer componente disponível na comunidade ou criado por você.

Componentes internos são totalmente compatíveis e o Suporte da Microsoft ajudará a isolar e resolver problemas relacionados a esses componentes.

> [AZURE.WARNING]Há suporte total a componentes fornecidos com o cluster HDInsight e o Suporte da Microsoft ajudará a isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. Isso pode resultar na resolução do problema ou na solicitação de você buscar nos canais disponíveis as tecnologias de código-fonte aberto, onde é possível encontrar conhecimento aprofundado sobre essa tecnologia. Por exemplo, há muitos sites de comunidades que podem ser usados, como o [Fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/pt-BR/home?forum=hdinsight) e [http://stackoverflow.com](http://stackoverflow.com). Além disso, projetos Apache têm sites de projetos em [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/) e [Spark](http://spark.apache.org/).

O serviço HDInsight possibilita o uso de componentes personalizados de várias formas. Seja como for usado ou instalado em um cluster, o mesmo nível de suporte se aplica ao componente. Mostramos, a seguir, uma lista das formas mais comuns de usar os componentes personalizados em clusters HDInsight:

1. Envio de trabalhos: trabalhos do Hadoop ou de outros tipos que executam ou usam componentes personalizados podem ser enviados para o cluster.
2. Personalização de clusters: durante a criação de clusters, você pode especificar configurações adicionais e componentes personalizados que serão instalados nos nós de cluster.
3. Exemplos: para componentes personalizados populares, a Microsoft e outras empresas podem fornecer exemplos de uso desses componentes em clusters HDInsight. Esses exemplos são fornecidos sem suporte.

## Desenvolver scripts de Ação de script

Confira [Desenvolver scripts de Ação de Script para o HDInsight][hdinsight-write-script].


## Consulte também

- [Provisionar clusters Hadoop no HDInsight usando opções personalizadas][hdinsight-provision-cluster] fornece instruções sobre como provisionar um cluster HDInsight usando outras opções personalizadas.
- [Desenvolver scripts de Ação de Script para o HDInsight][hdinsight-write-script]
- [Instalar e usar o Spark em clusters HDInsight][hdinsight-install-spark]
- [Instalar e usar R em clusters do HDInsight][hdinsight-install-r]
- [Instalar e usar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install.md).
- [Instalar e usar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: ../install-configure-powershell.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Estágios durante o provisionamento de cluster"

<!---HONumber=Oct15_HO1-->