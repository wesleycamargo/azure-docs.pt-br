<properties 
	pageTitle="Executar um trabalho do Hadoop usando o Banco de Dados de Documentos e o HDInsight | Microsoft Azure" 
	description="Saiba como executar um trabalho de Hive, Pig e MapReduce simples com o Banco de Dados de Documentos e o HDInsight do Azure."
	services="documentdb" 
	authors="AndrewHoh" 
	manager="jhubbard" 
	editor="mimig"
	documentationCenter=""/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="01/29/2016" 
	ms.author="anhoh"/>

#<a name="DocumentDB-HDInsight"></a>Executar um trabalho do Hadoop usando o Banco de Dados de Documentos e o HDInsight

Este tutorial mostra como executar trabalhos MapReduce do [Apache Hive][apache-hive], [Apache Pig][apache-pig] e [Apache Hadoop][apache-hadoop] no Azure HDInsight com o conector para Hadoop do Banco de Dados de Documentos. O conector para Hadoop do Banco de Dados de Documentos permite que ele atue como origem e como coletor para trabalhos de Hive, Pig e MapReduce. Este tutorial usa o Banco de Dados de Documentos como a fonte e o destino dos dados para trabalhos do Hadoop.

Depois de concluir este tutorial, você poderá responder às seguintes perguntas:

- Como eu carrego dados do Banco de Dados de Documentos usando um trabalho de Hive, Pig ou MapReduce?
- Como eu armazeno dados no Banco de Dados de Documentos usando um trabalho de Hive, Pig ou MapReduce?

Nós recomendamos que você comece assistindo ao vídeo a seguir, no qual executamos um trabalho do Hadoop usando o Banco de Dados de Documentos e o HDInsight.

> [AZURE.VIDEO use-azure-documentdb-hadoop-connector-with-azure-hdinsight]

Depois, volte a este artigo, no qual você verá todos os detalhes de como executar trabalhos analíticos nos seus dados do Banco de Dados de Documentos.

> [AZURE.TIP] Este tutorial presume que você tenha experiência anterior com o uso do Apache Hadoop, Hive e/ou Pig. Se você for iniciante no Apache Hadoop, Hive e Pig, recomendamos visitar a [documentação do Apache Hadoop][apache-hadoop-doc]. Este tutorial também pressupõe que você não tenha experiência anterior com o Banco de Dados de Documentos e tenha uma conta do Banco de Dados de Documentos. Se você for iniciante ou não tiver uma conta do Banco de Dados de Documentos, veja nossa página do [Guia de Introdução][getting-started].

Não tem tempo para fazer o tutorial e quer apenas a amostra completa de scripts do PowerShell para Hive, Pig e MapReduce? Sem problemas, elas estão [aqui][documentdb-hdinsight-samples]. O download contém também arquivos hql, pig e java para essas amostras.

## <a name="NewestVersion"></a>Versão Mais Recente

<table border='1'>
	<tr><th>Versão do Conector para Hadoop</th>
		<td>1.1.0</td></tr>
	<tr><th>URI do script</th>
		<td>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v03.ps1</td></tr>
	<tr><th>Data da Modificação</th>
		<td>07/20/2015</td></tr>
	<tr><th>Versões do HDInsight para as quais há suporte</th>
		<td>3.1, 3.2</td></tr>
	<tr><th>Log de alterações</th>
		<td>SDK Java do Banco de Dados de Documentos atualizado para 1.1.0</br>
			Removido o parâmetro de saída adicional para caminhos de indexação personalizados</br>
			Adicionado parâmetro opcional para precisão de cadeia de caracteres personalizada (-1 por padrão)</br>
			6/11/2015</br>
			Corrigida a compatibilidade do conector com o <a href="https://www.microsoft.com/download/details.aspx?id=40886">driver ODBC do Microsoft Hive</a></br>
			Adicionada a capacidade de alterar o tipo de oferta de coleção de saída (por padrão, oferta S3)</br>
		</td></tr>
</table>

## <a name="Prerequisites"></a>Pré-requisitos
Antes de seguir as instruções neste tutorial, certifique-se de ter o seguinte:

