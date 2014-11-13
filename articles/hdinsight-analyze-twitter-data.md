<properties urlDisplayName="Analyze Twitter data with HDInsight Hadoop" pageTitle="Analisar dados do Twitter com Hadoop no HDInsight | Azure" metaKeywords="" description="Saiba como usar o Hive para analisar dados do Twitter com Hadoop no HDInsight para encontrar a frequ&ecirc;ncia de uso de uma determinada palavra." metaCanonical="" services="HDInsight" documentationCenter="" title="Analisar dados do Twitter com Hadoop no HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Analisar dados do Twitter com Hadoop no HDInsight

Saiba como analisar dados do Twitter com o Hadoop usando o Hive no HDInsight.

Sites sociais são uma das forças principais para adoção de Big Data. APIs públicas fornecidas por sites, como o Twitter, são uma fonte útil de dados para analisar e compreender as tendências populares. Neste tutorial, você irá se conectar ao serviço da Web do Twitter para obter alguns Tweets usando o API de streaming do Twitter e, em seguida, você usará o Hive para obter uma lista de usuários do Twitter que enviaram a maioria dos Tweets que continham uma determinada palavra.

**Tempo estimado para conclusão:** 30 minutos

## Neste artigo

-   [Pré-requisitos][Pré-requisitos]
-   [Obter feed do Twitter][Obter feed do Twitter]
-   [Criar um script HiveQL][Criar um script HiveQL]
-   [Processar os dados usando o Hive][Processar os dados usando o Hive]
-   [Limpeza de tutorial][Limpeza de tutorial]
-   [Próximas etapas][Próximas etapas]

## <span id="prerequisites"></span></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

-   **Uma estação de trabalho** com o PowerShell do Azure instalado e configurado. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][Instalar e configurar o PowerShell do Azure]. Para executar scripts do PowerShell, você deve executar o PowerShell do Azure como administrador e configurar a política de execução como *RemoteSigned*. Consulte [Executar scripts do Windows PowerShell][Executar scripts do Windows PowerShell].

    Antes de executar scripts do PowerShell, verifique se você está conectado à sua assinatura do Azure usando o seguinte cmdlet:

        Add-AzureAccount

    Se você tiver várias assinaturas do Azure, use o seguinte cmdlet para definir a assinatura atual:

        Select-AzureSubscription <AzureSubscriptionName>

-   **Um cluster Azure HDInsight**. Para obter informações sobre como provisionar um cluster, consulte [Introdução ao HDInsight][Introdução ao HDInsight] ou [Provisionar clusters HDInsight][Provisionar clusters HDInsight]. Você precisará dos seguintes dados para percorrer o tutorial:

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Propriedade do cluster</th>
    <th align="left">Nome de variável do PowerShell</th>
    <th align="left">Valor</th>
    <th align="left">Descrição</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Nome do cluster HDInsight</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">Este é o nome do cluster HDInsight.</td>
    </tr>
    <tr class="even">
    <td align="left">Nome da conta de armazenamento do Azure</td>
    <td align="left">$storageAccountName</td>
    <td align="left"></td>
    <td align="left">Uma conta de armazenamento do Azure disponível para o cluster HDInsight. Para este tutorial, use a conta de armazenamento padrão especificada durante o processo de provisionamento do cluster.</td>
    </tr>
    <tr class="odd">
    <td align="left">Nome do contêiner de blob do Azure</td>
    <td align="left">$containerName</td>
    <td align="left"></td>
    <td align="left">Para este exemplo, utilize o contêiner de armazenamento de blob do Azure usado para o sistema de arquivos do cluster HDInsight padrão. Por padrão, o contêiner tem o mesmo nome do cluster HDInsight.</td>
    </tr>
    </tbody>
    </table>

**Compreender o armazenamento do HDInsight**

O HDInsight usa o armazenamento de Blob do Azure para armazenamento de dados. Ele é chamado *WASB* ou *Armazenamento do Azure - Blob*. O WASB é a implementação do HDFS da Microsoft no Armazenamento de Blob do Azure. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][Usar o Armazenamento de Blob do Azure com o HDInsight].

