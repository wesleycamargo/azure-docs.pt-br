<properties 
	pageTitle="Visão geral sobre a proteção de conteúdo | Microsoft Azure" 
	description="Este artigo fornece uma visão geral da proteção de conteúdo com os Serviços de Mídia." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/27/2016" 
	ms.author="juliako"/>

#Visão geral sobre a proteção de conteúdo


Os Serviços de Mídia do Microsoft Azure permitem proteger a mídia desde o momento em que ela deixa computador e durante o armazenamento, processamento e entrega. Os Serviços de Mídia permitem que você entregue o conteúdo ao vivo e sob demanda criptografado dinamicamente com o Standard de Criptografia Avançada (AES) (usando chaves de criptografia de 128 bits) ou quaisquer DRMs principais: Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados.

A imagem a seguir demonstra os fluxos de trabalho de proteção de conteúdo que oferece suporte ao AMS.

![Proteger com o PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

>[AZURE.NOTE]Para poder usar criptografia dinâmica, primeiro é necessário obter pelo menos uma unidade reservada de streaming no ponto de extremidade de streaming do qual você deseja transmitir conteúdo criptografado.

Este tópico explica os [conceitos e terminologia](media-services-content-protection-overview.md) relevantes para entender a proteção de conteúdo com o AMS. O tópico também contém [links](media-services-content-protection-overview.md#common-scenarios) para os tópicos que mostram como realizar as tarefas de proteção de conteúdo.

##Criptografia dinâmica

Os Serviços de Mídia do Microsoft Azure permitem que você forneça o conteúdo criptografado dinamicamente com a chave de limpeza do AES ou a criptografia DRM: Microsoft PlayReady, Google Widevine, Apple FairPlay.

Observe que, no momento, você pode criptografar o seguintes formatos de streaming: HLS, MPEG DASH, e Smooth Streaming. Você não pode criptografar o formato de streaming HDSou fazer o download progressivo.

Se você desejar que os Serviços de Mídia criptografem um ativo, você precisa associar uma chave de criptografia (CommonEncryption ou EnvelopeEncryption) ao ativo e também configurar políticas de autorização para a chave.

Você também precisa configurar a política de entrega do ativo. Se você deseja transmitir um ativo de armazenamento criptografado, certifique-se de especificar como deseja entregá-lo configurando a política de entrega de ativos.

Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a chave de limpeza do AES ou a criptografia DRM. Para descriptografar o fluxo, o player solicitará a chave do serviço de distribuição de chaves. Para decidir se o usuário está autorizado para obter a chave ou não, o serviço avalia as políticas de autorização que você especificou para a chave.

>[AZURE.NOTE]Para tirar proveito da criptografia dinâmica, você precisa obter primeiro pelo menos uma unidade de streaming OnDemand para o ponto de extremidade de streaming por meio do qual você planeja fornecer seu conteúdo criptografado. Para obter mais informações, consulte [Como dimensionar os Serviços de Mídia](media-services-portal-manage-streaming-endpoints.md).

##Criptografia do armazenamento

Use a criptografia de armazenamento para criptografar seu conteúdo limpo localmente usando a criptografia AES de 256 bits, em seguida, carregue-o no Armazenamento do Azure no qual é armazenado e criptografado em repouso. Ativos protegidos pela criptografia de armazenamento são descriptografados automaticamente e posicionados em um sistema de arquivos criptografado antes da codificação, então opcionalmente criptografados novamente antes do carregamento como um novo ativo de saída. O caso de uso primário para criptografia de armazenamento é quando você deseja proteger seus arquivos de mídia de entrada de alta qualidade com criptografia forte em repouso no disco.

Para entregar um ativo de armazenamento criptografado, você deve configurar a política de entrega do ativo para que o Serviços de Mídia saiba como você deseja distribuir seu conteúdo. Antes que seu ativo possa ser transmitido, o servidor de streaming remove a criptografia de armazenamento e transmite o conteúdo usando a política de distribuição especificada (por exemplo, AES, criptografia comum ou sem criptografia).

###Detalhes de implementação 

A criptografia de armazenamento AMS aplica a criptografia do modo **AES-CTR** ao arquivo inteiro. O modo AES-CTR é uma codificação de bloco que pode criptografar dados de comprimento arbitrário sem necessidade de preenchimento. Ela funciona criptografando um bloco de contador com o algoritmo AES e aplicando XOR à saída do AES com os dados para criptografar ou descriptografar. O bloco de contador usado é construído copiando o valor do InitializationVector para bytes de 0 a 7 do valor do contador e 8 a 15 do valor do contador são definidos como zero. Do bloco de contador de 16 bytes, os bytes de 8 a 15 (ou seja, os bytes menos significativos) são usados como um inteiro sem sinal de 64 bits simples que é incrementado em um para cada bloco subsequente de dados processados e é mantido em ordem de byte da rede. Observe que, se esse número inteiro atingir o valor máximo (0xFFFFFFFFFFFFFFFF), incrementá-lo redefinirá o contador de bloco para zero (bytes 8 a 15) sem afetar os 64 bits do contador (ou seja, bytes de 0 a 7). Para manter a segurança de criptografia de modo AES-CTR, o valor de InitializationVector para uma determinada CRIANÇA deve ser exclusivo para cada arquivo e os arquivos devem ser menor que 2^64 blocos em comprimento. Isso serve para garantir que um valor de contador jamais seja reutilizado com uma determinada chave. Para obter mais informações sobre o modo CTR, consulte [esta página wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (artigo da wiki usa o termo "Nonce" em vez de "InitializationVector").

Se você quiser ver como o algoritmo básico funciona, exiba a implementação do .NET do AMS dos seguintes métodos:

- [ApplyEncryptionTransform](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.BlobTransfer/BlobTransferBase.cs)
- [AesCtr](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/FileEncryptionTransform.cs)


## Criptografia comum (CENC)

A criptografia comum é usada ao criptografar o conteúdo com PlayReady e/ou Widewine.

## Usando a criptografia cbcs-aapl

Cbcs-aapl é usado ao criptografar seu conteúdo com o FairPlay.

## Criptografia de envelope 

Use esta opção se quiser proteger seu conteúdo com a chave de limpeza AES-128. Se você quiser uma opção mais segura, escolha um dos DRMs listados neste tópico.

##Serviço de entrega de licenças e chaves

Os Serviços de Mídia fornecem um serviço de distribuição de licenças DRM (PlayReady, Widevine e FairPlay) e chaves de limpeza AES para os clientes autorizados. Você pode usar o [portal do Azure](media-services-portal-protect-content.md), API REST ou SDK dos Serviços de Mídia para .NET para configurar políticas de autenticação e autorização para suas licenças e chaves.

##Restrição de token

A política de autorização de chave de conteúdo pode ter uma ou mais restrições de autorização: aberta ou restrição de token. A política restrita do token deve ser acompanhada por um token emitido por um Secure Token Service (STS). Os serviços de mídia oferecem suporte a tokens no formato Simple Web Tokens (SWT) e no formato JSON Web Token (JWT). Os serviços de mídia não fornecem Secure Token Services. Você pode criar um STS personalizado ou usar o Microsoft Azure ACS para emitir tokens. O STS deve ser configurado para criar um token assinado com as a chave especificada e declarações de emissão que você especificou na configuração de restrição do token. O serviço de distribuição de chaves dos Serviços de Mídia retornará a chave de criptografia para o cliente se o token for válido e as declarações no token corresponderem àquelas configuradas para a chave (ou licença).

Ao configurar a política restrita do token, você deve especificar os parâmetros da chave de verificação primária, emissor e audiência. A chave de verificação primária contém a chave que o token foi assinado, o emissor é o serviço de token seguro que emite o token. A audiência (às vezes chamada de escopo) descreve a intenção do token ou o recurso que o token autoriza o acesso. O serviço de distribuição de chaves dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo.

##URLs de streaming

Se o ativo foi criptografado com mais de um DRM, você deve usar uma marcação de criptografia na URL de streaming: (formato='m3u8-aapl' criptografia='xxx').

As seguintes considerações se aplicam:

- Pode ser especificado apenas zero ou um tipo de criptografia.
- O tipo de criptografia não precisa ser especificado na url se apenas uma criptografia foi aplicada no ativo.
- O tipo de criptografia diferencia as letras maiúsculas de minúsculas.
- Os seguintes tipos de criptografia podem ser especificados:
	- **cenc**: criptografia comum (Playready ou Widevine)
	- **cbcs-aapl**: Fairplay
	- **cbc**: criptografia de envelope AES.

##Cenários comuns

Os tópicos a seguir demonstram como proteger o conteúdo no armazenamento, entregar a mídia de streaming criptografada dinamicamente e usar o serviço de entrega de chaves/licenças do AMS

- [Proteger com o AES](media-services-protect-with-aes128.md)
- [Proteger com o PlayReady e/ou Widevine ](media-services-protect-with-drm.md)
- [Transmitir seu conteúdo HLS Protegido com o Apple FairPlay e/ou PlayReady](media-services-protect-hls-with-fairplay.md)

### Cenários adicionais

- [Como integrar o serviço de Licença do Azure PlayReady em seu próprio servidor de criptografia/streaming](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server).
- [Usando o castLabs para fornecer licenças DRM para os serviços de mídia do Azure](media-services-castlabs-integration.md)
 
##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Links relacionados

[Anunciando o PlayReady como um serviço e a criptografia dinâmica AES com os Serviços de Mídia do Azure](http://mingfeiy.com/playready)

[Explicação dos preços de entrega da licença do PlayReady dos Serviços de Mídia do Azure](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Como depurar para fluxo de criptografado AES nos Serviços de Mídia do Azure](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[Autenticação do token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrar o aplicativo do MVC OWIN dos serviços de mídia do Azure com base no aplicativo com Active Directory do Azure e restringir o fornecimento da chave de conteúdo com base em declarações JWT.](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Usar o ACS do Azure para emitir tokens](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png

<!---HONumber=AcomDC_0928_2016-->