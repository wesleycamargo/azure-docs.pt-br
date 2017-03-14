---
title: "Chamar, disparar ou aninhar aplicativos lógicos por meio de pontos de extremidade de HTTP – Aplicativo Lógico do Azure | Microsoft Docs"
description: "Adicionar e configurar pontos de extremidade de HTTP para chamar, disparar ou aninhar fluxos de trabalho de aplicativos lógicos no Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 8c4cb0aefcd7241b17c6afcf33496c1fc2e6f675
ms.lasthandoff: 03/03/2017

---

# <a name="add-http-endpoints-to-call-trigger-or-nest-logic-app-workflows"></a>Adicionar pontos de extremidade de HTTP para chamar, disparar ou aninhar fluxos de trabalho de aplicativo lógico

Os aplicativos lógicos podem nativamente expor pontos de extremidade síncronos de HTTP, como gatilhos, para que você possa chamar seus aplicativos lógicos. Você também pode usar um padrão de pontos de extremidade exigíveis para invocar aplicativos lógicos como fluxos de trabalho aninhados, adicionando a ação **Escolher um fluxo de trabalho de aplicativos lógicos** em seu aplicativo lógico.

Você pode usar esses gatilhos para receber solicitações:

* Solicitação
* ApiConnectionWebhook
* HttpWebhook

Este tópico usa o gatilho **Solicitação** como exemplo, mas todos os princípios se aplicam de forma idêntica a outros tipos de gatilhos.

## <a name="add-a-trigger-to-your-logic-app-definition"></a>Adicionar um gatilho à sua definição de aplicativo lógico

1. No Designer de Aplicativo Lógico, adicione um gatilho que pode receber solicitações de entrada para sua definição de aplicativo lógico. Por exemplo, adicione o gatilho **Solicitação** em seu aplicativo lógico.

2.    Em **Esquema JSON do corpo de solicitação**, você pode inserir um esquema JSON para o conteúdo que você espera receber. Se não tiver um esquema pronto, você poderá escolher **Usar o conteúdo de exemplo para gerar esquema** para gerar um esquema JSON de um conteúdo de exemplo.

    O designer usa este esquema para gerar tokens que o ajudam a consumir, analisar e transmitir dados do gatilho manual por meio de seu fluxo de trabalho.

    ![Adicionar a ação Solicitar][2]

2.    Depois de salvar sua definição de aplicativo lógico, em **HTTP POST para esta URL**, você obterá uma URL de retorno de chamada gerada, como neste exemplo:

    ``` text
    https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
    ```

    Essa URL contém uma chave de SAS (Assinatura de Acesso Compartilhado) nos parâmetros de consulta usados para autenticação. 
    Também é possível obter esse ponto de extremidade no Portal do Azure:

    ![Ponto de extremidade de URL][1]

    Ou chamando:

    ``` text
    POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
    ```

### <a name="change-the-http-method-for-your-trigger"></a>Alterar o método HTTP para o gatilho

Por padrão, o gatilho Solicitação aguarda uma solicitação HTTP POST. Para configurar um método de HTTP diferente, escolha **Mostrar opções avançadas**.

> [!NOTE]
> Somente um tipo de método é permitido.

### <a name="customize-the-relative-trigger-url"></a>Personalizar a URL de gatilho relativa

Você também pode personalizar o caminho relativo da URL de solicitação para aceitar os parâmetros.

1. No gatilho **Solicitação**, escolha **Mostrar opções avançadas**. Em **Caminho relativo**, digite `customer/{customerId}`.

    ![Gatilho de URL relativa](./media/logic-apps-http-endpoint/relativeurl.png)

2.    Para usar o parâmetro, atualize a ação **Resposta**.

    *    Você deverá ver `customerId` aparecer no seletor de token.
    *    Para retornar `Hello {customerId}`, atualize o corpo da ação **Resposta**.

    ![Resposta à URL relativa](./media/logic-apps-http-endpoint/relativeurlresponse.png)

3. Salve seu aplicativo lógico. A URL da solicitação é atualizada com o caminho relativo.

4. Copie e cole a nova URL de solicitação em uma nova janela do navegador. Substitua `{customerId}` por `123` e pressione Enter.

    Esse texto deve ser retornado: `Your customer Id is 123`

### <a name="security-for-the-trigger-url"></a>Segurança para a URL de gatilho

As URLs de retorno do aplicativo lógico são geradas com segurança usando uma SAS (Assinatura de Acesso Compartilhado). A assinatura é transmitida como um parâmetro de consulta e deve ser validada antes do aplicativo lógico ser acionado. A assinatura é gerada por meio de uma combinação exclusiva de uma chave secreta por aplicativo lógico, o nome do gatilho e a operação que está sendo executada. A menos que alguém tenha acesso à chave secreta do aplicativo lógico, não é possível gerar uma assinatura válida.

## <a name="call-your-logic-app-triggers-endpoint"></a>Chamar o ponto de extremidade do gatilho do aplicativo lógico

