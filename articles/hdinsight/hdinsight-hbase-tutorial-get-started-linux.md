<properties
	pageTitle="Tutorial do HBase: Introdução aos clusters HBase baseados em Linux no Hadoop | Microsoft Azure"
	description="Siga este tutorial do HBase para começar a usar o Apache HBase com o Hadoop no HDInsight. Criar tabelas a partir do shell do HBase e consultá-las usando o Hive."
	keywords="apache hbase, hbase, shell do hbase,tutorial do hbase"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/04/2016"
	ms.author="jgao"/>



# Tutorial do HBase: Introdução ao uso do Apache HBase com o Hadoop baseado em Linux no HDInsight 

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Saiba como criar um cluster HBase no HDInsight, criar tabelas HBase e consultar as tabelas usando o Hive. Para obter informações gerais do HBase, consulte [Visão geral do HBase do HDInsight][hdinsight-hbase-overview].

As informações neste documento são específicas de clusters HDInsight baseados em Linux. Para saber mais sobre clusters baseados no Windows, use o seletor de guias na parte superior da página para alternar.

###Pré-requisitos

Antes de começar este tutorial do HBase, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Secure Shell(SSU)](hdinsight-hadoop-linux-use-ssh-unix.md). 
- [curl](http://curl.haxx.se/download.html).

## Nome do cluster HBase

O procedimento a seguir usa um modelo ARM do Azure para criar um cluster HBase. Para compreender os parâmetros usados no procedimento e em outros métodos de criação de cluster, confira [Criar clusters Hadoop baseados em Linux no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem a seguir para abrir um modelo ARM no Portal do Azure. O modelo ARM está localizado em um contêiner de blob público. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2FHbase.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Na folha **Parâmetros**, insira o seguinte:

    - **ClusterName**: insira um nome para o cluster HBase que você criará.
    - **ClusterStorageAccountName**: cada cluster tem uma dependência de conta de armazenamento de Blobs do Azure. Depois que você excluir um cluster, os dados serão mantidos na conta de armazenamento.
    - **Nome e senha de logon do cluster**: o nome de logon padrão é **admin**.
    - **Nome de usuário e senha SSH**: o nome de usuário padrão é **sshuser**. Você pode renomeá-lo. Outros parâmetros são opcionais.  
3. Clique em **OK** para salvar os parâmetros.
4. Na folha **Implantação personalizada**, clique na caixa suspensa **Grupo de recursos** e clique em **Novo** para criar um novo grupo de recursos. O grupo de recursos é um contêiner que agrupa o cluster, a conta de armazenamento dependente e outros recursos vinculados.
5. Clique em **Termos legais** e em **Criar**.
6. Clique em **Criar**. A criação de um cluster demora cerca de 20 minutos.


>[AZURE.NOTE] Depois que um cluster HBase for excluído, você pode criar outro cluster HBase usando o mesmo contêiner de blob padrão. O novo cluster selecionará as tabelas HBase criadas por você no cluster original.

## Criar tabelas e inserir dados

Você pode usar o SSH para se conectar aos clusters HBase e usar o Shell do HBase para criar tabelas HBase, inserir dados e consultar dados. Para saber mais sobre como usar o SSH no Linux, no Unix, no OS X e no Windows, veja [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux, do Unix ou do OS X](hdinsight-hadoop-linux-use-ssh-unix.md) e [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
 

Para a maioria das pessoas, os dados aparecem no formato de tabela:

![dados tabulares do hbase hdinsight][img-hbase-sample-data-tabular]

No HBase, que é uma implementação de BigTable, os mesmos dados são assim:

![dados bigtable do hbase hdinsight][img-hbase-sample-data-bigtable]

Depois de concluir o procedimento a seguir, ele fará mais sentido.


**Para usar o shell HBase**

1. Do SSH, execute este comando:

		hbase shell

4. Crie um HBase com duas famílias de coluna:

		create 'Contacts', 'Personal', 'Office'
		list
5. Insira alguns dados:

		put 'Contacts', '1000', 'Personal:Name', 'John Dole'
		put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
		put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
		put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
		scan 'Contacts'

	![shell do hbase do hadoop hdinsight][img-hbase-shell]

6. Obter uma única linha

		get 'Contacts', '1000'

	Você verá os mesmos resultados que ao usar o comando de verificação, porque há apenas uma linha.

	Para saber mais sobre o esquema da tabela HBase, confira [Introdução ao design de esquema HBase][hbase-schema]. Para obter mais comandos HBase, consulte [Guia de referência do Apache HBase][hbase-quick-start].

6. Sair do shell

		exit

**Para carregar dados em massa na tabela de contatos HBase**

O HBase inclui vários métodos de carregamento de dados em tabelas. Para obter mais informações, consulte [Carregamento em massa](http://hbase.apache.org/book.html#arch.bulk.load).


Um arquivo de exemplo de dados foi carregado para um contêiner público de blobs, **wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*. O conteúdo do arquivo de dados é:

	8396	Calvin Raji		230-555-0191	230-555-0191	5415 San Gabriel Dr.
	16600	Karen Wu		646-555-0113	230-555-0192	9265 La Paz
	4324	Karl Xie		508-555-0163	230-555-0193	4912 La Vuelta
	16891	Jonn Jackson	674-555-0110	230-555-0194	40 Ellis St.
	3273	Miguel Miller	397-555-0155	230-555-0195	6696 Anchor Drive
	3588	Osa Agbonile	592-555-0152	230-555-0196	1873 Lion Circle
	10272	Julia Lee		870-555-0110	230-555-0197	3148 Rose Street
	4868	Jose Hayes		599-555-0171	230-555-0198	793 Crawford Street
	4761	Caleb Alexander	670-555-0141	230-555-0199	4775 Kentucky Dr.
	16443	Terry Chander	998-555-0171	230-555-0200	771 Northridge Drive

Você pode criar um arquivo de texto e carregá-lo à sua própria conta de armazenamento se desejar. Para obter instruções, consulte [Carregar dados para trabalhos de Hadoop no HDInsight][hdinsight-upload-data].

> [AZURE.NOTE] Este procedimento usa a tabela de contatos HBase que você criou no último procedimento.

1. Do SSH, execute o seguinte comando para transformar o arquivo de dados para o StoreFiles e armazene em um caminho relativo especificado por Dimporttsv.bulk.output:. Se você estiver no Shell do HBase, use o comando exit para sair.

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Execute o seguinte comando para carregar os dados de /example/data/storeDataFileOutput para a tabela do HBase:

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Você pode abrir o shell do HBase e usar o comando de verificação para listar o conteúdo da tabela.



## Usar o Hive para consultar o HBase

Você pode consultar os dados nas tabelas HBase usando o Hive. Esta seção cria uma tabela Hive que faz o mapeamento para a tabela do HBase e usa-a para consultar os dados em sua tabela do HBase.

1. Abra o **PuTTY** e conecte-se ao cluster. Consulte as instruções no procedimento anterior.
2. Abra o shell de Hive.

	hive
3. Execute script do HiveQL a seguir para criar uma tabela Hive que faz mapeamento para a tabela do HBase. Assegure-se de ter criado a tabela de amostra referenciada anteriormente neste tutorial, usando o Shell HBase antes de executar esta instrução.

		CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. Execute o seguinte script HiveQL. A consulta de Hive consulta os dados na tabela do HBase:

     	SELECT count(*) FROM hbasecontacts;

## Usar APIs de REST do HBase usando Curl

> [AZURE.NOTE] Ao usar o Curl ou qualquer outra comunicação do REST com WebHCat, você deve autenticar as solicitações, fornecendo o nome de usuário e a senha para o administrador do cluster HDInsight. Você também deve usar o nome do cluster como parte do URI (Uniform Resource Identifier) usado para enviar as solicitações ao servidor.
>
> Para os comandos nesta seção, substitua **USERNAME** pelo usuário para autenticar o cluster e substitua **PASSWORD** pela senha da conta de usuário. Substitua **CLUSTERNAME** pelo nome do cluster.
>
> A API REST é protegida por meio de [autenticação básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Você deve sempre fazer solicitações usando HTTPS (HTTP seguro) para ajudar a garantir que suas credenciais sejam enviadas com segurança para o servidor.

1. De uma linha de comando, use o seguinte comando para verificar se você pode se conectar ao cluster HDInsight:

		curl -u <UserName>:<Password> -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

	Você deve receber uma resposta com esta aparência:

    {"status": "ok", "versão": "v1"}

  Os parâmetros usados nesse comando são os seguintes:

    * **-u** - The user name and password used to authenticate the request.
    * **-G** - Indicates that this is a GET request.

2. Use o seguinte comando para listar as tabelas HBase:

		curl -u <UserName>:<Password> -G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. Use o seguinte comando para criar uma nova tabela HBase com duas famílias de coluna:

		curl -u <UserName>:<Password> -v -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" -H "Accept: application/json" -H "Content-Type: application/json" -d "{"@name":"test","ColumnSchema":[{"name":"Personal"},{"name":"Office"}]}"

	O esquema é fornecido no formato JSon.

4. Use o comando a seguir para inserir alguns dados:

		curl -u <UserName>:<Password> -v -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" -H "Accept: application/json" -H "Content-Type: application/json" -d "{"Row":{"key":"1000","Cell":{"column":"Personal:Name", "$":"John Dole"}}}"

5. Use o comando a seguir para obter uma linha:

		curl -u <UserName>:<Password> -v -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" -H "Accept: application/json"

## Verificar o status do cluster

O HBase em HDInsight é fornecido com uma interface do usuário da Web para monitorar clusters. Usando a interface do usuário da Web, você pode solicitar estatísticas ou informações sobre regiões.

O SSH também pode ser usado para criar um túnel de solicitações locais, como solicitações da Web, para o cluster HDInsight. A solicitação será encaminhada para o recurso solicitado como se tivesse sido originada no nó principal do cluster HDInsight. Para saber mais, confira [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel).

**Estabelecer um túnel de sessão SSH**

1. Abra o **PuTTY**.  
2. Se você tiver fornecido uma chave SSH quando criou sua conta de usuário durante o processo de criação, execute a etapa a seguir para selecionar a chave privada a ser usada para autenticar para o cluster:

	Em **Categoria**, expanda **Conexão**, expanda **SSH** e selecione **Autenticação**. Por fim, clique em **Procurar** e selecione o arquivo .ppk que contém a chave privada.

3. Em **Categoria**, clique em **Sessão**.
4. Opções básicas para a tela de sessão PuTTY, digite os seguintes valores:

	- **Nome do Host**: o endereço SSH do seu campo de servidor em nome de Host (ou endereço IP) do HDInsight. O endereço SSH é o nome do cluster, seguido de **-ssh.azurehdinsight.net**. Por exemplo, *mycluster-ssh.azurehdinsight.net*.
	- **Porta**: 22. A porta ssh no nó de cabeçalho 0 é 22.  
5. Na seção **Categoria** à esquerda da caixa de diálogo, expanda **Conexão**, expanda **SSH** e clique em **Túneis**.
6. Forneça as seguintes informações no formulário Opções de controle do encaminhamento de porta SSH:

	- **Porta de Origem**: a porta no cliente que você deseja encaminhar. Por exemplo, 9876.
	- **Dinâmico**: habilita roteamento de proxy SOCKS dinâmico.
7. Clique em **Adicionar** para adicionar as configurações.
8. Clique em **Abrir** na parte inferior da caixa de diálogo para abrir uma conexão SSH.
9. Quando solicitado, faça logon no servidor usando uma conta SSH. Isso estabelecerá uma sessão SSH e habilitará o túnel.

**Para localizar o FQDN dos zookeepers usando o Ambari**

1. Navegue até https://<ClusterName>.azurehdinsight.net/.
2. Insira suas credenciais de conta de usuário do cluster duas vezes.
3. No menu à esquerda, clique em **zookeeper**.
4. Clique em um dos três links do **Servidor ZooKeeper** na lista Resumo.
5. Copie o **Nome do host**. Por exemplo, zk0-NOMEDOCLUSTER.xxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net.

**Configurar um programa de cliente (Firefox) e verificar o status do cluster**

1. Abra o Firefox.
2. Clique no botão **Abrir Menu**.
3. Clique em **Opções**.
4. Clique em **Avançado**, clique em **Rede** e clique em **Configurações**.
5. Selecione **Configuração manual do proxy**.
6. Insira os valores a seguir:

	- **Host de Socks**: localhost
	- **Porta**: use a mesma porta configurada no túnel SSH do Putty. Por exemplo, 9876.
	- **SOCKS v5**: (selecionado)
	- **DNS remoto**: (selecionado)
7. Clique em **OK** para salvar as alterações.
8. Navegue até http://<TheFQDN of a ZooKeeper>:60010/master-status

Em um cluster de alta disponibilidade, você encontrará um link para o nó mestre HBase ativo atual que está hospedando a interface do usuário da Web.



## Próximas etapas?
Neste tutorial do HBase para o HDInsight, você aprendeu a criar um cluster HBase, criar tabelas e exibir os dados nessas tabelas por meio do shell HBase. Você também aprendeu a usar a consulta do Hive dos dados em tabelas HBase e como usar as APIs REST C# do HBase para criar uma tabela HBase e recuperar dados da tabela.

Para obter mais informações, consulte:

- [Visão geral do HBase do HDInsight][hdinsight-hbase-overview]\: o HBase é um banco de dados NoSQL de software livre Apache baseado no Hadoop que fornece acesso aleatório e uma sólida consistência para grandes quantidades de dados não estruturados e semiestruturados.


[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png

<!---HONumber=AcomDC_0218_2016-->