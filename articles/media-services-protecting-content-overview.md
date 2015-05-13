<properties 
	pageTitle="Protegendo seu conteúdo" 
	description="O tópico apresenta e visão geral de como proteger seu conteúdo com os Serviços de Mídia." 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="juliako"/>


#Protegendo seu conteúdo

##Visão geral

Os Serviços de Mídia do Azure permitem proteger a mídia desde o momento em que ela deixa computador e durante o armazenamento, processamento e entrega. Ao trabalhar com os Serviços de Mídia, um dos cenários comuns é:

###Proteger o conteúdo no armazenamento, fornecendo mídia de streaming criptografada dinamicamente  

Para poder usar criptografia dinâmica, primeiro é necessário obter pelo menos uma unidade reservada para streaming no ponto de extremidade de streaming por meio do qual você deseja transmitir conteúdo criptografado.

1. Carregue um arquivo mezanino de alta qualidade em um ativo. Aplique a opção de criptografia de armazenamento ao ativo.
1. Codifique para conjunto MP4 de taxa de bits adaptável. Aplique a opção de criptografia de armazenamento ao ativo de saída.
1. Crie uma chave de conteúdo de criptografia para o ativo que você quer que seja criptografado dinamicamente durante a reprodução.
2. Configure a política de autorização de chave de conteúdo.
1. Configure a política de entrega de ativos \(usada pelo empacotamento dinâmico e criptografia dinâmica\).
1. Publique o ativo, criando um localizador OnDemand.
1. Transmita o conteúdo publicado.  