Depois de criar o ponto de extremidade para o gatilho, você poderá disparar seu aplicativo lógico por meio de um `POST` para a URL completa. Você pode incluir mais cabeçalhos e qualquer conteúdo no corpo. Se o tipo do conteúdo for `application/json`, você poderá fazer referência às propriedades de dentro da solicitação. Caso contrário, o conteúdo será tratado como uma única unidade binária que pode ser transmitida para outras APIs, mas não podem ser referenciadas dentro do fluxo de trabalho, a menos que o conteúdo seja convertido. Por exemplo, se transmitir o conteúdo de `application/xml`, você poderá usar `@xpath()` para uma extração de XPath ou `@json()` para converter de XML para JSON. Saiba mais sobre [como trabalhar com tipos de conteúdo](../logic-apps/logic-apps-content-type.md).

Além disso, é possível especificar um esquema JSON na definição. Este esquema faz com que o designer gere tokens que poderão, mais tarde, ser transmitidos em etapas. O exemplo a seguir disponibilizará um token `title` e `name` no designer:

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

## <a name="reference-the-content-from-the-incoming-request"></a>Fazer referência ao conteúdo da solicitação de entrada

A função `@triggerOutputs()` produz o conteúdo da solicitação de entrada. A saída se parece com esta:

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

Para acessar a propriedade `body` de forma específica, você pode usar o atalho `@triggerBody()`. 

## <a name="respond-to-the-request"></a>Responder à solicitação

Para algumas solicitações que iniciam um aplicativo lógico, convém responder com algum conteúdo ao chamador. Para criar o código de status, o corpo e os cabeçalhos para sua resposta, você pode usar um tipo de ação chamado **Resposta**. No entanto, se nenhuma **Resposta** for incluída, o ponto de extremidade do aplicativo lógico responderá *imediatamente* com **202 Aceito**.

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

As respostas têm estas propriedades:

| Propriedade | Descrição |
| --- | --- |
| statusCode |O código de status HTTP para responder à solicitação de entrada. Este código pode ser qualquer código de status válido que comece com 2xx, 4xx ou 5xx. No entanto, não há permissão para códigos de status 3xx. |
| body |Um objeto body pode ser uma cadeia de caracteres, um objeto JSON ou até mesmo um conteúdo binário referenciado em uma etapa anterior. |
| headers |Você pode definir qualquer quantidade de cabeçalhos para incluir na resposta. |

Em seu aplicativo lógico, todas as etapas necessárias para a resposta deverão ser concluídas em *60 segundos* para a solicitação original receber a resposta, *a menos que você chame o fluxo de trabalho como um aplicativo lógico aninhado*. Se nenhuma resposta for recebida dentro de 60 segundos, a solicitação de entrada atingirá o tempo limite e receberá uma resposta de HTTP **408 Tempo Limite de Cliente**. Para aplicativos lógicos aninhados, o aplicativo lógico pai continuará a aguardar uma resposta até a conclusão, independentemente de quanto tempo for necessário.

## <a name="advanced-endpoint-configuration"></a>Configuração avançada do ponto de extremidade

Os Aplicativos Lógicos têm suporte interno para o ponto de extremidade de acesso direto e sempre usam o método `POST` para iniciar uma execução do aplicativo lógico. Anteriormente, o aplicativo de API **Ouvinte HTTP** também dava suporte à alteração de segmentos de URL e do método HTTP. Você pode até mesmo configurar a segurança adicional ou um domínio personalizado adicionando estes itens no host de aplicativo de API (o aplicativo Web que hospedava o aplicativo de API). 

Essa funcionalidade está disponível por meio do **Gerenciamento de API**:

* [Alterar o método de solicitação](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [Alterar os segmentos de URL da solicitação](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* Configurar seus domínios de Gerenciamento de API na guia **Configurar** no Portal Clássico do Azure.
* Configurar a política para verificar a autenticação Básica.

## <a name="summary-of-migration-from-2014-12-01-preview"></a>Resumo da migração da versão 2014-12-01-preview

| 2014-12-01-preview | 01-06-2016 |
| --- | --- |
| Clique no aplicativo de API **Ouvinte HTTP** |Clique em **Gatilho manual** (nenhum aplicativo de API é necessário) |
| Configuração “*Envia a resposta automaticamente*” do Ouvinte HTTP |Incluir ou não uma ação de **Resposta** na definição do fluxo de trabalho. |
| Configurar a autenticação básica ou OAuth |por meio do Gerenciamento de API |
| Configurar o método HTTP |por meio do Gerenciamento de API |
| Configurar o caminho relativo |por meio do Gerenciamento de API |
| Fazer referência ao corpo de entrada por meio de `@triggerOutputs().body.Content` |Fazer referência por meio de `@triggerOutputs().body` |
| **Enviar resposta HTTP** no Ouvinte HTTP |Clique em **Responder à solicitação HTTP** (nenhum aplicativo de API é necessário) |

[1]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[2]: ./media/logic-apps-http-endpoint/manualtrigger.png
[3]: ./media/logic-apps-http-endpoint/response.png