- Uma conta do Banco de Dados de Documentos, um banco de dados e uma coleção com documentos. Para saber mais, consulte o [Guia de Introdução ao Banco de Dados de Documentos][getting-started]. Importe dados de exemplo para a conta do Banco de Dados de Documentos com a [ferramenta de importação do Banco de Dados de Documentos][documentdb-import-data].
- Produtividade. Leituras e gravações do HDInsight serão contabilizadas de acordo com suas unidades de solicitação alocadas para suas coleções. Para saber mais, consulte [Produtividade provisionada, unidades de solicitação e operações de banco de dados][documentdb-manage-throughput].
- Capacidade para um procedimento armazenado adicional dentro de cada coleção de saída. Os procedimentos armazenados são usados para transferir os documentos resultantes. Para saber mais, consulte [Coleções e produtividade provisionada][documentdb-manage-document-storage].
- Capacidade para os documentos resultantes dos trabalhos de Hive, Pig ou MapReduce. Para saber mais, consulte [Gerenciar a capacidade e o desempenho do Banco de Dados de Documentos][documentdb-manage-collections].
- [*Opcional*] Capacidade para uma coleção adicional. Para saber mais, consulte [Armazenamento de documentos provisionado e sobrecarga de índice][documentdb-manage-document-storage].
	
> [AZURE.WARNING] Para evitar a criação de uma nova coleção durante um dos trabalhos, você pode imprimir os resultados no StdOut, salvar a saída no seu contêiner WASB ou especificar um coleção que já existe. Caso você especifique uma coleção existente, novos documentos serão criados dentro da coleção e os documentos existentes serão afetados somente se houver conflitos entre as *IDs*. **O conector substitui automaticamente documentos existentes com conflitos de ID**. Você pode desativar esse recurso definindo a opção de upsert como falsa. Se upsert estiver definido como falso e houver um conflito, o trabalho do Hadoop falhará, retornando um erro de conflito de ID.

## <a name="CreateStorage"></a>Etapa 1: Criar uma conta de Armazenamento do Azure

> [AZURE.IMPORTANT] Se você **já** tiver uma Conta de Armazenamento do Azure e quiser criar um novo contêiner de blob dentro dessa conta, passe para a [Etapa 2: Criar um cluster HDInsight personalizado](#ProvisionHDInsight).

O Azure HDInsight usa o Armazenamento de Blob do Azure para armazenar dados. Ele é chamado *WASB* ou *Armazenamento do Azure - Blob*. O WASB é a implementação do HDFS da Microsoft no Armazenamento de Blob do Azure. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage].

Ao provisionar um cluster HDInsight, você especifica uma conta de Armazenamento do Azure. Um contêiner de armazenamento de Blob específico dessa conta é designado como o sistema de arquivos padrão, exatamente como no HDFS. O cluster HDInsight, por padrão, é provisionado no mesmo data center que a conta de armazenamento especificada.

**Para criar uma conta do Armazenamento do Azure**

1. Faça logon no [Portal Clássico do Azure][azure-classic-portal].

2. Clique em **+ NOVO** no canto inferior esquerdo, aponte para **SERVIÇOS DE DADOS**, aponte para **ARMAZENAMENTO** e, em seguida, clique em **CRIAÇÃO RÁPIDA**.
	![Portal do Azure, em que você pode usar a criação rápida para configurar uma nova conta de armazenamento.][image-storageaccount-quickcreate]

3. Insira a **URL**, selecione os valores de **LOCAL** e **REPLICAÇÃO** e clique em **CRIAR CONTA DE ARMAZENAMENTO**. Não há suporte para grupos de afinidade.
	
	Você verá a nova conta de armazenamento na lista de armazenamento.

	> [AZURE.IMPORTANT] Para obter o melhor desempenho, certifique-se de que a conta de armazenamento, o cluster HDInsight e a conta do Banco de Dados de Documentos estão na mesma região do Azure.

4. Aguarde até que o **STATUS** da nova conta de armazenamento seja alterado para **Online**.

## <a name="ProvisionHDInsight"></a>Etapa 2: Criar um cluster HDInsight personalizado
Este tutorial usa a Ação de Script do Portal Clássico do Azure para personalizar o cluster HDInsight. Neste tutorial, usaremos o Portal Clássico do Azure para criar o cluster personalizado. Para sabe como usar cmdlets do PowerShell ou a SDK .NET do HDInsight, veja o artigo [Personalizar clusters HDInsight usando Ação de Script][hdinsight-custom-provision].

1. Entre no [Portal Clássico do Azure][azure-classic-portal]. Você pode já estar conectado desde a etapa anterior.

