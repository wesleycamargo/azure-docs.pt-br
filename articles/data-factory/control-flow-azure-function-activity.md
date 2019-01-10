---
title: Atividade de Função no Azure Data Factory | Microsoft Docs
description: Saiba como usar a atividade de função do Azure para executar uma função do Azure em um pipeline do Data Factory
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: douglasl
ms.openlocfilehash: 4b185236e5925152acb5f8a733e117186a2318cf
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53740885"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Atividade de função do Azure no Azure Data Factory

Saiba como usar a atividade do [Azure Functions](../azure-functions/functions-overview.md) para executar uma função do Azure em um pipeline do Data Factory. Para executar uma função do Azure, você precisará criar uma conexão de serviço vinculado e uma atividade que especifica a função do Azure que você planeja executar.

## <a name="azure-function-linked-service"></a>Serviço de função vinculado do Azure

O tipo de retorno da função do Azure deve ser um JObject válido. Qualquer outra coisa que falhe ou gere um erro de usuário genérico: *erro de chamada de ponto de extremidade*.

| **Propriedade** | **Descrição** | **Obrigatório** |
| --- | --- | --- |
| Tipo   | A propriedade type deve ser definida como: **AzureFunction** | Sim |
| url do aplicativo de função | URL para o Aplicativo de Funções do Azure. O formato é `https://<accountname>.azurewebsites.net`. Essa URL é o valor na seção **URL** ao exibir o Aplicativo de funções no portal do Azure  | Sim |
| teclas de função | Tecla de acesso para o Azure Function. Clique na seção **Gerenciar** seção para a função respectiva e copie-a na **Tecla de Função** ou na **Tecla Host**. Saiba mais aqui: [Gatilhos e associações HTTP do Azure Functions](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | Sim |
|   |   |   |

## <a name="azure-function-activity"></a>Atividade do Azure Function

| **Propriedade**  | **Descrição** | **Valores permitidos** | **Obrigatório** |
| --- | --- | --- | --- |
| Nome  | Nome da atividade no pipeline  | Cadeia de caracteres | Sim |
| Tipo  | O tipo de atividade é 'AzureFunctionActivity' | Cadeia de caracteres | Sim |
| serviço vinculado | O serviço de função do Azure vinculado para o Aplicativo de funções correspondente do Azure  | Referência de serviço vinculado | Sim |
| nome da função  | Nome da função no Aplicativo de funções do Azure que essa atividade chama | Cadeia de caracteres | Sim |
| estático  | Método da API REST para a chamada de função | Tipos com suporte de cadeia de caracteres: "GET", "POST", "PUT"   | Sim |
| cabeçalho  | Cabeçalhos que são enviados para a solicitação. Por exemplo, para definir o idioma e o tipo em uma solicitação: "cabeçalhos": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Não  |
| body  | corpo que é enviado junto com a solicitação para o método de api de função  | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) ou objeto.   | Necessário para os métodos PUT/POST |
|   |   |   | |

Consulte o esquema da carga de solicitação na seção  [Esquema de carga de solicitação](control-flow-web-activity.md#request-payload-schema) .

## <a name="more-info"></a>Mais informações

A atividade do Azure Function dá suporte a **roteamento**. Por exemplo, se seu aplicativo usa o seguinte roteamento - `https://functionAPP.azurewebsites.net/api/functionName/{value}?code=<secret>` - então, o `functionName` é `functionName/{value}`, que é possível parametrizar para fornecer os detalhes desejados `functionName` em tempo de execução.

A atividade do Azure Function também dá suporte a **consultas**.  Uma consulta deve fazer parte do `functionName` - por exemplo, `HttpTriggerCSharp2?name=hello` – em que o `function name` é `HttpTriggerCSharp2`.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as atividades no Data Factory no [Pipelines e atividades no Azure Data Factory](concepts-pipelines-activities.md).