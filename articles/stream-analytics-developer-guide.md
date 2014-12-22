<properties title="Azure Stream Analytics developer guide" pageTitle="Guia de desenvolvedor da Análise de fluxo | Azure" description="Learn how to develop Azure Stream Analytics applications" metaKeywords="" services="stream analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="10/28/2014" ms.author="jgao" />


# Guia de desenvolvedor da Análise de fluxo do Azure 

[Esta é uma documentação de pré-lançamento e está sujeito a alterações em versões futuras.] 

A Análise de fluxo do Azure é um serviço completamente gerenciado que oferece baixa latência, alta disponibilidade e processamento escalonável de eventos complexos ao longo do fluxo de dados na nuvem. Na versão de teste, Análise de fluxo permite que os clientes instalação trabalhos de streaming analisar os fluxos de dados e permite aos clientes unidade perto de análise em tempo real.  

Cenários de destino da Análise de fluxo:

- Executar o processamento de eventos complexos em dados de alta velocidade e grande volume   
- Coletar dados de eventos de ativos distribuídos globalmente ou equipamento como carros conectados ou grades de utilitário 
- Processar dados de telemetria para quase em tempo real de monitoramento e diagnóstico 
- Capturar e arquivar eventos em tempo real para processamento futuro

Para obter mais informações, consulte [Introdução à análise de fluxo do Azure][stream.analytics.introduction]. 

Trabalhos de Análise de fluxo são definidos como uma ou mais fontes, uma consulta de entrada de dados de fluxo de entrada e um destino de saída.  


##Neste artigo