2. No final da página, clique em **+ NOVO**, em **SERVIÇOS DE DADOS**, em **HDINSIGHT** e em **CRIAÇÃO PERSONALIZADA**.

3. Na página **Detalhes do Cluster**, digite ou escolha os valores a seguir:

	![Fornecer detalhes do cluster HDInsight inicial do Hadoop][image-customprovision-page1]

	<table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Nome do cluster</td><td>Nome do cluster.<br/>
		O nome DNS deve começar e terminar com um número alfanumérico e pode conter traços.<br/>
		O campo deve ser uma cadeia com 3 a 63 caracteres.</td></tr>
	<tr><td>Nome da assinatura</td>
		<td>Se você tiver mais de uma assinatura do Azure, selecione a assinatura correspondente à conta de armazenamento da <strong>Etapa 1</strong>. </td></tr>
	<tr><td>Tipo de cluster</td>
		<td>Para o tipo de cluster, selecione <strong>Hadoop</strong>.</td></tr>
	<tr><td>Sistema operacional</td>
		<td>Para sistema operacional, selecione <strong>Windows Server 2012 R2 Datacenter</strong>.</td></tr>
	<tr><td>Versão do HDInsight</td>
		<td>Escolha a versão. </br>Selecione <Strong>HDInsight versão 3.1</Strong>.</td></tr>
		</table>
	<p>Insira ou selecione os valores, conforme mostrado na tabela, e clique na seta para a direita.</p>

4. Na página **Configurar Cluster**, digite ou selecione os valores a seguir:

	<table border="1">
<tr><th>Nome</th><th>Valor</th></tr>
<tr><td>Nós de dados</td><td>Número de nós de dados que você deseja implantar. </br>Observe que os nós de dados do HDInsight estão associados ao desempenho e ao preço.</td></tr>
<tr><td>Região/Rede virtual</td><td>Escolha a mesma região da <strong>Conta de armazenamento</strong> recém-criada e da <strong>Conta do Banco de Dados de Documentos</strong>. </br> O HDInsight requer que a conta de armazenamento esteja localizada na mesma região. Mais adiante na configuração, você poderá escolher somente uma conta de armazenamento que esteja na mesma região especificada aqui.</td></tr>
	</table>
    Clique na seta à direita.

5. Na página **Configurar Usuário de Cluster**, forneça os seguintes valores:

    <table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Nome de usuário</td>
		<td>Especifique o nome do usuário do cluster HDInsight.</td></tr>
	<tr><td>Senha/Confirmar senha</td>
		<td>Especifique a senha do usuário do cluster HDInsight.</td></tr>
	</table>
    Clique na seta à direita.
    
6. Na página **Conta de Armazenamento**, forneça os seguintes valores:

	![Fornecer conta de armazenamento para o cluster Hadoop do HDInsight][image-customprovision-page4]

	<table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Conta de armazenamento</td>
		<td>Especifique a conta de armazenamento do Azure que será usada como o sistema de arquivos padrão para o cluster HDInsight. Você pode escolher uma das três opções a seguir: Usar Armazenamento Existente, Criar Novo Armazenamento ou Usar Armazenamento de Outra Assinatura</br></br>
		Selecione <strong>Usar Armazenamento Existente</strong>.
		</td>
		</td></tr>
	<tr><td>Nome da conta</td>
		<td>
		Para <strong>Nome da conta</strong>, selecione a conta criada na <strong>Etapa 1</strong>. A lista suspensa mostra somente as contas de armazenamento da mesma assinatura do Azure que estão localizadas no mesmo data center onde você optar por provisionar o cluster.
		</td></tr>
	<tr><td>Contêiner padrão</td>
		<td>Especifica o contêiner padrão na conta de armazenamento usado como o sistema de arquivos padrão para o cluster HDInsight. Se você escolher <strong>Usar Armazenamento Existente</strong> para o campo <strong>Conta de Armazenamento</strong> e não houver contêineres na conta, o contêiner será criado por padrão com o mesmo nome do cluster. Se um contêiner com o nome do cluster já existir, será acrescentado um número de sequência ao nome do contêiner.
    </td></tr>
	<tr><td>Contas de armazenamento adicionais</td>
		<td>O HDInsight dá suporte a várias contas de armazenamento. Não há limites de contas de armazenamento adicionais que podem ser usadas por um cluster. No entanto, se você criar um cluster usando o Portal Clássico do Azure, você terá um limite de sete, devido às restrições da interface do usuário. Cada conta de armazenamento adicional que você especificar adiciona uma página extra de Conta de Armazenamento ao assistente, onde você pode especificar as informações da conta.</td></tr>
	</table>
	Clique na seta à direita.

