<properties 
	pageTitle="Como criar um processador de mídia - Azure" 
	description="Saiba como criar um componente de processador de mídia para codificar, converter o formato, criptografar ou descriptografar conteúdo de mídia dos Serviços de Mídia do Azure." 
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
	ms.date="05/12/2015" 
	ms.author="juliako"/>


#Como obter uma instância do processador de mídia

Este artigo faz parte da série do [Fluxo de trabalho do Vídeo sob demanda dos Serviços de Mídia](media-services-video-on-demand-workflow.md).


##Visão geral

Nos Serviços de Mídia, um processador de mídia é um componente que manipula uma tarefa de processamento específica, como codificação, conversão de formato, criptografia ou descriptografia de conteúdo de mídia. Normalmente, você cria um processador de mídia quando está criando uma tarefa para codificar, criptografar ou converter o formato do conteúdo de mídia.

A tabela a seguir fornece o nome e a descrição de cada processador de mídia disponível.

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>Nome do processador de mídia</th>
       <th>Descrição</th>
	<th>Mais informações</th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Codificador de Mídia do Azure</td>
       <td>Permite que você execute tarefas de codificação usando o Codificador de Mídia do Azure.</td>
       <td><a href="http://msdn.microsoft.com/library/jj129582.aspx">Cadeias de caracteres de predefinição de tarefa para o Codificador de Mídia do Azure</a></td>
    </tr>
    <tr>
       <td>Fluxo de trabalho do Media Encoder Premium</td>
       <td>Permite que você execute tarefas de codificação usando o fluxo de trabalho do Media Encoder Premium.</td>
       <td><a href="http://azure.microsoft.com/documentation/articles/media-services-encode-with-premium-workflow/">Codificar com o fluxo de trabalho do Media Encoder Premium.</a></td>
    </tr>    
	<tr>
        <td>Indexador de Mídia do Azure</td>
        <td>Permite tornar arquivos e conteúdo de mídia pesquisáveis, bem como gerar faixas de legenda e palavras-chave.</td>
		<td><a href="http://azure.microsoft.com/documentation/articles/media-services-index-content/">Indexando arquivos de mídia com o Indexador de Mídia do Azure</a>.</td>
    </tr>
    <tr>
        <td>Empacotador de mídia do Microsoft Azure</td>
        <td>Permite converter ativos de mídia de .mp4 para o formato de smooth streaming. Além disso, permite converter ativos de smooth streaming para o formato Apple HTTP Live Streaming (HLS).</td>
		<td><a href="http://msdn.microsoft.com/library/hh973635.aspx">Cadeias de caracteres de predefinição de tarefa do Empacotador de Mídia do Azure</a></td>
    </tr>
    <tr>
        <td>Criptografador de Mídia do Microsoft Azure</td>
        <td>Permite criptografar ativos de mídia usando a Proteção do PlayReady.</td>
        <td><a href=" http://go.microsoft.com/fwlink/?LinkId=613274">Cadeias de caracteres de predefinição de tarefa do Empacotador de Mídia do Azure</a></td>
    </tr>
	<tr>
		<td>Azure Media Hyperlapse (visualização)</td>
		<td>Permite suavizar "impactos" no vídeo com estabilização do vídeo. Também permite que você a acelere o seu conteúdo em um clipe de consumo.</td>
		<td><a href="http://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db">Azure Media Hyperlapse</a></td>
	</tr>
    <tr>
        <td>Descriptografia do armazenamento</td>
        <td>Permite descriptografar ativos de mídia que foram criptografados usando a criptografia de armazenamento.</td>
		<td>N/D</td>
    </tr>  </tbody>
</table>

<br />

##Obter MediaProcessor

>[AZURE.NOTE]Ao trabalhar com a API REST dos serviços de mídia, as seguintes considerações se aplicam:
>
>Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos nas suas solicitações HTTP. Para obter mais informações, consulte [Configuração para desenvolvimento da API REST dos Serviços de Mídia](media-services-rest-how-to-use.md).

>Depois de se conectar com êxito a https://media.windows.net, você receberá um redirecionamento 301 especificando outro URI dos Serviços de Mídia. Você deve fazer chamadas subsequentes para o novo URI, conforme descrito em [Conectando-se aos Serviços de Mídia usando a API REST](media-services-rest-connect_programmatically.md).



A chamada REST a seguir mostra como obter uma instância do processador de mídia por nome (neste caso, **Azure Media Encoder**).

	
Solicitação:

	GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20Encoder' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-e769-477b-2233-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423635565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=6zwXEn7YJzVJbVCNpqDUjBLuE5iUwsdJbWvJNvpY3%2b8%3d
	x-ms-version: 2.8
	Host: media.windows.net
	
Resposta:
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 273
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 8a291764-4ed7-405d-aa6e-d3ebabb0b3f6
	request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	x-ms-request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 11 Feb 2015 00:19:56 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors","value":[{"Id":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609","Description":"Azure Media Encoder","Name":"Azure Media Encoder","Sku":"","Vendor":"Microsoft","Version":"4.4"}]}


##Próximas etapas
Agora que você sabe como obter uma instância do processador de mídia, vá para o tópico [Como codificar um ativo][] que mostra como usar o Codificador de Mídia do Azure para codificar um ativo.

[Como codificar um ativo]: media-services-rest-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[How to: Connect to Media Services Programmatically]: ../media-services-rest-connect_programmatically/

<!---HONumber=July15_HO4-->