Quando você provisiona um cluster HDInsight, um contêiner de armazenamento de Blob é designado como o sistema de arquivos padrão, como no HDFS. Além desse contêiner, você pode adicionar mais contêineres da mesma conta de armazenamento do Azure ou de diferentes contas de armazenamento do Azure durante o processo de provisionamento. Para obter instruções sobre como adicionar mais contas de armazenamento, consulte [Provisionar clusters HDInsight][Provisionar clusters HDInsight].

> [WACOM.NOTE] Para simplificar o script do PowerShell usado neste tutorial, todos os arquivos são armazenados no contêiner do sistema de arquivos padrão, localizado em */tutoriais/twitter*. Por padrão, esse contêiner tem o mesmo nome que o nome do cluster HDInsight. Se escolher usar um contêiner diferente para armazenar esses arquivos, atualize o script de acordo.

A sintaxe do WASB é:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Apenas a sintaxe *wasb://* tem suporte no cluster HDInsight versão 3.0. A sintaxe antiga *asv://* tem suporte nos clusters HDInsight 2.1 e 1.6, mas não tem suporte nos clusters HDInsight 3.0 e não terá suporte em versões posteriores.

> O caminho WASB é um caminho virtual. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][Usar o Armazenamento de Blob do Azure com o HDInsight].

Para um arquivo armazenado no contêiner do sistema de arquivos padrão, ele pode ser acessado no HDInsight usando qualquer um dos seguintes URIs (use tweets.txt como um exemplo):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/twitter/tweets.txt
    wasb:///tutorials/twitter/tweets.txt
    /tutorials/twitter/tweets.txt

Se você desejar acessar o arquivo diretamente da conta de armazenamento, o nome do blob para o arquivo será:

    tutorials/twitter/tweets.txt

A tabela a seguir lista os arquivos usados neste tutorial:

| Arquivos                           | Descrição                                                                                                                   |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|
| /tutorials/twitter/data/tweets.txt | Os dados de origem para o trabalho do Hive.                                                                                 |
| /tutorials/twitter/output          | A pasta de saída para o trabalho do Hive. O nome do arquivo de saída do trabalho do Hive do arquivo padrão é **000000\_0**. |
| tutorials/twitter/twitter.hql      | O arquivo de script HiveQL.                                                                                                 |
| /tutorials/twitter/jobstatus       | O status do trabalho do Hadoop.                                                                                             |

## <span id="feed"></span></a>Obter feed do Twitter

Neste tutorial, você usará as [APIs de streaming do Twitter][APIs de streaming do Twitter]. O API de streaming específico do Twitter que você usará é [statuses/filter][statuses/filter].

Os [dados dos TWEETS][dados dos TWEETS] são armazenados no formato JSon que contém uma estrutura aninhada complexa. Em vez de escrever várias linhas de código usando uma linguagem de programação convencional, você pode transformar essa estrutura aninhada em uma tabela do Hive para que possa ser consultada por uma linguagem semelhante ao SQL chamada HiveQL.

O Twitter usa OAuth para oferecer acesso autorizado à sua API. OAuth é um protocolo de autenticação que permite ao usuários aprovar o aplicativo para atuar em seu nome sem compartilhar sua senha. Mais informações podem ser encontradas em [oauth.net][oauth.net] ou no excelente [Guia para iniciantes do OAuth][Guia para iniciantes do OAuth] no Hueniverse.

A primeira etapa para usar OAuth é criar um novo aplicativo no site do desenvolvedor do Twitter.

**Para criar um aplicativo do Twitter**

