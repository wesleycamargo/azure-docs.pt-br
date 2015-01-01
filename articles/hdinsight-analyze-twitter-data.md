<properties urlDisplayName="Analyze Twitter data with HDInsight Hadoop" pageTitle="Analisar dados do Twitter com Hadoop no HDInsight | Azure" metaKeywords="" description="Learn how to use Hive to analyze Twitter data on Hadoop in HDInsight to find the usage frequency of a particular word." metaCanonical="" services="HDInsight" documentationCenter="" title="Analyze Twitter data with Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jgao" />

# Analisar dados do Twitter com Hadoop no HDInsight

Saiba como analisar dados do Twitter com o Hadoop usando o Hive no HDInsight.

Sites sociais são uma das forças principais para adoção de Big Data. APIs públicas fornecidas por sites, como o Twitter, são uma fonte útil de dados para analisar e compreender as tendências populares. Neste tutorial, você cai se conectar ao serviço Web do Twitter para obter alguns Tweets usando o API de streaming do Twitter e, em seguida, você usará o Hive para obter uma lista de usuários do Twitter que enviaram a maioria dos Tweets que continham uma determinada palavra.

**Tempo estimado para conclusão:**30 minutos

##Neste artigo

- [Pré-requisitos](#prerequisites)
- [Obter feed do Twitter](#feed)
- [Criar um script HiveQL](#script)
- [Processar os dados usando o Hive](#process)
- [Limpeza de tutorial](#cleanup)
- [Próximas etapas](#nextsteps)

##<a id="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

- **Uma estação de trabalho** com o PowerShell do Azure instalado e configurado.  Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install].Para executar scripts do PowerShell, você deve executar o PowerShell do Azure como administrador e configurar a política de execução como *RemoteSigned*.Consulte [Executar scripts do Windows PowerShell][powershell-script].

	Antes de executar scripts do PowerShell, verifique se você está conectado à sua assinatura do Azure usando o seguinte cmdlet:

		Add-AzureAccount

	Se você tiver várias assinaturas do Azure, use o seguinte cmdlet para definir a assinatura atual:

		Select-AzureSubscription <AzureSubscriptionName>



- **Um cluster Azure HDInsight**. Para obter informações sobre como provisionar um cluster, consulte [Introdução ao HDInsight][hdinsight-get-started] ou[Provisionar clusters HDInsight][hdinsight-provision].Você precisará dos seguintes dados para percorrer o tutorial:

	<table border="1">
	<tr><th>Propriedade do cluster</th><th>Nome de variável do PowerShell</th><th>Valor</th><th>Descrição</th></tr>
	<tr><td>Nome do cluster HDInsight</td><td>$clusterName</td><td></td><td>Este é o nome do cluster HDInsight.</td></tr>
	<tr><td>Nome da conta de armazenamento do Azure</td><td>$storageAccountName</td><td></td><td>Uma conta de armazenamento do Azure disponível para o cluster HDInsight. Para este tutorial, use a conta de armazenamento padrão especificada durante o processo de provisionamento do cluster.</td></tr>
	<tr><td>Nome do contêiner de blob do Azure</td><td>$containerName</td><td></td><td>Para este exemplo, utilize o contêiner de armazenamento de blob do Azure usado para o sistema de arquivos do cluster HDInsight padrão. Por padrão, o contêiner tem o mesmo nome do cluster HDInsight.</td></tr>
	</table>

**Compreender o armazenamento do HDInsight**

O HDInsight usa o armazenamento de Blob do Azure para armazenamento de dados.  Ele é chamado *WASB* ou *Armazenamento do Azure - Blob*. O WASB é a implementação do HDFS da Microsoft no Armazenamento de Blob do Azure. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage]. 

Quando você provisiona um cluster HDInsight, um contêiner de armazenamento de Blob é designado como o sistema de arquivos padrão, como no HDFS. Além desse contêiner, você pode adicionar mais contêineres da mesma conta de armazenamento do Azure ou de diferentes contas de armazenamento do Azure durante o processo de provisionamento. Para obter instruções sobre como adicionar mais contas de armazenamento, consulte [Provisionar clusters HDInsight][hdinsight-provision]. 

> [WACOM.NOTE] Para simplificar o script do PowerShell usado neste tutorial, todos os arquivos são armazenados no contêiner do sistema de arquivos padrão, localizado em */tutoriais/twitter*. Por padrão, esse contêiner tem o mesmo nome que o nome do cluster HDInsight. Se escolher usar um contêiner diferente para armazenar esses arquivos, atualize o script de acordo.

A sintaxe do WASB é:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Apenas a sintaxe *wasb://* tem suporte no cluster HDInsight versão 3.0. A sintaxe antiga *asv://* tem suporte nos clusters HDInsight 2.1 e 1.6, mas não tem suporte nos clusters HDInsight 3.0 e não terá suporte em versões posteriores.

> O caminho WASB é um caminho virtual.  Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage]. 

Para um arquivo armazenado no contêiner do sistema de arquivos padrão, ele pode ser acessado no HDInsight usando qualquer um dos seguintes URIs (use tweets.txt como um exemplo):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/twitter/tweets.txt
	wasb:///tutorials/twitter/tweets.txt
	/tutorials/twitter/tweets.txt

Se você desejar acessar o arquivo diretamente da conta de armazenamento, o nome do blob para o arquivo será:

	tutorials/twitter/tweets.txt

A tabela a seguir lista os arquivos usados neste tutorial:

<table border="1">
<tr><th>Arquivos</th><th>Descrição</th></tr>
<tr><td>/tutorials/twitter/data/tweets.txt</td><td>Os dados de origem para o trabalho do Hive.</td></tr>
<tr><td>/tutorials/twitter/output</td><td>A pasta de saída para o trabalho do Hive. O nome do arquivo de saída do trabalho do Hive do arquivo padrão é <strong>000000_0</strong>. </td></tr>
<tr><td>tutorials/twitter/twitter.hql</td><td>O arquivo de script HiveQL.</td></tr>
<tr><td>/tutorials/twitter/jobstatus</td><td>O status do trabalho do Hadoop.</td></tr>
</table>

##<a id="feed"></a>Obter feed do Twitter

Neste tutorial, você usará as [APIs de streaming do Twitter][twitter-streaming-api]. O API de streaming específico do Twitter que você usará é [statuses/filter][twitter-statuses-filter].

[Os dados dos tweets](https://dev.twitter.com/docs/platform-objects/tweets) são armazenados no formato JSon que contém uma estrutura aninhada complexa. Em vez de escrever várias linhas de código usando uma linguagem de programação convencional, você pode transformar essa estrutura aninhada em uma tabela do Hive para que possa ser consultada por uma linguagem semelhante ao SQL chamada HiveQL. 

O Twitter usa OAuth para oferecer acesso autorizado à sua API. OAuth é um protocolo de autenticação que permite ao usuários aprovar o aplicativo para atuar em seu nome sem compartilhar sua senha. Informações adicionais podem ser encontradas em [oauth.net](http://oauth.net/) ou no excelente [Beginner's Guide to OAuth](http://hueniverse.com/oauth/) do Hueniverse.

A primeira etapa para usar OAuth é criar um novo aplicativo no site do desenvolvedor do Twitter.

**Para criar um aplicativo do Twitter**

1. Entre em [https://apps.twitter.com/](https://apps.twitter.com/). Clique no link **Inscreva-se agora** se você não tiver uma conta do Twitter.
2. Clique em **Criar Novo Aplicativo**.
3. Digite o **Nome**, a **Descrição** e o **Site**. Você pode fazer uma URL para o campo Site.A tabela a seguir mostra alguns valores de exemplo para usar:

	<table border="1">
	<tr><th>Campo</th><th>Valor</th></tr>
	<tr><td>Nome</td><td>MyHDInsightApp</td></tr>
	<tr><td>Descrição</td><td>MyHDInsightApp</td></tr>
	<tr><td>Site</td><td>http://www.myhdinsightapp.com</td></tr>
	</table>
4. Marque **Sim, eu concordo** e clique em **Criar seu aplicativo do Twitter**.
5. Clique na guia **Permissões**.  A permissão padrão é **Somente leitura**.Isso é suficiente para este tutorial. 
6. Clique na guia **Chaves de API** tab.
7. Clique em **Criar meu token de acesso**.
8. Clique em **OAuth de teste** no canto superior direito da página.
9. Anote a **chave do consumidor, o ****segredo do consumidor, o ****token de acesso** e o **segredo do token de acesso**.Você precisará dos valores mais tarde no tutorial.

Neste tutorial, você usará o PowerShell para fazer uma chamada de serviço Web. A outra ferramenta popular para fazer chamadas de serviço Web é o [*Curl*][curl]. Você pode baixar o Curl [aqui][curl-download].

>[WACOM.NOTE] Quando usar o comando curl no Windows, use aspas duplas em vez de aspas simples para os valores de opção.

**Para obter tweets**

1. Abra o ISE do Windows PowerShell (na tela Iniciar do Windows 8, digite **PowerShell_ISE** e clique em **ISE do Windows PowerShell**. Consulte [Iniciar o Windows PowerShell no Windows 8 e no Windows][powershell-start]).

3. Copie o seguinte script no painel de script:

		Write-Host "Set variables ..." -ForegroundColor Green
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<BlobContainerName>"

		$destBlobName = "tutorials/twitter/data/tweets.txt"
		
		$trackString = "Azure, Cloud, HDInsight"
		$track = [System.Uri]::EscapeDataString($trackString);
		$lineMax = 100  #about 3 minutes every 100 lines
		
		$oauth_consumer_key = "<TwitterAppConsumerKey>";
		$oauth_consumer_secret = "<TwitterAppConsumerSecret>";
		$oauth_token = "<TwitterAppAccessToken>";
		$oauth_token_secret = "<TwitterAppAccessTokenSecret>";
		
		Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
		
		Write-Host "Create block blob object ..." -ForegroundColor Green
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
		
		Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
		
		Write-Host "Format oauth strings ..." -ForegroundColor Green
		$oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
		$ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
		$oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();
		
		$signature = "POST&";
		$signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
		$signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&"); 
		$signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
		$signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
		$signature += [System.Uri]::EscapeDataString("track=" + $track);
		
		$signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);
		
		$hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
		$hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
		$oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));
		
		$oauth_authorization = 'OAuth ';
		$oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
		$oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
		$oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
		$oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
		$oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
		$oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
		$oauth_authorization += 'oauth_version="1.0"';
		
		$post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track); 
				
		Write-Host "Create HTTP web request ..." -ForegroundColor Green
		[System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
		$request.Method = "POST";
		$request.Headers.Add("Authorization", $oauth_authorization);
		$request.ContentType = "application/x-www-form-urlencoded";
		$body = $request.GetRequestStream();
		
		$body.write($post_body, 0, $post_body.length);
		$body.flush();
		$body.close();
		$response = $request.GetResponse() ;
		
		Write-Host "Start stream reading ..." -ForegroundColor Green
		
		$sReader = New-Object System.IO.StreamReader($response.GetResponseStream())
		
		$inrec = $sReader.ReadLine()
		$count = 0
		while (($inrec -ne $null) -and ($count -le $lineMax))
		{
		    if ($inrec -ne "")
		    {
		        Write-Host $count.ToString() ", " -NoNewline -ForegroundColor Green
		        if ($count%10 -eq 0){
		            write-host " --- " -NoNewline
		            Get-Date -Format hh:mm:sstt
		        }
		
		        $writeStream.WriteLine($inrec)
		        $count ++
		    }
		
		    $inrec=$sReader.ReadLine()
		}
		
		Write-Host "Write to the destination blob ..." -ForegroundColor Green
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$destBlob.UploadFromStream($memStream) 
		
		$sReader.close()
		Write-Host "Complete!" -ForegroundColor Green

4. Defina as nove primeiras variáveis no script:	

	<table border="1">
	<tr><th>Variável</th><th>Descrição</th></tr>
	<tr><td>$storageAccountName</td><td>Conta de armazenamento do Azure usada para o sistema de arquivos padrão do cluster HDInsight. É a conta de armazenamento especificada na provisão. Consulte <a href="#prerequisites">Pré-requisitos</a>.</td></tr>
	<tr><td>$containerName</td><td>O contêiner de blob usado para o sistema de arquivos padrão do cluster HDInsight. É o contêiner especificado na provisão. Consulte <a href="#prerequisites">Pré-requisitos</a>.</td></tr>
	<tr><td>$destBlobName</td><td>É o nome de saída do blob.  O valore padrão é <strong>tutorials/twitter/data/tweets.txt</strong>. Se alterar o valor padrão, você precisará atualizar os scripts do PowerShell adequadamente.</td></tr>
	<tr><td>$trackString</td><td>O serviço Web retornará tweets relacionados a essas palavras-chave.  O valor padrão é <strong>Azure, Cloud, HDInsight</strong>. Se alterar o valor padrão, você precisará atualizar os scripts do PowerShell adequadamente.</td></tr>
	<tr><td>$lineMax</td><td>O valor determina quantos tweets o script lerá. Leva aproximadamente três minutos para ler 100 tweets. Você pode definir um número maior, mas levará mais tempo para baixar.</td></tr>
	<tr><td>$oauth_consumer_key</td><td>É <strong>a chave do consumidor</strong> do aplicativo do Twitter que você havia anotado ao criar o aplicativo do Twitter.</td></tr>
	<tr><td>$oauth_consumer_secret</td><td>É <strong>o segredo do consumidor</strong> que você havia anotado.</td></tr>
	<tr><td>$oauth_token</td><td>É <strong>o token de acesso</strong> que você havia anotado.</td></tr>
	<tr><td>$oauth_token_secret</td><td>É <strong>o segredo do token de acesso</strong> que você havia anotado.</td></tr>
	</table>

5. Pressione **F5** para executar o script. Se você tiver problemas, como alternativa, selecione todas as linhas e pressione **F8**.
6. Você deverá ver "Concluído!" no final da saída. Qualquer mensagem de erro será exibida em vermelho.

Como um procedimento de validação, você pode verificar o arquivo de saída, **/tutorials/twitter/data/tweets.txt**, em seu armazenamento de Blob do Azure usando um gerenciador de armazenamento do Azure ou o PowerShell do Azure.  Para um exemplo de script do PowerShell para listagem de arquivos, consulte [Usar o Armazenamento de Blob com o HDInsight][hdinsight-storage-powershell]. 



##<a id="script"></a>Criar um script HiveQL

Usando o PowerShell do Azure, você pode executar várias instruções HiveQL uma por vez ou empacotar a instrução HiveQL em um arquivo de script. Neste tutorial, você vai criar um script HiveQL. O arquivo de script deve ser carregado no WASB. Na próxima seção, você vai executar o arquivo de script usando o PowerShell do Azure.

O script HiveQL executará o seguinte:

1. **Removerá a tabela tweets_raw**, caso a tabela já exista.
2. **Criará a tabela tweets_raw do Hive**.  Esta tabela temporária estruturada do Hive mantém os dados para processamento de ETL adicional.Para obter informações sobre partição, consulte o [tutorial do Hive][apache-hive-tutorial].  
3. **Carregará os dados** da pasta de origem, /tutorials/twitter/data.O conjunto de dados grande de Tweets aninhados no formato JSon agora foi transformado em uma estrutura de tabela temporária do Hive.
3. **Removerá a tabela de tweets**, caso a tabela já exista;
4. **Criará a tabela de tweets**. Antes de consultar o conjunto de dados de Tweets usando o Hive, você precisa executar outro processo de ETL.  Esse processo de ETL define um esquema de tabela mais detalhado para os dados armazenados na tabela "twitter_raw".  
5. **Inserirá a tabela de substituição**.  Este script complexo do Hive iniciará um conjunto de longos trabalhos do Map Reduce pelo cluster do Hadoop.Dependendo do seu conjunto de dados e do tamanho do cluster, o processo pode demorar cerca de 10 minutos.
6. **Inserirá o diretório de substituição**. Execute uma consulta e passe o conjunto de dados para um arquivo.Essa consulta retornará uma lista de usuários do Twitter que enviou a maioria dos tweets que contêm a palavra "Azure".

**Para criar um script do Hive e carregá-lo no Azure**

1. Abra o ISE do Windows PowerShell.
2. Copie o seguinte script no painel de script:

		Write-Host "Define variables ..." -ForegroundColor Green
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<BlobContainerName>"
		
		$sourceDataPath = "/tutorials/twitter/data"
		$outputPath = "/tutorials/twitter/output"
		
		$hqlScriptFile = "tutorials/twitter/twitter.hql"
		
		$hqlStatements = @"
		set hive.exec.dynamic.partition = true;
		set hive.exec.dynamic.partition.mode = nonstrict;
		
		DROP TABLE tweets_raw;
		CREATE TABLE tweets_raw (
		    json_response STRING
		) 
		PARTITIONED BY (filesequence INT);
		
		LOAD DATA INPATH '$sourceDataPath'
		INTO TABLE tweets_raw
		PARTITION (filesequence = 1);
		
		DROP TABLE tweets;
		
		CREATE TABLE tweets
		(
			id BIGINT,
			created_at STRING,
			created_at_date STRING,
			created_at_year STRING,
			created_at_month STRING,
			created_at_day STRING,
			created_at_time STRING,
			in_reply_to_user_id_str STRING,
			text STRING,
			contributors STRING,
			retweeted STRING,
			truncated STRING,
			coordinates STRING,
			source STRING,
			retweet_count INT,
			url STRING,
			hashtags array<STRING>,
			user_mentions array<STRING>,
			first_hashtag STRING,
			first_user_mention STRING,
			screen_name STRING,
			name STRING,
			followers_count INT,
			listed_count INT,
			friends_count INT,
			lang STRING,
			user_location STRING,
			time_zone STRING,
			profile_image_url STRING,
			json_response STRING
		)
		PARTITIONED BY (filesequence INT);
		
		FROM tweets_raw
		INSERT OVERWRITE TABLE tweets
		PARTITION (filesequence)
		SELECT
		    cast(get_json_object(json_response, '$.id_str') as BIGINT),
		    get_json_object(json_response, '$.created_at'),
			concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
			substr (get_json_object(json_response, '$.created_at'),27,4)),
			substr (get_json_object(json_response, '$.created_at'),27,4),
			case substr (get_json_object(json_response,	'$.created_at'),5,3)
				when "Jan" then "01"
				when "Feb" then "02"
				when "Mar" then "03"
				when "Apr" then "04"
				when "May" then "05"
				when "Jun" then "06"
				when "Jul" then "07"
				when "Aug" then "08"
				when "Sep" then "09"
				when "Oct" then "10"
				when "Nov" then "11"
				when "Dec" then "12" end,
			substr (get_json_object(json_response, '$.created_at'),9,2),
			substr (get_json_object(json_response, '$.created_at'),12,8),
			get_json_object(json_response, '$.in_reply_to_user_id_str'),
			get_json_object(json_response, '$.text'),
			get_json_object(json_response, '$.contributors'),
			get_json_object(json_response, '$.retweeted'),
			get_json_object(json_response, '$.truncated'),
			get_json_object(json_response, '$.coordinates'),
			get_json_object(json_response, '$.source'),
			cast (get_json_object(json_response, '$.retweet_count') as INT),
			get_json_object(json_response, '$.entities.display_url'),
			array(	
				trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
			array(
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
			trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
			trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
			get_json_object(json_response, '$.user.screen_name'),
			get_json_object(json_response, '$.user.name'),
			cast (get_json_object(json_response, '$.user.followers_count') as INT),
			cast (get_json_object(json_response, '$.user.listed_count') as INT),
			cast (get_json_object(json_response, '$.user.friends_count') as INT),
			get_json_object(json_response, '$.user.lang'),
			get_json_object(json_response, '$.user.location'),
			get_json_object(json_response, '$.user.time_zone'),
			get_json_object(json_response, '$.user.profile_image_url'),
			json_response,
			filesequence
		WHERE (length(json_response) > 500);
		
		INSERT OVERWRITE DIRECTORY '$outputPath'
		SELECT name, screen_name, count(1) as cc 
			FROM tweets 
			WHERE text like "%Azure%" 
			GROUP BY name,screen_name 
			ORDER BY cc DESC LIMIT 10;
		"@
		
		Write-Host "Define the connection string ..." -ForegroundColor Green
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
		
		Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)
		
		Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
		$writeStream.Writeline($hqlStatements)
		
		Write-Host "Write to the destination blob ... " -ForegroundColor Green
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$hqlScriptBlob.UploadFromStream($memStream)
		
		Write-Host "Complete!" -ForegroundColor Green


4. Defina as duas primeiras variáveis no script:	

	<table border="1">
	<tr><th>Variável</th><th>Descrição</th></tr>
	<tr><td>$storageAccountName</td><td>Conta de armazenamento do Azure usada para o sistema de arquivos padrão do cluster HDInsight. É a conta de armazenamento especificada na provisão. Consulte <a href="#prerequisites">Pré-requisitos</a>.</td></tr>
	<tr><td>$containerName</td><td>O contêiner de blob usado para o sistema de arquivos padrão do cluster HDInsight. É o contêiner especificado na provisão. Consulte <a href="#prerequisites">Pré-requisitos</a>.</td></tr>
	<tr><td>$sourceDataPath</td><td>O local do WASB de onde as consultas do Hive farão a leitura. Não é necessário alterar essa variável.</td></tr>
	<tr><td>$outputPath</td><td>O local do WASB para onde as consultas do Hive passarão os resultados. Não é necessário alterar essa variável.</td></tr>
	<tr><td>$hqlScriptFile</td><td>O local e o nome de arquivo do arquivo de script HiveQL.</td></tr>
	</table>

5. Pressione **F5** para executar o script. Se você tiver algum problema, como alternativa, selecione todas as linhas e pressione **F8**.
6. Você deverá ver "Concluído!" no final da saída. Qualquer mensagem de erro será exibida em vermelho.

Como um procedimento de validação, você pode verificar o arquivo de saída, **/tutorials/twitter/twitter.hql**, em seu armazenamento de Blob do Azure usando um gerenciador de armazenamento do Azure ou o PowerShell do Azure.  Para um exemplo de script do PowerShell para listagem de arquivos, consulte [Usar o Armazenamento de Blob com o HDInsight][hdinsight-storage-powershell].  


##<a name="process"></a> Processar os dados do Twitter usando o Hive

Você concluiu todo o trabalho de preparação. Agora, você pode chamar o script do Hive e verificar os resultados.

### Enviar um trabalho do Hive

Use o seguinte script do PowerShell para executar o script do Hive. Você precisará definir a primeira variável.

	Write-Host "Set the variables ... " -ForegroundColor Green
	$clusterName = "<HDInsightClusterName>"
	
	$hqlScriptFile = "/tutorials/twitter/twitter.hql"
	$statusFolder = "/tutorials/twitter/jobstatus"
	
	Write-Host "Invoke Hive ... " -ForegroundColor Green
	Use-AzureHDInsightCluster $clusterName
	$response = Invoke-Hive -file $hqlScriptFile -StatusFolder $statusFolder -OutVariable $outVariable
	
	Write-Host "Display the standard error log ... " -ForegroundColor Green
	$jobID = ($response | Select-String job_ | Select-Object -First 1) -replace '\s*$' -replace '.*\s'
	Get-AzureHDInsightJobOutput -cluster $clusterName -JobId $jobID -StandardError 

### Verificar os resultados

Use o seguinte script do PowerShell para verificar a saída de trabalho do Hive. Você precisará definir as duas primeiras variáveis.

	Write-Host "Set the variables ... " -ForegroundColor Green
	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	Write-Host "Download the blob ..." -ForegroundColor Green
	Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
	
	Write-Host "Display the output ..." -ForegroundColor Green
	cat "./$blob"

> [WACOM.NOTE] A tabela Hive usa \001 como o delimitador de campo. O delimitador não é visível na saída. 

Depois que os resultados da análise foram colocados no WASB, é possível exportar os dados para o Banco de Dados SQL/SQL Server do Azure, exportar os dados para o Excel usando o Power Query ou conectar seu aplicativo aos dados usando o driver ODBC do Hive.  Para obter mais informações, consulte [Usar o Sqoop com o HDInsight][hdinsight-use-sqoop], [Analisar dados de atraso de voo usando o HDInsight][hdinsight-analyze-flight-delay-data], [Conectar o Excel ao HDInsight com o Power Query][hdinsight-power-query] e [Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive][hdinsight-hive-odbc].
   

##<a id="cleanup"></a>Limpeza de tutorial

Caso você queira executar novamente o tutorial, você precisará:

- **Recriar o arquivo de dados de tweets**.  O arquivo de dados de origem do Tweets foi removido pelo trabalho do Hive.  Você precisará gerar um novo arquivo de dados.O nome do arquivo é **tutorials/twitter/data/tweets.txt**. Consulte [Obter feed do Twitter](#feed).

##<a id="nextsteps"></a>Próximas etapas

Neste tutorial vimos como transformar o conjunto de dados não estruturado Json em tabela estruturada do Hive para consultar, explorar e analisar dados do Twitter usando o HDInsight no Azure. Para obter mais informações, consulte:

- [Introdução ao HDInsight][hdinsight-get-started]
- [Analisar dados de atraso de voo usando o HDInsight][hdinsight-analyze-flight-delay-data]
- [Conectar o Excel ao HDInsight com o Power Query][hdinsight-power-query]
- [Conectar o Excel ao HDInsight com o driver ODBC do ][hdinsight-hive-odbc]Microsoft Hive
- [Usar o Sqoop com o HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/pt-br/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell
[powershell-script]: http://technet.microsoft.com/pt-br/library/ee176961.aspx

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/


<!--HONumber=35_1-->
