<properties 
	pageTitle="Notas de versão dos Serviços de Mídia" 
	description="Notas de versão dos Serviços de Mídia" 
	services="media-services" 
	documentationCenter="media" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="media" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="juliako"/>


# Notas de versão dos Serviços de Mídia do Azure

Estas notas de versão resumem as alterações de versões anteriores e os problemas conhecidos.

>[AZURE.NOTE] Queremos ouvir nossos clientes e focar na correção de problemas que afetam você. Para relatar um problema ou fazer uma pergunta, publique no [Fórum MSDN de Serviços de Mídia do Azure].

- [Problemas conhecidos no momento](#issues)
- [Histórico de versões da API REST](#rest_version_history)
- [Versão de janeiro de 2015](#january_changes_15)
- [Versão de dezembro de 2014](#december_changes_14)
- [Versão de novembro de 2014](#november_changes_14)
- [Versão de outubro de 2014](#october_changes_14)
- [Versão de setembro de 2014](#september_changes_14)
- [Versão de agosto de 2014](#august_changes_14)
- [Versão de julho de 2014](#july_changes_14)
- [Versão de maio de 2014](#may_changes_14)
- [Versão de abril de 2014](#april_changes_14) 
- [Versões de janeiro\fevereiro de 2014](#jan_feb_changes_14) 
- [Versão de dezembro de 2013](#december_changes_13)
- [Versão de novembro de 2013](#november_changes_13)
- [Versão de agosto de 2013](#august_changes_13)
- [Versão de junho de 2013](#june_changes_13)
- [Versão de dezembro de 2012](#december_changes_12)
- [Versão de novembro de 2012](#november_changes_12)
- [Versão de visualização de junho de 2012](#june_changes_12)


## <a id="issues"></a>Problemas conhecidos no momento

### <a id="general_issues"></a>Problemas gerais dos Serviços de Mídia

<table border="1">
<tr><th>Problema</th><th>Descrição</yt></tr>
<tr><td>Vários cabeçalhos HTTP comuns não são fornecidos na API REST.</td><td>Se você desenvolver aplicativos de Serviços de Mídia usando a API REST, verá que não há suporte a alguns campos de cabeçalho HTTP comuns (incluindo CLIENT-REQUEST-ID, REQUEST-ID e RETURN-CLIENT-REQUEST-ID). Os cabeçalhos serão adicionados em uma atualização futura.</td></tr>
<tr><td>Codificar um ativo com um nome de arquivo que contenha caracteres de escape (por exemplo, %20) falha com "MediaProcessor : Arquivo não encontrado."</td><td>Nomes de arquivos que serão adicionados a um ativo e, então, codificados devem conter apenas caracteres e espaços alfanuméricos. O problema será corrigido em uma atualização futura.</td></tr>
<tr><td>O método ListBlobs que faz parte do SDK do Armazenamento do Azure versão 3.x falha.</td><td>Os Serviços de Mídia geram URLs SAS com base na versão de <a href="http://msdn.microsoft.com/library/azure/dn592123.aspx">12/02/2012</a> . Se você quiser usar o SDK de armazenamento do Azure para listar blobs em um contêiner de blob, use o método <a href="http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx">CloudBlobContainer.ListBlobs</a> que faz parte do SDK do armazenamento do Azure versão 2.x. O método ListBlobs que faz parte do SDK do Armazenamento do Azure versão 3.x falhará.</td></tr>
<tr><td>O mecanismo de aceleração dos Serviços de Mídia restringe o uso dos recursos para aplicativos que fazem solicitações excessivas ao serviço. O serviço pode retornar o código de status HTTP Serviço Não Disponível (503).</td><td>Para obter mais informações, consulte a descrição do código de status HTTP 503 no tópico <a href="http://msdn.microsoft.com/library/azure/dn168949.aspx">Códigos de erro dos Serviços de Mídia do Azure</a> .</td></tr>
</table><br/>

### <a id="dotnet_issues"></a>SDK dos Serviços de Mídia para .NET

<table border="1">
<tr><th>Problema</th><th>Descrição</yt></tr>
<tr><td>Os objetos de Serviços de Mídia no SDK não podem ser serializados, e, como resultado, não funcionam com Caching do Azure.</td><td>Se você tentar serializar o objeto AssetCollection do SDK para adicioná-lo ao Caching do Azure, uma exceção será lançada.</td></tr>
</table><br/>

## <a id="rest_version_history"></a>Histórico de versões da API REST

Para obter informações sobre o histórico de versões da API REST dos Serviços de Mídia, consulte [Referência da API REST dos Serviços de Mídia do Azure].

## <a id="january_changes_15"></a>Versão de janeiro de 2015

### Atualizações gerais dos Serviços de Mídia

Anúncio da disponibilidade geral (GA) de proteção de conteúdo com criptografia dinâmica. Para obter mais informações, consulte [serviços de mídia do Azure aprimora a segurança de streaming com a disponibilidade geral da tecnologia DRM](http://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### Atualizações do SDK do .NET dos Serviços de Mídia

O SDK do .NET dos Serviços de Mídia do Azure está agora na versão 3.1.0.1.

Esta versão marcou o construtor Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestriction padrão como obsoleto. O novo construtor usa TokenType como um argumento.

	TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>Versão de dezembro de 2014

### Atualizações gerais dos Serviços de Mídia

- Algumas atualizações e novos recursos foram adicionados ao processador de mídia do indexador do Azure. Para obter mais informações, consulte [Notas de versão de indexador de mídia do Azure 1.1.6.7](http://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
- Adicionada uma nova API de REST que permite que você atualize unidades reservadas de codificação: [EncodingReservedUnitType com REST](http://msdn.microsoft.com/library/azure/dn859236.aspx).
- Acrescentado suporte a CORS para o serviço de distribuição de chaves.
- Foram feitos aprimoramentos de desempenho nas opções das políticas de autorização de consulta.
- No data center da China, a [URL de entrega de chaves](http://msdn.microsoft.com/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url) agora é por cliente (assim como em outros data centers).
- Duração de destino automático HLS adicionada. Ao fazer streaming ao vivo, o HLS é sempre empacotado dinamicamente. Por padrão, os Serviços de Mídia calculam automaticamente a taxa de empacotamento de segmento HLS (FragmentsPerSegment) com base no intervalo entre quadros-chave (KeyFrameInterval), também conhecido como grupo de imagens - GOP, que é recebido do codificador ao vivo. Para obter mais informações, consulte [Trabalhando com Live Streaming dos Serviços de Mídia do Azure].
 
### Atualizações do SDK do .NET dos Serviços de Mídia

- [O SDK do .NET dos Serviços de Mídia do Azure](http://www.nuget.org/packages/windowsazure.mediaservices/) está agora na versão 3.1.0.0.
- Atualizada a dependência do SDK do .Net para .NET 4.5 Framework.
- Adicionada uma nova API que permite que você atualize unidades reservadas de codificação. Para obter mais informações, consulte [Atualizando tipo de unidade reservada e aumentando unidades reservadas de codificação usando o .NET](http://msdn.microsoft.com/library/azure/jj129582.aspx).
- Acrescentado suporte a JWT (JSON Web Token) para autenticação via token. Para obter mais informações, consulte [Autenticação de token JWT nos Serviços de Mídia do Azure e criptografia dinâmica](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
- Adicionados deslocamentos relativos para BeginDate e ExpirationDate no modelo de licença PlayReady.


## <a id="november_changes_14"></a>Versão de novembro de 2014

- Os Serviços de Mídia agora permitem que você inclua um conteúdo de Smooth Streaming (FMP4) ao vivo em uma conexão SSL. Para inserir por SSL, certifique-se de atualizar a URL de inserção para HTTPS.  Para obter mais informações sobre live streaming, consulte [Trabalhando com Live Streaming dos Serviços de Mídia do Azure].
- Observe que no momento, você não pode inserir um fluxo ao vivo RTMP por uma conexão SSL.
- Você também pode transmitir seu conteúdo por uma conexão SSL. Para fazer isso, certifique-se de que suas URLs de streaming começam com HTTPS.
- Observe que você só pode transmitir por SSL se o ponto de extremidade de streaming por meio do qual você pode distribuir o conteúdo tiver sido criado depois de 10 de setembro de 2014. Se suas URLs de streaming baseiam-se nos pontos de extremidade de streaming após 10 de setembro, a URL contém "streaming.mediaservices.windows.net" (o novo formato). URLs de streaming que contêm "origin.mediaservices.windows.net" (o formato antigo) não dão suporte a SSL. Se sua URL está no formato antigo e você deseja ser capaz de transmitir por SSL, [crie um novo ponto de extremidade de streaming](http://azure.microsoft.com/documentation/articles/media-services-manage-origins/). Use URLs criadas com base no novo ponto de extremidade de streaming para transmitir seu conteúdo por SSL.
   
## <a id="october_changes_14"></a>Versão de outubro de 2014

### <a id="new_encoder_release"></a>Versão do codificador de Serviços de Mídia

Anúncio da nova versão do Codificador de Mídia do Azure dos Serviços de Mídia. Com o mais recente codificador de Mídia do Azure, você é cobrado por GBs de saída, mas, por outro lado, o novo codificador tem recursos compatíveis com o Codificador de Mídia do Microsoft Azure. Para mais informações, consulte [Detalhes dos preços dos Serviços de Mídia]).

### <a id="oct_sdk"></a>SDK do .NET dos Serviços de Mídia 

O SDK dos Serviços de Mídia para extensões .NET agora está na versão 2.0.0.3.

O SDK dos Serviços de Mídia para .NET agora está na versão 3.0.0.8.

As seguintes alterações foram feitas:

- Refatoração em classes de política de repetição.
- Adicionando a sequência do agente do usuário para os cabeçalhos de solicitação http.
- Adicionando a etapa de compilação de restauração do nuget.
- Corrigindo testes de cenário para usar certificado X509 do repositório.
- Validando configurações durante a atualização de canal e do ponto de extremidade de streaming.
 

### Novo repositório GitHub para hospedar exemplos de Serviços de Mídia

Os exemplos estão localizados em [Repositório GitHub de exemplos dos Serviços de Mídia do Azure](https://github.com/Azure/Azure-Media-Services-Samples).


## <a id="september_changes_14"></a>Versão de setembro de 2014

Os metadados REST dos Serviços de Mídia agora estão na versão 2.7. Para obter mais informações sobre as últimas atualizações de REST, consulte a [Referência da API REST dos Serviços de Mídia do Azure].

SDK dos Serviços de Mídia para .NET agora está na versão 3.0.0.7
 
### <a id="sept_14_breaking_changes"></a>Alterações de última hora

* **Origin** foi renomeado para [StreamingEndpoint].
* Uma mudança no comportamento padrão ao usar o **Portal de Gerenciamento do Azure** para codificar e depois publicar arquivos MP4. 

	Anteriormente, ao usar o Portal de Gerenciamento para publicar um ativo de vídeo MP4 de arquivo único, uma URL SAS seria criada (URLs SAS permitem baixar o vídeo de um armazenamento blob). No momento, quando se usa o Portal de Gerenciamento para codificar e depois publicar um ativo de vídeo MP4 de arquivo único, a URL gerada aponta para um ponto de extremidade de streaming dos Serviços de Mídia do Azure.  Essa mudança não afeta vídeos MP4 que são carregados diretamente para os Serviços de Mídia e publicados sem serem codificados pelos Serviços de Mídia do Azure.
	
	No momento, há as duas opções a seguir para solucionar o problema. 
	
	* Habilitar unidades de streaming e usar empacotamento dinâmico para transmitir o ativo .mp4 como uma apresentação de smooth streaming.
	
	* Criar uma URL SAS para baixar (ou reproduzir progressivamente) o .mp4. Para obter mais informações sobre como criar um localizador SAS, consulte [Entregando Conteúdo]. 


### <a id="sept_14_GA_changes"></a>Novos recursos\cenários que fazem parte da versão do GA

* **Processador de mídia do indexador**. Para obter mais informações, consulte [Indexando arquivos de mídia com o Indexador de Mídia do Azure].

* A entidade [StreamingEndpoint] permite agora que você acrescente nomes de domínio (host) personalizados.

	Para um nome de domínio personalizado ser usado como nome do ponto de extremidade de streaming dos Serviços de Mídia, é preciso adicionar nomes de host personalizados ao ponto de extremidade de streaming. Use as APIs REST dos Serviços de Mídia ou o SDK do .NET para adicionar nomes de host personalizados.
	
	As seguintes considerações se aplicam:
	
	* É preciso ter propriedade do nome de domínio personalizado.
	
	* A propriedade do nome de domínio deve ser validada pelos Serviços de Mídia do Azure. Para validar o domínio, criar um CName que mapeia <ID_de_Conta_de_Serviços_de_Mídia>. <domínio_pai> verifydns. <mediaservices-dns-zone>. 
	
	* Você deve criar outro CName que mapeia o nome de host personalizado (por exemplo, sports.contoso.com) em nome de host do seu StreamingEndpont dos serviços de mídia (por exemplo, amstest.streaming.mediaservices.windows.net).


	Para obter mais informações, consulte a propriedade **CustomHostNames** no tópico [StreamingEndpoint].

### <a id="sept_14_preview_changes"></a>Novos recursos\cenários que fazem parte da versão de visualização pública

* Visualização de Live Streaming. Para obter mais informações, consulte [Trabalhando com Live Streaming dos Serviços de Mídia do Azure].

* Serviço de entrega de chave. Para obter mais informações, consulte [Usando o serviço de entrega de chave e criptografia dinâmica do AES-128].

* Criptografia dinâmica do EAS. Para obter mais informações, consulte [Usando o serviço de entrega de chave e criptografia dinâmica do AES-128].

* Serviço de entrega de licença do PlayReady. Para obter mais informações, consulte [Usando o serviço de entrega de licença e criptografia dinâmica do PlayReady].

* Criptografia dinâmica do PlayReady. Para obter mais informações, consulte [Usando o serviço de entrega de licença e criptografia dinâmica do PlayReady].

* Modelo de licença do PlayReady dos Serviços de Mídia. Para obter mais informações, consulte [Visão geral do modelo de licença do PlayReady dos Serviços de Mídia].

* Streaming de ativos criptografados de armazenamento. Para obter mais informações, consulte [Streaming de conteúdo criptografado de armazenamento].

## <a id="august_changes_14"></a>Versão de agosto de 2014

Ao codificar um ativo, um ativo de saída é produzido quando o trabalho de codificação é concluído. Até esta versão, o Codificador de Serviços de Mídia do Azure produzia metadados sobre os ativos de saída. A partir desta versão, o codificador também produz metadados sobre ativos de entrada. Para obter mais informações, consulte os tópicos [Metadados de entrada] e [Metadados de saída].


## <a id="july_changes_14"></a>Versão de julho de 2014

As seguintes correções de erro foram feitas ao Empacotador e Criptografador dos Serviços de Mídia do Azure:

* Apenas reproduções de áudio ao realizar transmux de um ativo de arquivo em tempo real para Live Streaming HTTP - isso foi corrigido e agora tanto áudio quanto vídeo são reproduzidos.

* Ao empacotar um ativo para Live Streaming HTTP e criptografia de envelope de 128 bits, os fluxos de pacote não são reproduzidos em dispositivos Android - esse erro foi corrigido e o fluxo empacotado é reproduzido em dispositivos Android com suporte a Live Streaming HTTP.

## <a id="may_changes_14"></a>Versão de maio de 2014

### <a id="may_14_changes"></a>Atualizações gerais dos Serviços de Mídia

Agora você pode usar [empacotamento dinâmico] para fluxo HTTP Live Streaming (HLS) v3. Para transmitir HLS v3, adicione o seguinte formato ao caminho do localizador de origem: *.ism/manifest(format=m3u8-aapl-v3). Para obter mais informações, consulte o [Blog de Nick Drouin].

O Empacotamento Dinâmico agora também oferece suporte à entrega de HLS (v3 e v4) criptografado com PlayReady com base em Smooth Streaming estaticamente criptografado com PlayReady. Para obter informações sobre como criptografar Smooth Streaming com PlayReady, consulte [Protegendo Smooth Stream com PlayReady].

### <a name="may_14_donnet_changes"></a>Atualizações do SDK do .NET dos Serviços de Mídia

As seguintes melhorias são incluídas na versão 3.0.0.5 do SDK do .NET dos Serviços de Mídia:

* Melhor velocidade e resiliência para carregar/baixar ativos de mídia.

* Melhorias na lógica de repetição e manipulação de exceção temporária: 

	* Detecção de erro temporário e lógica de repetição foram aprimorados para as exceções causadas por consultar, salvar alterações, carregar ou baixar arquivos. 
	
	* Ao obter Exceções da Web (por exemplo, durante uma solicitação de token ACS), você observará que erros fatais estão falhando mais rapidamente agora.

Para obter mais informações, consulte [Lógica de repetição no SDK de Serviços de Mídia para .NET].

## <a id="april_changes_14"></a>Versão do Codificador de abril de 2014

### <a name="april_14_enocer_changes"></a>Atualizações do Codificador de Serviços de Mídia

* Adicionado suporte para capturar arquivos AVI criados usando o editor não linear Grass Valley EDIUS, em que o vídeo é levemente compactado usando o codec Grass Valley HQ/HQX. Para obter mais informações, consulte [Grass Valley anuncia streaming EDIUS 7 pela nuvem].

* Adicionado suporte para especificar a convenção de nomenclatura para arquivos produzidos pelo Codificador de Mídia. Para obter mais informações, consulte [Controlando nomes de arquivo de saída do Codificador do Serviço de Mídia].

* Adicionado suporte para sobreposições de vídeo e/ou áudio. Para obter mais informações, consulte [Criando sobreposições].

* Adicionado suporte para unir vários segmentos de vídeo. Para obter mais informações, consulte [Unindo segmentos de vídeo].

* Corrigido um erro relacionado à transcodificação de MP4s em que o áudio havia sido codificado com a camada de Áudio 3 MPEG-1 (ou seja, MP3).


## <a id="jan_feb_changes_14"></a>Versões de janeiro\fevereiro de 2014

### <a name="jan_fab_14_donnet_changes"></a>SDK do .NET dos Serviços de Mídia do Azure 3.0.0.1, 3.0.0.2 e 3.0.0.3

As alterações em 3.0.0.1 e 3.0.0.2 incluem:

* Corrigidos problemas relacionados ao uso de consultas LINQ com declarações OrderBy.

* Divida as soluções de teste em [Github] em testes baseados em unidade e testes baseados em cenário.

Para obter mais detalhes sobre as alterações, consulte: [SDK do .NET dos Serviços de Mídia do Azure 3.0.0.1 e 3.0.0.2].

As seguintes alterações foram feitas na versão 3.0.0.3:

* Atualizadas as dependências de armazenamento do Azure para usar a versão 3.0.3.0. 

* Corrigido o problema de compatibilidade reversa para versões 3.0.*.*. 


## <a id="december_changes_13"></a>Versão de dezembro de 2013

### <a name="dec_13_donnet_changes"></a>SDK do .NET dos Serviços de Mídia do Azure 3.0.0.0

>[AZURE.NOTE] As versões 3.0.x.x não são compatíveis com versões 2.4.x.x.

A versão mais recente do SKD dos Serviços de Mídia agora é a 3.0.0.0. É possível baixar o pacote mais recente do Nuget ou obter os bits do [Github].

A partir do SDK de Serviços de Mídia versão 3.0.0.0, é possível reutilizar os tokens do [Serviço de Controle de Acesso (ACS) do Active Directory do Azure]. Para obter mais informações, consulte a seção "Reutilizando tokens de serviço de controle de acesso" no tópico [Conectando-se a Serviços de Mídia com o SDK de Serviços de Mídia para .NET].

### <a name="dec_13_donnet_ext_changes"></a>Extensões do SDK do .NET dos Serviços de Mídia do Azure 2.0.0.0

As Extensões do SDK do .NET dos Serviços de Mídia do Azure são um conjunto de métodos de extensão e funções auxiliares que simplificarão seu código e tornarão mais fácil desenvolver com os Serviços de Mídia do Azure. Você pode obter a versão mais recente das [Extensões do SDK do .NET dos Serviços de Mídia do Azure].

## <a id="november_changes_13"></a>Versão de novembro de 2013

### <a name="nov_13_donnet_changes"></a>Alterações do SDK do .NET dos Serviços de Mídia do Azure

A partir desta versão, o SDK para .NET dos Serviços de Mídia manipula erros de falhas temporários que podem ocorrer ao fazer chamadas à camada da API REST dos Serviços de Mídia.

## <a id="august_changes_13"></a>Versão de agosto de 2013

### <a name="aug_13_powershell_changes"></a>Cmdlets do Powershell dos Serviços de Mídia incluídos nas Ferramentas de SDK do Azure

Os seguintes cmdlets do PowerShell dos Serviços de Mídia agora estão incluídos em [azure-sdk-tools].

* Get-AzureMediaServices 

	Por exemplo, `Get-AzureMediaServicesAccount`.

* New-AzureMediaServicesAccount 

	Por exemplo, `New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`.

* New-AzureMediaServicesKey 

	Por exemplo, `New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`.

* Remove-AzureMediaServicesAccount 

	Por exemplo, `Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`.

## <a id="june_changes_13"></a>Versão de junho de 2013

### <a name="june_13_general_changes"></a>Alterações dos Serviços de Mídia do Azure

As alterações mencionadas nesta seção são atualizações incluídas nas versões de Serviços de Mídia de junho de 2013.

* Habilidade de vincular várias contas de armazenamento a uma conta de Serviço de Mídia. 

	StorageAccount
	
	Asset.StorageAccountName e Asset.StorageAccount

* Habilidade de atualizar Job.Priority. 

* Entidades e propriedades relacionadas à notificação: 

	JobNotificationSubscription
	
	NotificationEndPoint
	
	Trabalho

* Asset.Uri 

* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Alterações do SDK do .NET dos Serviços de Mídia do Azure

As seguintes alterações foram incluídas em versões do SDK dos Serviços de Mídia de junho de 2013. O SDK dos Serviços de Mídia mais recente está disponível em GitHub.

* A partir da versão 2.3.0.0, o SDK dos Serviços de Mídia oferece suporte para vincular várias contas de armazenamento a uma conta de Serviços de Mídia. As seguintes APIs têm suporte a esse recurso:
	
	O tipo IStorageAccount.
	
	A propriedade Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts.
	
	A propriedade StorageAccount.
	
	A propriedade StorageAccountName.
	
	Para obter mais informações, consulte [Gerenciando ativos de Serviços de Mídia através de várias contas de armazenamento].

* APIs relacionadas à notificação. A partir da versão 2.2.0.0, é possível escutar as notificações de armazenamento de Fila do Azure. Para obter mais informações, consulte [Manipulando notificações de trabalho dos Serviços de Mídia].
	
	A propriedade Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions.
	
	O tipo Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint.
	
	O tipo Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription.
	
	O tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection.
	
	O tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType.
	
	O tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState.

* Dependência do SDK do Cliente de Armazenamento do Azure 2.0 (Microsoft.WindowsAzure.StorageClient.dll).

* Dependência de OData 5.5 (Microsoft.Data.OData.dll).


## <a id="december_changes_12"></a>Versão de dezembro de 2012

### <a name="dec_12_dotnet_changes"></a>Alterações do SDK do .NET dos Serviços de Mídia do Azure

* Intellisense : Adicionada documentação do Intellisense ausente para muitos tipos.

* Microsoft.Practices.TransientFaultHandling.Core : Corrigido um problema em que o SDK ainda possuía uma dependência com uma versão antiga desse conjunto. O SDK agora faz referência à versão 5.1.1209.1 desse conjunto.

Correções para problemas encontrados no SDK de novembro de 2012:

* IAsset.Locators.Count : Essa contagem agora é relatada corretamente em novas interfaces IAsset depois de todos os localizadores terem sido excluídos.

* IAssetFile.ContentFileSize : Esse valor agora é definido adequadamente após um upload por IAssetFile.Upload(filePath).

* IAssetFile.ContentFileSize : Essa propriedade agora pode ser definida ao criar um arquivo de ativo. Antes era somente leitura.

* IAssetFile.Upload(filepath) : Foi corrigido um problema em que esse método de carregamento assíncrono lançava o seguinte erro ao carregar vários arquivos para o ativo. O erro era "O servidor falhou em autenticar a solicitação. Certifique-se de que o valor do cabeçalho Autorização seja formado corretamente, incluindo a assinatura."

* IAssetFile.UploadAsync : Corrigido um problema em que não mais de cinco arquivos podiam ser carregados ao mesmo tempo.

* IAssetFile.UploadProgressChanged : Esse evento agora é fornecido pelo SDK.

* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): Agora essa sobrecarga de método é fornecida.

* IAssetFile.DownloadAsync : Corrigido um problema em que não mais de cinco arquivos podiam ser baixados ao mesmo tempo.

* IAssetFile.Delete() : Corrigido um problema em que chamar o comando "excluir" poderia lançar uma exceção, caso nenhum arquivo fosse carregado para o IAssetFile.

* Trabalhos: Corrigido um problema em que encadear uma "Tarefa de MP4 para Smooth Streams" com uma "Tarefa de proteção do PlayReady" usando um modelo de trabalho não criaria nenhuma tarefa.

* EncryptionUtils.GetCertificateFromStore() : Esse método não lança mais uma exceção de referência nula devido a falhas em encontrar o certificado com base em problemas de configuração de certificado.

## <a id="november_changes_12"></a>Versão de novembro de 2012

As alterações mencionadas nesta seção eram atualizações incluídas no SDK de novembro de 2012 (versão 2.0.0.0). Essas alterações podem exigir que qualquer código escrito para a versão do SDK de visualização de junho de 2012 seja modificado ou reescrito.

* Ativos
	
	IAsset.Create(assetName) é a ÚNICA função de criação de ativo. IAsset.Create não carrega mais arquivos como parte do método de chamada. Use IAssetFile para carregar.
	
	O método IAsset.Publish e o valor de enumeração AssetState.Publish foram removidos do SDK de Serviços. Qualquer código que conte com esse valor deve ser reescrito.

* FileInfo

	Essa classe foi removida e substituída pelo IAssetFile.

	IAssetFiles

	IAssetFile substitui FileInfo e tem um comportamento diferente. Para usá-la, instancie o objeto IAssetFiles, seguido por um carregamento de arquivo usando o SDK de Serviços de Mídia ou o SDK de Armazenamento do Azure. As seguintes sobrecargas IAssetFile.Upload podem ser usadas:

	* IAssetFile.Upload(filePath): Um método síncrono que bloqueia o encadeamento e é recomendado apenas ao carregar um único arquivo.
	
	* IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): Um método assíncrono. Esse é o mecanismo de carregamento preferido. 

	Bug conhecido: usar o cancellationToken, de fato, cancelará o carregamento; porém, o estado de cancelamento das tarefas pode ser qualquer um entre vários estados. É preciso capturar e tratar as exceções adequadamente.

* Localizadores
	
	A versão específica da origem foi removida. O context.Locators.CreateSasLocator(ativo, accessPolicy) específico do SAS será marcado como preterido, ou removido pelo GA. Consulte a seção Localizadores em Nova Funcionalidade para comportamento atualizado.


## <a id="june_changes_12"></a>Versão de visualização de junho de 2012

A seguinte funcionalidade era nova na versão de novembro do SDK.

* Excluindo entidades

	Os objetos IAsset, IAssetFile, ILocator, IAccessPolicy, IContentKey agora são excluídos no nível do objeto, ou seja, IObject.Delete(), em vez de exigir uma exclusão na Coleção, que é cloudMediaContext.ObjCollection.Delete(objInstance).

* Localizadores

	O Localizadores agora devem ser criados usando o método CreateLocator e usando os valores enum LocatorType.SAS ou LocatorType.OnDemandOrigin como argumento para o tipo específico de localizador que você deseja criar.

	Novas propriedades foram adicionadas a Localizadores para tornar mais fácil obter URIs usáveis para seu conteúdo. Essa remodelagem de Localizadores foi feita para fornecer mais flexibilidade para capacidade de extensão de terceiros e aumentar a facilidade de uso para aplicativos cliente de mídia.

* Suporte ao método assíncrono

	O suporte assíncrono foi adicionado a todos os métodos.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Fórum do MSDN dos Serviços de Mídia do Azure]: http://social.msdn.microsoft.com/forums/azure/en-US/home?forum=MediaServices
[Referência da API REST dos Serviços de Mídia do Azure]: http://msdn.microsoft.com/library/azure/hh973617.aspx 
[Detalhes dos preços dos Serviços de Mídia]: http://azure.microsoft.com/ pricing/details/media-services/
[Metadados de entrada]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Metadados de saída]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Entregando conteúdo]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Indexando arquivos de mídia com o Indexador de Mídia do Azure]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Trabalhando com Live Streaming dos Serviços de Mídia do Azure]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Usando o serviço de entrega de chave e criptografia dinâmica do AES-128]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Usando o serviço de entrega de licença e criptografia dinâmica do PlayReady]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Recursos de visualização]: http://azure.microsoft.com/ services/preview/
[Visão geral do modelo de licença do PlayReady dos Serviços de Mídia]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Streaming de conteúdo criptografado de armazenamento]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
[Empacotamento dinâmico]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Blog de Nick Drouin]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protegendo Smooth Stream com PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Lógica de repetição no SDK de Serviços de Mídia para .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley anuncia Streaming EDIUS 7 pela nuvem]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Controlando nomes de arquivo de saída do Codificador do Serviço de Mídia]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Criando sobreposições]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Unindo segmentos de vídeo]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[SDK do .NET dos Serviços de Mídia do Azure 3.0.0.1 e 3.0.0.2]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Serviço de Controle de Acesso (ACS) do Active Directory do Azure]: http://msdn.microsoft.com/library/hh147631.aspx
[Conectando-se a Serviços de Mídia com o SDK de Serviços de Mídia para .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Extensões do SDK do .NET dos Serviços de Mídia do Azure]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
[Github]: https://github.com/Azure/azure-sdk-for-media-services
[Gerenciando ativos de Serviços de Mídia através de várias contas de armazenamento]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Manipulando notificações de trabalho dos Serviços de Mídia]: http://msdn.microsoft.com/library/azure/dn261241.aspx

<!--HONumber=45--> 