7. Na página **Ações de Script**, clique em **adicionar ação de script** para fornecer detalhes sobre o script do PowerShell que você executará para personalizar o cluster quando ele estiver sendo criado. O script do PowerShell instala o conector para Hadoop do Banco de Dados de Documentos em seus clusters HDInsight durante a criação do cluster.
	
	![Configurar a Ação de Script para personalizar um cluster do HDInsight][image-customprovision-page5]

	<table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Nome</td>
		<td>Especifique um nome para a ação de script.</td></tr>
	<tr><td>URI do script</td>
		<td>Especifique o URI para o script que é chamado para personalizar o cluster.</br></br>
		Insira: </br> <strong>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v03.ps1</strong>.</td></tr>
	<tr><td>Tipo de nó</td>
		<td>Especifica os nós em que o script de personalização é executado. Você pode escolher <b>Todos os nós</b>, <b>Somente nós principais</b> ou somente <b>Nós de trabalho</b>.</br></br>
		Selecione <strong>Todos os Nós</strong>.</td></tr>
	<tr><td>Parâmetros</td>
		<td>Especifique os parâmetros, se exigido pelo script.</br></br>
		<strong>Nenhum parâmetro necessário</strong>.</td></tr>
	</table>
	Clique na marca de seleção para concluir a criação do cluster.

## <a name="InstallCmdlets"></a>Etapa 3: Instalar e configurar o PowerShell do Azure

1. Instale o PowerShell do Azure. As instruções podem ser encontradas [aqui][powershell-install-configure].

	> [AZURE.NOTE] Como alternativa, só para consultas Hive, você pode usar o Editor de Hive online do HDInsight. Para fazer isso, entre no [Portal Clássico do Azure][azure-classic-portal] e clique em **HDInsight** no painel esquerdo para exibir uma lista dos clusters HDInsight. Clique no cluster no qual deseja executar consultas Hive e clique em **Console de Consulta**.

2. Abra o Ambiente de Script Integrado do PowerShell do Azure:
	- Em um computador com Windows 8 ou Windows Server 2012 ou posterior, você pode usar a Pesquisa interna. Na tela Inicial, digite **powershell ise** e clique em **Inserir**. 
	- Em um computador com uma versão anterior ao Windows 8 ou ao Windows Server 2012, use o menu Iniciar. No menu Iniciar, digite **Prompt de Comando** na caixa de pesquisa e, na lista de resultados, clique em **Prompt de Comando**. No Prompt de Comando, digite **powershell\_ise** e clique em **Inserir**.

3. Adicione sua conta do Azure.
	1. No Painel do Console, digite **Add-AzureAccount** e clique em **Inserir**. 
	2. Digite o endereço de email associado à sua assinatura do Azure e clique em **Continuar**. 
	3. Digite a senha da sua assinatura do Azure. 
	4. Clique em **Entrar**.

4. O diagrama a seguir identifica as partes importantes do seu Ambiente de Script de PowerShell do Azure.

	![Diagrama do PowerShell do Azure][azure-powershell-diagram]

## <a name="RunHive"></a>Etapa 4: Executar um trabalho Hive usando o Banco de Dados de Documentos e o HDInsight

> [AZURE.IMPORTANT] Todas as variáveis indicadas entre < e > devem ser preenchidas usando suas configurações.

1. Defina as seguintes variáveis no seu Painel de Script do PowerShell.

		# Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<AzureStorageContainerName>"

		# Provide the HDInsight cluster name where you want to run the Hive job.
		$clusterName = "<HDInsightClusterName>"