1.  Entre no <https://apps.twitter.com/>.Clique no link **Inscreva-se agora mesmo** se você não tiver uma conta do Twitter.
2.  Clique em **Criar Novo Aplicativo**.
3.  Digite o **Nome**, a **Descrição** e o **Site**. Você pode fazer uma URL para o campo Site. A tabela a seguir mostra alguns valores de exemplo para usar:

    | Campo     | Valor                         |
    |-----------|-------------------------------|
    | Nome      | MyHDInsightApp                |
    | Descrição | MyHDInsightApp                |
    | Site      | http://www.myhdinsightapp.com |

4.  Marque **Sim, eu concordo** e, em seguida, clique em **Criar seu aplicativo do Twitter**.
5.  Clique na guia **Permissões**. A permissão padrão é **Somente leitura**. Isso é suficiente para este tutorial.
6.  Clique na guia **Chaves de API**.
7.  Clique em **Criar meu token de acesso**.
8.  Clique em **OAuth de teste** no canto superior direito da página.
9.  Anote a **Chave do consumidor**, o **Segredo do consumidor**, o **Token de acesso** e o **Segredo do token de acesso**. Você precisará dos valores mais tarde no tutorial.

Neste tutorial, você usará o PowerShell para fazer uma chamada de serviço da Web. A outra ferramenta popular para fazer chamadas de serviço da Web é [*Curl*][*Curl*]. O Curl pode ser baixado [aqui][aqui].

> [WACOM.NOTE] Quando usar o comando curl no Windows, use aspas duplas em vez de aspas simples para os valores de opção.

**Para obter Tweets**

1.  Abra o ISE do Windows PowerShell (na tela Iniciar do Windows 8, digite **PowerShell\_ISE** e clique em **ISE do Windows PowerShell**. Consulte [Iniciar o Windows PowerShell no Windows 8 e no Windows][Iniciar o Windows PowerShell no Windows 8 e no Windows]).

2.  Copie o seguinte script no painel de script:

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

3.  Defina as nove primeiras variáveis no script:

    | Variável                 | Descrição                                                                                                                                                                                                        |
    |--------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | $storageAccountName      | Conta de armazenamento do Azure usada para o sistema de arquivos padrão do cluster HDInsight. É a conta de armazenamento especificada na provisão. Consulte [Pré-requisitos][Pré-requisitos].                                  |
    | $containerName           | O contêiner de blob usado para o sistema de arquivos padrão do cluster HDInsight. É o contêiner especificado na provisão. Consulte [Pré-requisitos][Pré-requisitos].                                                           |
    | $destBlobName            | É o nome de saída do blob. O valor padrão é **tutorials/twitter/data/tweets.txt**. Se alterar o valor padrão, você precisará atualizar os scripts do PowerShell adequadamente.                                   |
    | $trackString             | O serviço da Web retornará tweets relacionados a essas palavras-chave. O valor padrão é **Azure, Nuvem, HDInsight**. Se alterar o valor padrão, você precisará atualizar os scripts do PowerShell adequadamente. |
    | $lineMax                 | O valor determina quantos tweets o script lerá. Leva aproximadamente três minutos para ler 100 tweets. Você pode definir um número maior, mas levará mais tempo para fazer o download.                           |
    | $oauth\_consumer\_key    | Esta é a **chave de consumidor** do aplicativo do Twitter que você anotou anteriormente ao criar esse aplicativo.                                                                                                |
    | $oauth\_consumer\_secret | Este é o **segredo de consumidor** do aplicativo do Twitter que você anotou anteriormente.                                                                                                                       |
    | $oauth\_token            | Este é o **token de acesso** do aplicativo do Twitter que você anotou anteriormente.                                                                                                                             |
    | $oauth\_token\_secret    | Este é o **segredo de token de acesso** do aplicativo do Twitter que você anotou anteriormente.                                                                                                                  |

4.  Pressione **F5** para executar o script. Se você tiver problemas, como alternativa, selecione todas as linhas e então pressione **F8**.
5.  Você deverá ver "Concluído!" no final da saída. Qualquer mensagem de erro será exibida em vermelho.

