---
title: Proteger seu conteúdo com os Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da proteção de conteúdo com os Serviços de Mídia.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: juliako
ms.openlocfilehash: 600068113fec0549f3993ac57c1daa93577c6be6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399746"
---
# <a name="content-protection-overview"></a>Visão geral de proteção do conteúdo

É possível usar os Serviços de Mídia do Azure para proteger sua mídia desde o momento que ela sai do seu computador até o armazenamento, processamento e entrega. Com os Serviços de Mídia, é possível entregar o conteúdo ao vivo e sob demanda criptografado dinamicamente com a criptografia AES (AES-128) ou qualquer um dos três principais sistemas DRM (Gerenciamento de Direitos Digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados. 

A seguinte imagem ilustra o fluxo de trabalho de proteção de conteúdo dos Serviços de Mídia: 

![Proteger conteúdo](./media/content-protection/content-protection.png)

&#42;*chave AES-128"Limpar", CBCS e CENC dá suporte a criptografia dinâmica. Para obter detalhes, consulte a matriz de suporte [aqui](#streaming-protocols-and-encryption-types).*

Este artigo explica conceitos e terminologia relevantes para a compreensão da proteção de conteúdo com os Serviços de Mídia. O artigo também tem a seção [FAQ](#faq) e fornece links para artigos que mostram como proteger o conteúdo. 

## <a name="main-components-of-the-content-protection-system"></a>Componentes principais do sistema de proteção de conteúdo

Para concluir com êxito o design do sistema / aplicativo de "proteção de conteúdo", você precisa entender completamente o escopo do esforço. A lista a seguir fornece uma visão geral de três partes, que você precisaria implementar. 

1. Código de Serviços de Mídia do Azure
  
  * Modelos de licença, para PlayReady, Widevine e/ou FairPlay. Os modelos permitem que você configure direitos e permissões para cada um dos DRMs usados
  * Autorização de entrega de licença, especificando a lógica de verificação de autorização com base em declarações no JWT
  * Chaves de conteúdo, protocolos de streaming e DRMs correspondentes aplicados, definindo criptografia DRM

  > [!NOTE]
  > Você pode criptografar cada ativo com vários tipos de criptografia (AES-128, PlayReady, Widevine, FairPlay). Consulte [Tipos de criptografia e protocolos de streaming](#streaming-protocols-and-encryption-types) para ver o que faz sentido combinar.
  
  Os artigos a seguir mostram etapas para criptografar conteúdo com AES e/ou DRM: 
  
  * [Proteger com criptografia AES](protect-with-aes128.md)
  * [Proteger com DRM](protect-with-drm.md)

2. Player com o cliente do AES ou DRM. Um aplicativo de player de vídeo baseado em um player SDK (nativo ou baseado em navegador) precisa atender aos seguintes requisitos:
  * O SDK player dá suporte a clientes necessários do DRM
  * O SDK de player suporta os protocolos de streaming necessários: HLS, Smooth e/ou traço
  * O SDK do player precisa ser capaz de lidar com passar um token JWT na solicitação de aquisição de licença
  
    Você consegue criar um player usando a [API do Player de Mídia do Azure](http://amp.azure.net/libs/amp/latest/docs/). Use a [API ProtectionInfo do Player de Mídia do Azure](http://amp.azure.net/libs/amp/latest/docs/) para especificar qual tecnologia de DRM usar em diferentes plataformas de DRM.

    Para testar conteúdo criptografado AES ou CENC (Widevine e/ou PlayReady), é possível usar o [Player de Mídia do Azure](https://ampdemo.azureedge.net/azuremediaplayer.html). Certifique-se de clicar em "Opções avançadas" e verifique as opções de criptografia.

    Se você quiser testar o conteúdo do FairPlay criptografado, use [esse teste player](http://aka.ms/amtest). O player suporta DRM Widevine, PlayReady e FairPlay, bem como a criptografia de chave AES-128. É necessário escolher o navegador certo para testar diferentes DRMs: Chrome/Opera/Firefox para Widevine, MS Edge/IE11 para PlayReady, Safari no macOS para FairPlay.

3. O Secure Token Service (STS), que emite o JSON Web Token (JWT) como token de acesso para acesso a recursos de back-end. Você pode usar os serviços de entrega de licença do AMS como o recurso de back-end. Um STS deve definir o seguinte:

  * Emissor e público-alvo (ou escopo)
  * Declarações, que são dependentes nos requisitos de negócios na proteção de conteúdo
  * Verificação simétrica ou assimétrica para verificação de assinatura
  * Suporte de substituição de chave (se necessário)

    Você pode usar [esta ferramenta STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) para testar o STS, que suporta todos os 3 tipos de chave de verificação: simétrica, assimétrica ou AAD com substituição de chave. 

> [!NOTE]
> É altamente recomendável focar e testar completamente cada parte (descrita acima) antes de passar para a próxima peça. Para testar seu sistema de "proteção de conteúdo", use as ferramentas especificadas na lista acima.  

## <a name="streaming-protocols-and-encryption-types"></a>Tipos de criptografia e protocolos de streaming

É possível usar os Serviços de Mídia para entregar conteúdo criptografado dinamicamente com chave não criptografada AES ou criptografia DRM usando o PlayReady, o Widevine ou o FairPlay. No momento, você pode criptografar os formatos HLS (HTTP Live Streaming), MPEG DASH e Smooth Streaming. Cada protocolo dá suporte a métodos de criptografia a seguir:

|Protocolo|Formato de contêiner|Esquema de criptografia|
|---|---|---|---|
|MPEG-DASH|Todos|AES|
||CSF(fmp4) |CENC (Widevine + PlayReady) |
||CMAF(fmp4)|CENC (Widevine + PlayReady)|
|HLS|Todos|AES|
||MPG2-TS |CBCS (Fairplay) |
||MPG2-TS |CENC (PlayReady) |
||CMAF(fmp4) |CENC (PlayReady) |
|Smooth Streaming|fMP4|AES|
||fMP4 | CENC (PlayReady) |

## <a name="dynamic-encryption"></a>Criptografia dinâmica

Em Serviços de Mídia v3, uma chave de conteúdo está associada a StreamingLocator (consulte [Este exemplo](protect-with-aes128.md)). Se usar o serviço de distribuição de chaves dos Serviços de Mídia, auto gerar a chave de conteúdo. Você mesmo deve gerar a chave de conteúdo se estiver usando seu próprio serviço de entrega de chaves ou se precisar lidar com um cenário de alta disponibilidade no qual precisa ter a mesma chave de conteúdo em dois datacenters.

Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a chave não criptografada AES ou a criptografia DRM. Para descriptografar o fluxo, o player solicita a chave do serviço de entrega de chave dos Serviços de Mídia ou do serviço de entrega de chaves especificado. Para decidir se o usuário está autorizado ou não a obter a chave, o serviço avalia a política de chave de conteúdo que você especificou para a chave.

## <a name="aes-128-clear-key-vs-drm"></a>Chave AES-128 não criptografada vs. DRM

Geralmente, os clientes se perguntam se devem utilizar criptografia AES ou um sistema DRM. A principal diferença entre os dois sistemas é que, com a criptografia AES, a chave de conteúdo é transmitida ao cliente em um formato não criptografado. Como resultado, a chave usada para criptografar o conteúdo pode ser exibida em um rastreamento de rede no cliente em texto sem formatação. A criptografia de chave AES-128 não criptografada é adequada para casos de uso em que o visualizador é uma parte confiável (por exemplo, criptografar vídeos corporativos distribuídos em uma empresa para serem visualizados pelos funcionários).

PlayReady, Widevine e FairPlay fornecem um maior nível de criptografia em comparação com a criptografia de chave não criptografada AES-128. A chave de conteúdo é transmitida em um formato criptografado. Além disso, a descriptografia é identificada em um ambiente seguro no nível do sistema operacional, no qual é mais difícil para um usuário mal-intencionado atacar. O DRM é recomendado para casos de uso em que o visualizador pode não ser uma parte confiável e você exige o mais alto nível de segurança.

## <a name="storage-side-encryption"></a>Criptografia do armazenamento

Para proteger os Ativos em repouso, os ativos devem ser criptografados pela criptografia do armazenamento. A tabela a seguir mostra como a criptografia do armazenamento funciona nos Serviços de Mídia v3:

|Opção de criptografia|DESCRIÇÃO|Serviços de Mídia v3|
|---|---|---|---|
|Criptografia do Armazenamento dos Serviços de Mídia| Criptografia AES-256, chave gerenciada pelos Serviços de Mídia|Não é compatível<sup>(1)</sup>|
|[Criptografia do Serviço de Armazenamento para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Criptografia do servidor oferecida pelo Armazenamento do Microsoft Azure, chave gerenciada pelo Azure ou pelo cliente|Com suporte|
|[Criptografia do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Criptografia do cliente oferecida pelo armazenamento do Azure, chave gerenciada pelo cliente no Key Vault|Sem suporte|

<sup>1</sup> Nos Serviços de Mídia v3, a criptografia de armazenamento (criptografia AES-256) somente terá suporte para compatibilidade com versões anteriores quando os Ativos tiverem sido criados com os Serviços de Mídia v2. Isso significa que o v3 funciona com recursos criptografados de armazenamento existentes, mas não permite a criação de novos recursos.

## <a name="licenses-and-keys-delivery-service"></a>Serviço de entrega de licenças e chaves

Os Serviços de Mídia fornecem um serviço de entrega de chaves para entrega de licenças DRM (PlayReady, Widevine, FairPlay) e chaves AES para clientes autorizados. Você pode usar o API REST ou biblioteca de cliente de Serviços de Mídia para configurar políticas de autenticação e autorização para suas licenças e chaves.

## <a name="control-content-access"></a>Controlar o acesso de conteúdo

É possível controlar quem tem acesso ao seu conteúdo, configurando a política de chave de conteúdo. Os serviços de mídia oferecem suporte a várias maneiras de autenticar os usuários que fazem solicitações de chave. Você deve configurar a política de chave de conteúdo. O cliente (player) deve estar em conformidade com a política antes de a chave poder ser entregue ao cliente. A política de chave de conteúdo pode ter **abra** ou **token** restrição. 

Com uma política de chave de conteúdo restrita por token, a chave de conteúdo somente será enviada a um cliente que tenha um JWT (Token Web JSON) ou SWT (Token Web Simples) válido na solicitação de licença/chave. Esse token deve ser emitido por um STS (serviço de token de segurança). É possível usar o Azure Active Directory como um STS ou implantar um STS personalizado. O STS deve ser configurado para criar um token assinado com as a chave especificada e declarações de emissão que você especificou na configuração de restrição do token. O serviço de entrega de chaves dos Serviços de Mídia retornará a licença/chave solicitada ao cliente se o token for válido e as declarações no token corresponderem àquelas configuradas para a licença/chave.

Ao configurar a política restrita do token, você deve especificar os parâmetros de chave de verificação primária, emissor e público. A chave de verificação primária contém a chave com a qual o token foi assinado. O emissor é o serviço de token seguro que emite o token. O público, às vezes chamado de escopo, descreve a intenção do token ou do recurso ao qual o token autoriza o acesso. O serviço de distribuição de chaves dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo.

## <a name="a-idfaqfrequently-asked-questions"></a><a id="faq"/>Perguntas frequentes

### <a name="question"></a>Pergunta

Como implementar vários DRM (PlayReady, Widevine e FairPlay) sistema usando os serviços de mídia do Azure (AMS) v3 e também usar serviço de entrega de licença/chave de AMS?

### <a name="answer"></a>Resposta

Para o cenário de ponta a ponta, consulte o [exemplo de código a seguir](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs). 

O exemplo mostra como:

1. Crie e configure ContentKeyPolicies.

  O exemplo contém funções que configuram [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md), e [FairPlay](fairplay-license-overview.md) licenças.

    ```
    ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
    ```

2. Crie um StreamingLocator que está configurado para transmitir um ativo criptografado. 

  No caso deste exemplo, definimos **StreamingPolicyName** para **PredefinedStreamingPolicy.SecureStreaming** que dá suporte à criptografia de envelope e cenc e define duas chaves de conteúdo sobre o StreamingLocator. 

  Se você também deseja criptografar com o FairPlay, defina as **StreamingPolicyName** à **PredefinedStreamingPolicy.SecureStreamingWithFairPlay**.

3. Crie um token de teste.

  O método **GetTokenAsync** mostra como criar um teste de token.
  
4. Crie a URL de streaming.

  O método **GetDASHStreamingUrlAsync** mostra como construir a URL de streaming. Nesse caso, os fluxos de URL o conteúdo **DASH**.

### <a name="question"></a>Pergunta

Como e onde obter o token do JWT antes de usá-lo para a licença de solicitação ou a chave?

### <a name="answer"></a>Resposta

1. Para a produção, você precisa ter um Secure Token Services (STS) (serviço web) que emite o token JWT após uma solicitação HTTPS. Para teste, você pode usar o código mostrado no método **GetTokenAsync** definido no [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Player será necessário fazer uma solicitação, depois que um usuário é autenticado para o STS para esse token e atribuí-lo como o valor do token. Você pode usar o [API do Player de mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/).

* Para obter um exemplo de execução de STS, com a chave simétrica e assimétrica, consulte [http://aka.ms/jwt](http://aka.ms/jwt). 
* Para obter um exemplo de um player com base no Player de mídia do Azure usando tal token JWT, consulte [http://aka.ms/amtest](http://aka.ms/amtest) (expanda o link de "player_settings" para ver a entrada de token).

### <a name="question"></a>Pergunta

Como você autorizar solicitações para transmitir vídeos com a criptografia AES?

### <a name="answer"></a>Resposta

A abordagem correta é aproveitar o STS (serviço de Token seguro):

No STS, dependendo do perfil do usuário, adicione declarações diferentes (por exemplo, "Usuário Premium", "Usuário básico", "Usuário de avaliação gratuita"). Com diferentes declarações JWT, o usuário pode ver é diferente do conteúdo. É claro que, para conteúdo diferente/ativo, o ContentKeyPolicyRestriction terá o RequiredClaims correspondente.

Use APIs de serviços de mídia do Azure para configurar a licença/chave entrega e criptografar seus ativos (conforme mostrado em [Este exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs).

## <a name="next-steps"></a>Próximas etapas

Confira os artigos a seguir:

  * [Proteger com criptografia AES](protect-with-aes128.md)
  * [Proteger com DRM](protect-with-drm.md)

Informações adicionais podem ser encontradas no [design e implementação de referência de DRM](../previous/media-services-cenc-with-multidrm-access-control.md)


