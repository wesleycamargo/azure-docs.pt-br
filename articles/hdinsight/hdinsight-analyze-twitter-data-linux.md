<properties
	pageTitle="Analisar dados do Twitter com o Apache Hive no HDInsight | Microsoft Azure"
	description="Aprenda a usar o Python para armazenar Tweets que contêm palavras-chave específicas. Em seguida, use o Hive e Hadoop no HDInsight para transformar os dados brutos do TWitter em uma tabela Hive pesquisável."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="larryfr"/>

# Analisar dados do Twitter usando o Hive no HDInsight

Neste documento, você obterá tweets usando uma API de streaming do Twitter e então usará o Apache Hive em um cluster HDInsight baseado em Linux para processar os dados do formato JSON. O resultado será uma lista de usuários do Twitter que enviaram a maioria dos tweets com uma determinada palavra.

> [AZURE.NOTE] Embora diversas partes deste documento possam ser usadas com clusters HDInsight baseados no Windows (Python, por exemplo), muitas etapas deste documento são específicas de clusters HDInsight baseados em Linux. Para obter as etapas específicas para um cluster baseado no Windows, confira [Analisar dados do Twitter usando o Hive no HDInsight](hdinsight-analyze-twitter-data.md).

###Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- Um __cluster Azure HDInsight baseado em Linux__. Para obter informações sobre como criar um cluster, confira [Introdução ao HDInsight baseado em Linux](hdinsight-hadoop-linux-tutorial-get-started.md) para obter as etapas de criação de um cluster.

- Um __cliente SSH__. Para obter mais informações sobre como usar SSH com o HDInsight baseado em Linux, confira os seguintes artigos:

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

