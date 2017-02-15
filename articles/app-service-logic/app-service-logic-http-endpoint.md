---
title: "Aplicativos lógicos como pontos de extremidade escaláveis"
description: "Como criar e configurar pontos de extremidade de gatilho e usá-los em um Aplicativo Lógico no Serviço de Aplicativo do Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: 
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 372114b1420139bdf525521a427f924aae38db6f


---
# <a name="logic-apps-as-callable-endpoints"></a>Aplicativos lógicos como pontos de extremidade escaláveis
Aplicativos lógicos podem expor de forma nativa um ponto de extremidade HTTP síncrono como um gatilho.  Também é possível usar o padrão de pontos de extremidade que podem ser chamados para invocar os Aplicativos Lógicos como um fluxo de trabalho aninhado por meio da ação “fluxo de trabalho” em um Aplicativo Lógico.

Existem três tipos de gatilhos que podem receber solicitações:

* Solicitação
* ApiConnectionWebhook
* HttpWebhook

Para o restante do artigo, usaremos **request** como exemplo, mas todos os princípios são aplicados de forma idêntica aos outros dois tipos de gatilhos.

## <a name="adding-a-trigger-to-your-definition"></a>Adição de um gatilho para sua definição
A primeira etapa é adicionar um gatilho à sua definição de Aplicativo lógico que pode receber solicitações de entrada.  É possível pesquisar “Solicitação HTTP” no designer para adicionar o cartão de gatilho. É possível definir um Esquema JSON de corpo de solicitação e o designer gerará tokens para ajudá-lo a analisar e transmitir dados do gatilho manual por meio do fluxo de trabalho.  É recomendável usar uma ferramenta como [jsonschema.net](http://jsonschema.net) para gerar um esquema JSON por meio de um conteúdo de corpo de exemplo.

![Cartão do Gatilho de Solicitação][2]

Depois de salvar a definição de Aplicativo Lógico, uma URL de retorno de chamada semelhante a esta será gerada:

``` text
https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
```

Essa URL contém uma chave SAS nos parâmetros de consulta usados para autenticação.

Também é possível obter esse ponto de extremidade no portal do Azure:

![][1]

Ou chamando:

``` text
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

### <a name="security-for-the-trigger-url"></a>Segurança para a URL de gatilho
A URL de retorno de chamada do aplicativo de lógica é gerada com segurança usando uma Assinatura de Acesso Compartilhado.  A assinatura é passada como um parâmetro de consulta e deve ser validada antes do aplicativo lógico ser acionado.  Ele é gerado por meio de uma combinação exclusiva de uma chave secreta por aplicativo lógico, o nome do gatilho e a operação que está sendo executada.  A menos que alguém tenha acesso à chave secreta do aplicativo lógico, não seria possível gerar uma assinatura válida.

## <a name="calling-the-logic-app-triggers-endpoint"></a>Chamar o ponto de extremidade do gatilho do Aplicativo lógico
Depois de criar o ponto de extremidade de seu gatilho, você pode dispará-lo por meio de um `POST` para a URL completa. Você pode incluir cabeçalhos adicionais e qualquer conteúdo no corpo.

Se content-type for `application/json` , você poderá fazer referência a propriedades na solicitação. Caso contrário, ele será tratado como uma única unidade binária que pode ser transmitida para outras APIs, mas que não pode ser referenciada dentro do fluxo de trabalho sem a conversão do conteúdo.  Por exemplo, se você transmitir o conteúdo de `application/xml`, poderá usar `@xpath()` para fazer uma extração de xpath ou `@json()` para converter de XML para JSON.  Mais informações sobre como trabalhar com tipos de conteúdo [podem ser encontradas aqui](app-service-logic-content-type.md)

Além disso, é possível especificar um esquema JSON na definição. Isso faz com que o designer gere tokens que poderão, mais tarde, ser transmitidos em etapas.  Por exemplo, o conteúdo a seguir disponibilizará um token `title` e `name` no designer:

```
{
    "properties":{
        "title": {
            "type": "string"
        },
        "name": {
            "type": "string"
        }
    },
    "required": [
        "title",
        "name"
    ],
    "type": "object"
}
```

## <a name="referencing-the-content-of-the-incoming-request"></a>Como fazer referência ao conteúdo da solicitação de entrada
A função `@triggerOutputs()` produzirá o conteúdo da solicitação de entrada. Por exemplo, ele teria a seguinte aparência:

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

É possível usar o atalho `@triggerBody()` para acessar a propriedade `body` de forma específica. 

## <a name="responding-to-the-request"></a>Como responder à solicitação
Para algumas solicitações que iniciam um Aplicativo lógico, convém responder com algum conteúdo ao chamador. Há um novo tipo de ação chamado **response** que pode ser usado para construir o código de status, o corpo e os cabeçalhos da resposta. Observe que, se nenhuma forma **response** estiver presente, o ponto de extremidade do Aplicativo lógico responderá *imediatamente* com **202 Aceito**.

![Ação de Resposta HTTP][3]

``` json
"Response": {
            "conditions": [],
            "inputs": {
                "body": {
                    "name": "@{triggerBody()['name']}",
                    "title": "@{triggerBody()['title']}"
                },
                "headers": {
                    "content-type": "application/json"
                },
                "statusCode": 200
            },
            "type": "Response"
        }
```

As respostas contêm:

| Propriedade | Descrição |
| --- | --- |
| statusCode |O código de status HTTP para responder à solicitação de entrada. Pode ser qualquer código de status válido que comece com 2xx, 4xx ou 5xx. Não há permissão para códigos de status 3xx. |
| body |Um objeto body pode ser uma cadeia de caracteres, um objeto JSON ou até mesmo um conteúdo binário referenciado em uma etapa anterior. |
| headers |Você pode definir qualquer quantidade de cabeçalhos para inclusão na resposta |

Todas as etapas no Aplicativo Lógico necessárias para a resposta deverão ser concluídas em até *60 segundos* para que a solicitação original receba a resposta, **a menos que o fluxo de trabalho esteja sendo chamado como um Aplicativo Lógico aninhado**. Se nenhuma ação de resposta for atingida em até 60 segundos, a solicitação de entrada atingirá o tempo limite e receberá uma resposta HTTP **408 Tempo limite do cliente** .  Para Aplicativos Lógicos aninhados, o Aplicativo Lógico pai continuará esperando uma resposta até a conclusão, independentemente da quantidade de tempo necessário.

## <a name="advanced-endpoint-configuration"></a>Configuração avançada do ponto de extremidade
Os Aplicativos Lógicos têm suporte interno para o ponto de extremidade de acesso direto e sempre usam o método `POST` para iniciar uma execução do Aplicativo Lógico. O aplicativo de API **Ouvinte HTTP** também dava suporte à alteração de segmentos de URL e do método HTTP. Você podia até mesmo configurar a segurança adicional ou um domínio personalizado por meio da adição ao host de aplicativo da API (o aplicativo Web que hospedava o aplicativo da API). 

Essa funcionalidade está disponível por meio do **Gerenciamento de API**:

* [Alterar o método de solicitação](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [Alterar os segmentos de URL da solicitação](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* Configurar seus domínios de gerenciamento de API na guia **Configurar** no portal clássico do Azure
* Configurar a política para verificar a autenticação Básica (**link necessário**)

## <a name="summary-of-migration-from-2014-12-01-preview"></a>Resumo da migração da versão 2014-12-01-preview
| 2014-12-01-preview | 01-06-2016 |
| --- | --- |
| Clicar no aplicativo de API **Ouvinte HTTP** |Clicar em **Gatilho manual** (nenhum aplicativo de API é necessário) |
| Configuração “*Envia a resposta automaticamente*” do Ouvinte HTTP |Inclui ou não uma ação **response** na definição do fluxo de trabalho |
| Configurar a autenticação básica ou OAuth |por meio do gerenciamento de API |
| Configurar o método HTTP |por meio do gerenciamento de API |
| Configurar o caminho relativo |por meio do gerenciamento de API |
| Fazer referência ao corpo de entrada por meio de `@triggerOutputs().body.Content` |Fazer referência por meio de `@triggerOutputs().body` |
| **Enviar resposta HTTP** no Ouvinte HTTP |Clicar em **Responder à solicitação HTTP** (nenhum aplicativo de API é necessário) |

[1]: ./media/app-service-logic-http-endpoint/manualtriggerurl.png
[2]: ./media/app-service-logic-http-endpoint/manualtrigger.png
[3]: ./media/app-service-logic-http-endpoint/response.png



<!--HONumber=Nov16_HO3-->


