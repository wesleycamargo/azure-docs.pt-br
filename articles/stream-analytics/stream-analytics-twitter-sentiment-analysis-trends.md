---
title: "Análise de Sentimento do Twitter em tempo real com o Stream Analytics | Microsoft Docs"
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
ms.date: 03/09/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 09860b34bf4b1664e8d82af0e049cfd1a2d8defa
ms.lasthandoff: 03/10/2017

---

# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Análise de sentimento do Twitter em tempo real no Stream Analytics do Azure

Aprenda a compilar uma solução de análise de sentimento para análise de mídia social colocando os eventos em tempo real do Twitter nos Hubs de Eventos do Azure. Você escreverá uma consulta de Stream Analytics do Azure para analisar os dados. Em seguida, você armazenará os resultados para uma leitura minuciosa posterior ou usará um painel e o [Power BI](https://powerbi.com/) para fornecer análises em tempo real.

As ferramentas de análise de mídias sociais ajudam as organizações a compreender os tópicos em destaque, ou seja, os assuntos e atitudes relevantes que têm uma alta quantidade de postagens em mídias sociais. A análise de sentimento, também chamada de *mineração opinião*, usa as ferramentas de análise de mídia social para determinar as atitudes em direção a um produto, ideia e assim por diante. A análise de tendência do Twitter em tempo real é um ótimo exemplo, porque o modelo de assinatura com hashtag permite que você escute palavras-chave específicas e desenvolva a análise de sentimento no feed.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Cenário: Análise de sentimento de mídia social em tempo real

Uma empresa que tem um site de mídia de notícias está interessado em obter uma vantagem sobre seus concorrentes apresentando conteúdo do site que é imediatamente relevante para seus leitores. A empresa usa a análise de mídia social sobre os tópicos relevantes para seus leitores, fazendo uma análise de sentimento em tempo real nos dados do Twitter. Especificamente, para identificar os tópicos populares no Twitter em tempo real, a empresa precisa da análise em tempo real sobre o volume de tweets e o sentimento para os principais tópicos. Então, essencialmente, eles precisam de um mecanismo de análise para análise de sentimento baseado nesse feed de mídia social.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure
* Conta do Twitter e [token de acesso do OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* O arquivo [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) do GitHub.
* Opcional: código-fonte do cliente do Twitter do [GitHub](https://aka.ms/azure-stream-analytics-twitterclient)

## <a name="create-an-event-hub-input"></a>Criar uma entrada do hub de eventos

O aplicativo de exemplo gerará eventos e os enviará por push a uma instância dos Hubs de Eventos (um hub de eventos, de forma abreviada). Os Hubs de Eventos do Barramento de Serviço são o método preferencial de ingestão de eventos para Stream Analytics. Para obter mais informações, examine a documentação do Hub de Eventos na [documentação do Barramento de Serviço do Azure](/azure/service-bus/).

### <a name="use-the-following-steps-to-create-an-event-hub"></a>Use as seguintes etapas para criar um novo hub de eventos.

1. No [portal do Azure](https://portal.azure.com), clique em **NOVO** > digite *Hubs de Eventos* e, em seguida, selecione **Hubs de Eventos** na pesquisa resultante. Em seguida, clique em **Criar**.
2. Forneça um nome para o Hub de Eventos e crie um Grupo de Recursos. Especifiquei `socialtwitter-eh` e `socialtwitter-rg`, respectivamente. Marque a caixa para fixar a conta no painel e, em seguida, clique no botão **Criar**.
3. Depois que a implantação for concluída, clique no Hub de Eventos e, em seguida, clique em **Hubs de Eventos** em **Entidades**.
4. Clique no botão **+ Hub de Eventos** para criar seu Hub de Eventos. Forneça seu nome novamente (o meu foi `socialtwitter-eh`) e clique em **Criar**.
5. Para conceder acesso ao hub de eventos, precisamos criar uma política de acesso compartilhado. Clique no Hub de Eventos e, em seguida, em **Políticas de acesso compartilhado** em **Configurações**.
6. Em **POLÍTICAS DE ACESSO COMPARTILHADO**, crie uma nova política com permissões **GERENCIAR** clicando em **+ Adicionar**. Forneça um nome à política, marque **GERENCIAR** e, em seguida, clique em **Criar**.
7. Clique na nova política depois que ela for criada e, em seguida, no botão Copiar da entidade **CADEIA DE CONEXÃO – CHAVE PRIMÁRIA**. Precisaremos disso mais adiante no exercício. Em seguida, volte para o painel.

![pontos de extremidade da política de acesso compartilhado](./media/stream-analytics-twitter-sentiment-analysis-trends/keysandendpoints.png)

## <a name="configure-and-start-the-twitter-client-application"></a>Configurar e iniciar o aplicativo de cliente do Twitter

Fornecemos um aplicativo de cliente que se conectará aos dados do Twitter por meio das [APIs de Transmissão Contínua do Twitter](https://dev.twitter.com/streaming/overview) para coletar eventos de Tweets sobre um conjunto parametrizado de tópicos. A ferramenta de software livre [Sentiment140](http://help.sentiment140.com/) é usada para atribuir um valor de sentimento a cada tweet.

* 0 = negativo
* 2 = neutro
* 4 = positivo

Em seguida, os eventos de tweets são enviados por push ao hub de eventos.  

### <a name="follow-these-steps-to-set-up-the-application"></a>Siga estas etapas para configurar o aplicativo:

1. [Baixe o TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) e descompacte-o.
2. Execute o aplicativo `TwitterWPFClient.exe` e insira os dados da Chave de API do Twitter e o Segredo, o Token de Acesso do Twitter e o Segredo, além das informações do Hub de Eventos do Azure. Por fim, defina em quais palavras-chave você deseja determinar o sentimento. Observe que se você especificar mais de uma palavra (usando vírgulas para definir vários valores) e desejar ANY, é necessário inverter o controle para “Encontrar correspondência de ANY”.

   [Etapas para gerar um token de acesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

   Você precisará criar um aplicativo vazio para gerar um token.  

3. Substitua os valores de EventHubConnectionString e EventHubName em TwitterWpfClient.exe.config pela cadeia de conexão e pelo nome do hub de eventos. A cadeia de conexão que você copiou anteriormente fornece tanto o nome quanto a cadeia de conexão do hub de eventos, então esteja ciente da necessidade de separá-los e colocar cada um no campo correto. Por exemplo, considere a seguinte cadeia de conexão:  
   
   `Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub`
   
   O arquivo TwitterWpfClient.exe.config deve conter suas configurações, como no seguinte exemplo:
   
   ```
     add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
     add key="EventHubName" value="yourhub"
   ```
   
   É importante observar que o texto "EntityPath =" **não** aparece no valor EventHubName.
   
   Você também pode inserir os valores das informações de conexão do Twitter e do Azure diretamente no cliente. A mesma lógica se aplica, em que “EntityPath =” não é usado.
   
   ![wpfclient](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

4. *Opcional:* ajuste as palavras-chave a serem pesquisadas.  Por padrão, esse aplicativo procura algumas palavras-chave de jogos.  É possível ajustar os valores de **twitter_keywords** em TwitterWpfClient.exe.config, se desejar.
5. Execute TwitterWpfClient.exe e clique no botão verde Iniciar para coletar o sentimento social. Você verá eventos de Tweet com os valores **CreatedAt**, **Topic** e **SentimentScore** sendo enviados ao hub de eventos.

## <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

Agora que os eventos de tweets estão sendo transmitidos em tempo real do Twitter, podemos configurar um trabalho do Stream Analytics para analisar esses eventos em tempo real.

### <a name="provision-a-stream-analytics-job"></a>Provisionar um trabalho de análise de fluxo

No [portal do Azure](https://portal.azure.com/), clique em **NOVO** > digite **STREAM ANALYTICS** > e clique no resultado do bloco Stream Analytics. Especifique os valores a seguir e clique em **CRIAR**.

   * **NOME DO TRABALHO**: insira um nome de trabalho.
   * **Grupo de recursos**: selecione o grupo de recursos criado anteriormente neste exercício na opção “Usar existente”.
   * **CONTA DE ARMAZENAMENTO**: escolha a conta de armazenamento do Azure que você deseja usar para armazenar os dados de monitoramento de todos os trabalhos do Stream Analytics executados nesta região. Você tem a opção de escolher uma conta de armazenamento existente ou criar uma nova.   

![Novo trabalho](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

Depois de criar o trabalho, ele será aberto no portal do Azure.

## <a name="specify-the-job-input"></a>Especificar a entrada de trabalho

No trabalho do Stream Analytics, clique em **ENTRADAS** na parte central do painel de trabalho, em Topologia do Trabalho e, em seguida, clique em **ADICIONAR**. O portal solicitará algumas informações listadas abaixo. A maioria dos valores padrão será suficiente, mas eles estão definidos abaixo para sua informação.
  
   * **ALIAS DE ENTRADA**: insira um nome amigável para essa entrada de trabalho, como `TwitterStream`. Você usará esse nome na consulta posteriormente.
   * **NOME DO HUB DE EVENTOS**: selecione o nome do hub de eventos.
   * **NOME DE POLÍTICA DO HUB DE EVENTOS**: selecione a política de hub de eventos que você criou anteriormente neste tutorial.

Selecione o botão **Criar** .

## <a name="specify-job-query"></a>Especifique a consulta de trabalho

O Stream Analytics dá suporte a um modelo de consulta simples e declarativo que descreve as transformações. Para saber mais sobre a linguagem, consulte a [Referência de linguagem de consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Este tutorial o ajudará a criar e testar várias consultas sobre dados do Twitter.

Para comparar o número de menções entre tópicos, usaremos uma [Janela em Cascata](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obter a contagem de menções por tópico a cada cinco segundos.

Altere a consulta no editor de código para o código abaixo e clique em **Salvar**:

```
SELECT System.Timestamp as Time, Topic, COUNT(*)
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY TUMBLINGWINDOW(s, 5), Topic
```   

Essa consulta usa a palavra-chave **TIMESTAMP BY** para especificar um campo de carimbo de data/hora na carga a ser usada na computação temporal. Se esse campo não fosse especificado, a operação em janela seria realizada usando a hora em que cada evento chegou ao hub de eventos.  Saiba mais na seção “Hora de chegada versus tempo de aplicação” na [Referência de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).

A consulta também acessa um carimbo de data/hora para o final de cada janela usando a propriedade **System.Timestamp**.

![Caixa de consulta](./media/stream-analytics-twitter-sentiment-analysis-trends/querybox.png)

## <a name="create-output-sink"></a>Criar coletor de saída

Agora que definimos um fluxo de eventos, uma entrada de hub de eventos para a ingestão de eventos e uma consulta para executar uma transformação no fluxo, a última etapa é definir um coletor de saída para o trabalho.  Gravaremos os eventos de tweets agregados de nossa consulta de trabalho para o armazenamento de Blobs do Azure.  Você também pode enviar por push os resultados para um Banco de Dados SQL do Azure, um armazenamento de Tabelas do Azure ou para Hubs de Eventos, dependendo das suas necessidades de aplicativo específicas.

## <a name="specify-job-output"></a>Especifique a saída do trabalho

No trabalho do Stream Analytics, clique em **SAÍDA** em **Topologia do Trabalho** e, em seguida, clique em **ADICIONAR**. Depois, digite ou selecione os seguintes valores no painel:
   
   * **ALIAS DE SAÍDA**: insira um nome amigável para essa saída de trabalho. Esta demonstração usará `Output`.
   * **Coletor**: selecione Armazenamento de Blobs.
   * **CONTA DE ARMAZENAMENTO**: selecione “Criar uma nova conta de armazenamento”.
    * **CONTA DE ARMAZENAMENTO**: forneça um nome para a nova conta de armazenamento (`socialtwitter`, neste exemplo)
    * **CONTÊINER**: forneça um nome para o novo contêiner (`socialtwitter`, neste exemplo)

Deixe o restante das entradas como valores padrão. Por fim, clique em **Criar**.

## <a name="start-the-job"></a>Iniciar o trabalho

Como uma entrada de trabalho, uma consulta e uma saída foram especificadas, estamos prontos para iniciar o trabalho de Stream Analytics.

No painel de visão geral do trabalho, basta clicar em **INICIAR** na parte superior da página.

Na caixa de diálogo que será exibida, selecione **HORA DE INÍCIO DO TRABALHO** e clique no botão de **SELEÇÃO** na parte inferior da caixa de diálogo. O status do trabalho será alterado para **Iniciando** e logo mudará para **Em execução**.

![Trabalho em execução](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Exibir saída para análise sentimento

Depois que o trabalho estiver executando e processando o fluxo do Twitter em tempo real, escolha como você deseja exibir a saída para análise de sentimento. Use uma ferramenta como o [Gerenciador de Armazenamento do Azure](https://http://storageexplorer.com/) ou o [Gerenciador do Azure](http://www.cerebrata.com/products/azure-explorer/introduction) para exibir a saída do trabalho em tempo real. Aqui, você pode usar o [Power BI](https://powerbi.com/) estender seu aplicativo para incluir um painel personalizado como o mostrado na seguinte captura de tela.

![powerbi](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)

## <a name="another-query-of-interest-in-this-scenario"></a>Outra consulta de interesse neste cenário

Outra consulta de exemplo que criamos para esse cenário se baseia na [Janela Deslizante](https://msdn.microsoft.com/library/azure/dn835051.aspx). Para identificar os tópicos mais populares, procuraremos tópicos que ultrapassam um valor limite de menções em determinado período de tempo. Para as finalidades deste tutorial, verificaremos os tópicos mencionados mais de 20 vezes nos últimos cinco segundos.

```
SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY SLIDINGWINDOW(s, 5), topic
HAVING COUNT(*) > 20
```

## <a name="table-of-the-field-headers"></a>Tabela dos cabeçalhos de campo

Para divulgação completa, os rótulos de campo que você pode usar nesse exercício são listados nesta tabela. Fique à vontade para fazer experiências no editor de consultas.

Propriedade JSON | definição
--- | ---
CreatedAt | hora de criação do tweet
Tópico | Tópico correspondente à palavra-chave especificada
SentimentScore | pontuação de sentimento de Sentiment140
Autor | Identificador do Twitter que enviou o tweet
Texto | corpo completo do tweet


## <a name="get-support"></a>Obtenha suporte
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


