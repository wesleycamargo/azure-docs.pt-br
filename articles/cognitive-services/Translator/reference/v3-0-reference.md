---
title: Referência da API de Tradução de Texto da Microsoft V3.0 | Microsoft Docs
description: Consulte a documentação para a API de Tradução de Texto da Microsoft V3.0.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: cfaa9584e833b137b417d9074fbfcf606eb21388
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363791"
---
#<a name="translator-text-api-v30"></a>API de Tradução de Texto v3.0

## <a name="whats-new"></a>O que há de novo?

A versão 3 da API de Tradução de Texto da Microsoft fornece uma API de Web moderna baseada em JSON. Ela melhora o uso e o desempenho por meio da consolidação dos recursos existentes em menos operações, além de fornecer novos recursos.

 * Transliteração para converter texto em um idioma de um script para outro script.
 * Tradução para vários idiomas em uma solicitação.
 * Detecção de idioma, tradução e transliteração em uma solicitação.
 * Dicionário para pesquisar traduções alternativas de um termo, para localizar traduções reversas e exemplos mostrando os termos usados no contexto.
 * Resultados de detecção de idioma mais informativos.

## <a name="base-urls"></a>URLs base

A API de Texto v3.0 está disponível na nuvem a seguir:

| DESCRIÇÃO | Região | URL base                                        |
|-------------|--------|-------------------------------------------------|
| Azure       | Global | api.cognitive.microsofttranslator.com           |


## <a name="authentication"></a>Autenticação

Inscreva-se à API de Tradução de Texto nos Serviços Cognitivos da Microsoft e use sua chave de assinatura (disponível no Portal do Azure) para autenticação. 

A maneira mais simples é passar sua chave secreta do Azure para o serviço de Tradução usando o cabeçalho da solicitação `Ocp-Apim-Subscription-Key`.

Uma alternativa é usar sua chave secreta para obter um token de autorização do serviço de token. Depois, passe o token de autorização para o serviço de Tradução usando o cabeçalho de solicitação `Authorization`. Para obter um token de autorização, faça uma solicitação `POST` à URL a seguir:

| Ambiente     | URL do serviço de autenticação                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Estas são as solicitações de exemplo para obter um token de acordo com uma chave secreta:

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'
// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Uma solicitação bem-sucedida retorna o token de acesso codificado como texto sem formatação no corpo da resposta. O token válido é passado para o serviço de Tradução como um token de portador na Autorização.

```
Authorization: Bearer <Base64-access_token>
```

Um token de autenticação é válido por 10 minutos. O token deve ser usado novamente ao fazer várias chamadas para as APIs de Tradução. No entanto, se o programa faz solicitações para a API de Tradução por um longo período, seu programa deverá solicitar um novo token de acesso em intervalos regulares (por exemplo, a cada oito minutos).

Para resumir, uma solicitação de cliente para a API de Tradução incluirá um cabeçalho de autorização obtido na tabela a seguir:

<table width="100%">
  <th width="30%">Cabeçalhos</th>
  <th>DESCRIÇÃO</th>
  <tr>
    <td>Ocp-Apim-Subscription-Key</td>
    <td>*Use com a assinatura dos Serviços Cognitivos se você estiver passando a chave secreta*.<br/>O valor é a chave secreta do Azure da sua assinatura para a API de Tradução de Texto.</td>
  </tr>
  <tr>
    <td>Autorização</td>
    <td>*Use com a assinatura dos Serviços Cognitivos se você estiver passando um token de autenticação.*<br/>O valor é o token de portador: "portador <token>".</td>
  </tr>
</table> 

## <a name="errors"></a>Errors

Uma resposta de erro padrão é um objeto JSON com o par de nome/valor chamado `error`. O valor também é um objeto JSON com propriedades:

  * `code`: um código de erro definido pelo servidor.

  * `message`: uma cadeia de caracteres fornecendo uma representação legível do erro.

Por exemplo, um cliente com uma assinatura de avaliação gratuita receberia o seguinte erro após a cota livre esgotar:

```
{
  "error": {
    "code":403000,
    "message":"The subscription has exceeded its free quota."
    }
}
```
