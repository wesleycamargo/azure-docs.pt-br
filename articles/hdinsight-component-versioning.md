<properties urlDisplayName="HDInsight Hadoop Version" pageTitle="O que h&aacute; de novo nas vers&otilde;es de cluster Hadoop do HDInsight? | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="O HDInsight oferece suporte a v&aacute;rias vers&otilde;es de cluster Hadoop implant&aacute;veis a qualquer momento. Consulte as vers&otilde;es de distribui&ccedil;&atilde;o com suporte do Hadoop e do HortonWorks Data Platform (HDP)." services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="O que h&aacute; de novo nas vers&otilde;es de cluster fornecidas pelo HDInsight?" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# O que há de novo nas versões de cluster Hadoop fornecidas pelo HDInsight?

## Versões do HDInsight

O HDInsight oferece suporte a várias versões do cluster Hadoop que podem ser implantadas a qualquer momento. Cada opção de versão provisiona uma versão específica da distribuição de HDP (HortonWorks Data Platform) e um conjunto de componentes contidos nessa distribuição. A versão do componente associada a cada versão de cluster HDInsight é detalhada na tabela a seguir. Observe que a versão padrão do cluster usada pelo [Azure HDInsight][Azure HDInsight] atualmente é a 3.1 baseada em HDP 2.1.

<table>
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Componente</th>
<th align="left">HDInsight versão 3.1 (padrão)</th>
<th align="left">HDInsight versão 3,0</th>
<th align="left">HDIinsight versão 2.1</th>
<th align="left">HDInsight versão 1.6</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">HDP (Hortonworks Data Platform)</td>
<td align="left">2.1</td>
<td align="left">2.0</td>
<td align="left">1.3</td>
<td align="left">1.1</td>
</tr>
<tr class="even">
<td align="left">Apache Hadoop e YARN</td>
<td align="left">2.4.0</td>
<td align="left">2.2.0</td>
<td align="left">1.2.0</td>
<td align="left">1.0.3</td>
</tr>
<tr class="odd">
<td align="left">Tez</td>
<td align="left">0.4.0</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Apache Pig</td>
<td align="left">0.12.1</td>
<td align="left">0.12.0</td>
<td align="left">0.11.0</td>
<td align="left">0.9.3</td>
</tr>
<tr class="odd">
<td align="left">Apache Hive e HCatalog</td>
<td align="left">0.13.0</td>
<td align="left">0.12.0</td>
<td align="left">0.11.0</td>
<td align="left">0.9.0</td>
</tr>
<tr class="even">
<td align="left">HBase</td>
<td align="left">0.98.0</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Apache Sqoop</td>
<td align="left">1.4.4</td>
<td align="left">1.4.4</td>
<td align="left">1.4.3</td>
<td align="left">1.4.2</td>
</tr>
<tr class="even">
<td align="left">Apache Oozie</td>
<td align="left">4.0.0</td>
<td align="left">4.0.0</td>
<td align="left">3.3.2</td>
<td align="left">3.2.0</td>
</tr>
<tr class="odd">
<td align="left">Apache HCatalog</td>
<td align="left">Mesclado com o Hive</td>
<td align="left">Mesclado com o Hive</td>
<td align="left">Mesclado com o Hive</td>
<td align="left">0.4.1</td>
</tr>
<tr class="even">
<td align="left">Apache Templeton</td>
<td align="left">Mesclado com o Hive</td>
<td align="left">Mesclado com o Hive</td>
<td align="left">Mesclado com o Hive</td>
<td align="left">0.1.4</td>
</tr>
<tr class="odd">
<td align="left">Ambari</td>
<td align="left">1.5.1</td>
<td align="left">1.4.1</td>
<td align="left">API v1.0</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Zookeeper</td>
<td align="left">3.4.5</td>
<td align="left">3.4.5</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Mahout</td>
<td align="left">0.9.0</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
</tbody>
</table>