- __Python__ e [pip](https://pypi.python.org/pypi/pip)

##Obtenha um feed do Twitter

O Twitter permite que você recupere os [dados de cada tweet](https://dev.twitter.com/docs/platform-objects/tweets) como um documento JSON (JavaScript Object Notation) por meio de uma API REST. O [OAuth](http://oauth.net) é necessário para autenticação na API. Você também deve criar um _Aplicativo do Twitter_ com as configurações usadas para acessar a API.

###Criar um aplicativo do Twitter

1. Em um navegador da Web, entre em [https://apps.twitter.com/](https://apps.twitter.com/). Clique no link **Inscreva-se agora** se você não tem uma conta do Twitter.
2. Clique em **Criar Novo Aplicativo**.
3. Digite o **Nome**, a **Descrição** e o **Site**. Você pode fazer uma URL para o campo **Site**. A tabela a seguir mostra alguns valores de exemplo para usar:

	| Campo | Valor |
	|:----- |:----- |
	| Nome | MyHDInsightApp |
	| Descrição | MyHDInsightApp |
	| Site | http://www.myhdinsightapp.com |
	
4. Marque **Sim, eu concordo** e, em seguida, clique em **Criar seu aplicativo do Twitter**.
5. Clique na guia **Permissões**. A permissão padrão é **Somente leitura**. Isso é suficiente para este tutorial.
6. Clique na guia **Chaves e Tokens de acesso**.
7. Clique em **Criar meu token de acesso**.
8. Clique em **OAuth de teste** no canto superior direito da página.
9. Anote a **Chave do consumidor**, o **Segredo do consumidor**, o **Token de acesso** e o **Segredo do token de acesso**. Você precisará dos valores mais tarde.

>[AZURE.NOTE] Quando usar o comando curl no Windows, use aspas duplas, em vez de aspas simples, para os valores de opção.

###Baixar tweets

O código Python a seguir baixará 10.000 tweets do Twitter e os salvará em um arquivo chamado __tweets.txt__.

> [AZURE.NOTE] As etapas a seguir são executadas no cluster HDInsight, já que o Python já está instalado.

1. Conecte-se ao cluster HDInsight usando SSH:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	Caso você tenha usado uma senha para proteger sua conta de usuário SSH, ela será solicitada. Se você tiver usado uma chave pública, talvez precise usar o parâmetro `-i` para especificar a chave privada correspondente. Por exemplo: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
		
	Para obter mais informações sobre como usar SSH com o HDInsight baseado em Linux, confira os seguintes artigos:
	
	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows)
	
2. Por padrão, o utilitário __pip__ não está instalado no nó principal do HDInsight. Use o seguinte para instalar e atualizar esse utilitário:

		sudo apt-get install python-pip
		sudo pip install --upgrade pip

3. O código para baixar tweets depende do [Tweepy](http://www.tweepy.org/) e do [Progressbar](https://pypi.python.org/pypi/progressbar/2.2). Para instalá-los, use este comando:

		sudo apt-get install python-dev libffi-dev libssl-dev
		sudo apt-get remove python-openssl
		sudo pip install tweepy progressbar pyOpenSSL requests[security]
		
	> [AZURE.NOTE] O segredo da remoção do python-openssl, da instalação do python-dev, libffi-dev, do libssl-dev, do pyOpenSSL e de requests[security] é evitar um aviso do InsecurePlatform ao se conectar ao Twitter via SSL desde o Python.
	>
	> O Tweepy v3.2.0 é usado para evitar o [um erro](https://github.com/tweepy/tweepy/issues/576) que pode ocorrer durante o processamento de tweets.

4. Use o comando a seguir para criar um novo arquivo chamado __gettweets.py__:

		nano gettweets.py

5. Use o seguinte como o conteúdo do arquivo __gettweets.py__. Substitua as informações de espaço reservado de __consumer/_secret__, __consumer/_key_\_, __access/_token__ e __access/_token/_secret__ pelas informações do seu aplicativo do Twitter.

        #!/usr/bin/python

        from tweepy import Stream, OAuthHandler
        from tweepy.streaming import StreamListener
        from progressbar import ProgressBar, Percentage, Bar
        import json
        import sys

        #Twitter app information
        consumer_secret='Your consumer secret'
        consumer_key='Your consumer key'
        access_token='Your access token'
        access_token_secret='Your access token secret'

        #The number of tweets we want to get
        max_tweets=10000

        #Create the listener class that will receive and save tweets
        class listener(StreamListener):
            #On init, set the counter to zero and create a progress bar
            def __init__(self, api=None):
                self.num_tweets = 0
                self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

            #When data is received, do this
            def on_data(self, data):
                #Append the tweet to the 'tweets.txt' file
                with open('tweets.txt', 'a') as tweet_file:
                    tweet_file.write(data)
                    #Increment the number of tweets
                    self.num_tweets += 1
                    #Check to see if we have hit max_tweets and exit if so
                    if self.num_tweets >= max_tweets:
                        self.pbar.finish()
                        sys.exit(0)
                    else:
                        #increment the progress bar
                        self.pbar.update(self.num_tweets)
                return True

            #Handle any errors that may occur
            def on_error(self, status):
                print status

        #Get the OAuth token
        auth = OAuthHandler(consumer_key, consumer_secret)
        auth.set_access_token(access_token, access_token_secret)
        #Use the listener class for stream processing
        twitterStream = Stream(auth, listener())
        #Filter for these topics
        twitterStream.filter(track=["azure","cloud","hdinsight"])

6. Use __Ctrl + X__ e __Y__ para salvar o arquivo.

7. Use o comando a seguir para executar o arquivo e baixar os tweets:

		python gettweets.py

	Um indicador de progresso deve aparecer e contar até 100% enquanto os tweets são baixados e salvos no arquivo.

    > [AZURE.NOTE] Se estiver demorando muito tempo para a barra de progresso avançar, altere o filtro para controlar os tópicos mais populares; quando há muitos tweets sobre o tópico que você está filtrando, você pode obter rapidamente os 10000 tweets necessário.

###Carregar os dados

Para carregar os dados no WASB (o sistema de arquivos distribuídos usado pelo HDInsight), use os seguintes comandos:

	hdfs dfs -mkdir -p /tutorials/twitter/data
	hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt

Isso armazenará os dados em um local que todos os nós do cluster podem acessar.

##Executar o trabalho HiveQL


1. Use o comando a seguir para criar um arquivo com instruções HiveQL:

		nano twitter.hql
	
	Use o seguinte como conteúdo do arquivo:

        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        -- Drop table, if it exists
        DROP TABLE tweets_raw;
        -- Create it, pointing toward the tweets logged from Twitter
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
        -- Drop and recreate the destination table
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
        );
        -- Select tweets from the imported data, parse the JSON,
        -- and insert into the tweets table
        FROM tweets_raw
        INSERT OVERWRITE TABLE tweets
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
            json_response
        WHERE (length(json_response) > 500);
		
		
3. Pressione __Ctrl + X__ e pressione __Y__ para salvar o arquivo.

4. Use o comando a seguir para executar o HiveQL contido no arquivo:

		beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i twitter.hql		
		
	Isso carregará o shell do Hive, executará o HiveQL no arquivo __twitter.hql__ e, por fim, retornará um prompt do `jdbc:hive2//localhost:10001/>`.
	
5. Do prompt do Beeline, use o seguinte para verificar se você pode selecionar dados da tabela de __tweets__ criada pelo HiveQL no arquivo __twitter.hql__:
		
		SELECT name, screen_name, count(1) as cc
			FROM tweets
			WHERE text like "%Azure%"
			GROUP BY name,screen_name
			ORDER BY cc DESC LIMIT 10;

	Isso retornará no máximo 10 tweets com a palavra __Azure__ no texto da mensagem.

##Próximas etapas

Neste tutorial vimos como transformar o conjunto de dados não estruturado JSON em tabela estruturada do Hive para consultar, explorar e analisar dados do Twitter usando o HDInsight no Azure. Para obter mais informações, consulte:

- [Introdução ao HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Analisar dados de atraso de voo usando o HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

<!---HONumber=AcomDC_0713_2016-->