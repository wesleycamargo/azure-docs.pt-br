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
	ms.date="02/10/2015" 
	ms.author="juliako"/>


#Como codificar um ativo usando o Codificador de Mídia do Azure

Este artigo faz parte da série do [Fluxo de trabalho do Vídeo sob demanda dos Serviços de Mídia](media-services-video-on-demand-workflow.md). 

##Visão geral
Para fornecer vídeo digital pela internet, você deve compactar a mídia. Os arquivos de vídeo digital são muito grandes e podem ser muito grandes para entregar pela internet ou para dispositivos de seus clientes para exibir corretamente. A codificação é o processo de compactação de vídeo e áudio para que seus clientes possam exibir sua mídia.

Os trabalhos de codificação são uma das operações de processamento mais comuns nos serviços de mídia. Você cria trabalhos de codificação para converter arquivos de mídia de uma codificação para outra. Ao codificar, você pode usar o codificador de mídia integrado dos serviços de mídia. Você também pode usar um codificador fornecido por um parceiro de Serviços de Mídia. Os codificadores de terceiros estão disponíveis por meio do Azure Marketplace. Você pode especificar os detalhes das tarefas de codificação usando cadeias de caracteres predefinidas para seu codificador ou usando arquivos de configuração predefinidos. Para ver os tipos de predefinições disponíveis, consulte Predefinições de tarefa para os serviços de mídia do Azure. Se você usou um codificador de terceiros, você deve [validar seus arquivos](https://msdn.microsoft.com/library/azure/dn750842.aspx).

Convém sempre codificar arquivos de mezanino em uma conjunto de MP4 de taxa de bits adaptável e, em seguida, converter o conjunto para o formato desejado usando o [empacotamento dinâmico](https://msdn.microsoft.com/library/azure/jj889436.aspx).

Se seu ativo de saída tiver o armazenamento criptografado, você deverá configurar apolítica de entrega de ativos. Para obter mais informações, consulte [Configurando a política de entrega de ativos](media-services-rest-configure-asset-delivery-policy.md).

##Criar um trabalho com uma única tarefa de codificação 

>[AZURE.NOTE] Ao trabalhar com a API REST dos serviços de mídia, as seguintes considerações se aplicam:
>
>Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos nas suas solicitações HTTP. Para obter mais informações, consulte [Instalação para desenvolvimento de API REST dos serviços de mídia](media-services-rest-how-to-use.md).

>Depois de se conectar com êxito a https://media.windows.net, você receberá um redirecionamento 301 especificando outro URI dos serviços de mídia. Você deve fazer chamadas subsequentes para o novo URI conforme descrito em [Conectar aos serviços de mídia usando a API REST](media-services-rest-connect_programmatically.md). 


O exemplo a seguir mostra como criar e publicar um trabalho com uma tarefa definida para codificar um vídeo em uma determinada resolução e qualidade. Quando estiver codificando com o Codificador de Mídia do Azure, você pode usar as predefinições de tarefa de configuração especificadas [aqui](https://msdn.microsoft.com/library/azure/dn619389.aspx).
	
Solicitação:

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-2233-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336802231&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=gR%2fNaIZgolFLxBOmfSECrp16Mp0Mti3KoePVjBUCzls%3d
	Host: media.windows.net
	Content-Length: 476
	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Broadband 720p", "MediaProcessorId" : "nb:mpid:UUID:70bdc2c3-ebf4-42a9-8542-5afc1e55d217",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Response:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1017
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')
	Server: Microsoft-IIS/7.5
	x-ms-request-id: d2052a71-95b1-4a52-9420-ccca1202a5fb
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Fri, 11 May 2012 21:32:40 GMT
	
	{"d":{"__metadata":{"id":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')","uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"},"Tasks":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/Tasks"}},"OutputMediaAssets":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/OutputMediaAssets"}},"InputMediaAssets":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/InputMediaAssets"}},"Id":"nb:jid:UUID:40dc7bef-6bd9-2247-9f3d-d80bc257d715","Name":"NewTestJob","Created":"\/Date(1336771959431)\/","LastModified":"\/Date(1336771959431)\/","EndTime":null,"Priority":0,"RunningDuration":0,"StartTime":null,"State":0,"TemplateId":null}}

##Criar um trabalho com tarefas encadeadas

Em muitos cenários de aplicativo, os desenvolvedores querem criar uma série de tarefas de processamento. Nos serviços de mídia, você pode criar uma série de tarefas encadeadas. Cada tarefa executa etapas de processamento diferentes e pode usar diferentes processadores de mídia. As tarefas encadeadas podem entregar um ativo de uma tarefa para outra, desempenhando uma sequência linear de tarefas no ativo. No entanto, as tarefas executadas em um trabalho não precisam estar em uma sequência. Quando você cria uma tarefa encadeada, os objetos**ITask** são criados em um único objeto **IJob**.

>[AZURE.NOTE] Atualmente, há um limite de 30 tarefas por trabalho. Se você precisa encadear mais de 30 tarefas, crie mais de um trabalho para conter as tarefas.


	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-4e75-2233-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336802231&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=gR%2fNaIZgolFLxBOmfSECrp16Mp0Mti3KoePVjBUCzls%3d

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
	         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}


##Próximas etapas
Agora que você sabe como criar um trabalho para codificar um ativo, vá para o tópico [Como verificar o andamento do trabalho com os Serviços de Mídia](media-services-rest-check-job-progress.md).

[Azure Marketplace]: https://datamarket.azure.com/
[Predefinição do codificador]: http://msdn.microsoft.com/library/dn619392.aspx
[Como: Obter uma instância do processador de mídia]:http://go.microsoft.com/fwlink/?LinkId=301732
[Como: carregar um ativo criptografado]:http://go.microsoft.com/fwlink/?LinkId=301733
[Como: fornecer um ativo por download]:http://go.microsoft.com/fwlink/?LinkId=301734
[Como verificar o andamento do trabalho]:http://go.microsoft.com/fwlink/?LinkId=301737
[Predefinição de tarefa para o empacotador de mídia do Azure]:http://msdn.microsoft.com/library/windowsazure/hh973635.aspx


<!--HONumber=52-->