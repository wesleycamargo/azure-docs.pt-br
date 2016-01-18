<properties 
   pageTitle="Criar clusters Hadoop do HDInsight com Repositório Data Lake do Azure usando o portal | Azure" 
   description="Usar o Portal do Azure para criar e usar clusters HDInsight Hadoop com o Repositório Azure Data Lake" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/06/2016"
   ms.author="nitinme"/>

# Criar um cluster HDInsight com o Repositório Data Lake usando o Portal do Azure

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Using PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Aprenda a usar o Portal do Azure para criar um cluster HDInsight (Hadoop, HBase ou Storm) com acesso ao Repositório Azure Data Lake. Algumas considerações importantes para esta versão:

* **Para clusters Hadoop e Storm (Windows e Linux)**, o Repositório Data Lake só pode ser usado como uma conta de armazenamento adicional. A conta de armazenamento padrão para esses clusters ainda será o WASB (Blobs de Armazenamento do Azure).

* **Para clusters HBase (Windows e Linux)**, o Repositório Data Lake pode ser usado como um armazenamento padrão ou armazenamento adicional.


Neste artigo, provisionaremos um cluster Hadoop com o Repositório Data Lake como armazenamento adicional. A configuração do HDInsight para funcionar com o Repositório Data Lake usando o Portal do Azure envolve as seguintes etapas:

* Criar um cluster HDInsight com autenticação para uma Entidade de Serviço do Active Directory do Azure
* Configurar o acesso ao Repositório Data Lake usando a mesma Entidade de Serviço
* Executar um trabalho de teste no cluster

## Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite sua assinatura do Azure** para a visualização pública do Repositório Data Lake. Veja [instruções](data-lake-store-get-started-portal.md#signup).


## Criar um cluster HDInsight com autenticação para uma Entidade de Serviço do Active Directory do Azure

Nesta seção, você cria um cluster HDInsight Hadoop que usa o Repositório Data Lake como um armazenamento adicional. Nesta versão, para um cluster Hadoop, o Repositório Data Lake pode ser usado apenas como um armazenamento adicional para o cluster. O armazenamento padrão ainda será nos blobs de armazenamento do Azure (WASB). Portanto, vamos criar primeiro a conta de armazenamento e os contêineres de armazenamento exigidos para o cluster.

