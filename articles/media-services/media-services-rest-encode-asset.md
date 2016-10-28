<properties 
	pageTitle="Como codificar um ativo usando o Codificador de Mídia Padrão | Microsoft Azure" 
	description="Saiba como usar o Codificador de Mídia Padrão para codificar conteúdo de mídia nos Serviços de Mídia. Exemplos de código usam a API REST." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/19/2016"
	ms.author="juliako"/>


#Como codificar um ativo usando o Media Encoder Standard


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encode-asset.md)
- [REST](media-services-rest-encode-asset.md)
- [Portal](media-services-portal-encode.md)

##Visão geral
Para fornecer vídeo digital pela internet, você deve compactar a mídia. Os arquivos de vídeo digital são muito grandes e podem ser muito grandes para entregar pela internet ou para dispositivos de seus clientes para exibir corretamente. A codificação é o processo de compactação de vídeo e áudio para que seus clientes possam exibir sua mídia.

Os trabalhos de codificação são uma das operações de processamento mais comuns nos serviços de mídia. Você cria trabalhos de codificação para converter arquivos de mídia de uma codificação para outra. Ao codificar, é possível usar o codificador interno dos Serviços de Mídia (Codificador de Mídia Padrão). Você também pode usar um codificador fornecido por um parceiro de Serviços de Mídia. Os codificadores de terceiros estão disponíveis por meio do Azure Marketplace. Você pode especificar os detalhes das tarefas de codificação usando cadeias de caracteres predefinidas para seu codificador ou usando arquivos de configuração predefinidos. Para ver os tipos de predefinições disponíveis, veja [Predefinições de tarefa para o Codificador de Mídia Padrão](http://msdn.microsoft.com/library/mt269960).

Cada trabalho pode ter uma ou mais tarefas dependendo do tipo de processamento que você deseja realizar. Por meio da API REST, você pode criar trabalhos e as tarefas relacionadas em uma das duas maneiras:

- As tarefas podem ser definidas embutidas por meio da propriedade de navegação de tarefas nas entidades de trabalho, ou
- por meio do processamento do lote OData.


Convém sempre codificar arquivos de mezanino em uma conjunto de MP4 de taxa de bits adaptável e, em seguida, converter o conjunto para o formato desejado usando o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md). Para tirar proveito do empacotamento dinâmico, você precisa obter primeiro pelo menos uma unidade de streaming OnDemand para o ponto de extremidade de streaming por meio do qual você planeja fornecer seu conteúdo. Para obter mais informações, consulte [Como dimensionar os Serviços de Mídia](media-services-portal-manage-streaming-endpoints.md).

Se seu ativo de saída tiver o armazenamento criptografado, você deverá configurar a política de entrega de ativos. Para obter mais informações, consulte [Configurando a política de entrega de ativos](media-services-rest-configure-asset-delivery-policy.md).


>[AZURE.NOTE]Antes de começar a fazer referência a processadores de mídia, verifique se você tem a ID do processador de mídia correta. Para obter mais informações, consulte [Obter processadores de mídia](media-services-rest-get-media-processor.md).

##Criar um trabalho com uma única tarefa de codificação

>[AZURE.NOTE] Ao trabalhar com a API REST dos serviços de mídia, as seguintes considerações se aplicam:
>
>Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos nas suas solicitações HTTP. Para obter mais informações, consulte [Configuração para desenvolvimento da API REST dos Serviços de Mídia](media-services-rest-how-to-use.md).

