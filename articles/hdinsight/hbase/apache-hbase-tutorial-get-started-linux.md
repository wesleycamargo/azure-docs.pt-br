---
title: Introdução a um exemplo do HBase no HDInsight - Azure
description: Siga este exemplo do Apache HBase para começar a usar o hadoop no HDInsight. Criar tabelas a partir do shell do HBase e consultá-las usando o Hive.
keywords: exemplo hbasecommand,hbase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: c1c582c60a7c91de40983daa07bdec1e8d748f8a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718867"
---
# <a name="get-started-with-an-apache-hbase-example-in-hdinsight"></a>Introdução a um exemplo do Apache HBase no HDInsight

Aprenda como criar um cluster do [Apache HBase](https://hbase.apache.org/) no HDInsight, criar tabelas do HBase e consultar tabelas usando o [Apache Hive](https://hive.apache.org/).  Para obter informações gerais do HBase, confira [Visão geral do HBase do HDInsight][hdinsight-hbase-overview].

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial do HBase, você deverá ter os seguintes itens:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Secure Shell(SSH)](../hdinsight-hadoop-linux-use-ssh-unix.md). 
* [curl](https://curl.haxx.se/download.html).

## <a name="create-apache-hbase-cluster"></a>Criar cluster do Apache HBase
O procedimento a seguir usa um modelo do Azure Resource Manager para criar um cluster do HBase e a conta de Armazenamento do Azure padrão dependente. Para compreender os parâmetros usados no procedimento e em outros métodos de criação de cluster, consulte [Criar clusters Hadoop baseados em Linux no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Para obter mais informações sobre como usar o Data Lake Storage Gen2, consulte o [Guia de Início Rápido: Configurar clusters no HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

1. Clique na imagem a seguir para abrir o modelo no portal do Azure. O modelo está localizado em [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/).
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Na folha **Implantação personalizada**, insira os seguintes valores:
   
   * **Assinatura**: Selecione sua assinatura do Azure que é usada para criar o cluster.
   * **Grupo de recursos**: Crie um grupo de gerenciamento de recursos do Azure ou use um existente.
   * **Localização**: Especifique o local do grupo de recursos. 
   * **ClusterName**: Insira um nome para o cluster HBase.
   * **Nome e senha de logon do cluster**: O nome padrão de logon é **admin**.
   * **Nome de usuário e senha SSH**: O nome de usuário padrão é **sshuser**.  Você pode renomeá-lo.
     
     Outros parâmetros são opcionais.  
     
     Cada cluster tem uma dependência de conta de Armazenamento do Azure. Depois que você excluir um cluster, os dados serão mantidos na conta de armazenamento. O nome de conta de armazenamento padrão do cluster é o nome do cluster com "store" acrescentado. Ele é codificado na seção de variáveis do modelo.
3. Selecione **Concordo com os termos e condições declarados acima** e clique em **Comprar**. Demora cerca de 20 minutos para criar um cluster.

> [!NOTE]  
> Depois que um cluster HBase for excluído, você pode criar outro cluster HBase usando o mesmo contêiner de blob padrão. O novo cluster seleciona as tabelas HBase criadas por você no cluster original. É recomendável desabilitar as tabelas HBase antes de excluir o cluster para evitar inconsistências.
> 
> 

## <a name="create-tables-and-insert-data"></a>Criar tabelas e inserir dados
Você pode usar o SSH para se conectar a clusters do HBase e, em seguida, usar o [Shell do Apache HBase](https://hbase.apache.org/0.94/book/shell.html) para criar tabelas do HBase, inserir dados e consultar dados. Para obter mais informações, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Para a maioria das pessoas, os dados aparecem no formato de tabela:

![dados tabulares do HBase HDInsight][img-hbase-sample-data-tabular]

No HBase (uma implementação do [Cloud BigTable](https://cloud.google.com/bigtable/)), os mesmos dados se parecem com:

![Dados BigTable do HBase HDInsight][img-hbase-sample-data-bigtable]


**Para usar o shell HBase**

1. No SSH, execute este comando HBase:
   
    ```bash
    hbase shell
    ```

2. Crie um HBase com famílias de duas colunas:

    ```hbaseshell   
    create 'Contacts', 'Personal', 'Office'
    list
    ```
3. Insira alguns dados:
    
    ```hbaseshell   
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    scan 'Contacts'
    ```
   
    ![Shell do HBase do Hadoop HDInsight][img-hbase-shell]
4. Obter uma única linha
   
    ```hbaseshell
    get 'Contacts', '1000'
    ```
   
    Você deverá ver os mesmos resultados que aqueles exibidos ao usar o comando de verificação, porque há apenas uma linha.
   
    Para obter mais informações sobre o esquema da tabela HBase, consulte [Introdução ao projeto de esquema do Apache HBase][hbase-schema]. Para obter mais comandos HBase, confira [Guia de referência do Apache HBase][hbase-quick-start].
5. Sair do shell
   
    ```hbaseshell
    exit
    ```

**Para carregar dados em massa na tabela de contatos HBase**

O HBase inclui vários métodos de carregamento de dados em tabelas.  Para obter mais informações, consulte [Carregamento em massa](https://hbase.apache.org/book.html#arch.bulk.load).

Um arquivo de dados de exemplo pode ser encontrado em um contêiner de blob público *wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt*.  O conteúdo do arquivo de dados é:

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

Você pode, opcionalmente, criar um arquivo de texto e carregá-lo na sua própria conta de armazenamento se desejar. Para obter instruções, consulte [Fazer upload de dados para trabalhos do Apache Hadoop no HDInsight][hdinsight-upload-data].

> [!NOTE]  
> Este procedimento usa a tabela de contatos HBase que você criou no último procedimento.

1. No SSH, execute o seguinte comando para transformar o arquivo de dados para o StoreFiles e armazene em um caminho relativo especificado por Dimporttsv.bulk.output.  Se você estiver no Shell do HBase, use o comando exit para sair.

    ```bash   
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Execute o seguinte comando para carregar os dados de /example/data/storeDataFileOutput para a tabela do HBase:
   
    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Você pode abrir o shell do HBase e usar o comando de verificação para listar o conteúdo da tabela.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Usar o Apache Hive para consultar o Apache HBase

Você pode consultar os dados nas tabelas do HBase usando [Apache Hive](https://hive.apache.org/). Nesta seção você cria uma tabela Hive que faz o mapeamento para a tabela do HBase e usa-a para consultar os dados em sua tabela do HBase.

1. Abra o **PuTTY**e conecte-se ao cluster.  Consulte as instruções no procedimento anterior.
2. Na sessão de SSH, use o seguinte comando para iniciar o Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Para saber mais sobre o Beeline, consulte [Usar o Hive com Hadoop no HDInsight com Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
       
3. Execute o script do [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) a seguir para criar uma tabela Hive que é mapeada para a tabela do HBase. Assegure-se de ter criado a tabela de amostra referenciada anteriormente neste tutorial, usando o Shell HBase antes de executar esta instrução.

    ```hiveql   
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

4. Execute o seguinte script do HiveQL para consultar os dados na tabela do HBase:

    ```hiveql   
    SELECT count(rowkey) FROM hbasecontacts;
    ```

## <a name="use-hbase-rest-apis-using-curl"></a>Usar APIs de REST do HBase usando Curl

A API REST é protegida por meio de [autenticação básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Você deve sempre fazer solicitações usando HTTPS (HTTP seguro) para ajudar a garantir que suas credenciais sejam enviadas com segurança para o servidor.

1. Use o seguinte comando para listar as tabelas HBase:

    ```bash
    curl -u <UserName>:<Password> \
    -G https://<ClusterName>.azurehdinsight.net/hbaserest/
    ```

1. Use o seguinte comando para criar uma nova tabela HBase com famílias de duas colunas:

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    O esquema é fornecido no formato JSon.
1. Use o comando a seguir para inserir alguns dados:

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```
   
    Você deve codificar em base64 os valores especificados na opção -d. No exemplo:
   
   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Personal:Name
   * Sm9obiBEb2xl: Julio Dole
     
     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) permite inserir diversos valores (em lote).
1. Use o comando a seguir para obter uma linha:
   
    ```bash 
    curl -u <UserName>:<Password> \
    -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

Para saber mais sobre o Rest HBase, veja [Guia de referência do Apache HBase](https://hbase.apache.org/book.html#_rest).

> [!NOTE]  
> Não há suporte para thrift pelo HBase no HDInsight.
>
> Ao usar o Curl ou qualquer outra comunicação do REST com WebHCat, você deve autenticar as solicitações, fornecendo o nome de usuário e a senha para o administrador do cluster HDInsight. Você também deve usar o nome do cluster como parte do Uniform Resource Identifier (URI) usado para enviar as solicitações ao servidor:
> 
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
>   
>    Você deve receber uma resposta semelhante à resposta a seguir:
>   
>        {"status":"ok","version":"v1"}
   


## <a name="check-cluster-status"></a>Verificar o status do cluster
O HBase em HDInsight é fornecido com uma interface do usuário da Web para monitorar clusters. Usando a interface do usuário da Web, você pode solicitar estatísticas ou informações sobre regiões.

**Para acessar a interface do usuário mestre HBase**

1. Entre na interface do usuário da Web do Ambari em https://&lt;Clustername>.azurehdinsight.net.
2. Clique em **HBase** no menu à esquerda.
3. Clique em **Links rápidos** no topo da página, aponte para o link de nó ativo do Zookeeper e, em seguida, clique em **Interface do usuário mestre HBase**.  A interface do usuário é aberta em outra guia do navegador:

   ![Interface do Usuário HDInsight HBase HMaster](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   A Interface do Usuário Mestre HBase contém as seguintes seções:

   - servidores de região
   - mestres de backup
   - tables
   - tarefas
   - atributos de software

## <a name="delete-the-cluster"></a>Excluir o cluster
É recomendável desabilitar as tabelas HBase antes de excluir o cluster para evitar inconsistências.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Solução de problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como criar um cluster do Apache HBase e como criar tabelas e exibir os dados nessas tabelas a partir do shell do HBase. Você também aprendeu a usar a consulta do Hive dos dados em tabelas HBase e como usar as APIs REST C# do HBase para criar uma tabela HBase e recuperar dados da tabela.

Para obter mais informações, consulte:

* [Visão geral do HDInsight HBase][hdinsight-hbase-overview]: O Apache HBase é um banco de dados NoSQL de código-fonte aberto e Apache criado no Apache Hadoop que fornece acesso aleatório e consistência forte para grandes quantidades de dados não estruturados e semi-estruturados.

[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hbase-reference]: https://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: https://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]:apache-hbase-overview.md
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: https://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hbase-shell]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png