**Obter informações atuais sobre a versão do componente**

As versões de componente associadas às versões do cluster HDInsight podem ser alteradas em futuras atualizações no HDInsight. Uma maneira de determinar os componentes disponíveis e de verificar quais versões estão sendo usadas para um cluster para usar a API REST do Ambari. O comando **GetComponentInformation** pode ser usado para recuperar informações sobre um componente de serviço. Para obter detalhes, consulte a [Documentação do Ambari][Documentação do Ambari]. Outra maneira de obter essas informações é fazer logon em um cluster usando uma área de trabalho remota e examinar diretamente o conteúdo do diretório "C:\\apps\\dist".

**Notas de versão**

Consulte [HDInsight Release Notes (Notas de versão do HDInsight)][HDInsight Release Notes (Notas de versão do HDInsight)] para ver notas de versão adicionais sobre as versões mais recentes do HDInsight.

### Selecionar uma versão ao provisionar um cluster HDInsight

Ao criar um cluster por meio dos Cmdlets do PowerShell do HDInsight ou do SDK do .NET do HDInsight, você pode escolher a versão para o cluster Hadoop HDInsight usando o parâmetro "Version".

Se você usar a opção **Criação Rápida**, obterá a versão 3.1 do HDInsight que cria o cluster Hadoop por padrão. Se usar a opção **Criação Personalizada** do Portal do Azure, poderá escolher a versão do cluster que você implantará da lista suspensa **Versão do HDInsight** na página **Detalhes do Cluster**.

## Destaques do recurso

Por padrão, o Azure HDInsight agora dá suporte ao Hadoop 2.4 com o cluster HDInsight versão 3.1 (usando Hortonworks Data Platform 2.1). Ele tira total proveito dessa plataforma a fim de fornecer uma variedade de benefícios significativos aos clientes. Esses incluem, principalmente:

-   **HBase**: O HBase é um banco de dados NoSQL de baixa latência que permite o processamento transacional online de big data. Ele é oferecido como um cluster gerenciado integrado no ambiente do Azure. Os clusters são configurados para armazenar dados diretamente no armazenamento de Blob do Azure, que fornece baixa latência e elasticidade elevada no desempenho e opções de custo. Isso permite que os clientes compilem sites interativos que trabalham com grandes conjuntos de dados, compilem serviços que armazenam dados de sensor e telemetria de milhões de pontos de extremidade e analisem esses dados por meio de trabalhos do Hadoop.

-   **Painel do cluster**: Um novo aplicativo Web que é implantado no seu cluster HDInsight. Use-o para executar consultas do Hive, verificar logs de trabalho e procurar armazenamento de Blob do Azure. A URL usada para acessar o aplicativo Web é \<*NomeCluster*\>.azurehdinsight.net.

-   **Biblioteca do Microsoft Avro**: Esta biblioteca implementa o sistema de serialização de dados Apache Avro para o ambiente Microsoft .NET. O Apache Avro fornece um formato de intercâmbio de dados binários compactos para serialização. Ele utiliza o JSON para definir o esquema com neutralidade de linguagem que subscreve a interoperabilidade da linguagem. Os dados serializados em uma linguagem podem ser lidos em outra diferente. Atualmente, há suporte para C, C++, C#, Java, PHP, Python e Ruby. O formato de serialização Apache Avro é amplamente usado no Azure HDInsight para representar estruturas de dados complexas em um trabalho MapReduce do Hadoop.

-   **YARN**: uma nova estrutura de gerenciamento de aplicativos distribuída para uso geral que substituiu a estrutura clássica MapReduce do Apache Hadoop para processamento de dados nos clusters Hadoop. Ela efetivamente serve como o sistema operacional do Hadoop e leva o Hadoop de uma plataforma de dados de acesso único para processamento em lotes para uma plataforma multiuso que permite processamento em lotes, interativo, online e de fluxo. Essa nova estrutura de gerenciamento melhora a escalabilidade e a utilização do cluster de acordo com critérios, como garantias de capacidade, integridade e contratos de nível de serviço.