Como um procedimento de validação, você pode verificar o arquivo de saída, **/tutorials/twitter/data/tweets.txt**, em seu armazenamento de blob do Azure usando um gerenciador de armazenamento do Azure ou o PowerShell do Azure. Para um exemplo de script do PowerShell para listagem de arquivos, consulte [Usar o Armazenamento de Blob com o HDInsight][Usar o Armazenamento de Blob com o HDInsight].

## <span id="script"></span></a>Criar um script HiveQL

Usando o PowerShell do Azure, você pode executar várias instruções HiveQL uma por vez ou empacotar a instrução HiveQL em um arquivo de script. Neste tutorial, você irá criar um script HiveQL. O arquivo de script deve ser carregado no WASB. Na próxima seção, você irá executar o arquivo de script usando o PowerShell do Azure.

O script HiveQL executará o seguinte:

1.  **Remova a tabela tweets\_raw**, caso a tabela já exista.
2.  **Crie a tabela tweets\_raw do Hive**. Esta tabela temporária estruturada do Hive mantém os dados para processamento de ETL adicional. Para obter informações sobre partição, consulte o [tutorial do Hive][tutorial do Hive].
3.  **Carregue os dados** da pasta de origem, /tutorials/twitter/data. O conjunto de dados grande de Tweets aninhados no formato JSon agora foi transformado em uma estrutura de tabela temporária do Hive.
4.  **Remova a tabela de tweets**, caso a tabela já exista;
5.  **Crie a tabela de tweets**. Antes de consultar o conjunto de dados de Tweets usando o Hive, você precisa executar outro processo de ETL. Esse processo de ETL define um esquema de tabela mais detalhado para os dados armazenados na tabela "twitter\_raw".
6.  **Insira tabela de substituição**. Este script complexo do Hive iniciará um conjunto de longos trabalhos do Map Reduce pelo cluster do Hadoop. Dependendo do seu conjunto de dados e do tamanho do cluster, o processo pode demorar cerca de 10 minutos.
7.  **Insira diretório de substituição**. Execute uma consulta e passe o conjunto de dados para um arquivo. Essa consulta retornará uma lista de usuários do Twitter que enviou a maioria dos tweets que contêm a palavra "Azure".

**Para criar um script do Hive e carregá-lo para o Azure**

1.  Abra o ISE do Windows PowerShell.
2.  Copie o seguinte script no painel de script:

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
            case substr (get_json_object(json_response, '$.created_at'),5,3)
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

3.  Defina as duas primeiras variáveis no script:

    | Variável            | Descrição                                                                                                                                                                       |
    |---------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | $storageAccountName | Conta de armazenamento do Azure usada para o sistema de arquivos padrão do cluster HDInsight. É a conta de armazenamento especificada na provisão. Consulte [Pré-requisitos][Pré-requisitos]. |
    | $containerName      | O contêiner de blob usado para o sistema de arquivos padrão do cluster HDInsight. É o contêiner especificado na provisão. Consulte [Pré-requisitos][Pré-requisitos].                          |
    | $sourceDataPath     | O local do WASB de onde as consultas do Hive farão a leitura. Não é necessário alterar essa variável.                                                                           |
    | $outputPath         | O local do WASB para onde as consultas do Hive passarão os resultados. Não é necessário alterar essa variável.                                                                  |
    | $hqlScriptFile      | O local e o nome de arquivo do arquivo de script HiveQL.                                                                                                                        |

4.  Pressione **F5** para executar o script. Se você tiver problemas, como alternativa, selecione todas as linhas e então pressione **F8**.
5.  Você deverá ver "Concluído!" no final da saída. Qualquer mensagem de erro será exibida em vermelho.

Como procedimento de validação, você pode verificar o arquivo de saída, **/tutorials/twitter/twitter.hql**, em seu armazenamento de blob do Azure usando um gerenciador de armazenamento do Azure ou o PowerShell do Azure. Para um exemplo de script do PowerShell para listagem de arquivos, consulte [Usar o Armazenamento de Blob com o HDInsight][Usar o Armazenamento de Blob com o HDInsight].

