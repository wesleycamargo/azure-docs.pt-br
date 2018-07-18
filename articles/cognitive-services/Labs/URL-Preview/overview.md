---
title: O que é a visualização de URL do projeto? - Serviços Cognitivos da Microsoft | Microsoft Docs
description: Introdução à Visualização de URL de Projeto.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 6b486e0ab4092bef4fe829a5f166311a572a2900
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364220"
---
# <a name="what-is-project-url-preview"></a>O que é a visualização de URL do projeto?
O ponto de extremidade da Visualização de URL usa um parâmetro de consulta de URL e retorna uma resposta JSON com o nome do recurso de destino, uma breve descrição e um link para uma imagem para exibir uma visualização. A resposta também inclui o sinalizador [isFamilyFriendly](url-preview-reference.md#query-parameters) que indica se a URL contém conteúdo adulto, pirateado ou outro conteúdo ilegal. 

Para obter resultados de visualização de URL, envie uma solicitação GET e inclua o cabeçalho *Ocp-Apim-Subscription-Key* com um token válido:  
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```
A resposta: 
````
HTTP Headers:
BingAPIs-TraceId: 3CC74C94769440C0851D9DF0869FCE7F
BingAPIs-SessionId: 52219085A6364692958C9C83983A0DBA
X-MSEdge-ClientID: 13D44DC2DE946B4B0F25460CDF036AD6
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 3CC74C94769440C0851D9DF0869FCE7F Ref B: CO1EDGE0315 Ref C: 2018-04-11T18:47:40Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https:\/\/swiftkey.com\/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https:\/\/swiftkey.com\/images\/og\/default.jpg"
  }
}

````
## <a name="scenarios"></a>Cenários 

A API de Visualização de URL suporta descrições resumidas dos recursos da Web. Os desenvolvedores a utilizam para criar experiências de visualização avançadas.  Os usuários podem compartilhar ou marcar páginas da Web, notícias, blogs, fóruns, etc. Essa API também pode ser usada para a moderação de conteúdo.    

Os aplicativos usam a Visualização de URL para enviar solicitações da Web para o ponto de extremidade com uma consulta atribuída à URL a ser visualizada.  A resposta JSON contém as informações de visualização: nome, descrição do recurso, sinalizador *familyFriendly* e links que fornecem acesso a uma imagem representativa e a todo o recurso online. 

## <a name="terms-of-use"></a>Termos de uso
Use somente os dados de Visualização de URL de Projeto para visualizar trechos de código e imagens em miniatura com hiperlink para os sites de origem, no compartilhamento de URL iniciada pelo usuário final em mídias sociais, bot de bate-papo ou ofertas semelhantes. Não copie, armazene ou armazene em cache os dados recebidos da Visualização de URL de Projeto. Cumpra todas as solicitações para desabilitar visualizações que você pode receber dos proprietários do site ou de conteúdo.

Você ou terceiros em seu nome, não podem usar, manter, armazenar, armazenar em cache, compartilhar, ou distribuir quaisquer dados da API de Visualização de URL para teste, desenvolvimento, treinamento, distribuir ou disponibilização de quaisquer serviços ou recursos que não sejam da Microsoft. 

## <a name="throttling-requests"></a>Solicitações de limitação

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Próximas etapas
- [Início rápido em C#](csharp.md)
- [Início rápido em Java](java-quickstart.md)
- [Início rápido em JavaScript](javascript.md)
- [Início rápido em Node](node-quickstart.md)
- [Início rápido em Python](python-quickstart.md)
