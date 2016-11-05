---
title: Use a ação de Script no HDInsight para instalar o Solr em cluster do Hadoop | Microsoft Docs
description: Saiba como personalizar o cluster HDInsight com o Solr usando a Ação de Script.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2016
ms.author: nitinme

---
# Instalar e usar o Solr em clusters HDInsight do Hadoop
Saiba como personalizar o cluster HDInsight baseado em Windows com Solr usando a Ação de Script, e como usar o Solr para pesquisar dados. Para obter informações sobre como usar o Solr com um cluster baseado no Linux, consulte [Instalar e usar o Solr em clusters Hadoop do HDinsight (Linux)](hdinsight-hadoop-solr-install-linux.md).

Você pode instalar o Solr em qualquer tipo de cluster (Hadoop, Storm, HBase, Spark) no Azure HDInsight usando a *Ação de Script*. Um exemplo de script para instalar o R em um cluster HDInsight está disponível em um blob de armazenamento do Azure somente leitura em [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

O script de exemplo funciona apenas com o cluster HDInsight versão 3.1. Para obter mais informações sobre as versões do cluster HDInsight, consulte [Versões do cluster HDInsight](hdinsight-component-versioning.md).

O script de exemplo usado neste tópico cria um cluster do Solr baseado no Windows com uma configuração específica. Se você quiser configurar o cluster Solr com diferentes coleções, fragmentos, esquemas, réplicas, etc., você deve modificar o script e os binários do Solr adequadamente.

**Artigos relacionados**

* [Instalar e usar o Solr em clusters Hadoop do HDInsight (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Criar clusters Hadoop no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre como criar clusters.
* [Personalizar clusters HDInsight usando a Ação de Script][hdinsight-cluster-customize]\: informações gerais sobre como personalizar os clusters HDInsight usando a Ação de Script.
* [Desenvolver scripts de Ação de Script para o HDInsight](hdinsight-hadoop-script-actions.md)

## O que é Solr?
O <a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> é uma plataforma de pesquisa empresarial que habilita operações poderosas de pesquisa de texto completo nos dados. Enquanto o Hadoop permite armazenar e gerenciar grandes quantidades de dados, o Apache Solr oferece os recursos de pesquisa para recuperar rapidamente os dados.

## Instalar o Solr usando o Portal
1. Inicie a criação de um cluster usando a opção **CRIAÇÃO PERSONALIZADA**, conforme descrito em [Criar clusters Hadoop no HDInsight](hdinsight-provision-clusters.md#portal).
2. Na página **Ações de Script** do assistente, clique em **Adicionar ação de script** para fornecer detalhes sobre a ação de script, como mostrado abaixo:
   
    ![Usar Ação de Script para personalizar um cluster](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Usar Ação de Script para personalizar um cluster")
   
    <table border='1'>
        <tr><th>Propriedade</th><th>Valor</th></tr>
        <tr><td>Nome</td>
            <td>Especifique um nome para a ação de script. Por exemplo, <b>Instalar o Solr</b>.</td></tr>
        <tr><td>URI do script</td>
            <td>Especifique o URI (Uniform Resource Identifier) do script invocado para personalizar o cluster. Por exemplo, <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
        <tr><td>Tipo de nó</td>
            <td>Especifique os nós em que o script de personalização deve ser executado. Você pode escolher <b>Todos os nós</b>, <b>Somente nós principais</b> ou <b>Somente nós de trabalho</b>.
        <tr><td>Parâmetros</td>
            <td>Especifique os parâmetros, se exigido pelo script. O script para instalar o Solr não requer nenhum parâmetro, portanto você pode deixá-los em branco.</td></tr>
    </table>
   
    Você pode adicionar mais de uma ação de script para instalar vários componentes no cluster. Depois de adicionar os scripts, clique na marca de seleção para começar a criar o cluster.

## Usar o Solr
Você deve começar com indexação Solr, com alguns arquivos de dados. Em seguida, você pode usar o Solr para executar consultas de pesquisa em dados indexados. Execute as seguintes etapas para usar o Solr em um cluster HDInsight:

1. **Usar protocolo RDP (desktop remoto) para remoto para o cluster HDInsight com o Solr instalado**. No Portal do Azure, habilite a Área de Trabalho Remota para o cluster criado com Solr instalado e, em seguida, acesse remotamente o cluster. Para instruções, consulte [Conectar-se a clusters HDInsight usando RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. **Indexar o Solr carregando arquivos de dados**. Quando você indexa o Solr, coloque os documentos que você pode precisar pesquisar. Para indexar o Solr, acesso o cluster remotamente via RDP, navegue até a área de trabalho, abra a linha de comando do Hadoop e navegue até **C:\\apps\\dist\\solr-4.7.2\\example\\exampledocs**. Execute o comando a seguir:
   
        java -jar post.jar solr.xml monitor.xml
   
    Você verá a saída a seguir no console:
   
        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624
   
    O utilitário post.jar indexa o Solr com dois documentos de exemplo, **solr.xml** e **monitor.xml**. O utilitário post.jar e os documentos de exemplo estão disponíveis com a instalação do Solr.
3. **Use o painel do Solr para pesquisar nos documentos indexados**. Na sessão de acesso por RDP ao cluster HDInsight, abra o Internet Explorer e inicie o painel do Solr em **http://headnodehost:8983/solr/#/**. No painel esquerdo, na lista suspensa **Seletor de Núcleo**, selecione **coleção1** e, dentro dessa opção, clique em **Consulta**. Por exemplo, para selecionar e retornar todos os documentos em Solr, forneça os seguintes valores:
   
   * Na caixa de texto **q**, digite ***:***. Isso retornará como resultado todos os documentos que são indexados em Solr. Se você quiser procurar uma cadeia de caracteres específica dentro dos documentos, você pode inserir essa cadeia de caracteres aqui.
   * Na caixa de texto **wt**, selecione o formato de saída. O padrão é **json**. Clique em **Executar consulta**.
     
     ![Usar Ação de Script para personalizar um cluster](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Executar uma consulta no painel do Solr")
     
     A saída retorna os dois documentos que foram usados para indexação do Solr. A saída é semelhante ao seguinte:
     
           "response": {
               "numFound": 2,
               "start": 0,
               "maxScore": 1,
               "docs": [
                 {
                   "id": "SOLR1000",
                   "name": "Solr, the Enterprise Search Server",
                   "manu": "Apache Software Foundation",
                   "cat": [
                     "software",
                     "search"
                   ],
                   "features": [
                     "Advanced Full-Text Search Capabilities using Lucene",
                     "Optimized for High Volume Web Traffic",
                     "Standards Based Open Interfaces - XML and HTTP",
                     "Comprehensive HTML Administration Interfaces",
                     "Scalability - Efficient Replication to other Solr Search Servers",
                     "Flexible and Adaptable with XML configuration and Schema",
                     "Good unicode support: héllo (hello with an accent over the e)"
                   ],
                   "price": 0,
                   "price_c": "0,USD",
                   "popularity": 10,
                   "inStock": true,
                   "incubationdate_dt": "2006-01-17T00:00:00Z",
                   "_version_": 1486960636996878300
                 },
                 {
                   "id": "3007WFP",
                   "name": "Dell Widescreen UltraSharp 3007WFP",
                   "manu": "Dell, Inc.",
                   "manu_id_s": "dell",
                   "cat": [
                     "electronics and computer1"
                   ],
                   "features": [
                     "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                   ],
                   "includes": "USB cable",
                   "weight": 401.6,
                   "price": 2199,
                   "price_c": "2199,USD",
                   "popularity": 6,
                   "inStock": true,
                   "store": "43.17614,-90.57341",
                   "_version_": 1486960637584081000
                 }
               ]
             }
4. **Recomendado: fazer backup dos dados indexados do Solr para o Blob de Armazenamento do Azure (WASB) associado ao cluster HDInsight**. Como uma prática recomendada, você deve fazer backup dos dados indexados de nós do cluster Solr no armazenamento de Blob do Azure. Execute as seguintes etapas para fazê-lo:
   
   1. Na sessão RDP, abra o Internet Explorer e insira a seguinte URL:
      
           http://localhost:8983/solr/replication?command=backup
      
       Você verá uma resposta semelhante a essa:
      
           <?xml version="1.0" encoding="UTF-8"?>
           <response>
             <lst name="responseHeader">
               <int name="status">0</int>
               <int name="QTime">9</int>
             </lst>
             <str name="status">OK</str>
           </response>
   2. Na sessão remota, navegue até {HOME\_DO\_SOLR}\\{Coleção}\\data. Para o cluster criado usando o script de exemplo, esse caminho deve ser **C:\\apps\\dist\\solr-4.7.2\\example\\solr\\collection1\\data**. Nesse local, você deve ver uma pasta de capturas de tela criada com um nome semelhante a **snapshot.*timestamp***.
   3. Compacte a pasta de capturas de tela e carregue-la no armazenamento de Blob do Azure. Na linha de comando do Hadoop, navegue até o local da pasta de capturas de tela usando o seguinte comando:
      
             hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data
      
       Este comando copia a capturas de tela para /example/data/ sob o contêiner na conta de armazenamento padrão associada ao cluster.

## Instalar o Solr usando o Azure PowerShell
Consulte [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell). O exemplo demonstra como instalar o Spark usando o Azure PowerShell. Você precisa personalizar o script para usar [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## Instalar o Solr usando o SDK do .NET
Consulte [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). O exemplo demonstra como instalar o Spark usando o SDK do .NET. Você precisa personalizar o script para usar [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## Confira também
* [Instalar e usar o Solr em clusters Hadoop do HDInsight (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Criar clusters Hadoop no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre como criar clusters.
* [Personalizar clusters HDInsight usando a Ação de Script][hdinsight-cluster-customize]\: informações gerais sobre como personalizar os clusters HDInsight usando a Ação de Script.
* [Desenvolver scripts de Ação de Script para o HDInsight](hdinsight-hadoop-script-actions.md).
* [Instalar e usar o Spark em clusters HDInsight][hdinsight-install-spark]\: exemplo de Ação de Script sobre a instalação do Spark.
* [Instalar R em clusters HDInsight][hdinsight-install-r]\: exemplo de Ação de Script sobre a instalação do R.
* [Instalar Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install.md): exemplo de Ação de Script sobre a instalação do Giraph.

[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md

<!---HONumber=AcomDC_0914_2016-->