## <a name="process"></a> Processar os dados do Twitter usando o Hive

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
    $jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
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

> [WACOM.NOTE] A tabela do Hive usa \\001 como o delimitador de campo. O delimitador não é visível na saída.

Depois que os resultados da análise foram colocados no WASB, é possível exportar os dados para o Banco de Dados do SQL/SQL Server do Azure, exportar os dados para o Excel usando o Power Query ou conectar seu aplicativo aos dados usando o driver ODBC do Hive. Para obter mais informações, consulte [Usar Sqoop com HDInsight][Usar Sqoop com HDInsight] ,[Analisar os dados de atraso de voo usando o HDInsight][Analisar os dados de atraso de voo usando o HDInsight], [Conectar o Excel ao HDInsight com o Power Query][Conectar o Excel ao HDInsight com o Power Query] e [Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive][Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive].

## <span id="cleanup"></span></a>Limpeza de tutorial

Caso você queira executar novamente o tutorial, você precisará:

-   **Recriar o arquivo de dados de Tweets**. O arquivo de dados de origem do Tweets foi removido pelo trabalho do Hive. Você precisará gerar um novo arquivo de dados. O nome do arquivo é **tutorials/twitter/data/tweets.txt**. Consulte [Obter feed do Twitter][Obter feed do Twitter].

## <span id="nextsteps"></span></a>Próximas etapas

Neste tutorial vimos como transformar o conjunto de dados não estruturado Json em tabela estruturada do Hive para consultar, explorar e analisar dados do Twitter usando o HDInsight no Azure. Para obter mais informações, consulte:

-   [Introdução ao HDInsight][Introdução ao HDInsight]
-   [Analisar dados de atraso de voo usando o HDInsight][Analisar os dados de atraso de voo usando o HDInsight]
-   [Conectar o Excel ao HDInsight com o Power Query][Conectar o Excel ao HDInsight com o Power Query]
-   [Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive][Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive]
-   [Use o Sqoop com o HDInsight][Usar Sqoop com HDInsight]

  [Pré-requisitos]: #prerequisites
  [Obter feed do Twitter]: #feed
  [Criar um script HiveQL]: #script
  [Processar os dados usando o Hive]: #process
  [Limpeza de tutorial]: #cleanup
  [Próximas etapas]: #nextsteps
  [Instalar e configurar o PowerShell do Azure]: ../install-configure-powershell
  [Executar scripts do Windows PowerShell]: http://technet.microsoft.com/pt-br/library/ee176961.aspx
  [Introdução ao HDInsight]: ../hdinsight-get-started/
  [Provisionar clusters HDInsight]: ../hdinsight-provision-clusters/
  [Usar o Armazenamento de Blob do Azure com o HDInsight]: ../hdinsight-use-blob-storage/
  [APIs de streaming do Twitter]: https://dev.twitter.com/docs/streaming-apis
  [statuses/filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
  [dados dos TWEETS]: https://dev.twitter.com/docs/platform-objects/tweets
  [oauth.net]: http://oauth.net/
  [Guia para iniciantes do OAuth]: http://hueniverse.com/oauth/
  [*Curl*]: http://curl.haxx.se
  [aqui]: http://curl.haxx.se/download.html
  [Iniciar o Windows PowerShell no Windows 8 e no Windows]: http://technet.microsoft.com/pt-br/library/hh847889.aspx
  [Usar o Armazenamento de Blob com o HDInsight]: ../hdinsight-use-blob-storage/#powershell
  [tutorial do Hive]: https://cwiki.apache.org/confluence/display/Hive/Tutorial
  [Usar Sqoop com HDInsight]: ../hdinsight-use-sqoop/
  [Analisar os dados de atraso de voo usando o HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Conectar o Excel ao HDInsight com o Power Query]: ../hdinsight-connect-excel-power-query/
  [Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive]: ../hdinsight-connect-excel-hive-ODBC-driver/