>Depois de se conectar com êxito a https://media.windows.net, você receberá um redirecionamento 301 especificando outro URI dos Serviços de Mídia. Você deve fazer chamadas subsequentes para o novo URI, conforme descrito em [Conectando-se aos Serviços de Mídia usando a API REST](media-services-rest-connect-programmatically.md).
>
>Ao usar o JSON e especificar para usar a palavra-chave **metadata** na solicitação (por exemplo, para fazer referência a um objeto vinculado), é NECESSÁRIO definir o cabeçalho **Accept** como [formato JSON Detalhado](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Accept: application/json;odata=verbose.

O exemplo a seguir mostra como criar e publicar um trabalho com uma tarefa definida para codificar um vídeo em uma determinada resolução e qualidade. Quando estiver codificando com o Codificador de Mídia Padrão, você poderá usar as predefinições de configuração de tarefa especificadas [aqui](http://msdn.microsoft.com/library/mt269960).

Solicitação:

POST https://media.windows.net/API/Jobs HTTP/1.1 Content-Type: application/json;odata=verbose Accept: application/json;odata=verbose DataServiceVersion: 3.0 MaxDataServiceVersion: 3.0 x-ms-version: 2.11 Authorization: Bearer <token value> x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Multiple Bitrate 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Resposta:
	
	HTTP/1.1 201 Created

	. . . 

###Definir nome do ativo de saída

O exemplo a seguir mostra como definir o atributo assetName:

	{ "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName="CustomOutputAssetName">JobOutputAsset(0)</outputAsset></taskBody>"}

##Considerações

- As propriedades TaskBody DEVEM usar XML literal para definir o número de entrada ou os ativos de saída que serão usados pela tarefa. O tópico Tarefa contém a Definição de Esquema XML para o XML.
- Na definição de TaskBody, cada valor interno para <inputAsset> e <outputAsset> deve ser definido como JobInputAsset(valor) ou JobOutputAsset(valor).
- Uma tarefa pode ter vários ativos de saída. Um JobOutputAsset(x) só pode ser usado uma vez como uma saída de uma tarefa em um trabalho.
- Você pode especificar JobInputAsset ou JobOutputAsset como um ativo de entrada de uma tarefa.
- As tarefas não devem formar um ciclo.
- O parâmetro de valor passado para JobInputAsset ou JobOutputAsset representa o valor de índice para um ativo. Os ativos reais são definidos nas propriedades de navegação InputMediaAssets e OutputMediaAssets na definição de entidade de tarefa.
- Como os serviços de mídia são baseados no OData v3, os ativos individuais nas coleções de propriedade de navegação InputMediaAssets e OutputMediaAssets são referenciados por meio de um par nome-valor "\_\_metadata : uri".
- Os InputMediaAssets mapeiam para um ou mais ativos que você criou nos serviços de mídia. Os OutputMediaAssets são criados pelo sistema. Eles não fazem referência a um ativo existente.
- Os OutputMediaAssets podem ser nomeados usando o atributo assetName. Se esse atributo não estiver presente, então o nome do OutputMediaAsset será tudo o que é o valor de texto interno do elemento <outputAsset> com um sufixo do valor de Job Name ou o valor de Job Id (no caso em que a propriedade Name não esteja definida). Por exemplo, se você definir um valor de assetName como "Amostra", a propriedade Nome de OutputMediaAsset deve ser definida como "Amostra". No entanto, se você não definiu um valor para assetName, mas definiu o nome do trabalho como "NewJob", o nome do OutputMediaAsset poderia ser "JobOutputAsset(value)\_NewJob".


##Criar um trabalho com tarefas encadeadas

Em muitos cenários de aplicativo, os desenvolvedores querem criar uma série de tarefas de processamento. Nos serviços de mídia, você pode criar uma série de tarefas encadeadas. Cada tarefa executa etapas de processamento diferentes e pode usar diferentes processadores de mídia. As tarefas encadeadas podem entregar um ativo de uma tarefa para outra, desempenhando uma sequência linear de tarefas no ativo. No entanto, as tarefas executadas em um trabalho não precisam estar em uma sequência. Quando você cria uma tarefa encadeada, os objetos **ITask** são criados em um único objeto **IJob**.

>[AZURE.NOTE] Atualmente, há um limite de 30 tarefas por trabalho. Se você precisa encadear mais de 30 tarefas, crie mais de um trabalho para conter as tarefas.


	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

	{  
	   "Name":"NewTestJob",
	   "InputMediaAssets":[  
	      {  
	         "__metadata":{  
	            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
	         }
	      }
	   ],
	   "Tasks":[  
	      {  
	         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
	         "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
	         "TaskBody":"<?xml version="1.0" encoding="utf-16"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}


###Considerações

Para habilitar o encadeamento de tarefas:

- Um trabalho deve ter pelo menos 2 tarefas
- Deve haver pelo menos uma tarefa cujas entradas são a saída de outra tarefa no trabalho.

## Usar o processamento em lotes OData 

O exemplo a seguir mostra como usar o processamento em lotes OData para criar um trabalho e tarefas. Para obter informações sobre o processamento em lotes, consulte [Processamento em lote do protocolo OData (Open Data)](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).
 
	POST https://media.windows.net/api/$batch HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
	Accept: multipart/mixed
	Accept-Charset: UTF-8
	Authorization: Bearer <token>
	x-ms-version: 2.11
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	Host: media.windows.net
	
	
	--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
	Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d
	
	--changeset_122fb0a4-cd80-4958-820f-346309967e4d
	Content-Type: application/http
	Content-Transfer-Encoding: binary
	
	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-ID: 1
	Content-Type: application/json
	Accept: application/json
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	Accept-Charset: UTF-8
	Authorization: Bearer <token>
	x-ms-version: 2.11
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	
	{"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}
	
	--changeset_122fb0a4-cd80-4958-820f-346309967e4d
	Content-Type: application/http
	Content-Transfer-Encoding: binary
	
	POST https://media.windows.net/api/$1/Tasks HTTP/1.1
	Content-ID: 2
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	Accept-Charset: UTF-8
	Authorization: Bearer <token>
	x-ms-version: 2.11
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	
	{  
	   "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	   "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
	   "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName="Custom output name">JobOutputAsset(0)</outputAsset></taskBody>"
	}

	--changeset_122fb0a4-cd80-4958-820f-346309967e4d--
	--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--
 


## Criar um Trabalho usando um JobTemplate


Ao processar múltiplos Ativos usando um conjunto comum de Tarefas, os JobTemplates são úteis para especificar as predefinições padrão da Tarefa, a ordem das Tarefas e assim por diante.

O exemplo a seguir mostra como criar um JobTemplate com uma TaskTemplate definida de forma embutida. O TaskTemplate usa o Codificador de Mídia Padrão como o MediaProcessor para codificar o arquivo do Ativo; no entanto, outros MediaProcessors também podem ser usados.


	POST https://media.windows.net/API/JobTemplates HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version="1.0" encoding="utf-8"?><jobTemplate><taskBody taskTemplateId="nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

>[AZURE.NOTE]Ao contrário de outras entidades de Serviços de Mídia, você deve definir um novo identificador GUID para cada TaskTemplate e colocá-lo na propriedade taskTemplateId e na propriedade ID no corpo da solicitação. O esquema de identificação de conteúdo deve seguir o esquema descrito em Identificar Entidades de Serviços de Mídia do Azure. Além disso, os JobTemplates não podem ser atualizados. Em vez disso, você deve criar um novo com as suas alterações atualizadas.
 

Se for bem-sucedido, será retornada a seguinte resposta:
	
	HTTP/1.1 201 Created
	
	. . .


O exemplo a seguir mostra como criar um Trabalho referenciando uma ID de JobTemplate:

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
	 

Se for bem-sucedido, será retornada a seguinte resposta:
	
	HTTP/1.1 201 Created
	
	. . . 



##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Próximas etapas
Agora que você sabe como criar um trabalho para codificar um ativo, vá para o tópico [Como verificar o andamento do trabalho com os Serviços de Mídia](media-services-rest-check-job-progress.md).


##Consulte também

[Obter processadores de mídia](media-services-rest-get-media-processor.md)

<!---HONumber=AcomDC_0921_2016-->