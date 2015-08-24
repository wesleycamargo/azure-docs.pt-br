<properties 
	pageTitle="Carregar arquivos em uma conta de serviços de mídia usando a API REST" 
	description="Saiba como obter o conteúdo de mídia nos serviços de mídia ao criar e carregar ativos." 
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
	ms.date="08/11/2015"
	ms.author="juliako"/>


#Carregar arquivos em uma conta de serviços de mídia usando a API REST

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]
 

Nos serviços de mídia, você pode carregar seus arquivos digitais em um ativo. A entidade [Asset](https://msdn.microsoft.com/library/azure/hh974277.aspx) pode conter vídeo, áudio, imagens, coleções de miniaturas, sequências de texto e arquivos de legendas (e os metadados sobre esses arquivos). Depois que os arquivos são carregados no ativo, o conteúdo é armazenado com segurança na nuvem para processamento e transmissão adicionais.


>[AZURE.NOTE]Os Serviços de Mídia usam o valor da propriedade IAssetFile.Name ao compilar URLs para o conteúdo de streaming (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.). Por esse motivo, não é permitida a codificação de percentual. O valor da propriedade **Name** não pode ter nenhum dos seguintes [caracteres reservados para codificação de percentual](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#". Além disso, pode haver somente um '.' para a extensão de nome de arquivo.

O fluxo de trabalho básico para a ingestão de Ativos é dividido nas seguintes seções:

- Criar um ativo
- Criptografar um ativo (opcional)
- Carregar um arquivo no armazenamento de blob


##Criar um ativo

>[AZURE.NOTE]Ao trabalhar com a API REST dos serviços de mídia, as seguintes considerações se aplicam:
>
>Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos nas suas solicitações HTTP. Para obter mais informações, consulte [Configuração para desenvolvimento da API REST dos Serviços de Mídia](media-services-rest-how-to-use.md).

>Depois de se conectar com êxito a https://media.windows.net, você receberá um redirecionamento 301 especificando outro URI dos Serviços de Mídia. Você deve fazer chamadas subsequentes para o novo URI, conforme descrito em [Conectando-se aos Serviços de Mídia usando a API REST](media-services-rest-connect_programmatically.md).
 
Um ativo é um contêiner para múltiplos tipos ou conjuntos de objetos nos serviços de mídia, incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda codificada. Na API REST, criar um ativo requer enviar solicitação POST para serviços de mídia e colocar qualquer informação de propriedade sobre seus ativos no corpo da solicitação.

Uma das propriedades que você pode especificar quando criar um ativo está em **Opções**. **Opções** é um valor de enumeração que descreve as opções de criptografia em que um ativo pode ser criado. Um valor válido é um dos valores na lista abaixo, não uma combinação de valores.

- **None** = **0**: nenhuma criptografia será usada. Esse é o valor padrão. Observe que ao usar essa opção, seu conteúdo não é protegido quando está em trânsito ou em repouso no armazenamento. Se você pretende enviar um MP4 usando o download progressivo, use essa opção. 

- **StorageEncrypted** = **1**: especifique se você deseja que os arquivos sejam criptografados com a criptografia AES de 256 bits para carregamento e armazenamento.

	Se seu ativo tiver o armazenamento criptografado, você deverá configurar a política de entrega de ativos. Para obter mais informações, consulte [Configurando a política de entrega de ativos](media-services-rest-configure-asset-delivery-policy.md).

- **CommonEncryptionProtected** = **2**: especifique se você estiver carregando arquivos protegidos com um método de criptografia comum (por exemplo, PlayReady).

- **EnvelopeEncryptionProtected** = **4**: especifique se você estiver carregando HLS criptografado com arquivos AES. Observe que os arquivos devem ter sido codificados e criptografados pelo Gerenciador de Transformação.

>[AZURE.NOTE]Se o ativo for usar criptografia, você deve criar uma **ContentKey** e vinculá-lo ao seu ativo conforme descrito no tópico a seguir:[Como criar um ContentKey](media-services-rest-create-contentkey.md). Observe que, depois de carregar os arquivos para o ativo, você precisa atualizar as propriedades de criptografia na entidade **AssetFile** com os valores obtidos durante a criptografia dos **Ativos**. Faça isso usando a solicitação HTTP **MERGE**.


O exemplo a seguir mostra como criar um ativo.

**Solicitação HTTP**

	POST https://media.windows.net/api/Assets HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.11
	Host: media.windows.net
	
	{"Name":"BigBuckBunny.mp4"}
	

**Resposta HTTP**

Se for bem-sucedido, será retornado o seguinte:
	
	HTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 452
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
	request-id: e98be122-ae09-473a-8072-0ccd234a0657
	x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 18 Jan 2015 22:06:40 GMT
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
	   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "State":0,
	   "Created":"2015-01-18T22:06:40.6010903Z",
	   "LastModified":"2015-01-18T22:06:40.6010903Z",
	   "AlternateId":null,
	   "Name":"BigBuckBunny.mp4",
	   "Options":0,
	   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StorageAccountName":"storagetestaccount001"
	}
	
##Criar um AssetFile

A entidade [AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) representa um arquivo de áudio ou vídeo que é armazenado em um contêiner de blob. Um arquivo de ativo está sempre associado a um ativo e um ativo pode conter um ou vários arquivos de ativo. A tarefa do Codificador dos serviços de mídia falha se um objeto de arquivo de ativo não estiver associado um arquivo digital em um contêiner de blob.

Observe que a instância de **AssetFile** e o arquivo de mídia real são dois objetos diferentes. A instância de AssetFile contém metadados sobre o arquivo de mídia, enquanto o arquivo de mídia contém o conteúdo de mídia real.

Depois de carregar o arquivo de mídia digital em um contêiner de blob, você usará a solicitação HTTP **MERGE** para atualizar o AssetFile com informações sobre o arquivo de mídia (como mostrado posteriormente no tópico).

**Solicitação HTTP**

	POST https://media.windows.net/api/Files HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.11
	Host: media.windows.net
	Content-Length: 164
	
	{  
	   "IsEncrypted":"false",
	   "IsPrimary":"false",
	   "MimeType":"video/mp4",
	   "Name":"BigBuckBunny.mp4",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
	}


**Resposta HTTP**

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 535
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
	Server: Microsoft-IIS/8.5
	request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
	x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 00:34:07 GMT
	
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
	   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
	   "Name":"BigBuckBunny.mp4",
	   "ContentFileSize":"0",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "EncryptionVersion":null,
	   "EncryptionScheme":null,
	   "IsEncrypted":false,
	   "EncryptionKeyId":null,
	   "InitializationVector":null,
	   "IsPrimary":false,
	   "LastModified":"2015-01-19T00:34:08.1934137Z",
	   "Created":"2015-01-19T00:34:08.1934137Z",
	   "MimeType":"video/mp4",
	   "ContentChecksum":null
	}