2. 
	<p>Vamos começar pela cadeia de caracteres de consulta. Vamos escrever uma consulta do Hive que usa todas as IDs exclusivas (_rid) e os carimbos de data/hora (_ts) gerados pelo sistema de documentos de uma coleção do Banco de Dados de Documentos, registra todos os documentos por minuto e armazena os resultados em uma nova coleção do Banco de Dados de Documentos. </p>
	
	<p>Primeiro, vamos criar uma tabela Hive por meio da coleção do Banco de Dados de Documentos. Adicione o seguinte trecho de código ao painel de Script do PowerShell <strong>após</strong> o trecho de código do n.º 1. Inclua o parâmetro opcional DocumentDB.query para limitar os documentos a somente _ts e _rid. </p>
	
	> [AZURE.NOTE] **Ter nomeado DocumentDB.inputCollections não foi um erro.** Sim, podemos permitir a adição de várias coleções como uma entrada: </br> 
	'*DocumentDB.inputCollections*' = '*<Nome 1 de Coleção de Entrada de Banco de Dados de Documentos>*,*<Nome 2 de Coleção de Entrada de Banco de Dados de Documentos>*' </br>Os nomes de coleção são separados sem espaços, usando apenas uma única vírgula.


		# Create a Hive table using data from DocumentDB. Pass DocumentDB the query to filter transferred data to _rid and _ts.
		$queryStringPart1 = "drop table DocumentDB_timestamps; "  + 
                            "create external table DocumentDB_timestamps(id string, ts BIGINT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " + 
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "
 
3.  Em seguida, vamos criar uma tabela Hive para a coleção de saída. As propriedades do documento de saída serão o mês, dia, hora, minuto e o número total de ocorrências.

	> [AZURE.NOTE] **Mais uma vez, ter nomeado DocumentDB.outputCollections não foi um erro.** Sim, podemos permitir a adição de várias coleções como uma saída: </br> 
	'*DocumentDB.outputCollections*' = '*<Nome 1 de Coleção de Saída de Banco de Dados de Documentos>*,*<Nome 2 de Coleção de Saída de Banco de Dados de Documentos>*' </br> Os nomes de coleção são separados sem espaços, usando apenas uma única vírgula. </br></br> 
	Os documentos são distribuídos por round robin entre várias coleções. Um lote de documentos será armazenado em uma coleção, um segundo lote de documentos será armazenado na coleção seguinte e assim por diante.

		# Create a Hive table for the output data to DocumentDB.
	    $queryStringPart2 = "drop table DocumentDB_analytics; " +
                              "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                              "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " + 
                              "tblproperties ( " + 
                                  "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                  "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                  "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                  "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "

4. Por fim, vamos classificar os documentos por mês, dia, hora e minuto e inserir os resultados na tabela Hive de saída.

		# GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "

5. Adicione o trecho de script a seguir para criar uma definição de trabalho Hive por meio da consulta anterior.

		# Create a Hive job definition.
		$queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

	Você também pode usar a opção -File para especificar um arquivo de script HiveQL no HDFS.

6. Adicione o trecho a seguir para salvar a hora de início e enviar o trabalho Hive.

		# Save the start time and submit the job to the cluster.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7. Adicione o seguinte para aguardar a conclusão do trabalho Hive.

		# Wait for the Hive job to complete.
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8. Adicione o seguinte para imprimir a saída padrão e os horários de início e fim.

		# Print the standard error, the standard output of the Hive job, and the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. **Execute** seu novo script! **Clique** no botão de execução verde.

10. Confira os resultados. Faça logon no [Portal do Azure][azure-portal].
	1. Clique em <strong>Procurar</strong> no painel do lado esquerdo. </br>
	2. Clique em <strong>tudo</strong> na parte superior direita no painel de navegação. </br>
	3. Encontre <strong>Contas de Banco de Dados de Documentos</strong> e clique neste item. </br>
	4. Em seguida, encontre sua <strong>Conta de Banco de Dados de Documentos</strong>, depois o <strong>Banco de dados do Banco de Dados de Documentos</strong> e sua <strong>Coleção do Banco de Dados de Documentos</strong> associada à coleção de saída especificada em sua consulta Hive.</br>
	5. Por fim, clique em <strong>Gerenciador de Documentos</strong> abaixo de <strong>Ferramentas de Desenvolvedor</strong>.</br></p>

	Você verá os resultados da consulta Hive.

	![Resultados da consulta de Hive][image-hive-query-results]

## <a name="RunPig"></a>Etapa 5: Executar um trabalho Pig usando o Banco de Dados de Documentos e o HDInsight

> [AZURE.IMPORTANT] Todas as variáveis indicadas entre < e > devem ser preenchidas usando suas configurações.

