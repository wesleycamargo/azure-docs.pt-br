<properties 
	pageTitle="Visão geral sobre a proteção de conteúdo" 
	description="Este artigo fornece uma visão geral da proteção de conteúdo com os Serviços de Mídia." 
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
	ms.date="04/04/2015" 
	ms.author="juliako"/>

#Visão geral sobre a proteção de conteúdo

Os Serviços de Mídia do Microsoft Azure permitem distribuir o conteúdo criptografado dinamicamente com criptografia AES (padrão avançado) (usando chaves de criptografia de 128 bits) e PlayReady DRM. Os serviços de mídia também fornecem um serviço de distribuição de chaves e licenças do PlayReady aos clientes autorizados. 

![content-protection][content-protection]

Para poder usar criptografia dinâmica, primeiro é necessário obter pelo menos uma unidade reservada de streaming no ponto de extremidade de streaming do qual você deseja transmitir conteúdo criptografado.

##Conceitos

###Criptografia dinâmica

Os Serviços de Mídia do Microsoft Azure permitem distribuir o conteúdo criptografado dinamicamente com criptografia AES (padrão avançado) (usando chaves de criptografia de 128 bits) e PlayReady DRM. 

No momento, você pode criptografar o seguintes formatos de streaming: HLS, MPEG DASH e Smooth Streaming. Você não pode criptografar o formato de streaming HDSou fazer o download progressivo.

Se você deseja que os Serviços de Mídia criptografem um ativo, você precisa associar a seu ativo de uma chave de criptografia (CommonEncryption ou EnvelopeEncryption) e também configurar políticas de autorização para a chave.

Você também precisa configurar a política de entrega do ativo. Se você deseja transmitir um ativo de armazenamento criptografado, certifique-se de especificar como deseja entregá-lo configurando a política de entrega do ativo.  

Quando um fluxo é solicitado por um player, os serviços de mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a criptografia AES ou PlayReady. Para descriptografar o fluxo, o player solicitará a chave do serviço de distribuição de chaves. Para decidir se o usuário está autorizado para obter a chave ou não, o serviço avalia as políticas de autorização que você especificou para a chave.

###Serviços de entrega de licenças PlayReady DRM e chaves liberadas AES

Os Serviços de Mídia oferecem um serviço de distribuição de licenças PlayReady e chaves liberadas AES aos clientes autorizados. Você pode usar o Portal de Gerenciamento, a API REST ou o SDK dos Serviços de Mídia para .NET para configurar políticas de autenticação e autorização para suas licenças e chaves.

Observe que se você estiver usando o Portal, poderá configurar uma política AES (que será aplicada a todo o conteúdo criptografado do AES) e uma política do PlayReady (que será aplicada a todo o conteúdo PlayReady criptografado). Use o SDK dos Serviços de Mídia para .NET se quiser mais controle sobre as configurações.

###Modelo de licença do PlayReady

Os Serviços de Mídia fornecem um serviço de distribuição de licenças do PlayReady. Quando o player de usuário final (por exemplo, Silverlight) tenta reproduzir o conteúdo protegido por PlayReady, uma solicitação é enviada para o serviço de entrega de licenças para obter uma licença. Se o serviço de licença aprova a solicitação, ele emite a licença que é enviada ao cliente e pode ser usada para descriptografar e reproduzir o conteúdo especificado.

As licenças contêm os direitos e restrições que você deseja que o tempo de execução do PlayReady DRM imponha quando um usuário está tentando reproduzir conteúdo protegido. Os Serviços de Mídia fornecem APIs que permitem que você configure suas licenças do PlayReady. Para obter mais informações, consulte [Visão geral do modelo de licença do PlayReady dos Serviços de Mídia](https://msdn.microsoft.com/library/azure/dn783459.aspx)

###Restrição de token

A política de autorização de chave de conteúdo pode ter uma ou mais restrições de autorização: abrir, restrição de token ou restrição de IP. A política restrita do token deve ser acompanhada por um token emitido por um Secure Token Service (STS). Os Serviços de Mídia dão suporte a tokens no formato SWT (Simple Web Tokens) e no formato JWT (JSON Web Token). Os serviços de mídia não fornecem Secure Token Services. Você pode criar um STS personalizado ou usar o Microsoft Azure ACS para emitir tokens. O STS deve ser configurado para criar um token assinado com as a chave especificada e declarações de emissão que você especificou na configuração de restrição do token. O serviço de distribuição de chaves dos serviços de mídia retornará a chave (ou licença) solicitada para o cliente se o token for válido e as declarações no token corresponderem àquelas configurados para a chave (ou licença).
Ao configurar a política restrita do token, você deve especificar os parâmetros de chave de verificação primária, emissor e audiência. A chave de verificação primária contém a chave com a qual o token foi assinado. O emissor é o serviço de token seguro que emite o token. A audiência (às vezes chamada de escopo) descreve a intenção do token ou o recurso ao qual o token autoriza o acesso. O serviço de distribuição de chaves dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo.

##Cenários comuns

###Proteger o conteúdo no armazenamento, fornecer mídia de streaming criptografada dinamicamente  

1. Ingerir um arquivo mezzanine de alta qualidade em um ativo. Aplicar a opção de criptografia de armazenamento ao ativo.
2. Configurar pontos de extremidade de streaming.
1. Codificar para MP4 definir a taxa de bits adaptável. Aplicar a opção de criptografia de armazenamento ao ativo de saída.
1. Criar uma chave de criptografia de conteúdo para o ativo que você deseja que seja criptografado dinamicamente durante a reprodução.
2. Configurar a política de autorização de chave de conteúdo
1. Configurar a política de entrega de ativos (usada pelo empacotamento dinâmico e pela criptografia dinâmica).
1. Publicar o ativo criando um localizador OnDemand.
1. Fluxo de conteúdo publicado.

###Usar o serviço de entrega de chave e licença do Serviço de Mídia com seus próprios serviços de criptografia e fluxo

Para obter mais informações, consulte [Como integrar o serviço de licença do Azure PlayReady a seu próprio criptografador/servidor de streaming](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server).

##Tarefas comuns de criptografia de conteúdo

>[AZURE.NOTE] Se seu conteúdo tiver o armazenamento criptografado, configure a política de entrega de ativos.

###Configurando pontos de extremidade de streaming

Para obter uma visão geral sobre pontos de extremidade de streaming e obter informações sobre como gerenciá-los, consulte [Como gerenciar pontos de extremidade de streaming em uma conta dos Serviços de Mídia](media-services-manage-origins.md).

###Criando uma chave de conteúdo

Crie uma chave de conteúdo com a qual você deseja criptografar seu ativo usando o **.NET** ou a **API REST**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../includes/media-services-selector-create-contentkey.md)]

###Configurando a política de autorização de chave de conteúdo 

Configurar a proteção de conteúdo e a política de autorização de chaves usando **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

###Configurando a política de fornecimento de ativos

Configurar a política de fornecimento de ativos usando **.NET** ou **API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]


##Links relacionados

[Anunciando o PlayReady como um serviço e a criptografia dinâmica AES com os Serviços de Mídia do Azure](http://mingfeiy.com/playready)

[Explicação dos preços de entrega da licença do PlayReady dos Serviços de Mídia do Azure](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Como depurar para fluxo de criptografado AES nos Serviços de Mídia do Azure](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[Autenticação do token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrar o aplicativo do MVC OWIN dos serviços de mídia do Azure com base no aplicativo com Active Directory do Azure e restringir o fornecimento da chave de conteúdo com base em declarações JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Usar o ACS do Azure para emitir tokens](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection/media-services-content-protection.png

<!--HONumber=52-->