## Criando o AccessPolicy com permissão de gravação. 

Antes de carregar todos os arquivos no armazenamento de blob, defina os direitos de política de acesso para gravar em um ativo. Para fazer isso, POSTE uma solicitação HTTP para o conjunto de entidade AccessPolicies. Defina um valor de DurationInMinutes durante a criação ou você receberá uma mensagem de erro de servidor interno 500 em resposta. Para saber mais sobre AccessPolicies, consulte [AccessPolicy](http://msdn.microsoft.com/library/azure/hh974297.aspx).

O exemplo a seguir mostra como criar um AccessPolicy:
		
**Solicitação HTTP**

	POST https://media.windows.net/api/AccessPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.11
	Host: media.windows.net
	
	{"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**Solicitação HTTP**

	If successful, the following response is returned:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 312
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
	Server: Microsoft-IIS/8.5
	request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
	x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 18 Jan 2015 22:18:06 GMT

	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
	   "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "Created":"2015-01-18T22:18:06.6370575Z",
	   "LastModified":"2015-01-18T22:18:06.6370575Z",
	   "Name":"NewUploadPolicy",
	   "DurationInMinutes":440.0,
	   "Permissions":2
	}

##Obter a URL de carregamento

Para receber a URL de carregamento real, crie um localizador de SAS. Os localizadores definem a hora de início e o tipo de ponto de extremidade de conexão para clientes que desejam acessar arquivos em um ativo. Você pode criar várias entidades de localizador para um determinado par de AccessPolicy e ativos para manipular solicitações e necessidades de clientes diferentes. Cada um destes localizadores usam o valor StartTime mais o valor de DurationInMinutes do AccessPolicy para determinar quanto tempo uma URL pode ser usada. Para saber mais, consulte [Localizador](http://msdn.microsoft.com/library/azure/hh974308.aspx).


Uma URL SAS tem o seguinte formato:

	{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Algumas considerações se aplicam:

- Você não pode ter mais do que cinco localizadores exclusivos associados a um determinado ativo ao mesmo tempo. Para saber mais, consulte Localizador.
- Se você precisar carregar os arquivos imediatamente, você deve definir o valor StartTime como cinco minutos antes da hora atual. Isso ocorre porque pode haver uma defasagem horária entre o computador do cliente e os serviços de mídia. Além disso, o valor de StartTime deve estar no seguinte formato DateTime: AAAA-MM-DDTHH:mm:ssZ (por exemplo, "2014-05-23T17:53:50Z").	
- Pode haver um 30 a 40 segundos de atraso após a criação de um localizador quando ele está disponível para uso. Esse problema se aplica a URL SAS e localizadores de origem.

O exemplo a seguir mostra como criar um localizador URL SAS, conforme definido pela propriedade Type no corpo da solicitação ("1" para um localizador SAS e "2" para um localizador de origem sob demanda). A propriedade **Path** retornada contém a URL que você deve usar para carregar seu arquivo.
	
**Solicitação HTTP**
	
	POST https://media.windows.net/api/Locators HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.11
	Host: media.windows.net
	{  
	   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StartTime":"2015-02-18T16:45:53",
	   "Type":1
	}


**Resposta HTTP**

Se for bem-sucedido, será retornada a seguinte resposta:
		
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 949
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
	Server: Microsoft-IIS/8.5
	request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
	x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 03:01:29 GMT
	
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
	   "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
	   "ExpirationDateTime":"2015-02-19T00:05:53",
	   "Type":1,
	   "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
	   "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
	   "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
	   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StartTime":"2015-02-18T16:45:53",
	   "Name":null
	}

## Carregar um arquivo em um contêiner de armazenamento de blob
	
Depois de definir AccessPolicy e localizador, o arquivo real é carregado como um contêiner de armazenamento de blobs do Azure usando as APIs de REST do armazenamento do Azure. Você pode carregar na página ou em blobs de blocos.

>[AZURE.NOTE]Você deve adicionar o nome do arquivo para o arquivo que você deseja carregar no valor **Path** do Localizador recebido na seção anterior. Por exemplo, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .

Para saber mais sobre como trabalhar com blobs de armazenamento do Azure, consulte [API REST do serviço Blob](http://msdn.microsoft.com/library/azure/dd135733.aspx).


## Atualizar o AssetFile 

Agora que você carregou o arquivo, atualize as informações de tamanho do FileAsset (e outros). Por exemplo:
	
	MERGE https://media.windows.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.11
	Host: media.windows.net
	
	{  
	   "ContentFileSize":"1186540",
	   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
	   "MimeType":"video/mp4",
	   "Name":"BigBuckBunny.mp4",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
	}


**Resposta HTTP**

Se for bem-sucedido, será retornado o seguinte: HTTP/1.1 204 No Content

## Excluir o AccessPolicy e localizador 

**Solicitação HTTP**


	DELETE https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.11
	Host: media.windows.net

	
**Resposta HTTP**

Se for bem-sucedido, será retornado o seguinte:

	HTTP/1.1 204 No Content 
	...

**Solicitação HTTP**

	DELETE https://media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.11
	Host: media.windows.net

**Resposta HTTP**

Se for bem-sucedido, será retornado o seguinte:

	HTTP/1.1 204 No Content 
	...

 
[How to Get a Media Processor]: media-services-get-media-processor.md
 

<!---HONumber=August15_HO7-->