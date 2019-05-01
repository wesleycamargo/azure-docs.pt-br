---
title: Proteger o conteúdo usando criptografia dinâmica dos serviços de mídia - Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da proteção de conteúdo com criptografia dinâmica. Ele também fala sobre os tipos de criptografia e protocolos de streaming.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 40e7ebcddb5cc215e071f1a34dfa8f3f4ea95141
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64684575"
---
# <a name="content-protection-with-dynamic-encryption"></a>Proteção de conteúdo com criptografia dinâmica

É possível usar os Serviços de Mídia do Azure para proteger sua mídia desde o momento que ela sai do seu computador até o armazenamento, processamento e entrega. Com os Serviços de Mídia, é possível entregar o conteúdo ao vivo e sob demanda criptografado dinamicamente com a criptografia AES (AES-128) ou qualquer um dos três principais sistemas DRM (gerenciamento de direitos digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados. 

A seguinte imagem ilustra o fluxo de trabalho de proteção de conteúdo dos Serviços de Mídia: 

![Proteger conteúdo](./media/content-protection/content-protection.svg)

&#42;*chave AES-128"Limpar", CBCS e CENC dá suporte a criptografia dinâmica. Para obter detalhes, consulte a matriz de suporte [aqui](#streaming-protocols-and-encryption-types).*

Este artigo explica conceitos e terminologia relevantes para a compreensão da proteção de conteúdo com os Serviços de Mídia.

## <a name="main-components-of-a-content-protection-system"></a>Componentes principais de um sistema de proteção de conteúdo

Para concluir com êxito o design do sistema / aplicativo de "proteção de conteúdo", você precisa entender completamente o escopo do esforço. A lista a seguir fornece uma visão geral de três partes, que você precisaria implementar. 

1. Código de Serviços de Mídia do Azure
  
   O [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) exemplo mostra como implementar o sistema de DRM múltiplo com os serviços de mídia v3 e também usar o serviço de entrega de chave/licença dos serviços de mídia. Você pode criptografar cada ativo com vários tipos de criptografia (AES-128, PlayReady, Widevine, FairPlay). Consulte [Tipos de criptografia e protocolos de streaming](#streaming-protocols-and-encryption-types) para ver o que faz sentido combinar.
  
   O exemplo mostra como:

   1. Criar e configurar [políticas de chave de conteúdo](https://docs.microsoft.com/rest/api/media/contentkeypolicies).

      * Defina a autorização de entrega de licença, especificando a lógica de verificação de autorização com base em declarações no JWT.
      * Configure a criptografia do DRM, especificando a chave de conteúdo.
      * Configure [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md), e/ou [FairPlay](fairplay-license-overview.md) licenças. Os modelos permitem que você configure direitos e permissões para cada um dos DRMs usados.

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
   2. Criar uma [localizador de Streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) que é configurado para transmitir o ativo criptografado. 
  
      O **localizador de Streaming** tem a ser associada com um [Streaming política](https://docs.microsoft.com/rest/api/media/streamingpolicies). No exemplo, definimos StreamingLocator.StreamingPolicyName à política "Predefined_MultiDrmCencStreaming". Essa política indica que desejamos para duas chaves de conteúdo (envelope e CENC) para obter gerado e definir no localizador. Assim, as criptografias do envelope, PlayReady e Widevine são aplicadas (a chave é entregue ao cliente de reprodução com base nas licenças de DRM configuradas). Se você também quiser criptografar seu fluxo com CBCS (FairPlay), use “Predefined_MultiDrmStreaming”.
    
      Como queremos criptografar o vídeo, o **política de chave de conteúdo** que configuramos anteriormente também tem a ser associada com o **localizador de Streaming**. 
    
   3. Crie um token de teste.

      O método **GetTokenAsync** mostra como criar um teste de token.
   4. Crie a URL de streaming.

      O método **GetDASHStreamingUrlAsync** mostra como construir a URL de streaming. Nesse caso, os fluxos de URL o conteúdo **DASH**.

2. Player com o cliente do AES ou DRM. Um aplicativo de player de vídeo baseado em um player SDK (nativo ou baseado em navegador) precisa atender aos seguintes requisitos:
   * O SDK player dá suporte a clientes necessários do DRM
   * O SDK de player suporta os protocolos de streaming necessários: Smooth, DASH e/ou HLS
   * O SDK do player precisa ser capaz de lidar com passar um token JWT na solicitação de aquisição de licença
  
     Você consegue criar um player usando a [API do Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/). Use a [API ProtectionInfo do Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/) para especificar qual tecnologia de DRM usar em diferentes plataformas de DRM.

     Para testar conteúdo criptografado AES ou CENC (Widevine e/ou PlayReady), é possível usar o [Player de Mídia do Azure](https://ampdemo.azureedge.net/azuremediaplayer.html). Certifique-se de clicar em "Opções avançadas" e verifique as opções de criptografia.

     Se você quiser testar o conteúdo do FairPlay criptografado, use [esse teste player](https://aka.ms/amtest). O player suporta DRM Widevine, PlayReady e FairPlay, bem como a criptografia de chave AES-128. 
    
     Você precisa escolher o navegador correto para testar DRMs diferentes: Chrome/Opera/Firefox para Widevine, Microsoft Edge/IE11 para PlayReady, Safari no macOS para FairPlay.

3. O Secure Token Service (STS), que emite o JSON Web Token (JWT) como token de acesso para acesso a recursos de back-end. Você pode usar os serviços de entrega de licença do AMS como o recurso de back-end. Um STS deve definir o seguinte:

   * Emissor e público-alvo (ou escopo)
   * Declarações, que são dependentes nos requisitos de negócios na proteção de conteúdo
   * Verificação simétrica ou assimétrica para verificação de assinatura
   * Suporte de substituição de chave (se necessário)

     É possível usar [esta ferramenta de STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) para testar o STS, que dá suporte a todos os 3 tipos de chave de verificação: simétrica, assimétrica ou Azure AD com substituição de chave. 

> [!NOTE]
> É altamente recomendável focar e testar completamente cada parte (descrita acima) antes de passar para a próxima peça. Para testar seu sistema de "proteção de conteúdo", use as ferramentas especificadas na lista acima.  

## <a name="streaming-protocols-and-encryption-types"></a>Tipos de criptografia e protocolos de streaming

É possível usar os Serviços de Mídia para entregar conteúdo criptografado dinamicamente com chave não criptografada AES ou criptografia DRM usando o PlayReady, o Widevine ou o FairPlay. No momento, você pode criptografar os formatos HLS (HTTP Live Streaming), MPEG DASH e Smooth Streaming. Cada protocolo dá suporte a métodos de criptografia a seguir:

### <a name="hls"></a>HLS

O protocolo HLS suporta os seguintes formatos de contêiner e esquemas de criptografia.

|Formato de contêiner|Esquema de criptografia|Exemplo de URL|
|---|---|---|
|Todos|AES|`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) ||
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) ||
|CMAF(fmp4) |CENC (PlayReady) ||

CMAF/HLS + FairPlay (incluindo HEVC / H.265) é compatível com os seguintes dispositivos:

* v11 do iOS ou superior 
* iPhone 8 ou superior
* O MacOS high Sierra Intel 7ª CPU de Gen

### <a name="mpeg-dash"></a>MPEG-DASH

O protocolo de MPEG-DASH suporta os seguintes formatos de contêiner e esquemas de criptografia.

|Formato de contêiner|Esquema de criptografia|Exemplos de URL
|---|---|---|
|Todos|AES|`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)||

### <a name="smooth-streaming"></a>Smooth Streaming

O protocolo Smooth Streaming suporta os seguintes formatos de contêiner e esquemas de criptografia.

|Protocol|Formato de contêiner|Esquema de criptografia|
|---|---|---|
|fMP4|AES||
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/<id>/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Navegadores

Navegadores comuns oferecem suporte a clientes DRM a seguir:

|Navegador|Criptografia|
|---|---|
|Chrome|Widevine|
|Edge, IE 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="aes-128-clear-key-vs-drm"></a>Chave AES-128 não criptografada vs. DRM

Geralmente, os clientes se perguntam se devem utilizar criptografia AES ou um sistema DRM. A principal diferença entre os dois sistemas é que com a criptografia AES a chave de conteúdo é transmitida ao cliente via TLS para que a chave é criptografada em trânsito, mas sem qualquer criptografia adicional ("de modo transparente"). Como resultado, a chave usada para descriptografar o conteúdo está acessível para o player de cliente e pode ser exibida em um rastreamento de rede no cliente em texto sem formatação. A criptografia de chave AES-128 não criptografada é adequada para casos de uso em que o visualizador é uma parte confiável (por exemplo, criptografar vídeos corporativos distribuídos em uma empresa para serem visualizados pelos funcionários).

Sistemas DRM, como PlayReady, Widevine e FairPlay fornecem um nível adicional de criptografia na chave usada para descriptografar o conteúdo em comparação comparado a chave não criptografada AES-128. A chave de conteúdo é criptografada para uma chave protegida pelo tempo de execução DRM no adicional para qualquer criptografia de nível de transporte fornecida pelo TLS. Além disso, a descriptografia é identificada em um ambiente seguro no nível do sistema operacional, no qual é mais difícil para um usuário mal-intencionado atacar. O DRM é recomendado para casos de uso em que o visualizador pode não ser uma parte confiável e você exige o mais alto nível de segurança.

## <a name="dynamic-encryption-and-key-delivery-service"></a>Serviço de distribuição de chaves e criptografia dinâmica

Em serviços de mídia v3, uma chave de conteúdo está associada com o localizador de Streaming (consulte [Este exemplo](protect-with-aes128.md)). Se usar o serviço de distribuição de chaves dos serviços de mídia, você pode permitir que os serviços de mídia do Azure gere a chave de conteúdo para você. Você mesmo deve gerar a chave de conteúdo se estiver usando seu próprio serviço de entrega de chaves ou se precisar lidar com um cenário de alta disponibilidade no qual precisa ter a mesma chave de conteúdo em dois datacenters.

Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a chave não criptografada AES ou a criptografia DRM. Para descriptografar o fluxo, o player solicita a chave do serviço de entrega de chave dos Serviços de Mídia ou do serviço de entrega de chaves especificado. Para decidir se o usuário está autorizado ou não a obter a chave, o serviço avalia a política de chave de conteúdo que você especificou para a chave.

Os Serviços de Mídia fornecem um serviço de entrega de chaves para entrega de licenças DRM (PlayReady, Widevine, FairPlay) e chaves AES para clientes autorizados. Você pode usar o API REST ou biblioteca de cliente de Serviços de Mídia para configurar políticas de autenticação e autorização para suas licenças e chaves.

### <a name="custom-key-and-license-acquisition-url"></a>URL de aquisição de licença e a chave personalizada

Use os modelos a seguir se você quiser especificar um diferentes licença e a chave de serviço de entrega (não os serviços de mídia). Os dois campos substituíveis nos modelos estão lá para que você pode compartilhar sua política de Streaming em vários ativos em vez de criar uma política de Streaming por ativo. 

* EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate - modelo para a URL do serviço personalizado, entrega de chaves para jogadores do usuário final. Não é necessário ao usar os serviços de mídia do Azure para emitir chaves. O modelo dá suporte a tokens substituíveis que o serviço será atualizado em tempo de execução com o valor específico para a solicitação.  Os valores do token com suporte no momento são {AlternativeMediaId}, que é substituído com o valor de StreamingLocatorId.AlternativeMediaId e {ContentKeyId}, que é substituído pelo valor do identificador da chave que está sendo solicitado.
* StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate - modelo para a URL do serviço personalizado entregar as licenças para os jogadores do usuário final. Não é necessário ao usar os serviços de mídia do Azure para emitir licenças. O modelo dá suporte a tokens substituíveis que o serviço será atualizado em tempo de execução com o valor específico para a solicitação. Os valores do token com suporte no momento são {AlternativeMediaId}, que é substituído com o valor de StreamingLocatorId.AlternativeMediaId e {ContentKeyId}, que é substituído pelo valor do identificador da chave que está sendo solicitado. 
* StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate - mesmo que acima, somente para Widevine. 
* StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate - mesmo que acima, somente para FairPlay.  

Por exemplo: 

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

O `ContentKeyId` tem um valor da chave que está sendo solicitado e o `AlternativeMediaId` pode ser usado para mapear a solicitação a uma entidade no seu lado. Por exemplo, o `AlternativeMediaId` pode ser usado para ajudar a encontrar as permissões.

Para exemplos REST que usam personalizado da chave e URLs de aquisição de licença, consulte [criar diretivas de Streaming -](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)

## <a name="control-content-access"></a>Controlar o acesso de conteúdo

É possível controlar quem tem acesso ao seu conteúdo, configurando a política de chave de conteúdo. Os serviços de mídia oferecem suporte a várias maneiras de autorizar os usuários que fazem solicitações de chave. Você deve configurar a política de chave de conteúdo. O cliente (player) deve estar em conformidade com a política antes de a chave poder ser entregue ao cliente. A política de chave de conteúdo pode ter **abra** ou **token** restrição. 

Com uma política de chave de conteúdo restrita por token, a chave de conteúdo somente será enviada a um cliente que tenha um JWT (Token Web JSON) ou SWT (Token Web Simples) válido na solicitação de licença/chave. Esse token deve ser emitido por um STS (serviço de token de segurança). É possível usar o Azure Active Directory como um STS ou implantar um STS personalizado. O STS deve ser configurado para criar um token assinado com as a chave especificada e declarações de emissão que você especificou na configuração de restrição do token. O serviço de entrega de chaves dos Serviços de Mídia retornará a licença/chave solicitada ao cliente se o token for válido e as declarações no token corresponderem àquelas configuradas para a licença/chave.

Ao configurar a política restrita do token, você deve especificar os parâmetros de chave de verificação primária, emissor e público. A chave de verificação primária contém a chave com a qual o token foi assinado. O emissor é o serviço de token seguro que emite o token. O público, às vezes chamado de escopo, descreve a intenção do token ou do recurso ao qual o token autoriza o acesso. O serviço de distribuição de chaves dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo.

Os clientes normalmente usam um STS personalizado para incluir declarações personalizadas no token para selecionar entre ContentKeyPolicyOptions diferentes com diferentes parâmetros de licença DRM (uma licença de assinatura em comparação com uma licença de aluguel) ou para incluir uma declaração que representa a chave de conteúdo Identificador da chave que o token concede acesso.
 
## <a name="storage-side-encryption"></a>Criptografia do armazenamento

Para proteger os Ativos em repouso, os ativos devem ser criptografados pela criptografia do armazenamento. A tabela a seguir mostra como a criptografia do armazenamento funciona nos Serviços de Mídia v3:

|Opção de criptografia|DESCRIÇÃO|Serviços de Mídia v3|
|---|---|---|
|Criptografia do Armazenamento dos Serviços de Mídia| Criptografia AES-256, chave gerenciada pelos Serviços de Mídia|Não é compatível<sup>(1)</sup>|
|[Criptografia do Serviço de Armazenamento para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Criptografia do servidor oferecida pelo Armazenamento do Microsoft Azure, chave gerenciada pelo Azure ou pelo cliente|Com suporte|
|[Criptografia do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Criptografia do cliente oferecida pelo armazenamento do Azure, chave gerenciada pelo cliente no Key Vault|Sem suporte|

<sup>1</sup> Nos Serviços de Mídia v3, a criptografia de armazenamento (criptografia AES-256) somente terá suporte para compatibilidade com versões anteriores quando os Ativos tiverem sido criados com os Serviços de Mídia v2. Isso significa que o v3 funciona com recursos criptografados de armazenamento existentes, mas não permite a criação de novos recursos.

## <a name="troubleshoot"></a>Solução de problemas

Se você receber o `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` erro, verifique se você especificar a política de Streaming apropriada.

Se você obtiver erros que terminam com `_NOT_SPECIFIED_IN_URL`, certifique-se de que você especifica o formato de criptografia na URL. Por exemplo, `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Ver [tipos de criptografia e protocolos de Streaming](#streaming-protocols-and-encryption-types).

## <a name="provide-feedback"></a>Fornecer comentários

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

* [Proteger com criptografia AES](protect-with-aes128.md)
* [Proteger com DRM](protect-with-drm.md)
* [Crie o sistema de proteção de conteúdo de DRM múltiplo com controle de acesso](design-multi-drm-system-with-access-control.md)
* [Perguntas frequentes](frequently-asked-questions.md)