+ [Entradas](#inputs) 
+ [Consulta](#query)
+ [Saída](#output)
+ [Trabalhos de escala](#scale)
+ [Monitorar e solucionar problemas de trabalhos](#monitor)
+ [Gerenciar trabalhos](#manage)
+ [Próximas etapas](#nextsteps)



##<a name="inputs"></a>Entradas 

### Fluxo de dados

Cada definição de trabalho de Análise de fluxo deve conter fonte entrada pelo menos um fluxo de dados para ser consumida e transformados pelo trabalho.  [Armazenamento de Blob do Azure][azure.blob.storage] e [Hubs de evento do barramento de serviço do Azure][azure.event.hubs] têm suporte como fontes de entrada de fluxo de dados.  Fontes de entrada de Hub de eventos são usados para coletar fluxos de eventos de vários dispositivos diferentes e serviços, enquanto o armazenamento de Blob pode ser usada uma fonte de entrada para a ingestão de grandes quantidades de dados.  Como Blobs não transmitir dados, trabalhos de Análise de fluxo nos Blobs não será temporais por natureza, a menos que os registros no Blob conter carimbos de hora.

### Dados de referência

A Análise de fluxo também oferece suporte a um segundo tipo de fonte de entrada: Dados de referência.  Isso é usado para executar a correlação e pesquisas de dados auxiliares e os dados aqui são geralmente estáticos ou raramente alterados.  A versão de visualização, o armazenamento de Blob é a única fonte de entrada com suporte para dados de referência.

### Serialização
Para garantir o comportamento correto de consultas, análise de fluxo deve estar ciente do formato de serialização que está sendo usado em fluxos de dados de entrada. Formatos com suporte no momento são JSON, CSV e Avro para dados de Streaming e CSV para dados de referência.

### Propriedades geradas
Dependendo do tipo de entrada usado no trabalho, alguns campos adicionais com metadados de evento serão gerados.  Esses campos podem ser consultados bem como outras colunas de entrada.  Se um evento existente tem um campo que tem o mesmo nome que uma das propriedades abaixo, ele será substituído com os metadados de entrada.

<table border="1">
	<tr>
		<th></th>
		<th>Propriedade</th>
		<th>Descrição</th>
	</tr>
	<tr>
		<td rowspan="4" valign="top"><strong>Blob</strong></td>
		<td>BlobName</td>
		<td>O nome do blob de entrada de onde o evento veio</td>
	</tr>
	<tr>
		<td>EventProcessedUtcTime</td>
		<td>A data e a hora em que o registro de blob foi processado</td>
	</tr>
	<tr>
		<td>BlobLastModifiedUtcTime</td>
		<td>A data e hora da última modificação do blob</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>A ID de partição com base em zero para o adaptador de entrada</td>
	</tr>
	<tr>
		<td rowspan="3" valign="top"><b>Hub de evento</b></td>
		<td>EventProcessedUtcTime</td>
		<td>A data e hora em que o evento foi processado</td>
	</tr>
	<tr>
		<td>EventEnqueuedUtcTime</td>
		<td>A data e a hora em que o evento foi recebido pelo Hub de eventos</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>A ID de partição com base em zero para o adaptador de entrada</td>
	</tr>
</table>



###Recursos adicionais
Para obter detalhes sobre como criar fontes de entrada, consulte o [guia do desenvolvedor de Hubs de eventos do Azure][azure.event.hubs.developer.guide] e [Usar o armazenamento de Blob do Azure][azure.blob.storage.use].  



##<a name="query"></a>Consulta
A lógica para filtrar, manipular e processar dados de entrada é definida nos trabalhos de consulta de análise do fluxo.  Consultas são escritas usando a linguagem de consulta de análise de fluxo, uma linguagem semelhante ao SQL que é basicamente um subconjunto da sintaxe do T-SQL padrão com algumas extensões específicas para consultas Temporais.

###Windowing
Extensões de windowing permitem que agregações e cálculos sejam executados em subconjuntos de eventos que se enquadram em determinado período de tempo. Funções em janela são invocadas usando a instrução GROUP BY. Por exemplo, a consulta a seguir considera os eventos recebidos por segundo: 

	SELECT Count(*) 
	FROM Input1 
	GROUP BY TumblingWindow(second, 1) 

###Etapas de execução
Para consultas mais complexas, a cláusula SQL padrão com pode ser usada para especificar um conjunto de resultados nomeado temporário.  Por exemplo, esta consulta usa WITH para executar uma transformação com duas etapas de execução:
 
	WITH step1 AS ( 
		SELECT Avg(Reading) as avr 
		FROM temperatureInput1 
		GROUP BY Building, TumblingWindow(hour, 1) 
	) 

	SELECT Avg(avr) AS campus_Avg 
	FROM step1 
	GROUP BY TumblingWindow (day, 1) 

Para saber mais sobre a linguagem de consulta, consulte [Referência de linguagem de consulta da Análise de fluxo do Azure][stream.analytics.query.language.reference]. 

##<a name="output"></a>Saída
O código-fonte é onde os resultados do trabalho de Análise de fluxo serão gravados. Resultados são gravados continuamente o código-fonte enquanto o trabalho processa os eventos de entrada.  Há suporte para as fontes de saída a seguir:

- Hubs de eventos do barramento de serviço do Azure: Escolha o Hub de eventos como uma fonte de saída para cenários quando vários pipelines streaming precisam ser composta, como emissão de comandos para dispositivos.
- Blobs de armazenamento do Azure : Usar o armazenamento de Blob de longo prazo arquivamento de saída ou para armazenar dados para processamento posterior.
- Banco de Dados SQL do Azure: Esse código-fonte é apropriada para dados relacionais por natureza ou para aplicativos que dependem do conteúdo que está sendo hospedado em um banco de dados.


##<a name="scale"></a>Trabalhos de escala

Um trabalho de Análise de fluxo pode ser dimensionado na configuração de Unidades de streaming, que define a quantidade de potência de processamento que recebe um trabalho. Cada Unidade de streaming corresponde a aproximadamente 1 MB/segundo de taxa de transferência. Cada assinatura tem uma cota de 12 Unidades de streaming por região a ser alocada em trabalhos nessa região.

Para obter detalhes, consulte [trabalhos de Análise de fluxo de escala do Azure][stream.analytics.scale.jobs].


##<a name="monitor"></a>Monitorar e solucionar problemas de trabalhos

###Conta de armazenamento de monitoramento regionais

Para habilitar o monitoramento de trabalho, Análise de fluxo requer que você designa uma conta de Armazenamento do Azure para monitoramento de dados em cada região que contém os trabalhos de Análise de fluxo.  Isso é configurado no momento da criação do trabalho.  

###Métricas
As métricas a seguir estão disponíveis para monitorar o uso e o desempenho dos trabalhos de Análise de fluxo:

- A taxa de transferência de entrada: quantidade de dados recebidos pelo trabalho de Análise de fluxo, em termos de contagem de eventos
- A taxa de transferência de saída: quantidade de dados enviados pelo trabalho de Análise de fluxo de origem de saída, em termos de contagem de evento
- Contagem de erros: número de mensagens de erro gerada por um trabalho de Análise de fluxo

###Logs de operação
É a melhor abordagem para depuração ou um trabalho de Análise de fluxo de solução de problemas por meio de Logs de operação do Azure.  Logs de operação podem ser acessados na seção de Serviços de gerenciamento do portal.  Para inspecionar os logs do seu trabalho, defina tipo de serviço para "Análise de fluxo" e o nome do serviço para o nome do seu trabalho.


##<a name="manage"></a>Gerenciar trabalhos 

###Iniciando e parando trabalhos
Na versão de teste de Análise de fluxo, interromper um trabalho não preserva qualquer estado sobre os últimos eventos consumidos pelo trabalho.  Como resultado, reiniciar um trabalho interrompido pode resultar em eventos descartados ou dados duplicados.  Se um trabalho deve ser interrompido temporariamente, a prática recomendada é inspecionar a saída e usar o tempo de inserção do último registro para aproximar quando o trabalho foi interrompido.  Especifique esse tempo na configuração de Iniciar saída na guia Configurar quando o trabalho for reiniciado.
Esta é uma limitação temporária e permitindo que o trabalho Iniciar e parar sem perda de dados é uma prioridade alta para corrigir nos futuros lançamentos.  

###Configurar trabalhos
Você pode ajustar as seguintes configurações de nível superior para um trabalho de Análise de fluxo:

- Iniciar saída: Especifica quando esse trabalho iniciará a produzir saída resultante. Se a consulta associada inclui uma janela, o trabalho começará a pegar a entrada por meio de fontes de entrada no início da duração da janela necessário para produzir o primeiro evento de saída na hora especificada. Há duas opções, Hora de início do trabalho e Personalizado. A configuração padrão é a Hora de início do trabalho. Para a opção Personalizada, você deve especificar data e hora. Essa configuração é útil para especificar quantos dados históricos nas fontes de entrada para consumir ou para pegar a inclusão de dados de uma hora específica, como quando um trabalho foi passado interrompido. 
- Política fora de ordem: Configurações para manipular eventos que não chegam ao trabalho de Análise de fluxo sequencialmente. Você pode designar um limite de tempo para reordenar eventos dentro, especificando uma Janela de tolerância a falhas e também determinar uma ação em eventos fora essa janela: Remover ou Ajustar.  Remover retirará todos os eventos recebidos fora de ordem e Ajustar alterará o System.Timestamp de eventos fora de ordem para o carimbo de dara/hora do evento ordenado recebido mais recentemente.  
- Local: Use essa configuração para especificar a preferência de internacionalização para o trabalho de análise de fluxo. Enquanto carimbos de dados de localidade neutra, configurações que afetam como o trabalho será analisado, comparar e classificar dados. Para a versão de visualização, há suporte apenas en-US.


##<a name="support"></a>Obtenha suporte
Para obter suporte adicional, consulte o [fórum de Análise de fluxo do Azure][stream.analytics.forum]. 


##<a name="nextsteps"></a>Próximas etapas

- [Introdução à análise de fluxo do Azure][stream.analytics.introduction]
- [Introdução ao uso da Análise de fluxo do Azure][stream.analytics.get.started]
- [Escalonar trabalhos de Análise de fluxo do Azure][stream.analytics.scale.jobs]
- [Limitações e problemas conhecidos da Análise de fluxo do Azure][stream.analytics.limitations]
- [Referência da linguagem de consulta da Análise de fluxo do Azure][stream.analytics.query.language.reference]
- [Referência da API REST do gerenciamento da Análise de fluxo do Azure][stream.analytics.rest.api.reference] 



<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/pt-br/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/pt-br/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/pt-br/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/pt-br/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
