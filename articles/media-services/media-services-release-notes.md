<properties 
	pageTitle="Notas de versão dos Serviços de Mídia" 
	description="Notas de versão dos Serviços de Mídia" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="media" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/27/2015"   
	ms.author="juliako"/>


# Notas de versão dos Serviços de Mídia do Azure

Estas notas de versão resumem as alterações de versões anteriores e os problemas conhecidos.

>[AZURE.NOTE]Queremos ouvir nossos clientes e focar na correção de problemas que afetam você. Para relatar um problema ou fazer uma pergunta, publique no [Fórum MSDN de Serviços de Mídia do Azure].

- [Problemas conhecidos no momento](#issues)
- [Histórico de versão da API REST](#rest_version_history)
- [Versão de setembro de 2015](#september_changes_15)
- [Versão de agosto de 2015](#august_changes_15)
- [Versão de julho de 2015](#july_changes_15)
- [Versão de junho de 2015](#june_changes_15)
- [Versão de maio de 2015](#may_changes_15)
- [Versão de abril de 2015](#april_changes_15)
- [Versão de março de 2015](#march_changes_15)
- [Versão de fevereiro de 2015](#february_changes_15)
- [Versão de janeiro de 2015](#january_changes_15)
- [Versão de dezembro de 2014](#december_changes_14)
- [Versão de novembro de 2014](#november_changes_14)
- [Versão de outubro de 2014](#october_changes_14)
- [Versão de setembro de 2014](#september_changes_14)
- [Versão de agosto de 2014](#august_changes_14)
- [Versão de julho de 2014](#july_changes_14)
- [Versão de maio de 2014](#may_changes_14)
- [Versão de abril de 2014](#april_changes_14) 
- [Versões de janeiro\\fevereiro de 2014](#jan_feb_changes_14) 
- [Versão de dezembro de 2013](#december_changes_13)
- [Versão de novembro de 2013](#november_changes_13)
- [Versão de agosto de 2013](#august_changes_13)
- [Versão de junho de 2013](#june_changes_13)
- [Versão de dezembro de 2012](#december_changes_12)
- [Versão de novembro de 2012](#november_changes_12)
- [Versão de teste de junho de 2012](#june_changes_12)


##<a id="issues"></a>Problemas conhecidos no momento

### <a id="general_issues"></a>Problemas gerais dos Serviços de Mídia

Problema|Descrição
---|---
Vários cabeçalhos HTTP comuns não são fornecidos na API REST.|Se você desenvolver aplicativos de Serviços de Mídia usando a API REST, verá que não há suporte a alguns campos de cabeçalho HTTP comuns (incluindo CLIENT-REQUEST-ID, REQUEST-ID e RETURN-CLIENT-REQUEST-ID). Os cabeçalhos serão adicionados em uma atualização futura.
Codificar um ativo com um nome de arquivo que contenha caracteres de escape (por exemplo, %20) falha com “MediaProcessor : Arquivo não encontrado.”|Nomes de arquivos que serão adicionados a um ativo e, então, codificados devem conter apenas caracteres e espaços alfanuméricos. O problema será corrigido em uma atualização futura.
O método ListBlobs que faz parte do SDK do Armazenamento do Azure versão 3.x falha.|Os Serviços de Mídia geram URLs SAS com base na versão de [12/02/2012](http://msdn.microsoft.com/library/azure/dn592123.aspx). Se desejar que o SDK de Armazenamento do Azure liste os blobs em um contêiner de blob, use o método [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) que faz parte do SDK de Armazenamento do Azure versão 2.x. O método ListBlobs que faz parte do SDK do Armazenamento do Azure versão 3.x falhará.
O mecanismo de aceleração dos Serviços de Mídia restringe o uso dos recursos para aplicativos que fazem solicitações excessivas ao serviço. O serviço pode retornar o código de status HTTP Serviço Não Disponível (503).|Para obter mais informações, consulte a descrição do código de status HTTP 503 no tópico [Códigos de erro dos Serviços de Mídia do Azure](http://msdn.microsoft.com/library/azure/dn168949.aspx).


### <a id="dotnet_issues"></a>SDK dos Serviços de Mídia para Problemas do .NET

Problema|Descrição
---|---
Os objetos de Serviços de Mídia no SDK não podem ser serializados, e, como resultado, não funcionam com Caching do Azure.|Se você tentar serializar o objeto AssetCollection do SDK para adicioná-lo ao Caching do Azure, uma exceção será lançada.

##<a id="rest_version_history"></a>Histórico de versão da API REST

Para obter informações sobre o histórico de versões da API REST dos Serviços de Mídia, consulte [Referência da API REST dos Serviços de Mídia do Azure].

##<a id="september_changes_15"></a>Versão de setembro de 2015 

- AMS agora oferece a capacidade de proteger tanto VOD (vídeo sob demanda) quanto Fluxos Ao Vivo com tecnologia de DRM Modular Widevine. Você pode usar os seguintes parceiros de serviços de entrega para ajudá-lo a fornecer licenças Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) e [castLabs](http://castlabs.com/company/partners/azure/). Para obter mais informações, confira [este blog](http://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).

	Você pode usar o [SDL .NET AMS](https://www.nuget.org/packages/windowsazure.mediaservices/) (a partir da versão 3.5.1) ou a API REST para configurar seu AssetDeliveryConfiguration para usar o Widevine.

- O AMS adicionou suporte para vídeos ProRes da Apple. Agora você pode carregar os arquivos de vídeos de origem do QuickTime que usam Apple ProRes ou outros codecs. Para obter mais informações, confira [este blog](http://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).

- Agora você pode usar um Codificador de Mídia Padrão para fazer subrecortes e extração de arquivos dinâmicos. Para obter mais informações, confira [este blog](http://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

- Foram feitas as seguintes atualizações de filtragem:

	- Agora você pode usar o formato HLS (Apple HTTP Live Streaming) com filtro somente áudio. Essa atualização permite que você remova a faixa somente áudio especificando (somente áudio = false) na URL.
	- Ao definir filtros para os ativos, agora você pode combinar vários filtros (até três) em uma única URL.

- O AMS agora dá suporte ao I-Frames no HLS v4. O suporte do I-Frame otimiza as operações de avanço e retrocesso. Por padrão, todas as saídas do HLS v4 incluem o playlist do I-Frame (EXT-X-I-FRAME-STREAM-INF).
 
##<a id="august_changes_15"></a>Versão de agosto de 2015

- Agora, o SDK do Serviços de Mídia do Azure para Java versão V0.8.0 e novos exemplos estão disponíveis. Para obter mais informações, consulte:

	- [Postagem no blog](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
	- [Repositório de amostras de Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- Atualização do Azure Media Player com suporte de fluxo de vários áudios. Para obter mais informações, consulte:
	- [Postagem no blog](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

##<a id="july_changes_15"></a>Versão de julho de 2015

- Anunciando a disponibilidade geral do Media Encoder Standard. Para obter mais informações, confira [esta postagem no blog](http://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).

	O Codificador de Mídia Standard usa as predefinições descritas [nesta](http://go.microsoft.com/fwlink/?LinkId=618336) seção. Lembre-se de que ao usar uma predefinição para 4 mil codificações, você deve adquirir o tipo de unidade reservada **Premium**. Para obter mais informações, confira [Como escalonar a Codificação](media-services-portal-encoding-units).
- Legendas em tempo real ativas com os Serviços de Mídia e o Player do Azure. Para obter mais informações, confira [esta postagem no blog](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)

###Atualizações do SDK do .NET dos Serviços de Mídia

O SDK do .NET dos Serviços de Mídia do Azure está agora na versão 3.4.0.0. A seguinte funcionalidade foi adicionada nessa versão:

- Suporte implementado para arquivo morto dinâmico. Observe que você não pode baixar um ativo que contenha um arquivo morto dinâmico.
- Suporte implementado para filtros dinâmicos.
- Funcionalidade implementada que permite aos usuários manter o contêiner de armazenamento ao excluir ativos.
- Correções de bugs relacionados a políticas de repetição nos canais.
- **Fluxo de Trabalho Premium do Codificador de Mídia** habilitado.

##<a id="june_changes_15"></a>Versão de junho de 2015

###Atualizações do SDK do .NET dos Serviços de Mídia

O SDK do .NET dos Serviços de Mídia do Azure está agora na versão 3.3.0.0. A seguinte funcionalidade foi adicionada nessa versão:

- suporte para especificação de Descoberta do OpenId Connect,
- suporte para tratamento de substituição de chaves no lado do provedor de identidade. 

Se você estiver usando um provedor de identidade que expõe o documento de descoberta OpenID Connect (assim como os seguintes provedores: Active Directory do Azure, Google, Salesforce), você pode instruir os Serviços de Mídia do Azure para obter chaves de assinatura para a validação de token JWT de especificação de descoberta OpenId Connect.

Para saber mais, confira [Usar Chaves Web Json da especificação de descoberta OpenID Connect para trabalhar com autenticação de token JWT nos Serviços de Mídia do Azure](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).


##<a id="may_changes_15"></a>Versão de maio de 2015

Anunciando os seguintes novos recursos:

- [Uma visualização da Codificação Ativa com os Serviços de Mídia](media-services-manage-live-encoder-enabled-channels.md)
- [Manifesto dinâmico](media-services-dynamic-manifest-overview.md)
- [Uma visualização do processador de mídia Azure Media Hyperlapse](http://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

##<a id="april_changes_15"></a>Versão de abril de 2015

###Atualizações gerais dos Serviços de Mídia

- [Anunciando o Azure Media Player](http://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
- Começando com a REST 2.10 dos Serviços de Mídia, canais configurados para incluir um protocolo RTMP são criados com URLs de inclusão primária e secundária. Para saber mais, confira [Configurações de inclusão de canal](media-services-manage-channels-overview.md#channel_input)
- Atualizações do Indexador de Mídia do Azure
	- Suporte ao idioma espanhol
	- Novo formato xml de configuração
	
	Para saber mais, confira [este blog](http://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).
###Atualizações do SDK do .NET dos Serviços de Mídia

O SDK do .NET dos Serviços de Mídia do Azure está agora na versão 3.2.0.0.

Estas são algumas das atualizações voltadas para o cliente:
 
- **Alteração interruptiva**: alterados **TokenRestrictionTemplate.Issuer** e **TokenRestrictionTemplate.Audience** para ser de um tipo de cadeia de caracteres. 
- Atualizações relativas à criação personalizada de políticas de repetição. 
- Correções de bugs relativas a upload/download de arquivos. 
- A classe **MediaServicesCredentials** agora aceita os pontos de extremidade de controle de acesso primários e secundários para autenticar.



##<a id="march_changes_15"></a>Versão de março de 2015

### Atualizações gerais dos Serviços de Mídia

- Agora, os Serviços de Mídia fornecem integração com a CDN do Azure. Para dar suporte à integração, a propriedade **CdnEnabled** foi adicionada a **StreamingEndpoint**. **CdnEnabled** pode ser usado com APIs REST a partir da versão 2.9 (para saber mais, confira [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)). **CdnEnabled** pode ser usado com o SDK do .NET a partir da versão 3.1.0.2 (para saber mais, confira [StreamingEndpoint.aspx])https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint(v=azure.10).aspx)).
- Anúncio do **Fluxo de trabalho do Media Encoder Premium**. Para saber mais, confira [Apresentando a codificação Premium nos Serviços de Mídia do Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services).
 


##<a id="february_changes_15"></a>Versão de fevereiro de 2015

### Atualizações gerais dos Serviços de Mídia

A API REST dos Serviços de Mídia agora está na versão 2.9. A partir desta versão, você pode habilitar a integração da CDN do Azure com pontos de extremidade de streaming. Para saber mais, confira [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

##<a id="january_changes_15"></a>Versão de janeiro de 2015

### Atualizações gerais dos Serviços de Mídia

Anúncio da disponibilidade geral (GA) de proteção de conteúdo com criptografia dinâmica. Para saber mais, confira [Os Serviços de mídia do Azure aprimoram a segurança de streaming com a disponibilidade geral da tecnologia DRM](http://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

###Atualizações do SDK do .NET dos Serviços de Mídia

O SDK do .NET dos Serviços de Mídia do Azure está agora na versão 3.1.0.1.

Esta versão marcou o construtor Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestriction padrão como obsoleto. O novo construtor usa TokenType como um argumento.

	TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


##<a id="december_changes_14"></a>Versão de dezembro de 2014

###Atualizações gerais dos Serviços de Mídia

- Algumas atualizações e novos recursos foram adicionados ao processador de mídia do indexador do Azure. Para saber mais, confira [Notas de versão de indexador de mídia do Azure 1.1.6.7](http://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
- Adicionada uma nova API REST que permite que você atualize a codificação de unidades reservadas: [EncodingReservedUnitType com REST](http://msdn.microsoft.com/library/azure/dn859236.aspx).
- Acrescentado suporte a CORS para o serviço de distribuição de chaves.
- Foram feitos aprimoramentos de desempenho nas opções das políticas de autorização de consulta.
- No data center da China, a [URL de entrega de chaves](http://msdn.microsoft.com/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url) agora é por cliente (assim como em outros data centers).
- Duração de destino automático HLS adicionada. Ao fazer streaming ao vivo, o HLS é sempre empacotado dinamicamente. Por padrão, os Serviços de Mídia calculam automaticamente a taxa de empacotamento de segmento HLS (FragmentsPerSegment) com base no intervalo entre quadros-chave (KeyFrameInterval), também conhecido como grupo de imagens – GOP, que é recebido do codificador ao vivo. Para obter mais informações, consulte [Trabalhando com Live Streaming dos Serviços de Mídia do Azure].
 
###Atualizações do SDK do .NET dos Serviços de Mídia

- [O SDK do .NET dos Serviços de Mídia do Azure](http://www.nuget.org/packages/windowsazure.mediaservices/) está agora na versão 3.1.0.0.
- Atualizada a dependência do SDK do .Net para .NET 4.5 Framework.
- Adicionada uma nova API que permite que você atualize unidades reservadas de codificação. Para saber mais, confira [Atualizando tipo de unidade reservada e aumentando unidades reservadas de codificação usando o .NET](http://msdn.microsoft.com/library/azure/jj129582.aspx).
- Acrescentado suporte a JWT (JSON Web Token) para autenticação via token. Para saber mais, confira [Autenticação de token JWT nos Serviços de Mídia do Azure e criptografia dinâmica](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
- Adicionados deslocamentos relativos para BeginDate e ExpirationDate no modelo de licença PlayReady.


##<a id="november_changes_14"></a>Versão de novembro de 2014

- Os Serviços de Mídia agora permitem que você inclua um conteúdo de Smooth Streaming (FMP4) ao vivo em uma conexão SSL. Para inserir por SSL, certifique-se de atualizar a URL de inserção para HTTPS. Para saber mais sobre a transmissão ao vivo, confira [Trabalhando com a Transmissão ao Vivo dos Serviços de Mídia do Azure].
- Observe que no momento, você não pode inserir um fluxo ao vivo RTMP por uma conexão SSL.
- Você também pode transmitir seu conteúdo por uma conexão SSL. Para fazer isso, certifique-se de que suas URLs de streaming começam com HTTPS.
- Observe que você só pode transmitir por SSL se o ponto de extremidade de streaming por meio do qual você pode distribuir o conteúdo tiver sido criado depois de 10 de setembro de 2014. Se suas URLs de streaming baseiam-se nos pontos de extremidade de streaming após 10 de setembro, a URL contém "streaming.mediaservices.windows.net" (o novo formato). URLs de streaming que contêm "origin.mediaservices.windows.net" (o formato antigo) não dão suporte a SSL. Se a sua URL está no formato antigo e você deseja ser capaz de transmitir por SSL, [crie um novo ponto de extremidade de streaming](media-services-manage-origins.md). Use URLs criadas com base no novo ponto de extremidade de streaming para transmitir seu conteúdo por SSL.
   
##<a id="october_changes_14"></a>Versão de outubro de 2014

### <a id="new_encoder_release"></a>Versão do codificador de Serviços de Mídia

Anúncio da nova versão do Codificador de Mídia do Azure dos Serviços de Mídia. Com o mais recente Codificador de Mídia do Azure, você somente é cobrado por GBs de saída, mas, por outro lado, o novo codificador tem recursos compatíveis com o codificador anterior. Para mais informações [Detalhes dos preços dos Serviços de Mídia]).

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


##<a id="september_changes_14"></a>Versão de setembro de 2014

Os metadados REST dos Serviços de Mídia agora estão na versão 2.7. Para obter mais informações sobre as últimas atualizações de REST, consulte a [Referência da API REST dos Serviços de Mídia do Azure].

SDK dos Serviços de Mídia para .NET agora está na versão 3.0.0.7
 
### <a id="sept_14_breaking_changes"></a>Alterações interruptivas

* A **Origem** foi renomeada para [StreamingEndpoint].
* Uma mudança no comportamento padrão ao usar o **Portal de Gerenciamento do Azure** para codificar e depois publicar arquivos MP4. 

	Anteriormente, ao usar o Portal de Gerenciamento para publicar um ativo de vídeo MP4 de arquivo único, uma URL SAS seria criada (URLs SAS permitem baixar o vídeo de um armazenamento blob). No momento, quando se usa o Portal de Gerenciamento para codificar e depois publicar um ativo de vídeo MP4 de arquivo único, a URL gerada aponta para um ponto de extremidade de streaming dos Serviços de Mídia do Azure. Essa mudança não afeta vídeos MP4 que são carregados diretamente para os Serviços de Mídia e publicados sem serem codificados pelos Serviços de Mídia do Azure.
	
	No momento, há as duas opções a seguir para solucionar o problema.
	
	* Habilitar unidades de streaming e usar empacotamento dinâmico para transmitir o ativo .mp4 como uma apresentação de smooth streaming.
	
	* Criar uma URL SAS para baixar (ou reproduzir progressivamente) o .mp4. Para obter mais informações sobre como criar um localizador SAS, consulte [Entregando Conteúdo].


### <a id="sept_14_GA_changes"></a>Novos recursos\\cenários que fazem parte da versão do GA

* **Processador de Mídia do Indexador**. Para obter mais informações, consulte [Indexando arquivos de mídia com o Indexador de Mídia do Azure].

* A entidade [StreamingEndpoint] agora permite adicionar nomes (host) de domínio personalizados.

	Para um nome de domínio personalizado ser usado como nome do ponto de extremidade de streaming dos Serviços de Mídia, é preciso adicionar nomes de host personalizados ao ponto de extremidade de streaming. Use as APIs REST dos Serviços de Mídia ou o SDK do .NET para adicionar nomes de host personalizados.
	
	As seguintes considerações se aplicam:
	
	* É preciso ter propriedade do nome de domínio personalizado.
	
	* A propriedade do nome de domínio deve ser validada pelos Serviços de Mídia do Azure. Para validar o domínio, criar um CName que mapeia <MediaServicesAccountId>. <parent domain> verifydns. <mediaservices-dns-zone>.
	
	* Você deve criar outro CName que mapeia o nome de host personalizado (por exemplo, sports.contoso.com) em nome de host do seu StreamingEndpont dos serviços de mídia (por exemplo, amstest.streaming.mediaservices.windows.net).


	Para obter mais informações, consulte a propriedade **CustomHostNames** no tópico [StreamingEndpoint].

### <a id="sept_14_preview_changes"></a>Novos recursos\\cenários que fazem parte da versão de visualização pública

* Visualização de Live Streaming. Para obter mais informações, consulte [Trabalhando com Live Streaming dos Serviços de Mídia do Azure].

* Serviço de entrega de chave. Para obter mais informações, consulte [Usando o serviço de entrega de chave e criptografia dinâmica do AES-128].

* Criptografia dinâmica do EAS. Para obter mais informações, consulte [Usando o serviço de entrega de chave e criptografia dinâmica do AES-128].

* Serviço de entrega de licença do PlayReady. Para obter mais informações, consulte [Usando o serviço de entrega de licença e criptografia dinâmica do PlayReady].

* Criptografia dinâmica do PlayReady. Para obter mais informações, consulte [Usando o serviço de entrega de licença e criptografia dinâmica do PlayReady].

* Modelo de licença do PlayReady dos Serviços de Mídia. Para obter mais informações, consulte [Visão geral do modelo de licença do PlayReady dos Serviços de Mídia].

* Streaming de ativos criptografados de armazenamento. Para obter mais informações, consulte [Streaming de conteúdo criptografado de armazenamento].

##<a id="august_changes_14"></a>Versão de agosto de 2014

Ao codificar um ativo, um ativo de saída é produzido quando o trabalho de codificação é concluído. Até esta versão, o Codificador de Serviços de Mídia do Azure produzia metadados sobre os ativos de saída. A partir desta versão, o codificador também produz metadados sobre ativos de entrada. Para obter mais informações, consulte os tópicos [Metadados de entrada] e [Metadados de saída].


##<a id="july_changes_14"></a>Versão de julho de 2014

As seguintes correções de erro foram feitas ao Empacotador e Criptografador dos Serviços de Mídia do Azure:

* Apenas reproduções de áudio ao realizar transmux de um ativo de arquivo em tempo real para Live Streaming HTTP - isso foi corrigido e agora tanto áudio quanto vídeo são reproduzidos.

* Ao empacotar um ativo para Live Streaming HTTP e criptografia de envelope de 128 bits, os fluxos de pacote não são reproduzidos em dispositivos Android - esse erro foi corrigido e o fluxo empacotado é reproduzido em dispositivos Android com suporte a Live Streaming HTTP.

##<a id="may_changes_14"></a>Versão de maio de 2014

### <a id="may_14_changes"></a>Atualizações gerais dos Serviços de Mídia

Agora é possível usar o [Empacotamento Dinâmico] para transmitir Live Streaming HTTP (HLS) v3. Para transmitir HLS v3, adicione o seguinte formato para o caminho do localizador de origem: * .ism/manifest(format=m3u8-aapl-v3). Para obter mais informações, consulte o [Blog de Nick Drouin].

O Empacotamento Dinâmico agora também oferece suporte à entrega de HLS (v3 e v4) criptografado com PlayReady com base em Smooth Streaming estaticamente criptografado com PlayReady. Para obter informações sobre como criptografar Smooth Streaming com PlayReady, consulte [Protegendo Smooth Stream com PlayReady].

### <a name="may_14_donnet_changes"></a>Atualizações do SDK do .NET dos Serviços de Mídia

As seguintes melhorias são incluídas na versão 3.0.0.5 do SDK do .NET dos Serviços de Mídia:

* Melhor velocidade e resiliência para carregar/baixar ativos de mídia.

* Melhorias na lógica de repetição e manipulação de exceção temporária:

	* Detecção de erro temporário e lógica de repetição foram aprimorados para as exceções causadas por consultar, salvar alterações, carregar ou baixar arquivos. 
	
	* Ao obter Exceções da Web (por exemplo, durante uma solicitação de token ACS), você observará que erros fatais estão falhando mais rapidamente agora.

Para obter mais informações, consulte [Lógica de repetição no SDK de Serviços de Mídia para .NET].

##<a id="april_changes_14"></a>Versão do Codificador de abril de 2014

### <a name="april_14_enocer_changes"></a>Atualizações do Codificador de Serviços de Mídia

* Adicionado suporte para capturar arquivos AVI criados usando o editor não linear Grass Valley EDIUS, em que o vídeo é levemente compactado usando o codec Grass Valley HQ/HQX. Para obter mais informações, consulte [Grass Valley anuncia streaming EDIUS 7 pela nuvem].

* Adicionado suporte para especificar a convenção de nomenclatura para arquivos produzidos pelo Codificador de Mídia. Para obter mais informações, consulte [Controlando nomes de arquivo de saída do Codificador do Serviço de Mídia].

* Adicionado suporte para sobreposições de vídeo e/ou áudio. Para obter mais informações, consulte [Criando sobreposições].

* Adicionado suporte para unir vários segmentos de vídeo. Para obter mais informações, consulte [Unindo segmentos de vídeo].

* Corrigido um erro relacionado à transcodificação de MP4s em que o áudio havia sido codificado com a camada de Áudio 3 MPEG-1 (ou seja, MP3).


##<a id="jan_feb_changes_14"></a>Versões de janeiro\\fevereiro de 2014

### <a name="jan_fab_14_donnet_changes"></a>SDK do .NET dos Serviços de Mídia do Azure 3.0.0.1, 3.0.0.2 e 3.0.0.3

As alterações em 3.0.0.1 e 3.0.0.2 incluem:

* Corrigidos problemas relacionados ao uso de consultas LINQ com declarações OrderBy.

* Divida as soluções de teste em [Github] em testes baseados em unidade e testes baseados em cenário.

Para saber mais sobre as alterações, confira: [Versões do SDK do .NET dos Serviços de Mídia do Azure 3.0.0.1 e 3.0.0.2].

As seguintes alterações foram feitas na versão 3.0.0.3:

* Atualizadas as dependências de armazenamento do Azure para usar a versão 3.0.3.0. 

* Corrigido o problema de compatibilidade reversa para versões 3.0*.*.


##<a id="december_changes_13"></a>Versão de dezembro de 2013

### <a name="dec_13_donnet_changes"></a>SDK do .NET dos Serviços de Mídia do Azure 3.0.0.0

>[AZURE.NOTE]As versões 3.0.x.x não são compatíveis com versões 2.4.x.x.

A versão mais recente do SKD dos Serviços de Mídia agora é a 3.0.0.0. É possível baixar o pacote mais recente do Nuget ou obter os bits do [Github].

A partir do SDK de Serviços de Mídia versão 3.0.0.0, é possível reutilizar os tokens do [Serviço de Controle de Acesso (ACS) do Active Directory do Azure]. Para obter mais informações, consulte a seção “Reutilizando tokens de serviço de controle de acesso” no tópico [Conectando-se a Serviços de Mídia com o SDK de Serviços de Mídia para .NET].

### <a name="dec_13_donnet_ext_changes"></a>Extensões do SDK do .NET dos Serviços de Mídia do Azure 2.0.0.0

As Extensões do SDK do .NET dos Serviços de Mídia do Azure são um conjunto de métodos de extensão e funções auxiliares que simplificarão seu código e tornarão mais fácil desenvolver com os Serviços de Mídia do Azure. Você pode obter os bits mais recentes das [Extensões do SDK do .NET dos Serviços de Mídia do Azure].

##<a id="november_changes_13"></a>Versão de novembro de 2013

### <a name="nov_13_donnet_changes"></a>Alterações do SDK do .NET dos Serviços de Mídia do Azure

A partir desta versão, o SDK para .NET dos Serviços de Mídia manipula erros de falhas temporários que podem ocorrer ao fazer chamadas à camada da API REST dos Serviços de Mídia.

##<a id="august_changes_13"></a>Versão de agosto de 2013

### <a name="aug_13_powershell_changes"></a>Cmdlets do PowerShell dos Serviços de Mídia incluídos nas Ferramentas de SDK do Azure

Os seguintes cmdlets do PowerShell dos Serviços de Mídia agora estão incluídos em [azure-sdk-tools].

* Get-AzureMediaServices 

	Por exemplo: `Get-AzureMediaServicesAccount`.

* New-AzureMediaServicesAccount

	Por exemplo: `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.

* New-AzureMediaServicesKey

	Por exemplo: `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.

* Remove-AzureMediaServicesAccount

	Por exemplo: `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

##<a id="june_changes_13"></a>Versão de junho de 2013

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


##<a id="december_changes_12"></a>Versão de dezembro de 2012

### <a name="dec_12_dotnet_changes"></a>Alterações do SDK do .NET dos Serviços de Mídia do Azure

* Intellisense: adicionada a documentação do Intellisense que faltava para vários tipos.

* Microsoft.Practices.TransientFaultHandling.Core: corrigido um problema em que o SDK ainda tinha uma dependência a uma versão mais antiga desse assembly. O SDK agora faz referência à versão 5.1.1209.1 desse conjunto.

Correções para problemas encontrados no SDK de novembro de 2012:

* IAsset.Locators.Count : essa contagem agora é relatada corretamente em novas interfaces IAsset depois de todos os localizadores terem sido excluídos.

* IAssetFile.ContentFileSize : esse valor agora é definido adequadamente após um upload por IAssetFile.Upload(filePath).

* IAssetfile.ContentFileSize: essa propriedade agora pode ser definida durante a criação de um arquivo de ativo. Antes era somente leitura.

* IAssetFile.Upload(filePath) : foi corrigido um problema em que esse método de carregamento assíncrono lançava o erro abaixo ao carregar vários arquivos para o ativo. O erro era “O servidor falhou em autenticar a solicitação. Certifique-se de que o valor do cabeçalho Autorização seja formado corretamente, incluindo a assinatura.”

* IAssetFile.UploadAsync : corrigido um problema em que não mais de cinco arquivos podiam ser carregados ao mesmo tempo.

* IAssetFile.UploadProgressChanged: esse evento agora é fornecido pelo SDK.

* IAssetFile.DownloadAsync (string, BlobTransferClient, ILocator, CancellationToken): essa sobrecarga de método agora é fornecida.

* IAssetFile.DownloadAsync : corrigido um problema em que não mais de cinco arquivos podiam ser baixados ao mesmo tempo.

* IAssetFile.Delete() : corrigido um problema em que chamar o comando “excluir” poderia lançar uma exceção, caso nenhum arquivo fosse carregado para o IAssetFile.

* Trabalhos: corrigido um problema em que encadear uma “Tarefa de MP4 para Smooth Streams” com uma “Tarefa de proteção do PlayReady” usando um modelo de trabalho não criaria nenhuma tarefa.

* EncryptionUtils.GetCertificateFromStore() : esse método não lança mais uma exceção de referência nula devido a falhas em encontrar o certificado com base em problemas de configuração de certificado.

##<a id="november_changes_12"></a>Versão de novembro de 2012

As alterações mencionadas nesta seção eram atualizações incluídas no SDK de novembro de 2012 (versão 2.0.0.0). Essas alterações podem exigir que qualquer código escrito para a versão do SDK de visualização de junho de 2012 seja modificado ou reescrito.

* Ativos
	
	IAsset.Create(assetName) é a ÚNICA função de criação de ativo. IAsset.Create não carrega mais arquivos como parte do método de chamada. Use IAssetFile para carregar.
	
	O método IAsset.Publish e o valor de enumeração AssetState.Publish foram removidos do SDK de Serviços. Qualquer código que conte com esse valor deve ser reescrito.

* FileInfo

	Essa classe foi removida e substituída pelo IAssetFile.

	IAssetFiles

	IAssetFile substitui FileInfo e tem um comportamento diferente. Para usá-la, instancie o objeto IAssetFiles, seguido por um carregamento de arquivo usando o SDK de Serviços de Mídia ou o SDK de Armazenamento do Azure. As seguintes sobrecargas IAssetFile.Upload podem ser usadas:

	* IAssetFile.Upload(filePath): um método síncrono que bloqueia o encadeamento e é recomendado somente ao carregar um único arquivo.
	
	* IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): um método assíncrono. Esse é o mecanismo de carregamento preferido.

	Bug conhecido: usar o cancellationToken, de fato, cancelará o carregamento; porém, o estado de cancelamento das tarefas pode ser qualquer um entre vários estados. É preciso capturar e tratar as exceções adequadamente.

* Localizadores
	
	A versão específica da origem foi removida. O context.Locators.CreateSasLocator(ativo, accessPolicy) específico do SAS será marcado como preterido, ou removido pelo GA. Consulte a seção Localizadores em Nova Funcionalidade para comportamento atualizado.


##<a id="june_changes_12"></a>Versão de visualização de junho de 2012

A seguinte funcionalidade era nova na versão de novembro do SDK.

* Excluindo entidades

	Os objetos IAsset, IAssetFile, ILocator, IAccessPolicy, IContentKey agora são excluídos no nível do objeto, ou seja, IObject.Delete(), em vez de exigir uma exclusão na Coleção, que é cloudMediaContext.ObjCollection.Delete(objInstance).

* Localizadores

	O Localizadores agora devem ser criados usando o método CreateLocator e usando os valores enum LocatorType.SAS ou LocatorType.OnDemandOrigin como argumento para o tipo específico de localizador que você deseja criar.

	Novas propriedades foram adicionadas a Localizadores para tornar mais fácil obter URIs usáveis para seu conteúdo. Essa remodelagem de Localizadores foi feita para fornecer mais flexibilidade para capacidade de extensão de terceiros e aumentar a facilidade de uso para aplicativos cliente de mídia.

* Suporte ao método assíncrono

	O suporte assíncrono foi adicionado a todos os métodos.


##Roteiros de aprendizagem dos Serviços de Mídia

Você pode exibir os roteiros de aprendizagem do AMS aqui:

- [Fluxo de trabalho do streaming ao vivo do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Fluxo de trabalho do streaming sob demanda do AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Fórum MSDN de Serviços de Mídia do Azure]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Referência da API REST dos Serviços de Mídia do Azure]: http://msdn.microsoft.com/library/azure/hh973617.aspx
[Detalhes dos preços dos Serviços de Mídia]: http://azure.microsoft.com/pricing/details/media-services/
[Metadados de entrada]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Metadados de saída]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Entregando Conteúdo]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Indexando arquivos de mídia com o Indexador de Mídia do Azure]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[StreamingEndpoint.aspx]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Trabalhando com Live Streaming dos Serviços de Mídia do Azure]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Trabalhando com a Transmissão ao Vivo dos Serviços de Mídia do Azure]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Usando o serviço de entrega de chave e criptografia dinâmica do AES-128]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Usando o serviço de entrega de licença e criptografia dinâmica do PlayReady]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Visão geral do modelo de licença do PlayReady dos Serviços de Mídia]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Streaming de conteúdo criptografado de armazenamento]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure Management Portal]: https://manage.windowsazure.com
[Empacotamento Dinâmico]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Blog de Nick Drouin]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protegendo Smooth Stream com PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Lógica de repetição no SDK de Serviços de Mídia para .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley anuncia streaming EDIUS 7 pela nuvem]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Controlando nomes de arquivo de saída do Codificador do Serviço de Mídia]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Criando sobreposições]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Unindo segmentos de vídeo]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Versões do SDK do .NET dos Serviços de Mídia do Azure 3.0.0.1 e 3.0.0.2]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Serviço de Controle de Acesso (ACS) do Active Directory do Azure]: http://msdn.microsoft.com/library/hh147631.aspx
[Conectando-se a Serviços de Mídia com o SDK de Serviços de Mídia para .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Extensões do SDK do .NET dos Serviços de Mídia do Azure]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Gerenciando ativos de Serviços de Mídia através de várias contas de armazenamento]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Manipulando notificações de trabalho dos Serviços de Mídia]: http://msdn.microsoft.com/library/azure/dn261241.aspx
 

<!---HONumber=Oct15_HO1-->