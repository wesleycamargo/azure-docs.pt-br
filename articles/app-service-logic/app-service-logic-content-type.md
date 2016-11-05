---
title: Tratamento de tipos de conteúdo de Aplicativos Lógicos | Microsoft Docs
description: Entender como os Aplicativos Lógicos lidam com tipos de conteúdo em tempo de execução e de design
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: dwrede
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/03/2016
ms.author: jehollan

---
# Tratamento de tipos de conteúdo de Aplicativos Lógicos
Há muitos tipos diferentes de conteúdo podem fluir por um Aplicativo Lógico - incluindo JSON, XML, arquivos simples e dados binários. Embora haja suporte para todos os tipos de conteúdo, alguns são nativamente compreendidos pelo Mecanismo de Aplicativos Lógicos, e outros podem exigir conversões, como necessário. O artigo a seguir descreverá como o mecanismo lida com diferentes tipos de conteúdo e como eles podem ser tratados corretamente como necessário.

## Cabeçalho Content-Type
Para começarmos de forma simples, vamos examinar os dois `Content-Types` que não exigem conversão a ser usada em um Aplicativo Lógico - `application/json` e `text/plain`.

### Application/json
O mecanismo de fluxo de trabalho depende do cabeçalho `Content-Type` de chamadas HTTP para determinar o tratamento apropriado. Qualquer solicitação com o tipo de conteúdo `application/json` será armazenada e tratada como um Objeto JSON. Além disso, o conteúdo JSON pode ser analisado por padrão sem a necessidade de conversão. Então, uma solicitação com o cabeçalho content-type `application/json ` como esta:

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

pode ser analisada em um fluxo de trabalho com uma expressão como `@body('myAction')['foo'][0]` para obter um valor (neste caso, `bar`). Nenhuma conversão adicional é necessária. Se você estiver trabalhando com dados JSON, mas se não tiver um cabeçalho especificado, poderá convertê-lo manualmente para JSON usando a função `@json()` (por exemplo: `@json(triggerBody())['foo']`).

### Text/plain
Semelhante a `application/json`, as mensagens HTTP recebidas com o cabeçalho `Content-Type` de `text/plain` serão armazenadas em sua forma bruta. Além disso, se incluída em ações subsequentes sem qualquer conversão, a solicitação sairá com um cabeçalho `Content-Type`: `text/plain`. Por exemplo, se você estiver trabalhando com um arquivo simples, receberá o seguinte conteúdo HTTP:

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

como `text/plain`. Se, na próxima ação, você a enviar como o corpo de outra solicitação (`@body('flatfile')`), a solicitação teria um cabeçalho Content-Type `text/plain`. Se você estiver trabalhando com dados de texto sem formatação, mas se não tiver um cabeçalho especificado, poderá convertê-lo manualmente para texto sem formatação usando a função `@string()` (por exemplo: `@string(triggerBody())`)

### Application/xml e Application/octet-stream e Funções de Conversor
O Mecanismo de Aplicativo Lógico sempre manterá o `Content-Type` recebido na solicitação ou na resposta HTTP. Isso significa que, se um conteúdo for recebido com `Content-Type` de `application/octet-stream`, incluindo-os em uma ação subsequente sem nenhuma conversão, isso resultará em uma solicitação de saída com `Content-Type`: `application/octet-stream`. Dessa forma, o mecanismo pode garantir que os dados não se perderão conforme se movem pelo fluxo de trabalho. No entanto, o estado da ação (entradas e saídas) é armazenado em objetos JSON, conforme eles percorrem o fluxo de trabalho. Isso significa que, para preservar alguns tipos de dados, o mecanismo converterá o conteúdo em uma cadeia de caracteres binária codificada em base64 com os metadados apropriados que preservam `$content` e `$content-type` - que serão convertidos automaticamente. Você também pode converter entre tipos de conteúdo manualmente usando funções do conversor internas:

* `@json()` - converte dados em `application/json`
* `@xml()` - converte dados em `application/xml`
* `@binary()` - converte dados em `application/octet-stream`
* `@string()` - converte dados em `text/plain`
* `@base64()` - converte conteúdo em uma cadeia de caracteres de base64
* `@base64toString()` - converte uma cadeia de caracteres codificada em base64 em `text/plain`
* `@base64toBinary()` - converte uma cadeia de caracteres codificada em base64 em `application/octet-stream`
* `@encodeDataUri()` - codifica uma cadeia de caracteres como uma matriz de bytes dataUri
* `@decodeDataUri()` - decodifica uma dataUri em uma matriz de bytes

Por exemplo, se você tiver recebido uma solicitação HTTP com `Content-Type`: `application/xml` de:

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Eu poderia converter e usar mais tarde com algo como `@xml(triggerBody())`, ou em uma função como `@xpath(xml(triggerBody()), '/CustomerName')`.

### Outros tipos de conteúdo
Outros tipos de conteúdo têm suporte e funcionarão com um Aplicativo Lógico, mas poderão exigir a recuperação manual do corpo da mensagem por meio da decodificação do `$content`. Por exemplo, se eu estiver disparando de uma solicitação `application/x-www-url-formencoded` parecida com a seguinte:

```
CustomerName=Frank&Address=123+Avenue
```

já que não é um texto sem formatação ou um JSON, estará armazenado na ação como:

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

Onde `$content` é o conteúdo codificado como uma cadeia de caracteres em base64 para preservar todos os dados. Uma vez que atualmente não existe uma função nativa para dados de formulário, eu ainda poderia usar esses dados em um fluxo de trabalho ao acessar manualmente os dados com uma função como `@string(body('formdataAction'))`. Se eu quisesse que a minha solicitação de saída também tivesse o cabeçalho content-type `application/x-www-url-formencoded`, poderia simplesmente adicioná-lo ao corpo da ação sem qualquer conversão como `@body('formdataAction')`. No entanto, isso só funcionará se o corpo for o único parâmetro na entrada `body`. Se você tentar fazer `@body('formdataAction')` em uma solicitação `application/json`, obterá um erro de tempo de execução, pois o corpo codificado será enviado.

<!---HONumber=AcomDC_0803_2016-->