-   **Tez (somente HDInsight 3.1)**: Uma estrutura personalizável e de uso geral que cria tarefas simplificadas de processamento de dados em cargas de trabalho de pequena e grande escala no Hadoop. Ela oferece a capacidade de executar um DAG (gráfico acíclico direcionado) complexo de tarefas para um único trabalho, de modo que os projetos no ecossistema do Apache Hadoop, como Apache Hive e Apache Pig, possam atender aos requisitos de tempos de resposta interativa humana e extrema produtividade em uma escala de petabytes. Observe que o Hive 0.13 permite que consultas do Hive sejam executadas parte superior do Tez, em vez de no MapReduce.

-   **Alta disponibilidade**: Um segundo headnode foi adicionado aos clusters Hadoop implantados pelo HDInsight para aumentar a disponibilidade do serviço. Implementações padrão de clusters Hadoop normalmente têm um único headnode. O HDInsight remove esse ponto único de falha com a adição de um headnode secundário. A mudança para uma nova configuração de cluster de alta disponibilidade não altera o preço do cluster, a menos que os clientes provisionem clusters com um nó de cabeça extragrande em vez do nó de tamanho grande padrão.

-   **Desempenho do Hive**: melhorias na ordem de magnitude dos tempos de resposta de consulta do Hive (até 40x) e da compactação de dados (até 80%) usando o formato OCR **(Optimized Row Columnar)**.

-   **Pig, Sqoop, Oozie, Ambari**: Atualizações da versão do componente para o cluster HDInsight versão 3.0 (HDP 2.0/Hadoop 2.2) que fornece paridade com o cluster HDInsight versão 2.1 (HDP 1.3/Hadoop 1.2). Consulte as tabelas de versão abaixo para obter informações específicas.

-   **Mahout**: Essa biblioteca escalonável de algoritmos de aprendizado de máquina é pré-instalada em clusters Hadoop do HDInsight 3.1. Desse modo, você pode executar trabalhos do Mahout sem a necessidade de alguma configuração de cluster adicional.

-   **Suporte à Rede Virtual**: Os clusters do HDInsight podem ser usados com a Rede Virtual do Azure para oferecer suporte ao isolamento de recursos de nuvem ou a cenários híbridos que vinculam os recursos de nuvem àqueles em seu datacenter.

## Versões com suporte

A tabela a seguir lista as versões do HDInsight disponíveis no momento, as versões correspondentes do Hortonworks Data Platform (HDP) que elas usam e suas datas de lançamento. Quando conhecidas, suas datas de vencimento e reprovação também serão fornecidas. Observe o seguinte:

-   Clusters altamente disponíveis com dois headnodes são implantados por padrão para clusters HDInsight 2.1, 3.0 e 3.1. Eles não estão disponíveis para clusters HDInsight 1.6.
-   Depois que o suporte expirar para uma determinada versão, ele poderá não estar disponível pelo portal de gerenciamento do Azure. A tabela a seguir indica quais versões estão disponíveis no portal de gerenciamento do Azure. As versões do cluster continuarão disponíveis usando o parâmetro `Version` no comando PowerShell [New-AzureHDInsightCluster][New-AzureHDInsightCluster] e o .NET SDK até sua data de substituição.

<table border="1">
<tr>
<th>
Versão do HDInsight

</th>
<th>
Versão do HDP</a>

<th>
Alta Disponibilidade

</th>
</th>
<th>
Data do lançamento

</th>
<th>
Disponível no Portal de Gerenciamento do Azure

</th>
<th>
Data de validade do suporte

</th>
<th>
Data de reprovação

</th>
</tr>
<tr>
<td>
HDI 3,1

</td>
<td>
HDP 2,1

</td>
<td>
Sim

