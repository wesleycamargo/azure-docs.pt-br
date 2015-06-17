<properties 
	pageTitle="Saiba mais sobre conceitos principais do Stream Analytics | Microsoft Azure" 
	description="Conheça os conceitos principais do Azure Stream Analytics: os componentes de um trabalho do Stream Analytics, inclusive com entradas e saídas com suporte, configuração do trabalho e métricas." 
	keywords="event processing,data stream,key concepts,serialization"	
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="04/28/2015" 
	ms.author="jeffstok" />


# Principais conceitos de análise de fluxo: guia dos fundamentos de um trabalho de análise de fluxo 

O Azure Stream Analytics é um serviço completamente gerenciado que oferece baixa latência, alta disponibilidade e processamento escalonável de eventos complexos por fluxo de dados na nuvem. O Stream Analytics permite que os clientes configurem trabalhos de streaming para analisar fluxos de dados e realizem análise quase em tempo real. Este artigo explica os principais conceitos de um trabalho de análise do Stream Analytics.

## O que você pode fazer no Stream Analytics?
Com o Stream Analytics, você pode:

- Executar o processamento de eventos complexos em fluxos de dados de alta velocidade e grande volume.   
- Coletar dados de eventos de ativos distribuídos globalmente ou equipamento como carros conectados ou grades de utilitário 
- Processar dados de telemetria para monitoramento e diagnóstico quase em tempo real 
- Capturar e arquivar eventos em tempo real para processamento futuro

Para obter mais informações, consulte [Introdução ao Stream Analytics do Azure](stream.analytics.introduction).

Um trabalho do Stream Analytics inclui o seguinte: * uma ou mais fontes de entrada * Uma consulta sobre um fluxo de dados de entrada * Um destino de saída.


## Entradas

### Fluxo de dados

Cada definição de trabalho de Análise de fluxo deve conter fonte entrada pelo menos um fluxo de dados para ser consumida e transformados pelo trabalho. O [Armazenamento de Blob do Azure](azure.blob.storage) e os [Hubs de Evento do Azure](azure.event.hubs) têm suporte como fontes de entrada de fluxo de dados. As fontes de entrada de Hubs de eventos são usadas para coletar fluxos de eventos de vários dispositivos diferentes e serviços, enquanto o armazenamento de Blob pode ser usado em uma fonte de entrada para a ingestão de grandes quantidades de dados. Como os blobs não transmitem dados, os trabalhos de Análise de fluxo nos blobs não serão temporais por natureza, a menos que os registros no blob contenham os carimbos de data/hora.

### Dados de referência
A Stream Analytics também dá suporte a um segundo tipo de fonte de entrada: dados de referência. Isso é usado para executar a correlação e pesquisas de dados auxiliares e os dados aqui são geralmente estáticos ou raramente alterados. [Armazenamento de blob do Azure](azure.blob.storage) é a única fonte de entrada com suporte para dados de referência. Os blobs de fonte de dados de referência estão limitados a 50MB de tamanho.

Para habilitar o suporte para a atualização de dados de referência, o usuário precisa especificar uma lista de blobs na configuração de entrada usando os tokens {data} e {hora} dentro do padrão de caminho. O trabalho carregará o blob correspondente com base na data e na hora codificadas nos nomes de blob usando o fuso horário UTC.

Por exemplo, se o trabalho tem uma entrada de referência configurada no portal com o padrão de caminho, como: /sample/{date}/{time}/products.csv onde o formato de data é "AAAA-MM-DD" e a hora é em formato "HH:mm", o trabalho pegará um arquivo nomeado /sample/2015-04-16/17:30/products.csv em 17h30 em 16 de abril de 2015, fuso horário UTC (que é equivalente às 10h30 em 16 de abril de 2015 usando o fuso horário PST).


### Serialização
Para garantir o comportamento correto de consultas, análise de fluxo deve estar ciente do formato de serialização que está sendo usado em fluxos de dados de entrada. Os formatos de serialização com suporte no momento são JSON, CSV e Avro para fluxos de dados e CSV ou JSON para dados de referência.

### Propriedades geradas
Dependendo do tipo de entrada usado no trabalho, alguns campos adicionais com metadados de evento serão gerados. Esses campos podem ser consultados bem como outras colunas de entrada. Se um evento existente tem um campo que tem o mesmo nome que uma das propriedades abaixo, ele será substituído com os metadados de entrada.

