---
title: "Proteger seu conteúdo com os Serviços de Mídia do Azure | Microsoft Docs"
description: "Este artigo fornece uma visão geral da proteção de conteúdo com os Serviços de Mídia."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 6475a865b9d1b263bd7cc68c99acdb5f6959531e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2017
---
# <a name="protecting-content-overview"></a>Visão geral sobre a proteção de conteúdo
Os Serviços de Mídia do Microsoft Azure permitem proteger a mídia desde o momento em que ela deixa computador e durante o armazenamento, processamento e entrega. Os Serviços de Mídia permitem que você entregue o conteúdo ao vivo e sob demanda criptografado dinamicamente com a criptografia AES (AES-128) ou qualquer um dos três principais sistemas DRM: Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados. 

A seguinte imagem ilustra o fluxo de trabalho de proteção de conteúdo dos Serviços de Mídia do Microsoft Azure. 

![Proteger com o PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Este artigo explica conceitos e terminologia relevantes para a compreensão da proteção de conteúdo com os Serviços de Mídia do Azure. O artigo também fornece links para artigos que descrevem como proteger o conteúdo. 

## <a name="dynamic-encryption"></a>Criptografia dinâmica
Os Serviços de Mídia do Microsoft Azure permitem que você entregue o conteúdo criptografado dinamicamente com chave não criptografada AES ou criptografia DRM: Microsoft PlayReady, Google Widevine e Apple FairPlay. Observe que, no momento, você pode criptografar o seguintes formatos de streaming: HLS, MPEG DASH, e Smooth Streaming. Não há suporte para criptografia em downloads progressivos. Cada método de criptografia fornece suporte aos seguintes protocolos de streaming:
- AES: MPEG-DASH, Smooth Streaming e HLS
- PlayReady: MPEG-DASH, Smooth Streaming e HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

Para criptografar um ativo, é necessário associar uma chave de conteúdo de criptografia ao seu ativo e também configurar uma política de autorização para a chave. As chaves de conteúdo podem ser especificadas ou geradas automaticamente pelos Serviços de Mídia.

Você também precisa configurar a política de entrega do ativo. Se você deseja transmitir por streaming um ativo criptografado de armazenamento, certifique-se de especificar como deseja entregá-lo, configurando a política de entrega do ativo.

Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a chave de limpeza do AES ou a criptografia DRM. Para descriptografar o stream, o player solicita a chave do serviço de distribuição de chaves AMS. Para decidir se o usuário está autorizado para obter a chave ou não, o serviço avalia as políticas de autorização que você especificou para a chave.

## <a name="aes-128-clear-key-vs-drm"></a>Chave não criptografada AES-128 vs DRM
Geralmente, os clientes se perguntam se devem utilizar criptografia AES ou um sistema DRM. A principal diferença entre o uso da criptografia AES e sistemas DRM é que, com a criptografia AES a chave de conteúdo é transmitida ao cliente em um formato não criptografado ("em não criptografado"). Como resultado, a chave utilizada para criptografar o conteúdo pode ser exibida em um rastreamento de rede no cliente em texto não criptografado. A chave não criptografas AES-128 é adequada para casos de uso em que o visualizador é uma parte confiável (por exemplo: criptografar vídeos corporativos distribuídos em uma empresa para serem visualizados pelos funcionários).

PlayReady, Widevine e FairPlay fornecem um maior nível de criptografia em comparação com a criptografia de chave não criptografada AES-128. A chave de conteúdo é transmitida em um formato criptografado. Além disso, a descriptografa é identificada em um ambiente seguro no nível do sistema operacional onde é significativamente mais difícil para um usuário mal-intencionado atacar. DRM é recomendado para casos de uso em que o visualizador pode não ser uma parte confiável e você exige o mais alto nível de segurança.

## <a name="storage-encryption"></a>Criptografia do armazenamento
É possível utilizar a criptografia de armazenamento para criptografar seu conteúdo não criptografado localmente, utilizando criptografia de 256 bits AES e, em seguida, fazer o upload para o Armazenamento do Microsoft Azure, onde será armazenado criptografado em repouso. Ativos protegidos pela criptografia de armazenamento são descriptografados automaticamente e posicionados em um sistema de arquivos criptografado antes da codificação, então opcionalmente criptografados novamente antes do carregamento como um novo ativo de saída. O caso de uso principal para criptografia de armazenamento é quando você deseja proteger seus arquivos de mídia de entrada com criptografia forte de alta qualidade em repouso no disco.

Para entregar um ativo de armazenamento criptografado, você deve configurar a política de entrega do ativo para que o Serviços de Mídia saiba como você deseja distribuir seu conteúdo. Antes que seu ativo possa ser transmitido por streaming, o servidor de streaming descriptografa e transmite por streaming o conteúdo, utilizando a política de entrega especificada (por exemplo, AES, criptografia comum ou sem criptografia).

## <a name="types-of-encryption"></a>Tipos de criptografia
Playready e Widevine utilizam Criptografia Comum (modo de AES-CTR). FairPlay utiliza a criptografia de modo AES-CBC. Criptografia de chave não criptografada AES-128 utiliza Criptografia de Envelope.

## <a name="licenses-and-keys-delivery-service"></a>Serviço de entrega de licenças e chaves
Os Serviços de Mídia fornecem um serviço de entrega de chaves para entrega de licenças DRM (PlayReady, Widevine, FairPlay) e chaves AES para clientes autorizados. Você pode usar o [portal do Azure](media-services-portal-protect-content.md), API REST ou SDK dos Serviços de Mídia para .NET para configurar políticas de autenticação e autorização para suas licenças e chaves.

## <a name="control-content-access"></a>Controlar o acesso de conteúdo
É possível controlar quem tem acesso ao seu conteúdo, configurando a política de autorização de chave de conteúdo. A política de autorização de chave de conteúdo fornece suporte para restrição de token ou aberta.

### <a name="open-authorization"></a>Autorização aberta
Com uma política de autorização aberta, a chave de conteúdo é enviada para qualquer cliente (sem restrição).

### <a name="token-authorization"></a>Autorização de token
Com uma política de autorização restrita por token, a chave de conteúdo somente será enviada a um cliente que apresente um Token Web JSON (JWT) ou Token Web Simples (SWT) válido na solicitação de licença/chave. Esse token deve ser emitido por um STS (Serviço de Token de Segurança). É possível utilizar o Microsoft Active Directory como um STS ou implantar um STS personalizado. O STS deve ser configurado para criar um token assinado com as a chave especificada e declarações de emissão que você especificou na configuração de restrição do token. O serviço de distribuição de chaves dos Serviços de Mídia retornará a licença/chave solicitada ao cliente se o token for válido e as declarações no token corresponderem àquelas configuradas para a licença/chave.

Ao configurar a política restrita do token, você deve especificar os parâmetros da chave de verificação primária, emissor e audiência. A chave de verificação primária contém a chave que o token foi assinado, o emissor é o serviço de token seguro que emite o token. O público, às vezes chamado de escopo, descreve a intenção do token ou do recurso ao qual o token autoriza o acesso. O serviço de distribuição de chaves dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo.

## <a name="streaming-urls"></a>URLs de streaming
Se o ativo foi criptografado com mais de um DRM, você deve usar uma marcação de criptografia na URL de streaming: (formato='m3u8-aapl' criptografia='xxx').

As seguintes considerações se aplicam:
* Não é possível especificar mais do que um tipo de criptografia.
* O tipo de criptografia não precisa ser especificado na URL, se apenas uma criptografia for aplicada ao ativo.
* O tipo de criptografia diferencia as letras maiúsculas de minúsculas.
* Os seguintes tipos de criptografia podem ser especificados:  
  * **cenc**:  para PlayReady ou Widevine (Criptografia Comum)
  * **cbcs-aapl**: para FairPlay (Criptografia AES-CBC)
  * **cbc**: para criptografia de envelope AES (Criptografia de Envelope)

## <a name="next-steps"></a>Próximas etapas
Os artigos a seguir descrevem as próximas etapas para a introdução de proteção de conteúdo:
* [Proteger com criptografia de armazenamento](media-services-rest-storage-encryption.md)
* [Proteger com criptografia AES](media-services-protect-with-aes128.md)
* [Proteger com PlayReady e/ou Widevine](media-services-protect-with-playready-widevine.md)
* [Proteger com FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Links relacionados
[Explicação dos preços de entrega da licença do PlayReady dos Serviços de Mídia do Azure](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Como depurar para fluxo de criptografado AES nos Serviços de Mídia do Azure](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[Autenticação do token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrar o aplicativo baseado em MVC OWIN dos Serviços de Mídia do Microsoft Azure com o Azure Active Directory e restringir a distribuição de chaves de conteúdo com base em reivindicações JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