</td>
<td>
</td>
<td>
Sim

</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td>
HDI 3,0

</td>
<td>
HDP 2,0

</td>
<td>
Sim

</td>
<td>
02/11/2014

</td>
<td>
Sim

</td>
<td>
09/17/2014

</td>
<td>
06/30/2015

</td>
</tr>
<tr>
<td>
HDI 2,1

</td>
<td>
HDP 1,3

</td>
<td>
Sim

</td>
<td>
10/28/2013

</td>
<td>
Não

</td>
<td>
05/12/2014

</td>
<td>
05/31/2015

</td>
</tr>
<tr>
<td>
HDI 1.6

</td>
<td>
HDP 1.1

</td>
<td>
Não

</td>
<td>
10/28/2013

</td>
<td>
Não

</td>
<td>
04/26/2014

</td>
<td>
05/31/2015

</td>
</tr>
</table>
**Implantação de clusters não padrão**

Há suporte para a criação dos clusters HDInsight 3.1 no Hadoop 2.4 pelo Portal do Azure, pelo SDK do HDInsight e pelo PowerShell do Azure. Observe que, por padrão, os clusters HDInsight 3.1 são criados no Hadoop 2.4, de modo que os usuários devem usar a opção **Criação Personalizada** no portal para especificar outras versões do cluster HDInsight, caso eles precisem ser criados.

### O SLA (Contrato de Nível de Serviço) para versões do cluster HDInsight

O SLA é definido em termos de uma "Janela de Suporte". Uma janela de suporte refere-se ao período de tempo que uma versão do cluster HDInsight é suportada pelo Atendimento ao Cliente da Microsoft. Um cluster HDInsight ficará fora da janela de suporte se sua versão tiver uma **Data de Validade de Suporte** após a data atual. Uma lista das versões com suporte do cluster HDInsight pode ser encontrada na tabela acima. A data de validade de suporte de determinada versão do HDInsight (assim que uma versão X+1 mais nova estiver disponível) é calculada como a mais posterior de:

-   Fórmula 1: Adicione 180 dias à data em que o cluster HDInsight versão X foi lançado
-   Fórmula 2: Adicione 90 dias à data em que o cluster HDInsight versão X+1 (a versão subsequente após X) foi disponibilizado no Portal de Gerenciamento do Azure.

A **Data de Reprovação** é a data após a qual a versão do cluster não pode ser criada no HDInsight.

> [WACOM.NOTE] O cluster HDInsight 2.1 e 3.0 é executado no SO Convidado do Azure [Família 4][Família 4], que usa a versão de 64 bits do Windows Server 2012 R2 e dá suporte ao .NET Framework 4.0, 4.5. e 4.5.1.

## Notas de versão do Hortonworks associadas a versões do HDInsight

-   O cluster HDInsight versão 3.1 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 2.1][Hortonworks Data Platform 2.1]. Este é o cluster Hadoop padrão criado ao usar o portal do Azure HDInsight.

-   O cluster HDInsight versão 3,0 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 2,0][Hortonworks Data Platform 2,0].

-   O cluster HDInsight versão 2,1 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 1,3][Hortonworks Data Platform 1,3].

-   O cluster HDInsight versão 1.6 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 1.1][Hortonworks Data Platform 1.1].

  [Azure HDInsight]: http://go.microsoft.com/fwlink/?LinkID=285601
  [Documentação do Ambari]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [HDInsight Release Notes (Notas de versão do HDInsight)]: http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-release-notes/
  [New-AzureHDInsightCluster]: http://msdn.microsoft.com/pt-br/library/dn593744.aspx
  [Família 4]: http://msdn.microsoft.com/pt-br/library/azure/ee924680.aspx#explanation
  [Hortonworks Data Platform 2.1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html
  [Hortonworks Data Platform 2,0]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html
  [Hortonworks Data Platform 1,3]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html
  [Hortonworks Data Platform 1.1]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html