<table border="1">
	<tr>
		<th></th>
		<th>Propriedade</th>
		<th>Descrição</th>
	</tr>
	<tr>
		<td rowspan="4" valign="top"><strong>Blob</strong></td>
		<td>BlobName</td>
		<td>O nome do blob de entrada de onde o evento veio.</td>
	</tr>
	<tr>
		<td>EventProcessedUtcTime</td>
		<td>A data e a hora em que o registro de blob foi processado.</td>
	</tr>
	<tr>
		<td>BlobLastModifiedUtcTime</td>
		<td>A data e hora da última modificação do blob.</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>A ID de partição com base em zero para o adaptador de entrada.</td>
	</tr>
	<tr>
		<td rowspan="3" valign="top"><b>Hub de evento</b></td>
		<td>EventProcessedUtcTime</td>
		<td>A data e a hora em que o evento foi processado.</td>
	</tr>
	<tr>
		<td>EventEnqueuedUtcTime</td>
		<td>A data e a hora em que o evento foi recebido pelos Hubs de eventos.</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>A ID de partição com base em zero para o adaptador de entrada.</td>
	</tr>
</table>

###Partições com pouca ou nenhuma entrada de dados
Ao ler por meio de fontes de entrada que têm várias partições e uma ou mais partições se atrasar ou não tiver dados, o trabalho de streaming precisará decidir como lidar com essa situação para manter os eventos que fluem através do sistema. A configuração de entrada 'Máximo permitido de atraso de chegada' controla esse comportamento e é definida por padrão para aguardar indefinidamente pelos dados, o que significa que os carimbos de data/hora dos eventos não serão alterados, mas também aqueles eventos fluirão com base na partição de entrada mais lenta e pararão de fluir se uma ou mais partições de entrada não tiverem dados de entrada. Isso é útil se os dados forem distribuídos uniformemente entre as partições de entrada e a consistência de tempo entre os eventos for crítica.

Você também pode optar por esperar apenas por um período limitado: o 'Máximo permitido de atraso de chegada' determina o intervalo após o qual o trabalho decidirá avançar, deixando as partições de intervalo de entrada para trás e atuando em eventos de acordo com a configuração de 'Ação para eventos tardios’, descartando seus eventos ou ajustando os carimbos de data/hora de seus eventos se os dados chegarem mais tarde. Isso será útil se a latência for crítica e a mudança de carimbo de data/hora for tolerada, mas a entrada não puder ser distribuída uniformemente.

###Partições com eventos fora de ordem
Quando a consulta de trabalho de streaming usa a palavra-chave TIMESTAMP BY, não há nenhuma garantia sobre a ordem na qual os eventos chegam para a entrada, alguns eventos na mesma partição de entrada podem estar atrasando, o parâmetro 'Máximo permitido de desordem dentro de uma entrada' faz com que o trabalho de streaming atue em eventos que estão fora da tolerância de ordem, de acordo com a configuração de 'Ação para eventos tardios', soltando seus eventos ou ajustando os carimbos de data/hora de seus eventos.

### Recursos adicionais
Para obter detalhes sobre como criar fontes de entrada, consulte o [Guia do desenvolvedor de Hubs de eventos do Azure](azure.event.hubs.developer.guide) e [Usar o armazenamento de Blob do Azure](azure.blob.storage.use).



## Consultar
A lógica para filtrar, manipular e processar dados de entrada é definida nos trabalhos de consulta do Stream Analytics. As consultas são escritas usando a linguagem de consulta de Stream Analytics, uma linguagem semelhante ao SQL que é basicamente um subconjunto da sintaxe do Transact-SQL padrão com algumas extensões específicas para consultas Temporais.

### Windowing
Extensões de windowing permitem que agregações e cálculos sejam executados em subconjuntos de eventos que se enquadram em determinado período de tempo. As funções em janela são invocadas usando a instrução **GROUP BY**. Por exemplo, a consulta a seguir considera os eventos recebidos por segundo:

	SELECT Count(*) 
	FROM Input1 
	GROUP BY TumblingWindow(second, 1) 

### Etapas de execução
Para consultas mais complexas, a cláusula SQL padrão **WITH** pode ser usada para especificar um conjunto de resultados nomeados temporários. Por exemplo, esta consulta usa **WITH** para executar uma transformação com duas etapas de execução:
 
	WITH step1 AS ( 
		SELECT Avg(Reading) as avr 
		FROM temperatureInput1 
		GROUP BY Building, TumblingWindow(hour, 1) 
	) 

	SELECT Avg(avr) AS campus_Avg 
	FROM step1 
	GROUP BY TumblingWindow (day, 1) 

