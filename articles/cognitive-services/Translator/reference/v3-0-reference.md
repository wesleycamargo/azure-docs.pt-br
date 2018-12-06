---
title: Referência da API de texto do tradutor V3.0
titlesuffix: Azure Cognitive Services
description: Documentação de referência para a API de texto do tradutor V3.0.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 6f679536d69f700fd6678eb3bbbb869e42439cde
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853346"
---
# <a name="translator-text-api-v30"></a>API de Tradução de Texto v3.0

## <a name="whats-new"></a>O que há de novo?

A versão 3 do API de tradução de texto fornece uma API de Web modernos baseados em JSON. Ela melhora o uso e o desempenho por meio da consolidação dos recursos existentes em menos operações, além de fornecer novos recursos.

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
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
O código de erro é um número de 6 dígitos que combina o código de status HTTP de 3 dígitos seguido por um número de 3 dígitos para categorizar ainda mais o erro. Códigos de erro comuns são:

| Código | DESCRIÇÃO |
|:----|:-----|
| 400000| Uma das entradas de solicitação não é válida.|
| 400001| O parâmetro "scope" é inválido.|
| 400002| O parâmetro "category" é inválido.|
| 400003| Um especificador de linguagem está ausente ou inválido.|
| 400004| Um especificador de script de destino ("To script") está ausente ou é inválido.|
| 400005| Um texto de entrada está faltando ou é inválido.|
| 400006| A combinação de idioma e script não é válida.|
| 400018| Um especificador de script de origem ("From script") está ausente ou é inválido.|
| 400019| Um dos idiomas especificados não é suportado.|
| 400020| Um dos elementos na matriz de texto de entrada não é válido.|
| 400021| O parâmetro da versão da API está ausente ou é inválido.|
| 400023| Um dos pares de idiomas especificados não é válido.|
| 400035| O idioma de origem (campo "De") não é válido.|
| 400036| O idioma de destino (campo "Para") está ausente ou é inválido.|
| 400042| Uma das opções especificadas (campo "Opções") não é válida.|
| 400043| O ID de rastreio do cliente (campo ClientTraceId ou cabeçalho X-ClientTranceId) está ausente ou é inválido.|
| 400050| O texto de entrada é muito longo.|
| 400064| O parâmetro "translation" está ausente ou é inválido.|
| 400070| O número de scripts de destino (parâmetro ToScript) não corresponde ao número de idiomas de destino (para o parâmetro To).|
| 400071| O valor não é válido para o TextType.|
| 400072| A matriz de texto de entrada possui muitos elementos.|
| 400073| O parâmetro de script não é válido.|
| 400074| O corpo da solicitação não é válido como JSON.|
| 400075| A combinação de pares de idiomas e categorias não é válida.|
| 400077| O tamanho máximo da solicitação foi excedido.|
| 400079| O sistema personalizado solicitado para tradução entre e para o idioma não existe.|
| 401000| A solicitação não está autorizada porque as credenciais estão ausentes ou são inválidas.|
| 401015| "As credenciais fornecidas são para a Speech API. Esta solicitação requer credenciais para a API de texto. Por favor, use uma assinatura para o Translator Text API ".|
| 403000| A operação não é permitida.|
| 403001| A operação não é permitida porque a assinatura excedeu sua cota gratuita.|
| 405000| O método de solicitação não é suportado para o recurso solicitado.|
| 408001| O sistema de conversão personalizada solicitado ainda não está disponível. Tente novamente em alguns minutos.|
| 415000| O cabeçalho Content-Type está ausente ou é inválido.|
| 429000, 429001, 429002| O servidor rejeitou a solicitação porque o cliente está enviando muitas solicitações. Reduza a frequência de solicitações para evitar o afogamento.|
| 500000| Erro inesperado. Se o erro persistir, informe-o com data / hora do erro, solicite o identificador do cabeçalho de resposta X-RequestId e o identificador de cliente do cabeçalho de solicitação X-ClientTraceId.|
| 503000| O serviço está temporariamente indisponível. Tente novamente. Se o erro persistir, informe-o com data / hora do erro, solicite o identificador do cabeçalho de resposta X-RequestId e o identificador de cliente do cabeçalho de solicitação X-ClientTraceId.|