Este tópico fornece uma visão geral dos principais [conceitos](media-services-protecting-content-overview.md#concepts) e links para tópicos que mostram como executar [tarefas](media-services-protecting-content-overview.md#tasks) relacionadas ao fornecimento de conteúdo.

##<a id="concepts"></a>Conceitos

###Opções de criptografia de ativos

Dependendo do tipo de conteúdo que você deseja carregar, armazenar e fornecer, o Serviços de Mídia fornece várias opções de criptografia dentre as quais você pode escolher.

**None** - nenhuma criptografia é usada. Esse é o valor padrão. Observe que ao usar essa opção, seu conteúdo não é protegido quando está em trânsito ou em repouso no armazenamento.

Se você pretende enviar um MP4 usando o download progressivo, use essa opção para carregar seu conteúdo.

**StorageEncrypted** - use essa opção para criptografar seu conteúdo limpo localmente usando a criptografia AES de 256 bits e, em seguida, carregue-o para o armazenamento do Azure onde ele é armazenado, criptografado em rest. Ativos protegidos pela criptografia de armazenamento são descriptografados automaticamente e posicionados em um sistema de arquivos criptografado antes da codificação, então opcionalmente criptografados novamente antes do carregamento como um novo ativo de saída. O caso de uso primário para criptografia de armazenamento é quando você deseja proteger seus arquivos de mídia de entrada de alta qualidade com criptografia forte em repouso no disco.

Para entregar um ativo de armazenamento criptografado, você deve configurar a política de entrega do ativo para que o Serviços de Mídia saiba como você deseja distribuir seu conteúdo. Antes que seu ativo possa ser transmitido, o servidor de streaming remove a criptografia de armazenamento e transmite o conteúdo usando a política de entrega especificada \(por exemplo, AES, PlayReady ou sem criptografia\).

**CommonEncryptionProtected** - use esta opção se você desejar criptografar conteúdo \(ou carregar conteúdo que já foi criptografado\) com criptografia comum ou DRM PlayReady \(por exemplo, Smooth Streaming protegido com DRM PlayReady\).

**EnvelopeEncryptionProtected** – Use esta opção se você deseja proteger conteúdo \(ou carregar conteúdo já protegido\) HLS \(HTTP Live Streaming\) criptografado com AES. Observe que se você estiver carregando um HLS já criptografado com AES, ele deve ter sido criptografado com o Transform Manager.

###Criptografia dinâmica

Os Serviços de Mídia do Microsoft Azure permitem distribuir o conteúdo criptografado dinamicamente com criptografia AES \(padrão avançado\), usando chaves de criptografia de 128 bits e PlayReady DRM.

Observe que, no momento, você pode criptografar o seguintes formatos de streaming: HLS, MPEG DASH, e Smooth Streaming. Você não pode criptografar o formato de streaming HDSou fazer o download progressivo.

Se você desejar que os Serviços de Mídia criptografem um ativo, você precisa associar uma chave de criptografia \(CommonEncryption ou EnvelopeEncryption\) ao ativo e também configurar políticas de autorização para a chave.

Você também precisa configurar a política de entrega do ativo. Se você deseja transmitir um ativo de armazenamento criptografado, certifique-se de especificar como deseja entregá-lo configurando a política de entrega de ativos.

Quando um fluxo é solicitado por um player, os serviços de mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a criptografia AES ou PlayReady. Para descriptografar o fluxo, o player solicitará a chave do serviço de distribuição de chaves. Para decidir se o usuário está autorizado para obter a chave ou não, o serviço avalia as políticas de autorização que você especificou para a chave.

>[AZURE.NOTE]Para tirar proveito da criptografia dinâmica, você precisa obter primeiro pelo menos uma unidade de streaming OnDemand para o ponto de extremidade de streaming por meio do qual você planeja fornecer seu conteúdo criptografado. Para obter mais informações, consulte [Como dimensionar os Serviços de Mídia](media-services-manage-origins.md#scale_streaming_endpoints).

###Serviços de distribuição de licenças do PlayReady DRM e chaves não criptografadas AES

Os Serviços de Mídia também fornecem um serviço de distribuição de chaves e licenças do PlayReady aos clientes autorizados. Você pode usar o Portal de Gerenciamento do Azure, a API REST ou o SDK dos Serviços de Mídia para .NET para configurar políticas de autenticação e autorização para suas licenças e chaves.

Observe que se você estiver usando o Portal, você pode configurar uma política AES \(que será aplicada a todo o conteúdo criptografado do AES\) e uma política do PlayReady \(que será aplicada a todo o conteúdo PlayReady criptografado\). Use o SDK dos Serviços de Mídia para .NET se quiser mais controle sobre as configurações.

###Modelo de licença do PlayReady

Os Serviços de Mídia fornecem um serviço de distribuição de licenças do PlayReady. Quando o player de usuário final \(por exemplo, Silverlight\) tenta reproduzir o conteúdo protegido por PlayReady, uma solicitação é enviada para o serviço de entrega de licença para obtenção de uma. Se o serviço de licença aprova a solicitação, ele emite a licença que é enviada ao cliente e pode ser usada para descriptografar e reproduzir o conteúdo especificado.

As licenças contêm os direitos e restrições que você deseja que o tempo de execução do PlayReady DRM imponha quando um usuário está tentando reproduzir conteúdo protegido. Os Serviços de Mídia fornecem APIs que permitem que você configure suas licenças do PlayReady. Para obter mais informações, consulte [Visão geral do modelo de licença do PlayReady dos Serviços de Mídia](https://msdn.microsoft.com/library/azure/dn783459.aspx)

###Restrição de token

A política de autorização de chave de conteúdo pode ter uma ou mais restrições de autorização: aberta, restrição de token ou restrição de IP. A política restrita do token deve ser acompanhada por um token emitido por um Secure Token Service \(STS\). Os serviços de mídia oferecem suporte a tokens no formato Simple Web Tokens \(SWT\) e no formato JSON Web Token \(JWT\). Os serviços de mídia não fornecem Secure Token Services. Você pode criar um STS personalizado ou usar o Microsoft Azure ACS para emitir tokens. O STS deve ser configurado para criar um token assinado com as a chave especificada e declarações de emissão que você especificou na configuração de restrição do token. O serviço de distribuição de chaves dos Serviços de Mídia retornará a chave de criptografia para o cliente se o token for válido e as declarações no token corresponderem àquelas configuradas para a chave \(ou licença\).

Ao configurar a política restrita do token, você deve especificar os parâmetros da chave de verificação primária, emissor e audiência. A chave de verificação primária contém a chave que o token foi assinado, o emissor é o serviço de token seguro que emite o token. A audiência \(às vezes chamada de escopo\) descreve a intenção do token ou o recurso que o token autoriza o acesso. O serviço de distribuição de chaves dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo.

##<a id="tasks"></a>Tarefas relacionadas

###Configurando pontos de extremidade de streaming

Para uma visão geral sobre streaming de pontos de extremidade e informações sobre como gerenciá-los, consulte [Como gerenciar pontos de extremidade de streaming em uma conta de Serviços de Mídia](media-services-manage-origins.md).

###Carregando mídia 

Carregue seus arquivos usando **Portal de Gerenciamento do Azure**, **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-upload-files](../includes/media-services-selector-upload-files.md)]

###Codificação de ativos

Codifique-os com o **Codificador de Mídia do Azure** usando o **Portal de Gerenciamento**, o **.NET** ou **API REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

###Criação de uma chave de conteúdo

Crie uma chave de conteúdo com a qual você deseja criptografar seu ativo usando **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../includes/media-services-selector-create-contentkey.md)]

###Configuração da política de autorização de chave de conteúdo 

Configurar a política de autorização de chave e a proteção de conteúdo usando **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

###Configurando a política de fornecimento de ativos

Configure a política de fornecimento de ativos usando **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]


<!--HONumber=52-->