1. Entre no novo [Portal do Azure](https://portal.azure.com).

2. Siga as etapas em [Criar clusters de Hadoop no HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal) para começar a provisionar um cluster HDInsight.
 
3. Na folha **Configuração opcional**, clique em **Fonte de dados**. Na folha **Fonte de dados**, especifique os detalhes para a conta de armazenamento e o contêiner de armazenamento, especifique **Local** como **Leste dos EUA 2**, e, em seguida, clique em **Identidade do AAD do Cluster**.

	![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Adicionar entidade de serviço no cluster HDInsight")

4. Na folha **Identidade do AAD do Cluster**, é possível selecionar uma Entidade de Serviço existente ou criar uma novo.
	
	* **Criar uma Entidade de Serviço** Na folha **Identidade do ADD do Cluster**, clique em **Criar nova**. Clique em **Entidade de Serviço**, e, em seguida, na folha **Criar uma Entidade de Serviço**, forneça valores para criar uma nova entidade de serviço. Como parte disso, um certificado e um aplicativo do Active Directory do Azure também é criado. Clique em **Criar**.

		![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "Adicionar entidade de serviço no cluster HDInsight")

		Na folha **Identidade do ADD do Cluster**, clique em **Selecionar** para prosseguir com a entidade de serviço que será criada.

		![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Adicionar entidade de serviço no cluster HDInsight")


	* **Escolha uma Entidade de Serviço existente**. Na folha **Identidade do AAD do Cluster**, clique em **Usar existente**. Clique em **Entidade de Serviço**, e, em seguida, na folha **Selecionar uma Entidade de Serviço**, pesquise por uma entidade de serviço existente. Clique em um nome de entidade de serviço e, em seguida, clique em **Selecionar**.

		![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Adicionar entidade de serviço no cluster HDInsight")

		Na folha **Identidade do AAD do Cluster**, carregar o certificado (.pfx) que você criou anteriormente e forneça a senha usada para criar o certificado. Clique em **Selecionar**. Isso conclui a configuração do Active Directory do Azure para o cluster HDInsight.

		![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Adicionar entidade de serviço no cluster HDInsight")

6. Clique em **Selecionar** sobre a folha **Fonte de dados** e continue com o provisionamento de cluster, conforme descrito em [Criar cluster de Hadoop no HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal).

7. Depois que o cluster for provisionado, você pode verificar que a Entidade de Serviço está associada com o cluster HDInsight. Para fazer isso, na folha cluster, clique em **Configurações**, clique em **Identidade do AAD do Cluster**, e você deverá ver a entidade de serviço associado.

	![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Adicionar entidade de serviço no cluster HDInsight")

## <a name="acl"></a>Configurar a entidade de serviço para acessar o sistema de arquivos do Repositório Data Lake

1. Entre no novo [Portal do Azure](https://portal.azure.com).

2. Se você não tiver uma conta do Repositório Data Lake, crie uma. Siga as instruções em [Introdução ao Repositório Azure Data Lake usando o Portal do Azure](data-lake-store-get-started-portal.md).

	Se você já tiver uma conta do Repositório Data Lake, no painel esquerdo, clique em **Procurar**, clique em **Repositório Data Lake**, e, em seguida, clique no nome de conta ao qual você deseja conceder acesso.

	Execute as seguintes tarefas na sua conta do Repositório Data Lake.

	* [Criar uma pasta no seu Repositório Data Lake](data-lake-store-get-started-portal.md#createfolder).
	* [Carregar um arquivo no seu Repositório Data Lake](data-lake-store-get-started-portal.md#uploaddata). Se estiver procurando alguns dados de exemplo para carregar, é possível obter a pasta **Dados da Ambulância** no [Repositório Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

	Você usará posteriormente os arquivos carregados quando você testar a conta do Repositório Data Lake dados com o cluster HDInsight.

3. Na folha do Repositório Data Lake, clique em **Gerenciador de Dados**.

	![Gerenciador de dados](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.start.data.explorer.png "Gerenciador de dados")

4. Na folha **Gerenciador de Dados**, clique na raiz de sua conta e, na folha de sua conta, clique no ícone **Acesso**.

	![Definir ACLs no sistema de arquivos do Data Lake](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.1.png "Definir ACLs no sistema de arquivos do Data Lake")

5. A folha **Acesso** lista o acesso padrão e o acesso personalizado já atribuídos à raiz. Cliquem no ícone **Adicionar** para adicionar ACLs de nível personalizado e incluir a entidade de serviço que você criou anteriormente.

	![Listar acesso padrão e personalizado](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.2.png "Listar acesso padrão e personalizado")

6. Clique no ícone **Adicionar** para abrir a folha **Adicionar Acesso Personalizado**. Nessa folha, clique em **Selecionar Usuário ou Grupo** e na folha **Selecionar Usuário ou Grupo**, procure a entidade de serviço que você criou anteriormente no Active Directory do Azure. O nome da entidade de serviço criada anteriormente é **HDIADL**. Clique em um nome de entidade de serviço e, em seguida, clique em **Selecionar**.

	![Adicionar um grupo](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.3.png "Adicionar um grupo")

7. Clique em **Selecionar Permissões**, selecione as permissões que você deseja atribuir a essa entidade de serviço e clique em **OK**.

	![Atribuir permissões ao grupo](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.4.png "Atribuir permissões ao grupo")

8. Na folha **Adicionar Acesso Personalizado**, clique em **OK**. O grupo recém-adicionado, com as permissões associadas, estará listado na folha **Acesso**.

	![Atribuir permissões ao grupo](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.5.png "Atribuir permissões ao grupo")

7. Se for necessário, também é possível modificar as permissões de acesso depois de adicionar a entidade de serviço. Marque ou desmarque a caixa de seleção de cada tipo de permissão (Leitura, Gravação, Execução) com base em seu desejo de remover ou atribuir essa permissão. Clique em **Salvar** para salvar as alterações ou em **Descartar** para desfazer as alterações.



## Executar trabalhos de teste no cluster HDInsight para usar o Repositório Data Lake do Azure

Após a configuração de um cluster HDInsight, você poderá executar trabalhos de teste no cluster a fim de testar se o cluster HDInsight pode acessar dados no Repositório Data Lake do Azure. Para fazer isso, vamos executar algumas consultas de hive que visam o Repositório Data Lake.

1. Abra a folha de cluster para o cluster que você acabou de provisionar e, em seguida, clique em **Painel**.

	![Iniciar painel do cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Iniciar painel do cluster")

	Quando solicitado, insira as credenciais de administrador para o cluster.

2. Isso abre o Console de consulta do Microsoft Azure HDInsight. Clique em **Editor Hive**.

	![Abrir o editor Hive](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster2.png "Abrir o editor Hive")

3. No Editor de Hive, digite a seguinte consulta e, em seguida, clique em **Enviar**.

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

	Nesta consulta de Hive, criamos uma tabela de dados armazenados no Repositório Data Lake em `adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder`. Esse local tem um arquivo de dados de exemplo que você deve ter carregado anteriormente.

	A tabela **Sessão de trabalho** na parte inferior mostra o status do trabalho sendo alterado de **Inicializando**, para **Executando**, para **Concluído**. Você também pode clicar em **Exibir detalhes** para obter mais informações sobre o trabalho concluído.

	![Criar tabela](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster3.png "Criar tabela")

4. Execute a consulta a seguir para verificar se a tabela foi criada.

		SHOW TABLES;

	Clique em Exibir detalhes correspondente a essa consulta e a saída deve mostrar o seguinte:

		hivesampletable
		vehicles

	**veículos** é a tabela que você criou anteriormente. **hivesampletable** é uma tabela de exemplo disponível em todos os clusters de HDInsight por padrão.

5. Você também pode executar uma consulta para recuperar dados de **veículos**.

		SELECT * FROM vehicles LIMIT 5;

## Repositório Data Lake usando comandos do HDFS

Após a configuração do cluster HDInsight para usar o Repositório Data Lake, você poderá usar os comandos do shell HDFS para acessar o armazenamento.

1. Entre no novo [Portal do Azure](https://portal.azure.com).

2. Clique em **Procurar**, em **Clusters HDInsight** e clique no cluster HDInsight que você criou.

3. Na folha do cluster, clique em **Área de Trabalho Remota** e na folha **Área de Trabalho Remota**, clique em **Conectar**.

	![Remoto em cluster HDI](./media/data-lake-store-hdinsight-hadoop-use-portal/ADL.HDI.PS.Remote.Desktop.png "Criar um grupo de recursos do Azure")

	Quando receber a solicitação, insira as credenciais fornecidas para o usuário da área de trabalho remota.

4. Na sessão remota, inicie o Windows PowerShell e use os comandos do sistema de arquivos HDFS para listar os arquivos no Repositório Data Lake do Azure.

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	Isso deve listar o arquivo carregado anteriormente no Repositório Data Lake.

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

	Você também pode usar o comando `hdfs dfs -put` para carregar alguns arquivos no Repositório Data Lake e usar `hdfs dfs -ls` para verificar se os arquivos foram carregados com êxito.

## Considerações para o provisionamento do cluster de HBase que usa o Repositório Data Lake como armazenamento padrão

Para clusters de HBase, você pode usar contas do Repositório Data Lake como armazenamento padrão. Se você optar por fazer isso, para provisionar um cluster com êxito, a entidade de serviço associado ao cluster **deve** ter acesso à conta do Repositório Data Lake. Você pode garantir isso de duas maneiras:

* **Se você usar uma entidade de serviço existente**, você deve garantir que a entidade de serviço é adicionada à ACL no nível raiz do Repositório Data Lake antes de iniciar o provisionamento do cluster.
* **Se você optar por criar uma nova entidade de serviço** como parte do provisionamento do cluster, assim que o cluster inicia o provisionamento, você deve adicionar a entidade de serviço recém-criada para o nível raiz do sistema de arquivo do Repositório Data Lake. Se você não conseguir fazer isso, o cluster será configurado mas os serviços do HBase não serão iniciados. Para solucionar esse problema, você deve, em seguida, adicionar a entidade de serviço à ACL da conta do Repositório Data Lake e, em seguida, reiniciar os serviços do HBase usando a interface do usuário da Web do Ambari.

Para obter instruções sobre como adicionar uma entidade de serviço a um sistema de arquivos do Repositório Data Lake, consulte [Configurar a entidade de serviço para acessar o sistema de arquivos do Repositório Data Lake](#acl).



## Veja também

* [PowerShell: Criar um cluster HDInsight para usar o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_0107_2016-->