1. Defina as seguintes variáveis no seu Painel de Script do PowerShell.

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"

2. <p>Vamos começar pela cadeia de caracteres de consulta. Vamos escrever uma consulta Pig que usa todas as IDs exclusivas (_rid) e os carimbos de data/hora (_ts) gerados pelo sistema de documentos de uma coleção do Banco de Dados de Documentos, registra todos os documentos por minuto e armazena os resultados em uma nova coleção do Banco de Dados de Documentos.</p>
    <p>Primeiro, carregue documentos do Banco de Dados de Documentos no HDInsight. Adicione o seguinte trecho de código ao painel de Script do PowerShell <strong>após</strong> o trecho de código do n.º 1. Adicione uma consulta do Banco de Dados de Documentos ao parâmetro de consulta opcional do Banco de Dados de Documentos para limitar os documentos a somente _ts e _rid.</p>
    
    > [AZURE.NOTE] Sim, podemos permitir a adição de várias coleções como uma entrada: </br> 
    '*<Nome 1 de Coleção de Entrada de Banco de Dados de Documentos>*,*<Nome 2 de Coleção de Entrada de Banco de Dados de Documentos>*'</br> Os nomes de coleção são separados sem espaços, usando apenas uma única vírgula. </b>

	OS documentos são distribuídos em round robin entre várias coleções. Um lote de documentos será armazenado em uma coleção, um segundo lote de documentos será armazenado na coleção seguinte e assim por diante.

		# Load data from DocumentDB. Pass DocumentDB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  Em seguida, vamos classificar os documentos por mês, dia, hora, minuto e o número total de ocorrências.

		# GROUP BY minute and COUNT entries for each.
        $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                            "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                            "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "

4. Por fim, vamos armazenar os resultados em nossa nova coleção de saída.

    > [AZURE.NOTE] Sim, podemos permitir a adição de várias coleções como uma entrada: </br> 
    '<Nome 1 de Coleção de Saída de Banco de Dados de Documentos>,<Nome 2 de Coleção de Saída de Banco de Dados de Documentos>'</br> Os nomes de coleção são separados sem espaços, usando apenas uma única vírgula.</br> 
    Os documentos serão distribuídos em round robin entre os vários documentos. Um lote de documentos será armazenado em uma coleção, um segundo lote de documentos será armazenado na coleção seguinte e assim por diante.

		# Store output data to DocumentDB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "

5. Adicione o trecho de script a seguir para criar uma definição de trabalho Pig por meio da consulta anterior.

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

	Você também pode usar a opção -File para especificar um arquivo script Pig no HDFS.

6. Adicione o trecho a seguir para salvar a hora de início e enviar o trabalho Pig.

		# Save the start time and submit the job to the cluster.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition

7. Adicione o seguinte para aguardar a conclusão do trabalho Pig.

		# Wait for the Pig job to complete.
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8. Adicione o seguinte para imprimir a saída padrão e os horários de início e fim.

		# Print the standard error, the standard output of the Hive job, and the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
		
9. **Execute** seu novo script! **Clique** no botão de execução verde.

10. Confira os resultados. Faça logon no [Portal do Azure][azure-portal].
	1. Clique em <strong>Procurar</strong> no painel do lado esquerdo. </br>
	2. Clique em <strong>tudo</strong> na parte superior direita no painel de navegação. </br>
	3. Encontre <strong>Contas de Banco de Dados de Documentos</strong> e clique neste item. </br>
	4. Em seguida, encontre sua <strong>Conta do Banco de Dados de Documentos</strong>, depois o <strong>Banco de dados do Banco de Dados de Documentos</strong> e sua <strong>Coleção do Banco de Dados de Documentos</strong> associada à coleção de saída especificada na consulta Pig.</br>
	5. Por fim, clique em <strong>Gerenciador de Documentos</strong> abaixo de <strong>Ferramentas de Desenvolvedor</strong>.</br></p>

	Você verá os resultados da consulta Pig.

	![Resultados da consulta de Pig][image-pig-query-results]

## <a name="RunMapReduce"></a>Etapa 6: Executar um trabalho MapReduce usando o Banco de Dados de Documentos e o HDInsight

1. Defina as seguintes variáveis no seu Painel de Script do PowerShell.
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name
		
