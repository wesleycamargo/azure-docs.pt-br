---
title: "Passar o token de autenticação para os Serviços de Mídia do Azure | Microsoft Docs"
description: "Saiba como enviar os tokens de autenticação do cliente para o serviço de distribuição de chaves dos Serviços de Mídia do Azure"
services: media-services
keywords: "Proteção de conteúdo, gerenciamento de direitos digitais, autenticação de token"
documentationcenter: 
author: dbgeorge
manager: jasonsue
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: dwgeo
ms.openlocfilehash: 0e56726266898e5738dd797a8a019987d457170e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2017
---
# <a name="how-clients-pass-tokens-to-azure-media-services-key-delivery-service"></a>Como os clientes passam tokens para o serviço de distribuição de chaves dos Serviços de Mídia do Azure
Recebemos constantemente perguntas sobre como um player pode passar um token para os nossos serviços de distribuição de chaves, que serão verificados e o player recebe a chave. Damos suporte ao Simple Web Token (SWT) e ao JSON Web Token (JWT), esses dois formatos de token. A autenticação de token pode ser aplicada a qualquer tipo de chave – independentemente de você estar fazendo uma criptografia comum ou criptografia de envelope AES no sistema.

Veja a seguir de que maneiras você pode passar o taken com o seu player, dependendo do player e da sua plataforma de destino:
- Por meio do cabeçalho de autorização HTTP.
> [!NOTE]
> Observe que o prefixo "Bearer" é esperado para as especificações de OAuth 2.0. Há um player de exemplo com a configuração do Token hospedado na [página de demonstração](http://ampdemo.azureedge.net/) do Player de Mídia do Azure. Escolha AES (Token JWT) ou AES (Token SWT) para definir a fonte de vídeo. O token é passado por meio do cabeçalho de autorização.

- Através da adição de um parâmetro de Consulta de Url com “token=tokenvalue”.  
> [!NOTE]
> Observe que nenhum prefixo "Bearer" é esperado. Como o token é enviado através de uma URL, é necessário proteger a cadeia de caracteres do token. Observe aqui um código de exemplo C# sobre como fazer isso:

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

- Através do campo CustomData.
Apenas para a aquisição de licença do PlayReady, através do campo CustomData do Desafio de Aquisição de Licença do PlayReady. Nesse caso, o token deve estar dentro do documento xml descrito abaixo.

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```
Coloque o seu token de autenticação no elemento <Token>.

- Através de uma lista de reprodução HLS alternativa. Se você precisar configurar a autenticação de Token para a reprodução AES + HLS em iOS/Safari, não existe nenhuma forma de você enviar diretamente no token. Consulte esta [postagem no blog](http://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) sobre como alternar a lista de reprodução para habilitar esse cenário.

## <a name="next-steps"></a>Próximas etapas
Examine os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]