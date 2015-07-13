<properties 
	pageTitle="Como codificar um ativo usando o Codificador de Mídia do Azure" 
	description="Saiba como usar o Codificador de Mídia do Azure para codificar conteúdo de mídia nos Serviços de Mídia. Exemplos de código usam a API REST." 
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
	ms.date="06/18/2015" 
	ms.author="juliako"/>


#Como codificar um ativo usando o Codificador de Mídia do Azure

Este artigo faz parte da série do [Fluxo de trabalho do Vídeo sob demanda dos Serviços de Mídia](media-services-video-on-demand-workflow.md).

##Visão geral
Para fornecer vídeo digital pela internet, você deve compactar a mídia. Os arquivos de vídeo digital são muito grandes e podem ser muito grandes para entregar pela internet ou para dispositivos de seus clientes para exibir corretamente. A codificação é o processo de compactação de vídeo e áudio para que seus clientes possam exibir sua mídia.

Os trabalhos de codificação são uma das operações de processamento mais comuns nos serviços de mídia. Você cria trabalhos de codificação para converter arquivos de mídia de uma codificação para outra. Ao codificar, você pode usar o codificador de mídia integrado dos serviços de mídia. Você também pode usar um codificador fornecido por um parceiro de Serviços de Mídia. Os codificadores de terceiros estão disponíveis por meio do Azure Marketplace. Você pode especificar os detalhes das tarefas de codificação usando cadeias de caracteres predefinidas para seu codificador ou usando arquivos de configuração predefinidos. Para ver os tipos de predefinições disponíveis, consulte [Predefinições de tarefa para os Serviços de Mídia do Azure](https://msdn.microsoft.com/library/azure/dn619392.aspx). Se você usou um codificador de terceiros, você deve [validar seus arquivos](https://msdn.microsoft.com/library/azure/dn750842.aspx).

Cada trabalho pode ter uma ou mais tarefas dependendo do tipo de processamento que você deseja realizar. Por meio da API REST, você pode criar Trabalhos e as Tarefas relacionadas de uma destas duas maneiras: as Tarefas podem ser definidas embutidas por meio da propriedade de navegação Tarefas nas entidades de Trabalho ou por meio do processamento de lote OData. O SDK dos Serviços de Mídia usa processamento em lotes. No entanto, para fins de legibilidade dos exemplos de código neste tópico, as tarefas são definidas embutidas. Para obter informações sobre o processamento em lotes, consulte [Processamento em lote do protocolo OData (Open Data)](http://www.odata.org/documentation/odata-version-3-0/batch-processing/). Você também pode encontrar um exemplo de processamento em lotes no tópico [Trabalho](https://msdn.microsoft.com/library/azure/hh974289.aspx).

Convém sempre codificar arquivos de mezanino em uma conjunto de MP4 de taxa de bits adaptável e, em seguida, converter o conjunto para o formato desejado usando o [empacotamento dinâmico](https://msdn.microsoft.com/library/azure/jj889436.aspx). Para tirar proveito do empacotamento dinâmico, você precisa obter primeiro pelo menos uma unidade de streaming OnDemand para o ponto de extremidade de streaming por meio do qual você planeja fornecer seu conteúdo. Para obter mais informações, consulte [Como dimensionar os Serviços de Mídia](media-services-manage-origins.md#scale_streaming_endpoints).

Se seu ativo de saída tiver o armazenamento criptografado, você deverá configurar a política de entrega de ativos. Para obter mais informações, consulte [Configurando a política de entrega de ativos](media-services-rest-configure-asset-delivery-policy.md).



##Criar um trabalho com uma única tarefa de codificação 

>[AZURE.NOTE]Ao trabalhar com a API REST dos serviços de mídia, as seguintes considerações se aplicam:
>
>Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos nas suas solicitações HTTP. Para obter mais informações, consulte [Configuração para desenvolvimento da API REST dos Serviços de Mídia](media-services-rest-how-to-use.md).

>Depois de se conectar com êxito a https://media.windows.net, você receberá um redirecionamento 301 especificando outro URI dos Serviços de Mídia. Você deve fazer chamadas subsequentes para o novo URI, conforme descrito em [Conectando-se aos Serviços de Mídia usando a API REST](media-services-rest-connect_programmatically.md).


O exemplo a seguir mostra como criar e publicar um trabalho com uma tarefa definida para codificar um vídeo em uma determinada resolução e qualidade. Quando estiver codificando com o Codificador de Mídia do Azure, você pode usar as predefinições de tarefa de configuração especificadas [aqui](https://msdn.microsoft.com/library/azure/dn619389.aspx).
	
Solicitação:

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Broadband 720p", "MediaProcessorId" : "nb:mpid:UUID:70bdc2c3-ebf4-42a9-8542-5afc1e55d217",  "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Resposta:
	
	HTTP/1.1 201 Created

	. . . 

##Considerações

- As propriedades TaskBody DEVEM usar XML literal para definir o número de entrada ou os ativos de saída que serão usados pela tarefa. O tópico Tarefa contém a Definição de Esquema XML para o XML.
- Na definição de TaskBody, cada valor interno para <inputAsset> e <outputAsset> deve ser definido como JobInputAsset(value) ou JobOutputAsset(value).
- Uma tarefa pode ter vários ativos de saída. Um JobOutputAsset(x) só pode ser usado uma vez como uma saída de uma tarefa em um trabalho.
- Você pode especificar JobInputAsset ou JobOutputAsset como um ativo de entrada de uma tarefa.
- As tarefas não devem formar um ciclo.
- O parâmetro de valor passado para JobInputAsset ou JobOutputAsset representa o valor de índice para um ativo. Os ativos reais são definidos nas propriedades de navegação InputMediaAssets e OutputMediaAssets na definição de entidade de tarefa. 
- Como os serviços de mídia são baseados no OData v3, os ativos individuais nas coleções de propriedade de navegação InputMediaAssets e OutputMediaAssets são referenciados por meio de um par nome-valor "__metadata : uri".
- Os InputMediaAssets mapeiam para um ou mais ativos que você criou nos serviços de mídia. Os OutputMediaAssets são criados pelo sistema. Eles não fazem referência a um ativo existente.
- Os OutputMediaAssets podem ser nomeados usando o atributo assetName. Se esse atributo não estiver presente, então o nome do OutputMediaAsset será tudo o que é o valor de texto interno do elemento <outputAsset> com um sufixo do valor do nome do trabalho ou o valor da ID de trabalho (no caso em que a propriedade Nome não esteja definida). Por exemplo, se você definir um valor de assetName como "Amostra", a propriedade Nome de OutputMediaAsset deve ser definida como "Amostra". No entanto, se você não definiu um valor para assetName, mas definiu o nome do trabalho como "NewJob", o nome do OutputMediaAsset poderia ser "JobOutputAsset(value)_NewJob". 

O exemplo a seguir mostra como definir o atributo assetName:

	{ "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName="CustomOutputAssetName">JobOutputAsset(0)</outputAsset></taskBody>"}



##Criar um trabalho com tarefas encadeadas

Em muitos cenários de aplicativo, os desenvolvedores querem criar uma série de tarefas de processamento. Nos serviços de mídia, você pode criar uma série de tarefas encadeadas. Cada tarefa executa etapas de processamento diferentes e pode usar diferentes processadores de mídia. As tarefas encadeadas podem entregar um ativo de uma tarefa para outra, desempenhando uma sequência linear de tarefas no ativo. No entanto, as tarefas executadas em um trabalho não precisam estar em uma sequência. Quando você cria uma tarefa encadeada, os objetos **ITask** são criados em um único objeto **IJob**.

>[AZURE.NOTE]Atualmente, há um limite de 30 tarefas por trabalho. Se você precisa encadear mais de 30 tarefas, crie mais de um trabalho para conter as tarefas.


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
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version="1.0" encoding="utf-16"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}

###Considerações

Para habilitar o encadeamento de tarefas:

- Um trabalho deve ter pelo menos 2 tarefas
- Deve haver pelo menos uma tarefa cujas entradas são a saída de outra tarefa no trabalho.


## Criar um Trabalho usando um JobTemplate


Ao processar múltiplos Ativos usando um conjunto comum de Tarefas, os JobTemplates são úteis para especificar as predefinições padrão da Tarefa, a ordem das Tarefas e assim por diante.

O exemplo a seguir mostra como criar um JobTemplate com uma TaskTemplate definida de forma embutida. A TaskTemplate usa o Codificador de Mídia do Azure como o MediaProcessor para codificar o arquivo de Ativo; no entanto, outros MediaProcessors também podem ser usados.


	POST https://media.windows.net/API/JobTemplates HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version="1.0" encoding="utf-8"?><jobTemplate><taskBody taskTemplateId="nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

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


##Controlando nomes de arquivos de saída de codificador do Serviço de Mídia 

Por padrão, o Codificador de Mídia do Azure cria nomes de arquivo de saída ao combinar diversos atributos do ativo de entrada e o processo de codificação. Cada atributo é identificado usando uma macro, conforme discutido abaixo.

Segue uma lista completa das macros disponíveis para a nomeação de arquivo de saída: Taxa de Bits de Áudio – a taxa de bits usada durante a codificação de áudio, especificada em kbps

- Codec de Áudio – o codec usado para codificação de áudio, os valores válidos são: AAC, WMA e DDP
- Contagem de Canais – o número de canais de áudio codificados, os valores válidos são: 1, 2 ou 6
- Extensão padrão – a extensão de arquivo padrão 
- Idioma – o código de idioma BCP-47 representando a linguagem usada no áudio. O padrão atual é "und". 
- Nome de arquivo original – o nome do arquivo carregado no Armazenamento do Azure
- StreamId – a ID do fluxo, conforme definido pelo atributo streamID do elemento <StreamInfo> no arquivo de predefinição 
- Codec de vídeo – o codec usado para codificar, os valores válidos são: H264 e VC1
- Taxa de Bits de Vídeo – a taxa de bits usada durante a codificação do vídeo, especificada em kbps

Essas macros podem ser combinadas em qualquer permutação para controlar o nome dos arquivos gerados pelo Codificador de Serviços de Mídia. Por exemplo, a convenção de nomenclatura padrão é:

	{Original File Name}_{Video Codec}{Video Bitrate}{Audio Codec}{Language}{Channel Count}{Audio Bitrate}.{Default Extension}

A convenção de nomenclatura de arquivo é especificada usando o atributo DefaultMediaOutputFileName do elemento [Preset](https://msdn.microsoft.com/library/azure/dn554334.aspx). Por exemplo:

	<Preset DefaultMediaOutputFileName="{Original file name}{StreamId}_LongOutputFileName{Bit Rate}{Video Codec}{Video Bitrate}{Audio Codec}{Audio Bitrate}{Language}{Channel Count}.{Default extension}"
	  Version="5.0">
	<MediaFile …>
	   <OutputFormat>
	      <MP4OutputFormat StreamCompatibility="Standard">
	         <VideoProfile>
	            <MainH264VideoProfile … >
	               <Streams  AutoSize="False"
	                         FreezeSort="False">
	                  <StreamInfo StreamId="1"
	                              Size="1280, 720">
	                     <Bitrate>
	                       <ConstantBitrate Bitrate="3400"
	                                        IsTwoPass="False"
	                                        BufferWindow="00:00:05" />
	                     </Bitrate>
	                   </StreamInfo>
	                  </Streams>
	               </MainH264VideoProfile>
	            </VideoProfile>
	         </MP4OutputFormat>
	   </OutputFormat>
	</MediaFile>

O codificador inserirá sublinhados entre cada macro, por exemplo, a configuração acima resultará em um nome de arquivo como: MyVideo_H264_4500kpbs_AAC_und_ch2_128kbps.mp4.

##Próximas etapas
Agora que você sabe como criar um trabalho para codificar um ativo, vá para o tópico [Como verificar o andamento do trabalho com os Serviços de Mídia](media-services-rest-check-job-progress.md).

[Azure Marketplace]: https://datamarket.azure.com/
[Encoder Preset]: http://msdn.microsoft.com/library/dn619392.aspx
[How to: Get a Media Processor Instance]: http://go.microsoft.com/fwlink/?LinkId=301732
[How to: Upload an Encrypted Asset]: http://go.microsoft.com/fwlink/?LinkId=301733
[How to: Deliver an Asset by Download]: http://go.microsoft.com/fwlink/?LinkId=301734
[How to Check Job Progress]: http://go.microsoft.com/fwlink/?LinkId=301737
[Task Preset for Azure Media Packager]: http://msdn.microsoft.com/library/windowsazure/hh973635.aspx
 

<!---HONumber=62-->