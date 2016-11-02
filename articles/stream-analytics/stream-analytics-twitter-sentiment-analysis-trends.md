<properties
    pageTitle="Análise de sentimento Twitter em tempo real com a Stream Analytics | Microsoft Azure"
    description="Saiba como usar a Stream Analytics para análise de sentimento Twitter em tempo real. Orientações passo a passo de geração de eventos aos dados em um painel em tempo real."
    keywords="análise de tendência do twitter em tempo real, análise de sentimento, análise de mídia social, exemplo de análise de tendência"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>



# <a name="social-media-analysis:-real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Análise de mídia social: análise de sentimento do Twitter em tempo real na Stream Analytics do Azure

Aprenda a compilar uma solução de análise de sentimento para análise de mídia social colocando os eventos em tempo real do Twitter nos Hubs de Eventos do Azure. Você escreverá uma consulta de Stream Analytics do Azure para analisar os dados. Você poderá, em seguida, armazenar os resultados para uma leitura minuciosa posterior ou usar um painel e o [Power BI](https://powerbi.com/) para fornecer percepções em tempo real.

As ferramentas de análise de mídias sociais ajudam as organizações a compreender os tópicos em destaque, ou seja, os assuntos e atitudes relevantes que têm uma alta quantidade de postagens em mídias sociais. A análise de sentimento, também chamada de *mineração opinião*, usa as ferramentas de análise de mídia social para determinar as atitudes em direção a um produto, ideia e assim por diante. A análise de tendência em tempo real do Twitter é um ótimo exemplo porque o modelo de assinatura com hashtag permite que você ouça palavras-chave específicas e desenvolva a análise de sentimento no feed.

## <a name="scenario:-sentiment-analysis-in-real-time"></a>Cenário: análise de sentimento em tempo real

Uma empresa que tem um site de mídia de notícias está interessado em obter uma vantagem sobre seus concorrentes apresentando conteúdo do site que é imediatamente relevante para seus leitores. A empresa usa a análise de mídia social sobre os tópicos relevantes para seus leitores, fazendo uma análise de sentimento em tempo real nos dados do Twitter. Especificamente, para identificar os tópicos em destaque no Twitter, a empresa precisa de análise em tempo real sobre o volume de tweets e de sentimento para os tópicos principais. Então, essencialmente, eles precisam de um mecanismo de análise para análise de sentimento baseado nesse feed de mídia social.

## <a name="prerequisites"></a>Pré-requisitos
-   Conta do Twitter e [token de acesso do OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
-   [TwitterClient.zip](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip) do Centro de Download da Microsoft
-   Opcional: código-fonte do cliente do Twitter do [GitHub](https://aka.ms/azure-stream-analytics-twitterclient)

## <a name="create-an-event-hub-input-and-a-consumer-group"></a>Criar uma entrada de Hub de Eventos e um grupo de consumidores

O aplicativo de exemplo gerará eventos e os enviará por push a uma instância de Hubs de Eventos (um Hub de Eventos, de forma abreviada). Os Hubs de Eventos do Barramento de Serviço são o método preferencial de ingestão de eventos para Stream Analytics. Consulte a documentação sobre Hubs de Eventos na [documentação do Barramento de Serviço](/documentation/services/service-bus/).

Use as seguintes etapas para criar um novo hub de eventos.

1.  No Portal do Azure, clique em **NOVO** > **SERVIÇOS DE APLICATIVOS** > **BARRAMENTO DE SERVIÇO** > **HUB DE EVENTOS** > **CRIAÇÃO RÁPIDA** e forneça um nome, uma região e um namespace novo ou existente.  
2.  Como prática recomendada, cada trabalho de Stream Analytics deve ser lido de um único grupo de consumidores de Hubs de Eventos. Vamos orientá-lo durante o processo de criação de um grupo de consumidores mais tarde. Você pode saber mais sobre os grupos de consumidores em [Visão geral dos Hubs de Eventos do Azure](https://msdn.microsoft.com/library/azure/dn836025.aspx). Para criar um grupo de consumidores, navegue até o hub de eventos recém-criado, clique na guia **GRUPOS DE CONSUMIDORES**, clique em **CRIAR** na parte inferior da página e forneça um nome para o seu grupo de consumidores.
3.  Para conceder acesso ao hub de eventos, precisamos criar uma política de acesso compartilhado. Clique na guia **CONFIGURAR** de seu hub de eventos.
4.  Em **POLÍTICAS DE ACESSO COMPARTILHADO**, crie uma nova política com permissões para **GERENCIAR**.

    ![Políticas de Acesso Compartilhado em que você pode criar uma política com permissões para Gerenciar.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)

5.  Na parte inferior da página, clique em **SALVAR** .
6.  Navegue até o **PAINEL**, clique em **INFORMAÇÕES DE CONEXÃO** na parte inferior da página e copie e salve as informações de conexão. (Use o ícone de cópia que aparece sob o ícone de pesquisa).

## <a name="configure-and-start-the-twitter-client-application"></a>Configurar e iniciar o aplicativo de cliente do Twitter

Fornecemos um aplicativo de cliente que se conectará aos dados do Twitter por meio das [APIs de Transmissão Contínua do Twitter](https://dev.twitter.com/streaming/overview) para coletar eventos de Tweets sobre um conjunto parametrizado de tópicos. A ferramenta de software livre [Sentiment140](http://help.sentiment140.com/) é usada para atribuir um valor de sentimento a cada tweet.

- 0 = negativo
- 2 = neutro
- 4 = positivo

Em seguida, os eventos de tweets são enviados por push ao hub de eventos.  

Siga estas etapas para configurar o aplicativo:

1.  [Baixar a solução TwitterClient](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip).
2.  Abra TwitterClient.exe.config e substitua oauth_consumer_key, oauth_consumer_secret, oauth_token e oauth_token_secret por tokens do Twitter que têm seus valores.  

    [Etapas para gerar um token de acesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

    Observe que você precisa criar um aplicativo vazio para gerar um token.  
3.  Substitua os valores de EventHubConnectionString e EventHubName em TwitterClient.exe.config pela cadeia de conexão e o nome do hub de eventos. A cadeia de conexão que você copiou anteriormente fornece tanto o nome quanto a cadeia de conexão do hub de eventos, então esteja ciente da necessidade de separá-los e colocar cada um no campo correto. Por exemplo, considere a seguinte cadeia de conexão:

        Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub

    O arquivo TwitterClient.exe.config deve conter suas configurações, como no exemplo a seguir:

        add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
        add key="EventHubName" value="yourhub"

    É importante observar que o texto "EntityPath =" __não__ aparece no valor EventHubName.

4.  *Opcional:* ajuste as palavras-chave a serem pesquisadas.  Por padrão, esse aplicativo procura por "Azure,Skype,XBox,Microsoft,Seattle".  Você poderá ajustar os valores de **twitter_keywords** em TwitterClient.exe.config, se desejar.
5.  Execute TwitterClient.exe para iniciar o aplicativo. Você verá eventos de Tweet com os valores **CreatedAt**, **Topic** e **SentimentScore** sendo enviados ao hub de eventos.

    ![Análise de sentimento: valores de SentimentScore enviados para um hub de eventos.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

Agora que os eventos de Tweets estão sendo transmitidos em temo real do Twitter, podemos configurar um trabalho de Stream Analytics para analisar esses eventos em tempo real.

### <a name="provision-a-stream-analytics-job"></a>Provisionar um trabalho de análise de fluxo

1.  No [portal do Azure](https://manage.windowsazure.com/), clique em **NOVO** > **SERVIÇOS DE DADOS** > **STREAM ANALYTICS** > **CRIAÇÃO RÁPIDA**.
2.  Especifique os seguintes valores e, em seguida, clique em **CRIAR TRABALHO DE STREAM ANALYTICS**:

    * **NOME DO TRABALHO**: insira um nome de trabalho.
    * **REGIÃO**: selecione a região em que você deseja executar o trabalho. Considere a opção de colocar o trabalho e o hub de eventos na mesma região para garantir um desempenho melhor e assegurar que você não pague para transferir dados entre regiões.
    * **CONTA DE ARMAZENAMENTO**: escolha a conta de armazenamento do Azure que você deseja usar para armazenar os dados de monitoramento de todos os trabalhos do Stream Analytics executados nesta região. Você tem a opção de escolher uma conta de armazenamento existente ou criar uma nova.

3.  Clique em **STREAM ANALYTICS** no painel à esquerda para listar os trabalhos de Stream Analytics.  
    ![Ícone do serviço Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)

    O novo trabalho será mostrado com um status de **CRIADO**. Observe que o botão **INICIAR** , na parte inferior da página, está desabilitado. Você deve configurar a entrada, a saída, a consulta do trabalho e assim por diante antes de iniciar o trabalho.


### <a name="specify-job-input"></a>Especificar entrada de trabalho
1.  No trabalho de Stream Analytics, clique em **ENTRADAS** na parte superior da página e clique em **ADICIONAR ENTRADA**. A caixa de diálogo que se abre orientará você por algumas etapas para configurar sua entrada.
2.  Clique **FLUXO DE DADOS** e clique no botão à direita.
3.  Clique em **HUB DE EVENTOS** e clique no botão à direita.
4.  Digite ou selecione os seguintes valores na terceira página:

    * **ALIAS DE ENTRADA**: insira um nome amigável para a entrada do trabalho, como *TwitterStream*. Observe que você usará esse nome na consulta posteriormente.
    **HUB DE EVENTOS**: se o hub de eventos que você criou estiver na mesma assinatura que o trabalho do Stream Analytics, selecione o namespace que contém o hub de eventos.

        Se o hub de eventos estiver em uma assinatura diferente, clique em **Usar Hub de Eventos de Outra Assinatura** e insira manualmente as informações para o **NAMESPACE DO BARRAMENTO DE SERVIÇO**, o **NOME DO HUB DE EVENTOS**, o **NOME DA POLÍTICA DO HUB DE EVENTOS**, a **CHAVE DE POLÍTICA DO HUB DE EVENTOS** e a **CONTAGEM DE PARTIÇÕES DO HUB DE EVENTOS**.

    * **NOME DO HUB DE EVENTOS**: selecione o nome do hub de eventos.

    * **NOME DE POLÍTICA DO HUB DE EVENTOS**: selecione a política de hub de eventos que você criou anteriormente neste tutorial.

    * **GRUPO DE CONSUMIDORES DO HUB DE EVENTOS**: digite o nome do grupo de consumidores criado anteriormente neste tutorial.
5.  Clique no botão direito.
6.  Especifique os seguintes valores:

    * **FORMATO DO SERIALIZADOR DE EVENTO**: JSON
    * **CODIFICAÇÃO**: UTF8

7.  Clique no botão de **SELEÇÃO** para adicionar essa fonte e verificar se o Stream Analytics pode se conectar com êxito ao hub de eventos.

### <a name="specify-job-query"></a>Especifique a consulta de trabalho

O Stream Analytics dá suporte a um modelo de consulta simples e declarativo que descreve as transformações. Para saber mais sobre a linguagem, consulte a [Referência de linguagem de consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Este tutorial o ajudará a criar e testar várias consultas sobre dados do Twitter.

#### <a name="sample-data-input"></a>Entrada de dados de exemplo

Para validar sua consulta em relação aos dados do trabalho real, você pode usar o recurso de **DADOS DE EXEMPLO** para extrair eventos de seu fluxo e criar um arquivo .json dos eventos para teste.

1.  Selecione a entrada do seu hub de eventos e clique em **DADOS DE EXEMPLO** na parte inferior da página.
2.  Na caixa de diálogo que é aberta, especifique uma **HORA DE INÍCIO** para iniciar a coleta de dados e uma **DURAÇÃO** para indicar quantos dados extras devem ser consumidos.
3.  Clique no botão **DETALHES** e clique no link **Clique aqui** para baixar e salvar o arquivo .json gerado.

#### <a name="pass-through-query"></a>Consulta de passagem
Para começar, faremos uma consulta de passagem simples que projeta todos os campos em um evento.

1.  Clique em **CONSULTA** na parte superior da página do trabalho de Stream Analytics.
2.  No editor de código, substitua o modelo de consulta inicial pelo seguinte:

        SELECT * FROM TwitterStream

    Certifique-se de que o nome da fonte de entrada corresponda ao nome da entrada que você especificou anteriormente.

3.  Clique em **Teste** no editor de consultas.
4.  Vá até seu arquivo .json de exemplo.
5.  Clique no botão de **SELEÇÃO** e veja os resultados abaixo da definição de consulta.

    ![Resultados exibidos abaixo da definição de consulta](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### <a name="count-of-tweets-by-topic:-tumbling-window-with-aggregation"></a>Contagem de tweets por tópico: janela em cascata com agregação

Para comparar o número de menções entre tópicos, usaremos uma [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obter a contagem de menções por tópico a cada cinco segundos.

1.  Altere a consulta no editor de código para:

        SELECT System.Timestamp as Time, Topic, COUNT(*)
        FROM TwitterStream TIMESTAMP BY CreatedAt
        GROUP BY TUMBLINGWINDOW(s, 5), Topic

    Essa consulta usa a palavra-chave **TIMESTAMP BY** para especificar um campo de carimbo de data/hora na carga a ser usada na computação temporal. Se esse campo não fosse especificado, a operação em janela seria realizada usando a hora em que cada evento chegou ao hub de eventos.  Saiba mais na seção "Hora de chegada versus hora do aplicativo" na [Referência de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    A consulta também acessa um carimbo de data/hora para o final de cada janela usando a propriedade **System.Timestamp**.

2.  Clique em **Executar novamente** no editor de consultas para ver os resultados da consulta.

#### <a name="identify-trending-topics:-sliding-window"></a>Identificar os tópicos mais populares: janela deslizante

Para identificar os tópicos mais populares, procuraremos tópicos que ultrapassam um valor limite de menções em determinado período de tempo. Neste tutorial, verificaremos os tópicos mencionados mais de 20 vezes nos últimos cinco segundos usando uma [SlidingWindow](https://msdn.microsoft.com/library/azure/dn835051.aspx).

1.  Altere a consulta no editor de consulte para: SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions     FROM TwitterStream TIMESTAMP BY CreatedAt     GROUP BY SLIDINGWINDOW(s, 5), topic     HAVING COUNT(*) > 20

2.  Clique em **Executar novamente** no editor de consultas para ver os resultados da consulta.

    ![Saída de consulta de Janela Deslizante](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### <a name="count-of-mentions-and-sentiment:-tumbling-window-with-aggregation"></a>Contagem de menções e sentimento: janela em cascata com agregação
A consulta final que testaremos usa uma **TumblingWindow** para obter o número de menções e a média, o mínimo, o máximo e o desvio padrão da pontuação de sentimento de cada tópico a cada cinco segundos.

1.  Altere a consulta no editor de código para:

        SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),
        Max(SentimentScore), STDEV(SentimentScore)
        FROM TwitterStream TIMESTAMP BY CreatedAt
        GROUP BY TUMBLINGWINDOW(s, 5), Topic

2.  Clique em **Executar novamente** no editor de consultas para ver os resultados da consulta.
3.  Esta é a consulta que usaremos para nosso painel.  Na parte inferior da página, clique em **SALVAR** .


## <a name="create-output-sink"></a>Criar coletor de saída

Agora que definimos um fluxo de eventos, uma entrada de hub de eventos para a ingestão de eventos e uma consulta para executar uma transformação no fluxo, a última etapa é definir um coletor de saída para o trabalho.  Gravaremos os eventos de tweets agregados de nossa consulta de trabalho para o armazenamento de Blobs do Azure.  Você também pode enviar por push os resultados para um Banco de Dados SQL do Azure, um armazenamento de Tabelas do Azure ou para Hubs de Eventos, dependendo das suas necessidades de aplicativo específicas.

Use as etapas abaixo para criar um contêiner para o armazenamento de Blobs, se ainda não tiver um:

1.  Use uma conta de armazenamento existente ou crie uma nova conta de armazenamento clicando em **NOVO** > **SERVIÇOS DE DADOS** > **ARMAZENAMENTO** > **CRIAÇÃO RÁPIDA** e seguindo as instruções na tela.
2.  Selecione a conta de armazenamento, clique em **CONTÊINERES** na parte superior da página e clique em **ADICIONAR**.
3.  Especifique um **NOME** para seu contêiner e defina seu **ACESSO** como **Blob Público**.

## <a name="specify-job-output"></a>Especifique a saída do trabalho

1.  No trabalho de Stream Analytics, clique em **SAÍDA** na parte superior da página e clique em **ADICIONAR SAÍDA**. A caixa de diálogo aberta orientará você por várias etapas para configurar sua saída.
2.  Clique em **ARMAZENAMENTO DE BLOBS** e clique no botão à direita.
3.  Digite ou selecione os seguintes valores na terceira página:

    * **ALIAS DE SAÍDA**: insira um nome amigável para essa saída de trabalho.

    * **ASSINATURA**: se o armazenamento de Blobs que você criou estiver na mesma assinatura que o trabalho do Stream Analytics, clique em **Usar Conta de Armazenamento da Assinatura Atual**. Se o armazenamento estiver em uma assinatura diferente, clique em **Usar Conta de Armazenamento de Outra Assinatura** e insira manualmente as informações para a **CONTA DE ARMAZENAMENTO**, a **CHAVE DA CONTA DE ARMAZENAMENTO** e o **CONTÊINER**.

    * **CONTA DE ARMAZENAMENTO**: selecione o nome da conta de armazenamento.

    * **CONTÊINER**: selecione o nome do contêiner.

    * **PREFIXO DE NOME DE ARQUIVO**: digite um prefixo de arquivo a ser usado durante a gravação da saída do blob.

4.  Clique no botão direito.
5.  Especifique os seguintes valores:
    * **FORMATO DO SERIALIZADOR DE EVENTO**: JSON
    * **CODIFICAÇÃO**: UTF8
6.  Clique no botão de **SELEÇÃO** para adicionar essa fonte e verificar se o Stream Analytics pode se conectar com êxito à conta de armazenamento.

## <a name="start-job"></a>Iniciar trabalho

Como uma entrada de trabalho, uma consulta e uma saída foram especificadas, estamos prontos para iniciar o trabalho de Stream Analytics.

1.  No trabalho **PAINEL**, clique em **INICIAR** na parte inferior da página.
2.  Na caixa de diálogo que será exibida, selecione **HORA DE INÍCIO DO TRABALHO** e clique no botão de **SELEÇÃO** na parte inferior da caixa de diálogo. O status do trabalho será alterado para **Iniciando** e logo mudará para **Em execução**.


## <a name="view-output-for-sentiment-analysis"></a>Exibir saída para análise sentimento

Depois que o trabalho estiver executando e processando o fluxo do Twitter em tempo real, escolha como você deseja exibir a saída para análise de sentimento. Use uma ferramenta como o [Gerenciador de Armazenamento do Azure](https://azurestorageexplorer.codeplex.com/) ou o [Gerenciador do Azure](http://www.cerebrata.com/products/azure-explorer/introduction) para exibir a saída do trabalho em tempo real. Aqui, você pode usar o [Power BI](https://powerbi.com/) estender seu aplicativo para incluir um painel personalizado como o mostrado na seguinte captura de tela.

![Análise de mídia social: resultado da análise de fluxo sentimento análise (mineração de opinião) em um painel do Power BI.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## <a name="get-support"></a>Obtenha suporte
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)


## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)



<!--HONumber=Oct16_HO2-->


