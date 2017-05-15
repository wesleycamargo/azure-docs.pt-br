---
title: "Análise de Sentimento do Twitter em tempo real com o Stream Analytics do Azure | Microsoft Docs"
description: "Saiba como usar a Stream Analytics para análise de sentimento Twitter em tempo real. Orientações passo a passo de geração de eventos aos dados em um painel em tempo real."
keywords: "análise de tendência do twitter em tempo real, análise de sentimento, análise de mídia social, exemplo de análise de tendência"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 42068691-074b-4c3b-a527-acafa484fda2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/28/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: d4fae06cb240c1687b059ae991b8d09f94e2ffc3
ms.contentlocale: pt-br
ms.lasthandoff: 05/01/2017

---

# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Análise de sentimento do Twitter em tempo real no Stream Analytics do Azure

Aprenda a compilar uma solução de análise de sentimento para análise de mídia social colocando os eventos em tempo real do Twitter nos Hubs de Eventos do Azure. Neste cenário, você escreverá uma consulta de Stream Analytics do Azure para analisar os dados. Em seguida, você armazenará os resultados para uso posterior ou usará um painel e o [Power BI](https://powerbi.com/) para fornecer análises em tempo real.

As ferramentas de análise de mídias sociais ajudam as organizações a compreender os tópicos mais populares. Os tópicos mais populares são entidades e atitudes com um alto volume de postagens em mídia social. A análise de sentimento, também chamada de *mineração opinião*, usa as ferramentas de análise de mídia social para determinar as atitudes em direção a um produto, ideia e assim por diante.

A análise de tendência do Twitter em tempo real é um ótimo exemplo de uma ferramenta de análise, porque o modelo de assinatura com hashtag permite que você escute palavras-chave específicas e desenvolva a análise de sentimento no feed.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Cenário: Análise de sentimento de mídia social em tempo real

Uma empresa com um site de mídia de notícias está interessada em obter uma vantagem sobre seus concorrentes apresentando conteúdo do site imediatamente relevante para seus leitores. A empresa usa a análise de mídia social sobre os tópicos relevantes para seus leitores, fazendo uma análise de sentimento em tempo real nos dados do Twitter.

Especificamente, para identificar os tópicos em destaque no Twitter, a empresa precisa de análise em tempo real sobre o volume de tweets e de sentimento para os tópicos principais. Em outras palavras, eles precisam de um mecanismo de análise para análise de sentimento baseado nesse feed de mídia social.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure
* Uma conta do Twitter e um [token de acesso do OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* O arquivo [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) do GitHub
* Opcional: o código-fonte para o cliente do Twitter do [GitHub](https://aka.ms/azure-stream-analytics-twitterclient)

## <a name="create-an-event-hub-input"></a>Criar uma entrada do hub de eventos

O aplicativo de exemplo gera eventos e os envia por push a uma instância de Hubs de Eventos (um hub de eventos, de forma abreviada). Os hubs de eventos do Barramento de Serviço do Azure são o método preferencial de ingestão de eventos para Stream Analytics. Para saber mais, examine a documentação do hub de eventos na [documentação do Barramento de Serviço do Azure](/azure/service-bus/).

### <a name="create-an-event-hub"></a>Criar um Hub de Evento

Use as etapas a seguir para criar um hub de eventos:

1. No [portal do Azure](https://portal.azure.com), selecione **NOVO**, digite **Hubs de Eventos** e então selecione **Hubs de Eventos** na pesquisa resultante. Em seguida, selecione **Criar**.

2. Forneça um nome para o hub de eventos e crie um grupo de recursos. Especifiquei `socialtwitter-eh` e `socialtwitter-rg`, respectivamente. Selecione a opção para fixar a conta no painel e selecione o botão **Criar**.

3. Depois que a implantação for concluída, selecione o hub de eventos. Em seguida, em **Entidades**, selecione **Hubs de Eventos**.

4. Para criar o hub de eventos, selecione o botão **+ Hub de Eventos**. Forneça seu nome novamente (o nosso foi `socialtwitter-eh`) e selecione **Criar**.

5. Para conceder acesso ao hub de eventos, precisamos criar uma política de acesso compartilhado. Selecione o hub de eventos e, em **Configurações**, selecione **Políticas de acesso compartilhado**.

6. Em **Políticas de acesso compartilhado**, crie uma nova política com permissões **GERENCIAR** ao selecionar **+ Adicionar**. Dê um nome à política, marque **GERENCIAR** e então selecione **Criar**.

7. Selecione a nova política após ela ter sido criada e selecione o botão Copiar para a entidade **CADEIA DE CONEXÃO - CHAVE PRIMÁRIA**. Precisaremos disso mais tarde no exercício. Em seguida, volte para o painel.

![Pontos de extremidade da política de acesso compartilhado](./media/stream-analytics-twitter-sentiment-analysis-trends/keysandendpoints.png)

## <a name="configure-and-start-the-twitter-client-application"></a>Configurar e iniciar o aplicativo de cliente do Twitter

Nós criamos um aplicativo de cliente que se conectará aos dados do Twitter por meio das [APIs de Streaming do Twitter](https://dev.twitter.com/streaming/overview) para coletar eventos de Tweets sobre um conjunto parametrizado de tópicos. A ferramenta de software livre [Sentiment140](http://help.sentiment140.com/) atribui um valor de sentimento a cada tweet como a seguir:

* 0 = negativo
* 2 = neutro
* 4 = positivo

Em seguida, os eventos de Tweet são enviados por push ao hub de eventos.  

### <a name="set-up-the-application"></a>Configurar o aplicativo
 Siga estas etapas para configurar o aplicativo:

1. [Baixe o TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) e descompacte-o.

2. Execute o aplicativo `TwitterWPFClient.exe`. Em seguida, insira os dados da Chave de API do Twitter e o Segredo, o Token de Acesso do Twitter e o Segredo, além das informações do hub de eventos. Por fim, defina para quais palavras-chave você deseja determinar o sentimento.

### <a name="generate-an-oauth-access-token"></a>Gerar um token de acesso OAuth
Para saber mais, veja [Etapas para gerar um token de acesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens).  

 Para gerar um token, será necessário criar um aplicativo vazio.  

1. Substitua os valores de EventHubConnectionString e EventHubName em TwitterWpfClient.exe.config pela cadeia de conexão e pelo nome do hub de eventos. A cadeia de conexão que você copiou anteriormente fornece tanto o nome quanto a cadeia de conexão do hub de eventos. Separe-os e coloque cada um no campo correto. Por exemplo, considere a seguinte cadeia de conexão:

 ```
    Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub
 ```  

   O arquivo TwitterWpfClient.exe.config deve conter suas configurações, como mostrado no seguinte exemplo:

 ```
      add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
      add key="EventHubName" value="yourhub"
   ```
   > [!NOTE]
   > O texto "EntityPath =" **não** aparece no valor de EventHubName.

    Você também pode inserir os valores das informações de conexão do Twitter e do Azure diretamente no cliente. A mesma lógica se aplica, em que “EntityPath =” não é usado.

   ![wpfclient](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

2. **Opcional:** ajuste as palavras-chave a serem pesquisadas. Por padrão, esse aplicativo procura algumas palavras-chave de jogos.  É possível ajustar os valores de **twitter_keywords** em TwitterWpfClient.exe.config, se desejar.

3. Execute TwitterWpfClient.exe. Então selecione o botão iniciar verde para coletar sentimento social. Você vê eventos de Tweet com os valores **CreatedAt**, **Topic** e **SentimentScore** sendo enviados ao hub de eventos.

## <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

Agora que os eventos de Tweets estão sendo transmitidos em temo real do Twitter, podemos configurar um trabalho de Stream Analytics para analisar esses eventos em tempo real.

### <a name="provision-a-stream-analytics-job"></a>Provisionar um trabalho de análise de fluxo

Para provisionar um trabalho do Stream Analytics, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), selecione **NOVO**, digite **STREAM ANALYTICS** e então selecione o resultado do bloco Stream Analytics.

2. Especifique os valores a seguir e selecione **CRIAR**.

   * **NOME DO TRABALHO**: insira um nome de trabalho.

   * **Grupo de recursos**: selecione o grupo de recursos criado anteriormente neste exercício na opção “usar existente”.

   * **CONTA DE ARMAZENAMENTO**: escolha a conta de armazenamento do Azure que você deseja usar para armazenar os dados de monitoramento de todos os trabalhos do Stream Analytics executados nesta região. Você tem a opção de escolher uma conta de armazenamento existente ou criar uma nova.   

![Novo trabalho](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

Depois que o trabalho tiver sido criado, ele será aberto no portal do Azure.

## <a name="specify-the-job-input"></a>Especificar a entrada de trabalho
Para especificar a entrada do trabalho, execute as seguintes etapas:

1. No trabalho do Stream Analytics, em **Topologia do Trabalho** no meio do painel de trabalho, selecione **ENTRADAS**. Em seguida, selecione **ADICIONAR**.

    Em seguida, o portal solicita algumas das informações a seguir. A maioria dos valores padrão funciona; eles são definidos aqui:

   * **ALIAS DE ENTRADA**: insira um nome amigável para essa entrada de trabalho, como `TwitterStream`. Você usa esse nome na consulta posteriormente.  

   * **NOME DO HUB DE EVENTOS**: selecione o nome do hub de eventos.

   * **NOME DE POLÍTICA DO HUB DE EVENTOS**: selecione a política de hub de eventos que você criou anteriormente neste tutorial.

2. Selecione o botão **Criar**.

## <a name="specify-the-job-query"></a>Especificar a consulta de trabalho

O Stream Analytics dá suporte a um modelo de consulta simples e declarativo que descreve as transformações. Para saber mais sobre a linguagem, consulte a [Referência de linguagem de consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Este tutorial ajuda você a criar e testar várias consultas sobre dados do Twitter.

Para comparar o número de menções entre tópicos, usaremos uma [Janela em Cascata](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obter a contagem de menções por tópico a cada cinco segundos.

Altere a consulta no editor de códigos para o seguinte e, em seguida, selecione **salvar**:

```
SELECT System.Timestamp as Time, Topic, COUNT(*)
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY TUMBLINGWINDOW(s, 5), Topic
```   

Essa consulta usa a palavra-chave **TIMESTAMP BY** para especificar um campo de carimbo de data/hora na carga a ser usada na computação temporal. Se esse campo não for especificado, a operação em janela será realizada usando a hora em que cada evento chegou ao hub de eventos. Saiba mais na seção “Hora de chegada versus tempo de aplicação” na [Referência de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).

A consulta também acessa um carimbo de data/hora para o final de cada janela usando a propriedade **System.Timestamp**.

![Caixa de consulta](./media/stream-analytics-twitter-sentiment-analysis-trends/querybox.png)

## <a name="create-an-output-sink"></a>Criar um coletor de saída

Agora que definimos um fluxo de eventos, uma entrada de hub de eventos para a ingestão de eventos e uma consulta para executar uma transformação no fluxo, a última etapa é definir um coletor de saída para o trabalho.  

Nós gravamos os eventos de tweets agregados de nossa consulta de trabalho para o armazenamento de Blobs do Azure.  Você também pode enviar por push os resultados para um Banco de Dados SQL do Azure, um armazenamento de Tabelas do Azure ou para Hubs de Eventos, dependendo das suas necessidades de aplicativo específicas.

## <a name="specify-the-job-output"></a>Especificar a saída de trabalho
Para especificar a saída do trabalho, execute as seguintes etapas:

1. No trabalho do Stream Analytics, selecione **SAÍDA** em **Topologia do Trabalho** e então selecione **ADICIONAR**.

2. Digite ou selecione os seguintes valores no painel:

   * **ALIAS DE SAÍDA**: insira um nome amigável para essa saída de trabalho. Esta demonstração usa `Output`.

   * **Coletor**: selecione **Armazenamento de blobs**.

   * **CONTA DE ARMAZENAMENTO**: selecione **Criar uma nova conta de armazenamento**.

    * **CONTA DE ARMAZENAMENTO**: forneça um nome para a nova conta de armazenamento (`socialtwitter`, neste exemplo).

    * **CONTÊINER**: forneça um nome para o novo contêiner (`socialtwitter`, neste exemplo).

3. Deixe o restante das entradas como valores padrão. Por fim, selecione **Criar**.

## <a name="start-the-job"></a>Iniciar o trabalho

Como uma entrada de trabalho, uma consulta e uma saída foram especificadas, estamos prontos para iniciar o trabalho de Stream Analytics.

Para iniciar o trabalho, execute as seguintes etapas:

1. No painel de visão geral do trabalho, na parte superior da página, selecione **INICIAR**.

2. Na caixa de diálogo que será exibida, selecione **HORA DE INÍCIO DO TRABALHO** e então selecione o botão **VERIFICAR** na parte inferior da caixa de diálogo. O status do trabalho muda primeiro para **Iniciando** e então para **Em execução**.

![Trabalho em execução](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Exibir saída para análise sentimento

Depois que o trabalho tiver sido iniciado e estiver processando o fluxo do Twitter em tempo real, escolha como você deseja exibir a saída para análise de sentimento.

Use uma ferramenta como o [Gerenciador de Armazenamento do Azure](https://http://storageexplorer.com/) ou o [Gerenciador do Azure](http://www.cerebrata.com/products/azure-explorer/introduction) para exibir a saída do trabalho em tempo real. Aqui, você pode usar o [Power BI](https://powerbi.com/) estender seu aplicativo para incluir um painel personalizado como o mostrado na seguinte captura de tela.

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)

## <a name="create-another-query-to-identify-trending-topics"></a>Criar outra consulta para identificar os tópicos mais populares

Outra consulta de exemplo que criamos para esse cenário se baseia na [Janela Deslizante](https://msdn.microsoft.com/library/azure/dn835051.aspx). Para identificar os tópicos mais populares, procuraremos tópicos que ultrapassam um valor limite de menções em determinado período de tempo.

Para as finalidades deste tutorial, verificaremos os tópicos mencionados mais de 20 vezes nos últimos cinco segundos.

```
SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY SLIDINGWINDOW(s, 5), topic
HAVING COUNT(*) > 20
```

## <a name="use-field-headers"></a>Usar cabeçalhos de campo

Os rótulos de campo que você pode usar nesse exercício são listados nesta tabela. Fique à vontade para fazer experiências no editor de consultas.

Propriedade JSON | Definição
--- | ---
CreatedAt | A hora em que foi criado o tweet
Tópico | O tópico que corresponde à palavra-chave especificada
SentimentScore | A pontuação de sentimento do Sentiment140
Autor | O identificador do Twitter que enviou o tweet
Texto | O corpo completo do tweet


## <a name="get-support"></a>Obtenha suporte
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

