<properties
	pageTitle="Stream Analytics: detecção de fraudes em tempo real | Microsoft Azure"
	description="Aprenda a criar uma solução para detecção de fraudes em tempo real com a Stream Analytics. Use um hub de eventos para o processamento de eventos em tempo real."
	keywords="detecção de anomalias, detecção de fraudes, detecção de anomalias em tempo real"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun" />

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="09/26/2016"
	ms.author="jeffstok" />



# Introdução ao uso de Stream Analytics do Azure: detecção de fraudes em tempo real

Aprenda a criar uma solução de ponta a ponta para detecção de fraudes em tempo real com o Stream Analytics da Azure. Exibir eventos nos Hubs de eventos do Azure, escrever consultas de Stream Analytics para agregação ou alerta e enviar os resultados em um coletor de saída para obter informações sobre os dados com processamento em tempo real. A detecção de anomalias em tempo real para telecomunicações é abrangida, mas a técnica de exemplo é igualmente adequada para outros tipos de detecção de fraudes, como cenários de roubo de identidade ou de cartão de crédito.

O Stream Analytics é um serviço completamente gerenciado que oferece baixa latência, alta disponibilidade e processamento escalonável de eventos complexos por streaming de dados na nuvem. Para obter mais informações, consulte [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md).


## Cenário: detecção de fraudes de telecomunicações e SIM em tempo real

Uma empresa de telecomunicações tem um grande volume de dados para as chamadas de entrada. A empresa precisa do seguinte de seus dados:
* Reduzir esses dados a um valor gerenciável e obter informações sobre o uso do cliente ao longo do tempo e as regiões geográficas.
* Detectar fraudes de SIM (várias chamadas vindas da mesma identidade ao mesmo tempo, mas em locais geograficamente diferentes) em tempo real para que possam responder facilmente notificando os clientes ou desligando o serviço.

Nos cenários da Internet das Coisas (IoT) canônica onde há uma tonelada de telemetria ou dados de sensor que estão sendo gerados – e os clientes desejam agregá-los ou alertar sobre anomalias em tempo real.

## Pré-requisitos

- Baixar [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) do Centro de Download da Microsoft
- Opcional: código-fonte do gerador de evento do [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator)

## Criar uma entrada de Hubs de Eventos do Azure e um Grupo de Consumidores

O aplicativo de exemplo gerará eventos e os enviará por push a uma instância do Hub de eventos para processamento em tempo real. Os Hubs de evento do Barramento de Serviço são o método preferencial de ingestão de eventos para Stream Analytics e você pode aprender mais sobre os Hubs de eventos na [Documentação do barramento de serviço do Azure](/documentation/services/service-bus/).

Para criar um Hub de Evento:

1.	No [Portal do Azure](https://manage.windowsazure.com/) clique em **Novo** > **Serviços de Aplicativos** > **Barramento de Serviço** > **Hub de eventos** > **Criação rápida**. Forneça um nome, uma região e um namespace novo ou existente para criar um novo Hub de eventos.
2.	Como prática recomendada, cada trabalho de Stream Analytics deve ser lido por meio de um único Grupo de Consumidores de Hub de Eventos. Vamos orientá-lo abaixo ao longo do processo de criação de um Grupo de Consumidores, e você poderá [saber mais sobre esses Grupos de Consumidores](https://msdn.microsoft.com/library/azure/dn836025.aspx). Para criar um Grupo de Consumidores, navegue até o Hub de Eventos recém-criado e clique na guia **Grupos de Consumidores**. Em seguida, clique em **Criar** na parte inferior da página e forneça um nome para o seu Grupo de Consumidores.
3.	Para conceder acesso ao Hub de Eventos, precisamos criar uma política de acesso compartilhado. Clique na guia **Configurar** de seu Hub de Eventos.
4.	Em **Políticas de acesso compartilhado**, crie uma nova política com permissões para **Gerenciar**.

	![Políticas de Acesso Compartilhado em que você pode criar uma política com permissões para Gerenciar.](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-shared-access-policies.png)

5.	Na parte inferior da página, clique em **Salvar**.
6.	Navegue até o **Painel**, clique em **Informações de Conexão** na parte inferior da página e copie e salve as informações de conexão.

## Configurar e iniciar o aplicativo gerador de evento

Nós fornecemos um aplicativo cliente que gerará metadados de chamadas de entrada de exemplo e os enviará por push ao Hub de eventos. Siga as etapas abaixo para configurar este aplicativo.

1.	Baixe o [arquivo TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip). Em seguida, descompacte-o em um diretório.

    **Observação**: o Windows pode bloquear o arquivo zip baixado. Clique com o botão direito do mouse no arquivo e selecione Propriedades. Se a mensagem "Este arquivo veio de outro computador e pode ser bloqueado para ajudar a proteger este computador" for exibida, marque a caixa "Desbloquear" e clique em Aplicar no arquivo zip.

2.	Substitua os valores Microsoft.ServiceBus.ConnectionString e EventHubName em **telcodatagen.exe.config** pela cadeia de conexão e pelo nome do Hub de Eventos.

    **Observação**: a cadeia de conexão copiada do Portal do Azure coloca o nome da conexão no final. Certifique-se de remover a ";EntityPath=<value>" da chave de adição = campo.

3.	Inicie o aplicativo. A utilização é o seguinte:

   telcodatagen.exe [#NumCDRsPerHour] [Probabilidade de Fraude do Cartão SIM] [#DurationHours]

O exemplo a seguir gerará 1000 eventos com uma probabilidade de 20% de fraude ao longo de 2 horas.

    telcodatagen.exe 1000 .2 2

Você verá os registros que estão sendo enviados para o Hub de eventos. Alguns campos-chave que vamos usar neste aplicativo de detecção de fraudes em tempo real são definidos aqui:

| Registro | Definição |
| ------------- | ------------- |
| CallrecTime | Carimbo de data/hora para a hora de início da chamada. |
| SwitchNum | Chave do telefone usada para se conectar à chamada. |
| CallingNum | Número de telefone do autor da chamada. |
| CallingIMSI | Identidade do Assinante Móvel Internacional (IMSI). Identificador exclusivo do autor da chamada. |
| CalledNum | Número de telefone do destinatário da chamada. |
| CalledIMSI | Identidade do Assinante Móvel Internacional (IMSI). Identificador exclusivo do destinatário da chamada. |


## Criar um trabalho de Stream Analytics
Agora que temos um fluxo de eventos de telecomunicações, podemos configurar um trabalho de Stream Analytics para analisar esses eventos em tempo real.

### Provisionar um trabalho de análise de fluxo

1.	No **Portal do Azure, clique em Novo > Serviços de Dados > Stream Analytics > Criação Rápida**.
2.	Especifique os seguintes valores e, em seguida, clique em **Criar trabalho de Stream Analytics**:

	* **Nome do Trabalho**: Insira um nome de trabalho.

	* **Região**: Selecione a região onde você deseja executar o trabalho. Considere a opção de colocar o trabalho e o hub de eventos na mesma região para garantir um desempenho melhor e assegurar que você não pague para transferir dados entre regiões.

	* **Conta de Armazenamento**: escolha a Conta de Armazenamento do Azure que você deseja usar para armazenar os dados de monitoramento de todos os trabalhos do Stream Analytics executados nesta região. Você tem a opção de escolher uma conta de armazenamento existente ou criar uma nova.

3.	Clique em **Stream Analytics** no painel esquerdo para listar os trabalhos do Stream Analytics.

	![Ícone do serviço Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-service-icon.png)

4.	O novo trabalho será mostrado com um status de **Criado**. Observe que o botão **Iniciar** na parte inferior da página está desabilitado. Você deve configurar a entrada, a saída, a consulta do trabalho e assim por diante antes de iniciar o trabalho.

### Especificar entrada de trabalho
1.	No trabalho de Stream Analytics, clique em **Entradas** na parte superior da página e clique em **Adicionar entrada**. A caixa de diálogo que se abre orientará você por algumas etapas para configurar sua entrada.
2.	Selecione **Fluxo de Dados** e então clique no botão direito.
3.	Selecione **Hub de Eventos** e então clique no botão direito.
4.	Digite ou selecione os seguintes valores na terceira página:

	* **Alias de entrada**: Insira um nome amigável para a entrada do trabalho, como *CallStream*. Observe que você usará esse nome na consulta posteriormente.
	* **Hub de Eventos**: se o Hub de Eventos que você criou estiver na mesma assinatura que o trabalho do Stream Analytics, selecione o namespace que contém o hub de eventos.

	Se o hub de eventos estiver em uma assinatura diferente, selecione **Usar Hub de Eventos de Outra Assinatura** e insira manualmente as informações para o **Namespace do Barramento de Serviço**, o **Nome do Hub de Eventos**, o **Nome da Política do Hub de Eventos**, a **Chave de Política do Hub de Eventos** e a **Contagem de Partições do Hub de Eventos**.

	* **Nome do Hub de Eventos**: selecione o nome do Hub de Eventos.

	* **Nome de política do hub de eventos**: Selecione a política de hub de eventos criada anteriormente neste tutorial.

	* **Grupo de Consumidores do Hub de Eventos**: digite o Grupo de Consumidores criado anteriormente neste tutorial.
5.	Clique no botão direito.
6.	Especifique os seguintes valores:

	* **Formato do Serializador de Evento**: JSON
	* **Codificação**: UTF8
7.	Clique no botão de verificação para adicionar essa fonte e para verificar se o Stream Analytics pode se conectar com êxito ao hub de eventos.

### Especifique a consulta de trabalho

O Stream Analytics oferece suporte a um modelo de consulta simples e declarativo para descrever as transformações. Para saber mais sobre a linguagem, consulte a [Referência de linguagem de consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/dn834998.aspx). Este tutorial o ajudará a criar e testar várias consultas sobre o fluxo de dados em tempo real de chamada.

#### Opcional: Dados de entrada de exemplo
Para validar sua consulta em relação aos dados do trabalho real, você pode usar o recurso de **Dados de Exemplo** para extrair eventos de seu fluxo e criar um arquivo .JSON dos eventos para teste. As etapas a seguir mostram como fazer isso e também fornecemos um arquivo [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) de exemplo para teste.

1.	Selecione a entrada do seu Hub de Eventos e clique em **Dados de exemplo** na parte inferior da página.
2.	Na caixa de diálogo que será exibida, especifique uma **Hora de Início** para iniciar a coleta de dados e uma **Duração** para indicar quantos dados extras devem ser consumidos.
3.	Clique no botão de verificação para iniciar os dados de amostragem da entrada. Pode levar um ou dois minutos para o arquivo de dados ser produzido. Quando o processo estiver concluído, clique em **Detalhes**, baixe e salve o arquivo .JSON gerado.

	![Baixe e salve os dados processados em um arquivo JSON](./media/stream-analytics-real-time-fraud-detection/stream-analytics-download-save-json-file.png)

#### Consulta de passagem

Se deseja arquivar todos os eventos, você pode usar uma consulta de passagem para ler todos os campos na carga do evento ou da mensagem. Para começar, faça uma consulta de passagem simples que projete todos os campos em um evento.

1.	Clique em **Consulta** na parte superior da página do trabalho de Stream Analytics.
2.	Adicione o seguinte ao editor de código:

		SELECT * FROM CallStream

	> Certifique-se de que o nome da fonte de entrada corresponde ao nome da entrada que você especificou anteriormente.

3.	Clique em **Teste** no editor de consultas.
4.	Forneça um arquivo de teste, que poderá ser o criado nas etapas anteriores, ou use o [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/SampleDataFiles/Telco.json).
5.	Clique no botão verificar e veja o resultado exibido abaixo da definição de consulta.

	![Resultados da definição de consulta](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sim-fraud-output.png)


### Projeção de coluna

Agora diminuiremos os campos retornados para um conjunto menor.

1.	Altere a consulta no editor de código para:

		SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum
		FROM CallStream

2.	Clique em **Executar novamente** no editor de consultas para ver os resultados da consulta.

	![Saída no editor de consultas.](./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-output.png)

### Contagem de chamadas de entrada por região: janela em cascata com agregação

Para comparar a quantidade de chamadas de entrada por região, aproveitaremos uma [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obter a contagem de chamadas de entrada agrupadas por SwitchNum a cada 5 segundos.

1.	Altere a consulta no editor de código para:

		SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount
		FROM CallStream TIMESTAMP BY CallRecTime
		GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

	Essa consulta usa a palavra-chave **Timestamp By** para especificar um campo de carimbo de data/hora na carga a ser usada na computação temporal. Se esse campo não fosse especificado, a operação em janela seria realizada usando a hora em que cada evento chegou ao Hub de Eventos. Consulte ["Hora de chegada versus hora do aplicativo" na Referência de linguagem de consulta de Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).

	Observe que você pode acessar um carimbo de data/hora para o final de cada janela usando a propriedade **System.Timestamp**.

2.	Clique em **Executar novamente** no editor de consultas para ver os resultados da consulta.

	![Resultados da consulta para Timestand By](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-rerun.png)

### Detecção de fraudes SIM com uma associação a si mesmo

Para identificar o uso potencialmente fraudulento, vamos observar as chamadas originadas do mesmo usuário, mas em locais diferentes em menos de 5 segundos. Nós [ingressamos](https://msdn.microsoft.com/library/azure/dn835026.aspx) no fluxo de eventos de chamada com ele próprio para verificar esses casos.

1.	Altere a consulta no editor de código para:

		SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
		CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
		FROM CallStream CS1 TIMESTAMP BY CallRecTime
		JOIN CallStream CS2 TIMESTAMP BY CallRecTime
		ON CS1.CallingIMSI = CS2.CallingIMSI
		AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
		WHERE CS1.SwitchNum != CS2.SwitchNum

2.	Clique em **Executar novamente** no editor de consultas para ver os resultados da consulta.

	![Resultados da consulta de uma associação](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-join.png)

### Criar coletor de saída

Agora que definimos um fluxo de eventos, uma entrada de Hub de Eventos para a ingestão de eventos e uma consulta para executar uma transformação no fluxo, a última etapa é definir um coletor de saída para o trabalho. Vamos escrever eventos para comportamento fraudulento no armazenamento de Blob.

Siga as etapas abaixo para criar um contêiner para o armazenamento de Blob, se ainda não tiver um.

1.	Use uma conta de armazenamento existente ou crie uma nova conta de armazenamento clicando em **NOVO > SERVIÇOS DE DADOS > ARMAZENAMENTO > CRIAÇÃO RÁPIDA** e seguindo as instruções.
2.	Selecione a conta de armazenamento, clique em **CONTÊINERES** na parte superior da página e clique em **ADICIONAR**.
3.	Especifique um **NOME** para seu contêiner e defina seu **ACESSO** como Blob Público.

## Especifique a saída do trabalho

1.	No trabalho de Stream Analytics, clique em **SAÍDA** na parte superior da página e depois em **ADICIONAR SAÍDA**. A caixa de diálogo aberta orientará você por algumas etapas para configurar sua saída.
2.	Selecione **ARMAZENAMENTO DE BLOB** e, em seguida, clique no botão à direita.
3.	Digite ou selecione os seguintes valores na terceira página:

	* **ALIAS DE SAÍDA**: insira um nome amigável para essa saída de trabalho.
	* **ASSINATURA**: se o Armazenamento de Blobs que você criou estiver na mesma assinatura que o trabalho do Stream Analytics, selecione **Usar Conta de Armazenamento da Assinatura Atual**. Se o armazenamento estiver em uma assinatura diferente, selecione **Usar Conta de Armazenamento de Outra Assinatura** e insira manualmente as informações para a **CONTA DE ARMAZENAMENTO**, a **CHAVE DE CONTA DE ARMAZENAMENTO** e o **CONTÊINER**.
	* **CONTA DE ARMAZENAMENTO**: selecione o nome da conta de armazenamento.
	* **CONTÊINER**: selecione o nome do contêiner.
	* **PREFIXO DE NOME DE ARQUIVO**: digite um prefixo de arquivo a ser usado durante a gravação da saída do blob.

4.	Clique no botão direito.
5.	Especifique os seguintes valores:

	* **FORMATO DO SERIALIZADOR DE EVENTO**: JSON
	* **CODIFICAÇÃO**: UTF8

6.	Clique no botão de seleção para adicionar essa fonte e verificar se o Stream Analytics pode se conectar com êxito à conta de armazenamento.

## Inicie o trabalho para processamento em tempo real

Visto que uma entrada de trabalho, uma consulta e uma saída foram especificadas, estamos prontos para iniciar o trabalho de Stream Analytics para uma detecção de fraude em tempo real.

1.	No trabalho **PAINEL**, clique em **INICIAR** na parte inferior da página.
2.	Na caixa de diálogo que será exibida, selecione **HORA DE INÍCIO DO TRABALHO** e clique no botão de seleção na parte inferior da caixa de diálogo. O status do trabalho será alterado para **Iniciando** e logo mudará para **Em execução**.

## Exiba a saída de detecção de fraudes

Use uma ferramenta como o [Azure Storage Explorer](http://storageexplorer.com/) ou o [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) para exibir eventos fraudulentos conforme eles forem gravados para a sua saída em tempo real.

![Detecção de fraudes: eventos fraudulentos exibidos em tempo real](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-view-real-time-fraudent-events.png)

## Obtenha suporte
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=AzureStreamAnalytics)


## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0928_2016-->