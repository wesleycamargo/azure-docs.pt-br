---
title: Analisar dados do Twitter com o Apache Hive no HDInsight | Microsoft Docs
description: "Aprenda a usar o Python para armazenar Tweets que contêm palavras-chave específicas. Em seguida, use o Hive e Hadoop no HDInsight para transformar os dados brutos do TWitter em uma tabela Hive pesquisável."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e1e249ed-5f57-40d6-b3bc-a1b4d9a871d3
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: d391c5c6289aa63e969f63f189eb5db680883f0a
ms.openlocfilehash: db0f94bdeefac577765586f6b07ba13f9cfd2867
ms.lasthandoff: 03/01/2017

---
# <a name="analyze-twitter-data-using-hive-on-linux-based-hdinsight"></a>Analisar dados do Twitter usando o Hive no HDInsight baseado em Linux

Saiba como usar o Apache Hive em um cluster HDInsight para processar os dados do Twitter. O resultado será uma lista de usuários do Twitter que enviaram a maioria dos tweets que contêm uma determinada palavra.

> [!IMPORTANT]
> As etapas deste documento foram testadas em cluster HDInsight baseado em Linux.
>
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, veja [Substituição do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Pré-requisitos

* Um **cluster Azure HDInsight baseado em Linux**. Para obter informações sobre como criar um cluster, confira [Introdução ao HDInsight baseado em Linux](hdinsight-hadoop-linux-tutorial-get-started.md) para obter as etapas de criação de um cluster.
* Um **cliente SSH**. Para obter mais informações sobre como usar SSH com o HDInsight baseado em Linux, confira os seguintes artigos:
  
  * [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
  * [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
* **Python** e [pip](https://pypi.python.org/pypi/pip)

## <a name="get-a-twitter-feed"></a>Obtenha um feed do Twitter

O Twitter permite que você recupere os [dados de cada tweet](https://dev.twitter.com/docs/platform-objects/tweets) como um documento JSON (JavaScript Object Notation) por meio de uma API REST. [OAuth](http://oauth.net) é necessário para autenticação na API.

### <a name="create-a-twitter-application"></a>Criar um aplicativo do Twitter

1. Em um navegador da Web, entre em [https://apps.twitter.com/](https://apps.twitter.com/). Clique no link **Inscreva-se agora** se você não tem uma conta do Twitter.

2. Clique em **Criar Novo Aplicativo**.

3. Digite o **Nome**, a **Descrição** e o **Site**. Você pode fazer uma URL para o campo **Site** . A tabela a seguir mostra alguns valores de exemplo para usar:
   
   | Campo | Valor |
   |:--- |:--- |
   | Nome |MyHDInsightApp |
   | Descrição |MyHDInsightApp |
   | Site |http://www.myhdinsightapp.com |

4. Marque **Sim, eu concordo** e, em seguida, clique em **Criar seu aplicativo do Twitter**.

5. Clique na guia **Permissões** . A permissão padrão é **Somente leitura**.

6. Clique na guia **Chaves e Tokens de acesso** .

7. Clique em **Criar meu token de acesso**.

8. Clique em **OAuth de teste** no canto superior direito da página.

9. Anote a **chave do consumidor**, o **Segredo do consumidor**, o **Token de acesso** e o **Segredo do token de acesso**.

> [!NOTE]
> Quando usar o comando curl no Windows, use aspas duplas, em vez de aspas simples, para os valores de opção.


### <a name="download-tweets"></a>Baixar tweets

O código Python a seguir baixa 10.000 tweets do Twitter e os salva em um arquivo chamado **tweets.txt**.

> [!NOTE]
> As etapas a seguir são executadas no cluster HDInsight, já que o Python já está instalado.
> 
> 

1. Conecte-se ao cluster HDInsight usando SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Caso você tenha usado uma senha para proteger sua conta de usuário do SSH, ela será solicitada. Se você tiver usado uma chave pública, talvez precise usar o parâmetro `-i` para especificar a chave privada correspondente. Por exemplo: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
   
    Para obter mais informações sobre como usar SSH com o HDInsight baseado em Linux, confira os seguintes artigos:
   
   * [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Por padrão, o utilitário **pip** não está instalado no nó principal do HDInsight. Use o seguinte para instalar e atualizar esse utilitário:
   
   ```bash
   sudo apt-get install python-pip
   sudo pip install --upgrade pip
   ```

3. Use os comandos a seguir para instalar [Tweepy](http://www.tweepy.org/) e [Progressbar](https://pypi.python.org/pypi/progressbar/2.2):
   
   ```bash
   sudo apt-get install python-dev libffi-dev libssl-dev
   sudo apt-get remove python-openssl
   sudo pip install tweepy progressbar pyOpenSSL requests[security]
   ```

   > [!NOTE]
   > O segredo da remoção do python-openssl e da instalação do python-dev, libffi-dev, libssl-dev, pyOpenSSL e requests[security] é evitar um aviso do InsecurePlatform ao se conectar ao Twitter via SSL por meio do Python.
   > 
   > O Tweepy v3.2.0 é usado para evitar o [um erro](https://github.com/tweepy/tweepy/issues/576) que pode ocorrer durante o processamento de tweets.

4. Use o comando a seguir para criar um arquivo chamado **gettweets.py**:
   
   ```bash
   nano gettweets.py
   ```

5. Use o texto a seguir como o conteúdo do arquivo **gettweets.py**. Substitua as informações de espaço reservado de **consumer\_secret**, **consumer\_key**, **access/\_token** e **access\_token\_secret** pelas informações do seu aplicativo do Twitter.
   
   ```python
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

   #Create the listener class that receives and saves tweets
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
   ```

6. Use **Ctrl + X** e **Y** para salvar o arquivo.

7. Use o comando a seguir para executar o arquivo e baixar os tweets:
   
    ```bash
    python gettweets.py
    ```
   
    Um indicador de progresso deve aparecer e contar até 100% enquanto os tweets são baixados e salvos no arquivo.
   
   > [!NOTE]
   > Se estiver demorando muito tempo para a barra de progresso Avançar, você deverá alterar o filtro para rastrear os tópicos mais populares. Quando há muitos tweets sobre o tópico no filtro, você pode obter rapidamente os 10.000 tweets necessários.

### <a name="upload-the-data"></a>Carregar os dados

Para carregar os dados no armazenamento do HDInsight, use os seguintes comandos:

   ```bash
   hdfs dfs -mkdir -p /tutorials/twitter/data
   hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Esses comandos armazenam os dados em um local que todos os nós no cluster podem acessar.

## <a name="run-the-hiveql-job"></a>Executar o trabalho HiveQL

1. Use o comando a seguir para criar um arquivo com instruções HiveQL:
   
   ```bash
   nano twitter.hql
   ```

    Use o texto a seguir como o conteúdo do arquivo:
   
   ```hiveql
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
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
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
   ```

2. Pressione **Ctrl + X** e pressione **Y** para salvar o arquivo.
3. Use o comando a seguir para executar o HiveQL contido no arquivo:
   
   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i twitter.hql
   ```

    Esse comando executa o arquivo **twitter.hql**. Quando a consulta for concluída, você verá um prompt `jdbc:hive2//localhost:10001/>`.

4. Do prompt do Beeline, use o seguinte para verificar se você pode selecionar dados da tabela de **tweets** criada pelo HiveQL no arquivo **twitter.hql**:
   
   ```hiveql
   SELECT name, screen_name, count(1) as cc
       FROM tweets
       WHERE text like "%Azure%"
       GROUP BY name,screen_name
       ORDER BY cc DESC LIMIT 10;
   ```

    Essa consulta retornará no máximo 10 tweets com a palavra **Azure** no texto da mensagem.

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como transformar um conjunto de dados JSON não estruturado uma em tabela estruturada do Hive. Para saber mais sobre o Hive no HDInsight, consulte os documentos a seguir:

* [Introdução ao HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Analisar dados de atraso de voo usando o HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

