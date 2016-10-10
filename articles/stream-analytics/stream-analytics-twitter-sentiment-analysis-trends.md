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


# Análise de mídia social: análise de sentimento do Twitter em tempo real na Stream Analytics do Azure

Aprenda a compilar uma solução de análise de sentimento para análise de mídia social colocando os eventos em tempo real do Twitter nos Hubs de Eventos. Você escreverá uma consulta do Stream Analytics para analisar os dados e, em seguida, armazenar os resultados para uma leitura minuciosa posterior ou usar um painel para fornecer percepções em tempo real usando o [Power BI](https://powerbi.com/).

As ferramentas de análise de mídias sociais ajudam as organizações a compreender os tópicos mais comentados, os assuntos relevantes e as atitudes com uma alta quantidade de postagens em mídias sociais. Análise de sentimento - também chamado de "mineração opinião" - usa as ferramentas de análise de mídia social para determinar as atitudes em direção a um produto, ideia e assim por diante. A análise de tendência do Twitter em tempo real é um ótimo exemplo porque o modelo de assinatura com hashtag permite que você ouça palavras-chave específicas e desenvolva a análise de sentimento no feed.

## Cenário - análise de sentimento em tempo real

Um site de mídia de notícias está interessado em obter uma vantagem sobre seus concorrentes apresentando conteúdo do site que é imediatamente relevante para seus leitores. Eles usam a análise de mídia social sobre os tópicos relevantes para seus leitores, fazendo uma análise de sentimento em tempo real nos dados do Twitter. Especificamente, para identificar quais são os tópicos mais populares, eles precisam da análise em tempo real no Twitter sobre o volume de tweets e de sentimento para os tópicos principais. Então, essencialmente eles precisam de um mecanismo de análise para análise de sentimento baseado nesse feed de mídia social.

## Pré-requisitos
1.	Conta do Twitter e [token de acesso do OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
2.	[TwitterClient.zip](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip) do Centro de Download da Microsoft
3.	Opcional: código-fonte do cliente do Twitter do [GitHub](https://aka.ms/azure-stream-analytics-twitterclient)

## Criar uma entrada de Hub de Eventos e um Grupo de Consumidores

O aplicativo de exemplo gerará eventos e os enviará a uma instância de Hubs de Eventos (um Hub de Eventos, de forma abreviada). Os Hubs de Eventos do Barramento de Serviço são o método preferencial de ingestão de eventos para Stream Analytics. Consulte a documentação sobre Hubs de Eventos na [documentação do Barramento de Serviço](/documentation/services/service-bus/)

Siga as etapas abaixo para criar um Hub de Eventos.

1.	No Portal do Azure, clique em **NOVO** > **SERVIÇOS DE APLICATIVOS** > **BARRAMENTO DE SERVIÇO** > **HUB DE EVENTOS** > **CRIAÇÃO RÁPIDA** e forneça um nome, uma região e um namespace novo ou existente para criar um novo Hub de Eventos.
2.	Como prática recomendada, cada trabalho de Stream Analytics deve ser lido de um único Grupo de Consumidores de Hubs de Eventos. Vamos orientá-lo ao longo do processo de criação de um Grupo de Consumidores abaixo, e você poderá saber mais sobre eles aqui. Para criar um Grupo de Consumidores, navegue até o Hub de Eventos recém-criado e clique na guia **GRUPOS DE CONSUMIDORES**. Em seguida, clique em **CRIAR** na parte inferior da página e forneça um nome para o Grupo de Consumidores.
3.	Para conceder acesso ao Hub de Eventos, precisamos criar uma política de acesso compartilhado. Clique na guia **CONFIGURAR** de seu Hub de Eventos.
4.	Em **POLÍTICAS DE ACESSO COMPARTILHADO**, crie uma nova política com permissões para **GERENCIAR**.


  	![Políticas de Acesso Compartilhado em que você pode criar uma política com permissões para Gerenciar.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)

5.	Na parte inferior da página, clique em **SALVAR**.
6.	Navegue até o **PAINEL**, clique em **INFORMAÇÕES DE CONEXÃO** na parte inferior da página e copie e salve as informações de conexão. (Use o ícone de cópia que aparece sob o ícone de pesquisa).

## Configurar e iniciar o aplicativo de cliente do Twitter

Fornecemos um aplicativo de cliente que acessará os dados do Twitter por meio das [APIs de Transmissão Contínua do Twitter](https://dev.twitter.com/streaming/overview) para coletar eventos de Tweets sobre um conjunto parametrizado de tópicos. A ferramenta de software livre de terceiros [Sentiment140](http://help.sentiment140.com/) é usada para atribuir um valor de sentimento a cada tweet (0: negativo, 2: neutro, 4: positivo) e, em seguida, os eventos de Tweets são enviados ao Hub de Eventos.

Siga estas etapas para configurar o aplicativo:

1.	[Baixar a solução TwitterClient](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip)
2.	Abra o TwitterClient.exe.config e substitua oauth\_consumer\_key, oauth\_consumer\_secret, oauth\_token e oauth\_token\_secret por tokens do Twitter pelos seus valores.

	[Etapas para gerar um token de acesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)

	Observe que você precisa criar um aplicativo vazio para gerar um token.
3.	Substitua os valores de EventHubConnectionString e EventHubName em TwitterClient.exe.config pela cadeia de conexão e o nome do Hub de Eventos. A cadeia de conexão que você copiou anteriormente fornece a você tanto o nome quanto a cadeia de conexão do Hub de eventos, então esteja ciente da necessidade de separá-los e colocar cada um no campo correto. Por exemplo, dada uma cadeia de conexão:

    Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub

	O arquivo TwitterClient.exe.config deve conter as configurações, como no exemplo a seguir:

	add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey" add key="EventHubName" value="yourhub"

	É importante observar que o texto "EntityPath =" NÃO aparece no valor EventHubName.
	
4.	*Opcional:* ajuste as palavras-chave a serem pesquisadas. Por padrão, esse aplicativo procura por "Azure,Skype,XBox,Microsoft,Seattle". Você poderá ajustar os valores de twitter\_keywords em TwitterClient.exe.config, se desejar.
5.	Executar o **TwitterClient.exe** para iniciar o aplicativo. Você verá eventos de Tweet com os valores CreatedAt, Topic e SentimentScore sendo enviados ao Hub de Eventos:

	![Análise de sentimento: valores de SentimentScore enviados para um hub de eventos.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## Criar um trabalho de Stream Analytics

Agora que temos eventos de Tweets, podemos configurar, em tempo real, um trabalho de Stream Analytics para analisar esses eventos em tempo real.

### Provisionar um trabalho de análise de fluxo

1.	No [Portal do Azure](https://manage.windowsazure.com/), clique em **NOVO** > **SERVIÇOS DE DADOS** > **STREAM ANALYTICS** > **CRIAÇÃO RÁPIDA**.
2.	Especifique os seguintes valores e, em seguida, clique em **CRIAR TRABALHO DE STREAM ANALYTICS**:

	* **NOME DO TRABALHO**: insira um nome de trabalho.
	* **REGIÃO**: selecione a região em que você deseja executar o trabalho. Considere a opção de colocar o trabalho e o hub de eventos na mesma região para garantir um desempenho melhor e assegurar que você não pague para transferir dados entre regiões.
	* **CONTA DE ARMAZENAMENTO**: escolha a conta de armazenamento que você deseja usar para armazenar dados de monitoramento de todos os trabalhos de Stream Analytics em execução nessa região. Você tem a opção de escolher uma conta de armazenamento existente ou criar uma nova.

3.	Clique em **STREAM ANALYTICS** no painel à esquerda para listar os trabalhos de Stream Analytics. ![Ícone do serviço Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)

4.	O novo trabalho será mostrado com um status de **CRIADO**. Observe que o botão **INICIAR**, na parte inferior da página, está desabilitado. Você deve configurar a entrada, a saída, a consulta do trabalho e assim por diante antes de iniciar o trabalho.


### Especificar entrada de trabalho
1.	No trabalho de Stream Analytics, clique em **ENTRADAS** na parte superior da página e clique em **ADICIONAR ENTRADA**. A caixa de diálogo que será aberta o orientará ao longo de uma série de etapas para configurar sua entrada.
2.	Selecione **FLUXO DE DADOS**, e, em seguida, clique no botão à direita.
3.	Selecione o **HUB DE EVENTOS** e, em seguida, clique no botão à direita
4.	Digite ou selecione os seguintes valores na terceira página:

	* **ALIAS DE ENTRADA**: insira um nome amigável para a entrada do trabalho, como TwitterStream. Observe que você usará esse nome na consulta posteriormente. **HUB DE EVENTOS**: se o Hub de Eventos que você criou estiver na mesma assinatura que o trabalho do Stream Analytics, selecione o namespace que contém o hub de eventos.

		Se o hub de eventos estiver em uma assinatura diferente, selecione **Usar Hub de Eventos de Outra Assinatura** e insira manualmente as informações para o **NAMESPACE DO BARRAMENTO DE SERVIÇO**, o **NOME DO HUB DE EVENTOS**, o **NOME DA POLÍTICA DO HUB DE EVENTOS**, a **CHAVE DE POLÍTICA DO HUB DE EVENTOS** e a **CONTAGEM DE PARTIÇÕES DO HUB DE EVENTOS**.

	* **NOME DO HUB DE EVENTOS**: selecione o nome do Hub de Eventos
	* **NOME DE POLÍTICA DO HUB DE EVENTOS**: selecione a política de hub de eventos criada anteriormente neste tutorial.
	* **GRUPO DE CONSUMIDORES DO HUB DE EVENTOS**: digite o Grupo de Consumidores criado anteriormente neste tutorial.
5.	Clique no botão direito.
6.	Especifique os seguintes valores:

	* **FORMATO DO SERIALIZADOR DE EVENTO**: JSON
	* **CODIFICAÇÃO**: UTF8

7.	Clique no botão de seleção para adicionar essa fonte e verificar se o Stream Analytics pode se conectar com êxito ao hub de eventos.

### Especifique a consulta de trabalho

A Análise de fluxo dá suporte a um modelo de consulta simples e declarativo para descrever as transformações. Para saber mais sobre a linguagem, consulte a [Referência de linguagem de consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx). Este tutorial o ajudará a criar e testar várias consultas sobre dados do Twitter.

#### Entrada de dados de exemplo

Para validar sua consulta em relação aos dados do trabalho real, você pode usar o recurso de DADOS DE EXEMPLO para extrair eventos de seu fluxo e criar um arquivo .JSON dos eventos para teste.

1.	Selecione a entrada de seu Hub de Eventos e clique em **DADOS DE EXEMPLO** na parte inferior da página.
2.	Na caixa de diálogo que será exibida, especifique uma **HORA DE INÍCIO** para iniciar a coleta de dados e uma **DURAÇÃO** para indicar quantos dados adicionais devem ser consumidos.
3.	Clique no botão **DETALHES** e, em seguida, clique no link **Clique aqui** para fazer baixar e salvar o arquivo .JSON que será gerado.

#### Consulta de passagem
Para começar, faremos uma consulta de passagem simples que projeta todos os campos em um evento.

1.	Clique em **CONSULTA** na parte superior da página do trabalho de Stream Analytics.
2.	No editor de código, substitua o modelo de consulta inicial pelo seguinte:

		SELECT * FROM TwitterStream

	Certifique-se de que o nome da fonte de entrada corresponde ao nome da entrada que você especificou anteriormente.

3.	Clique em **TESTE** no editor de consultas
4.	Navegue até seu arquivo .JSON de exemplo
5.	Clique no botão verificar e veja o resultado exibido abaixo da definição de consulta.

	![Resultados exibidos abaixo da definição de consulta](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### Contagem de tweets por tópico: janela em cascata com agregação

Para comparar o número de menções entre tópicos, usaremos uma [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obter a contagem de menções por tópico a cada cinco segundos.

1.	Altere a consulta no editor de código para:

		SELECT System.Timestamp as Time, Topic, COUNT(*)
		FROM TwitterStream TIMESTAMP BY CreatedAt
		GROUP BY TUMBLINGWINDOW(s, 5), Topic

	Observe que essa consulta usa a palavra-chave **TIMESTAMP BY** para especificar um campo de carimbo de data/hora na carga a ser usada na computação temporal. Se esse campo não fosse especificado, a operação em janela seria realizada usando a hora em que cada evento chegou ao Hub de Eventos. Saiba mais em "Hora de chegada versus hora do aplicativo" na [Referência de consulta de Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).

	Essa consulta também acessa um carimbo de data/hora para o fim de cada janela com **System.Timestamp**.

2.	Clique em **EXECUTAR NOVAMENTE** no editor de consultas para ver o resultado da consulta.

#### Identificando os tópicos mais populares: janela deslizante

Para identificar os tópicos mais populares, procuraremos tópicos que ultrapassam um valor limite de menções em determinado período de tempo. Neste tutorial, verificaremos os tópicos mencionados mais de 20 vezes nos últimos cinco segundos usando uma [SlidingWindow](https://msdn.microsoft.com/library/azure/dn835051.aspx).

1.	Altere a consulta no editor de código para:

		SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
		FROM TwitterStream TIMESTAMP BY CreatedAt
		GROUP BY SLIDINGWINDOW(s, 5), topic
		HAVING COUNT(*) > 20

2.	Clique em **EXECUTAR NOVAMENTE** no editor de consultas para ver o resultado da consulta.

	![Saída de consulta de Janela Deslizante](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### Contagem de menções e sentimento: janela em cascata com agregação

A consulta final que testaremos usa uma TumblingWindow para obter o número de menções e a média, o mínimo, o máximo e o desvio padrão da pontuação de sentimento de cada tópico a cada cinco segundos.

1.	Altere a consulta no editor de código para:

		SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),
    	Max(SentimentScore), STDEV(SentimentScore)
		FROM TwitterStream TIMESTAMP BY CreatedAt
		GROUP BY TUMBLINGWINDOW(s, 5), Topic

2.	Clique em **EXECUTAR NOVAMENTE** no editor de consultas para ver o resultado da consulta.
3.	Esta é a consulta que usaremos para nosso painel. Na parte inferior da página, clique em **SALVAR**.


## Criar coletor de saída

Agora que definimos um fluxo de eventos, uma entrada de Hub de Eventos para a ingestão de eventos e uma consulta para executar uma transformação no fluxo, a última etapa é definir um coletor de saída para o trabalho. Gravaremos os eventos de tweets agregados de nossa consulta de trabalho para um Blob do Azure. Você também pode enviar o resultado para um Banco de Dados SQL, um Repositório de Tabela ou um Hub de Eventos, dependendo das suas necessidades de aplicativo específicas.

Siga as etapas abaixo para criar um contêiner para o armazenamento de Blob, se ainda não tiver um:

1.	Use uma conta de Armazenamento existente ou crie uma nova conta de Armazenamento clicando em **NOVO** > **SERVIÇOS DE DADOS** > **ARMAZENAMENTO** > **CRIAÇÃO RÁPIDA** > e seguindo as instruções na tela.
2.	Selecione a conta de Armazenamento, clique em **CONTÊINERES** na parte superior da página e clique em **ADICIONAR**.
3.	Especifique um **NOME** para seu contêiner e defina seu **ACESSO** como Blob Público.

## Especifique a saída do trabalho

1.	No trabalho de Stream Analytics, clique em **SAÍDA** na parte superior da página e clique em **ADICIONAR SAÍDA**. A caixa de diálogo que será aberta o orientará ao longo de uma série de etapas para configurar a saída.
2.	Selecione **ARMAZENAMENTO DE BLOB** e, em seguida, clique no botão à direita.
3.	Digite ou selecione os seguintes valores na terceira página:

	* **ALIAS DE SAÍDA**: insira um nome amigável para essa saída de trabalho
	* **ASSINATURA**: se o Armazenamento de Blobs que você criou estiver na mesma assinatura que o trabalho do Stream Analytics, selecione **Usar Conta de Armazenamento da Assinatura Atual**. Se o armazenamento estiver em uma assinatura diferente, selecione **Usar Conta de Armazenamento de Outra Assinatura** e insira manualmente as informações para a **CONTA DE ARMAZENAMENTO**, a **CHAVE DE CONTA DE ARMAZENAMENTO** e o **CONTÊINER**.
	* **CONTA DE ARMAZENAMENTO**: selecione o nome da Conta de Armazenamento
	* **CONTÊINER**: selecione o nome do Contêiner
	* **PREFIXO DE NOME DE ARQUIVO**: digite um prefixo de arquivo a ser usado durante a gravação da saída do blob

4.	Clique no botão direito.
5.	Especifique os seguintes valores:
	* **FORMATO DO SERIALIZADOR DE EVENTO**: JSON
	* **CODIFICAÇÃO**: UTF8
6.	Clique no botão de seleção para adicionar essa fonte e verificar se o Stream Analytics pode se conectar com êxito à conta de armazenamento.

## Iniciar trabalho

Como uma entrada de trabalho, uma consulta e uma saída foram especificadas, estamos prontos para iniciar o trabalho de Stream Analytics.

1.	No trabalho **PAINEL**, clique em **INICIAR** na parte inferior da página.
2.	Na caixa de diálogo que será exibida, selecione **HORA DE INÍCIO DO TRABALHO** e clique no botão de marca de seleção na parte inferior da caixa de diálogo. O status do trabalho será alterado para **Iniciando** e logo mudará para **Em execução**.


## Exibir saída para análise sentimento

Depois que o trabalho estiver executando e processando, o fluxo do Twitter em tempo real, escolha como você deseja exibir a saída para análise sentimento. Use uma ferramenta como o [Gerenciador de Armazenamento do Azure](https://azurestorageexplorer.codeplex.com/) ou o [Gerenciador do Azure](http://www.cerebrata.com/products/azure-explorer/introduction) para exibir a saída do trabalho em tempo real. Daqui, você pode ampliar seu aplicativo para incluir um painel personalizado sobre a saída, como aquele mostrado abaixo usando o [Power BI](https://powerbi.com/).

![Análise de mídia social: resultado da análise de fluxo sentimento análise (mineração de opinião) em um painel do Power BI.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## Obtenha suporte
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=AzureStreamAnalytics)


## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=AcomDC_0928_2016-->