Para saber mais sobre a linguagem de consulta, consulte [Referência de linguagem de consulta da Análise de fluxo do Azure](stream.analytics.query.language.reference).

## Saída
O destino de saída é onde os resultados do trabalho de Stream Analytics serão gravados. Os resultados são gravados continuamente para o destino de saída enquanto o trabalho processa os eventos de entrada. Há suporte para os destinos de saída a seguir:

- Hubs de eventos do Azure - Escolha os Hubs de eventos como um destino de saída para vários cenários de pipelines de streaming que precisam ser compostos juntos, como emitir comandos para dispositivos.
- Armazenamento de Blob do Azure - Use o armazenamento de Blob para arquivamento de longo prazo de saída ou para armazenar dados para processamento posterior.
- Armazenamento de tabela do Azure - o armazenamento de tabela do Azure é um repositório de dados estruturado com menos restrições no esquema. Entidades com esquemas diferentes e diferentes tipos podem ser armazenadas na mesma tabela do Azure. O armazenamento de Tabela do Azure pode ser usado para armazenar dados de persistência e para recuperação eficiente. Para obter mais informações, consulte [Introdução ao Armazenamento do Azure](../storage.introduction.md) e [Criando uma estratégia de particionamento escalonável para o Armazenamento de tabela do Azure](https://msdn.microsoft.com/library/azure/hh508997.aspx).
- Banco de dados SQL do Azure - Esse destino de saída é apropriado para dados relacionais por natureza ou para aplicativos que dependem do conteúdo que está sendo hospedado em um banco de dados.


## Trabalhos de escala

Um trabalho do Stream Analytics pode ser dimensionado na configuração de unidades de streaming, que define a quantidade de potência de processamento de dados que recebe um trabalho. Cada unidade de streaming corresponde a aproximadamente 1MB/segundo de transferência. Cada assinatura tem uma cota de 12 unidades de streaming por região a ser alocada em trabalhos nessa região.

Para obter detalhes, consulte [Trabalhos de Análise de fluxo de escala do Azure](stream.analytics.scale.jobs).


## Monitorar e solucionar problemas de trabalhos

### Conta de armazenamento de monitoramento regional

Para habilitar o monitoramento de trabalho, o Stream Analytics requer que você designe uma conta de Armazenamento do Azure para monitoramento de dados em cada região que contém os trabalhos de Stream Analytics. Isso é configurado no momento da criação do trabalho.

### Métricas
As métricas a seguir estão disponíveis para monitorar o uso e o desempenho dos trabalhos de Análise de fluxo:

- Erros - O número de mensagens de erro geradas por um trabalho de Stream Analytics.
- Eventos de entrada - a quantidade de dados recebidos pelo trabalho de Stream Analytics, em termos de 
- contagem de eventos.
- Eventos de saída - A quantidade de dados enviados pelo trabalho de Stream Analytics para o destino de saída, em termos de contagem de evento.
- Eventos fora de ordem - O número de eventos recebidos fora de ordem que foram descartados ou que receberam um carimbo de data/hora ajustado, com base na política de fora de ordem.
- Erros de conversão de dados - O número de erros de conversão de dados gerado por um trabalho de Stream Analytics.

### Logs de operação
É a melhor abordagem para depuração ou um trabalho de Stream Analytics de solução de problemas por meio de logs de operação do Azure. Os logs de operação podem ser acessados na seção **Serviços de Gerenciamento** do portal. Para inspecionar os logs do seu trabalho, defina o **Tipo de serviço** para **Stream Analytics** e o **Nome do serviço** para o nome do seu trabalho.


## Gerenciar trabalhos 

### Iniciar e parar trabalhos
Ao iniciar um trabalho, você será solicitado a especificar um valor **Iniciar saída**, que determina quando este trabalho começará a produzir saída resultante. Se a consulta associada inclui uma janela, o trabalho começará a pegar a entrada por meio de fontes de entrada no início da duração da janela necessário para produzir o primeiro evento de saída na hora especificada. Há três opções: **Hora de início do trabalho**, **Personalizado** e **Interrompido pela última vez**. A configuração padrão é a **Hora de início do trabalho**. Para casos quando um trabalho foi interrompido temporariamente, a prática recomendada é escolher **Interrompido pela última vez** para o valor **Iniciar saída** para retomar o trabalho da última hora de saída e evitar a perda de dados. Para a opção **Personalizada**, você deve especificar data e hora. Essa configuração é útil para especificar quantos dados históricos há nas fontes de entrada para consumir ou para pegar a inclusão de dados de uma hora específica.

### Configurar trabalhos
Você pode ajustar as seguintes configurações de nível superior para um trabalho de Análise de fluxo:

- **Iniciar saída** - Use essa configuração para especificar quando esta tarefa começará a produzir saída resultante. Se a consulta associada inclui uma janela, o trabalho começará a pegar a entrada por meio de fontes de entrada no início da duração da janela necessárias para produzir o primeiro evento de saída na hora especificada. Há duas opções, **Hora de início do trabalho** e **Personalizado**. A configuração padrão é a **Hora de início do trabalho**. Para a opção **Personalizada**, você deve especificar data e hora. Essa configuração é útil para especificar quantos dados históricos nas fontes de entrada para consumir ou para pegar a inclusão de dados de uma hora específica, como quando um trabalho foi passado interrompido. 
- **Política de fora de ordem** - As configurações para manipular eventos que não chegam ao trabalho de Stream Analytics em sequência. Você pode designar um limite de tempo para reordenar eventos, especificando uma janela de tolerância a falhas e também determinando uma ação em eventos fora essa janela: **Remover** ou **Ajustar**. **Remover** removerá todos os eventos recebidos fora de ordem, e **Ajustar** alterará o sistema. O carimbo de data/hora de eventos fora de ordem para o carimbo de data/hora do evento ordenado recebido mais recentemente. 
- **Política de entrada tardia** - Ao ler por meio de fontes de entrada que têm várias partições e uma ou mais partições se atrasar ou não tiver dados, o trabalho de streaming precisará decidir como lidar com essa situação para manter os eventos que fluem através do sistema. A configuração de entrada 'Máximo permitido de atraso de chegada' controla esse comportamento e é definida por padrão para aguardar indefinidamente pelos dados, o que significa que os carimbos de data/hora dos eventos não serão alterados, mas também aqueles eventos fluirão com base na partição de entrada mais lenta e pararão de fluir se uma ou mais partições de entrada não tiverem dados de entrada. Isso é útil se os dados forem distribuídos uniformemente entre as partições de entrada e a consistência de tempo entre os eventos for crítica. O usuário também pode optar por apenas esperar por um período limitado, o 'Máximo permitido de atraso de chegada' determina o intervalo após o qual o trabalho decidirá avançar, deixando as partições de intervalo de entrada para trás e atuando em eventos de acordo com a configuração de 'Ação para eventos tardios’, descartando seus eventos ou ajustando os carimbos de data/hora de seus eventos se os dados chegarem mais tarde. Isso será útil se a latência for crítica e a mudança de carimbo de data/hora for tolerada, mas a entrada não puder ser distribuída uniformemente.
- **Localidade** - Use essa configuração para especificar a preferência de internacionalização para o trabalho de Stream Analytics. Enquanto carimbos de dados de localidade neutra, configurações que afetam como o trabalho será analisado, comparar e classificar dados. Para a versão de visualização, há suporte apenas para **en-US**.

### Status

O status dos trabalhos de Stream Analytics pode ser inspecionado no portal do Azure. Os trabalhos em execução podem estar em um dos três estados: **Ocioso**, **Em processamento** ou **Degradado**. A definição de cada um desses estados está ilustrada abaixo:

- **Ocioso** - Nenhum byte de entrada foi detectado desde que o trabalho foi criado ou nos últimos 2 minutos. Se um trabalho está no estado **Ocioso** por um longo período de tempo, é provável que a entrada exista, mas não haja nenhum byte bruto para processar.
- **Em processamento** - Um valor diferente de zero de eventos de entrada filtrados foi consumido pelo trabalho de Stream Analytics com êxito. Se um trabalho estiver preso no estado **Em processamento** sem produzir saída, é provável que a janela de tempo de processamento de dados seja grande ou que a lógica de consulta seja complicada.
- **Degradado** - Este estado indica que um trabalho de Stream Analytics está encontrando um dos seguintes erros: erros de comunicação de entrada/saída, erros de consulta ou erros de tempo de execução capaz de novas tentativas. Para distinguir qual tipo de erro você está encontrando no trabalho, exiba os logs de operação.


## Obtenha suporte
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)


## Próximas etapas

Agora que você está familiarizado com os principais conceitos do Stream Analytics, experimente:

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)





<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.limitations]: ../stream-analytics-limitations.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=58--> 