2. Executaremos um trabalho MapReduce que computa o número de ocorrências para cada propriedade do Documento por meio de sua coleção do Banco de Dados de Documentos. Adicione este trecho de script **após** o trecho acima.

		# Define the MapReduce job.
		$TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

	> [AZURE.NOTE] TallyProperties-v01.jar vem com a instalação personalizada do Conector para Hadoop do Banco de Dados de Documentos.

3. Adicione o comando a seguir para enviar o trabalho MapReduce.

		# Save the start time and submit the job.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	Além da definição do trabalho MapReduce, você também deve fornecer o nome do cluster HDInsight onde você deseja executar o trabalho MapReduce e as credenciais. O Start-AzureHDInsightJob é uma chamada assincronizada. Para verificar a conclusão do trabalho, use o cmdlet *AzureHDInsightJob de espera*.

4. Adicione o seguinte comando para verificar se há erros na execução do trabalho MapReduce.
	
		# Get the job output and print the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green 

5. **Execute** seu novo script! **Clique** no botão de execução verde.

6. Confira os resultados. Faça logon no [Portal do Azure][azure-portal].
	1. Clique em <strong>Procurar</strong> no painel do lado esquerdo.
	2. Clique em <strong>tudo</strong> na parte superior direita no painel de navegação.
	3. Encontre <strong>Contas do Banco de Dados de Documentos</strong> e clique neste item.
	4. Em seguida, encontre sua <strong>Conta do Banco de Dados de Documentos</strong>, depois o <strong>Banco de dados do Banco de Dados de Documentos</strong> e sua <strong>Coleção do Banco de Dados de Documentos</strong> associada à coleção de saída especificada no seu trabalho MapReduce.
	5. Por fim, clique em <strong>Gerenciador de Documentos</strong> abaixo de <strong>Ferramentas de Desenvolvedor</strong>.

	Você verá os resultados do trabalho MapReduce.

	![Resultados da consulta de MapReduce][image-mapreduce-query-results]

## <a name="NextSteps"></a>Próximas etapas

Parabéns! Você acaba de executar seus primeiros trabalhos Hive, Pig e MapReduce usando o HDInsight e o Banco de Dados de Documentos do Azure.

Nós abrimos o código-fonte do nosso Conector do Hadoop. Se estiver interessado, você pode contribuir com o [GitHub][documentdb-github].

Para saber mais, consulte os seguintes artigos:

- [Desenvolver um aplicativo Java com o Banco de Dados de Documentos][documentdb-java-application]
- [Desenvolver programas Java MapReduce para Hadoop no HDInsight][hdinsight-develop-deploy-java-mapreduce]
- [Introdução ao uso do Hadoop com o Hive no HDInsight para analisar o uso de fone de celular][hdinsight-get-started]
- [Usar o MapReduce com o HDInsight][hdinsight-use-mapreduce]
- [Usar o Hive com o HDInsight][hdinsight-use-hive]
- [Usar o Pig com o HDInsight][hdinsight-use-pig]
- [Personalizar os clusters HDInsight usando a Ação de Script][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: documentdb-get-started.md

[azure-classic-portal]: https://manage.windowsazure.com/
[azure-powershell-diagram]: ./media/documentdb-run-hadoop-with-hdinsight/azurepowershell-diagram-med.png
[azure-portal]: https://portal.azure.com/

[documentdb-hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[documentdb-github]: https://github.com/Azure/azure-documentdb-hadoop
[documentdb-java-application]: documentdb-java-application.md
[documentdb-manage-collections]: documentdb-manage.md#Collections
[documentdb-manage-document-storage]: documentdb-manage.md#IndexOverhead
[documentdb-manage-throughput]: documentdb-manage.md#ProvThroughput
[documentdb-import-data]: documentdb-import-data.md

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md#powershell
[hdinsight-develop-deploy-java-mapreduce]: ../hdinsight/hdinsight-develop-deploy-java-mapreduce.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight/hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-storage]: ../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight-use-mapreduce]: ../hdinsight/hdinsight-use-mapreduce.md
[hdinsight-use-pig]: ../hdinsight/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page1.png
[image-customprovision-page4]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page4.png
[image-customprovision-page5]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page5.png
[image-storageaccount-quickcreate]: ./media/documentdb-run-hadoop-with-hdinsight/storagequickcreate.png
[image-hive-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: ../powershell-install-configure.md
 

<!---HONumber=AcomDC_0204_2016-->