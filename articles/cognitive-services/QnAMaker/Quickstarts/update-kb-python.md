---
title: Atualizar a base de dados de conhecimento – Início Rápido do Python – Serviços Cognitivos do Azure | Microsoft Docs
description: Como atualizar uma base de dados de conhecimento em Python para o QnA Maker.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: quickstart
ms.date: 06/18/2018
ms.author: nolachar
ms.openlocfilehash: 79b2af150558aa5da8e060e1b5117b91ffd34387
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "43768520"
---
# <a name="update-a-knowledge-base-in-python"></a>Como atualizar uma base de dados de conhecimento em Python

O código a seguir atualiza uma base de dados de conhecimento existente usando o método [Atualizar](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600).

Se você não tiver uma base de dados de conhecimento, pode criar uma de amostra para usar para este início rápido: [Criar uma nova base de dados de conhecimento](create-new-kb-python.md).

1. Crie um novo projeto Python no seu IDE favorito.
1. Adicione o código fornecido abaixo.
1. Substitua o valor `subscriptionKey` por uma chave de assinatura válida.
1. Substitua o valor `kb` por uma ID de base de dados de conhecimento válida. Encontre esse valor acessando uma das suas [bases de dados de conhecimento do QnA Maker](https://www.qnamaker.ai/Home/MyServices). Selecione a base de dados de conhecimento que você deseja atualizar. Uma vez nessa página, localize o 'kdid=' na URL, conforme mostrado abaixo. Use seu valor para o seu exemplo de código.

    ![ID da base de dados de conhecimento do QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

1. Execute o programa.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ADD KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ADD ID HERE';

# Represents the various elements used to create HTTP request path
# for QnA Maker operations.
host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

'''
Formats and indents JSON for display.
:param content: The JSON to format and indent.
:type: string
:return: Formatted and indented JSON.
:rtype: string
'''
def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

'''
Sends a PATCH HTTP request.
:param path: The URL path of your request.
:param content: The contents of your PATCH request.
:type: string
:return: URL with status of PATCH request in updating the kb, actual response.
:rtype: string, string
'''
def update_kb(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("PATCH", path, content, headers)
    response = conn.getresponse ()
    return response.getheader('Location'), response.read ()

'''
Gets the status of the specified QnA Maker operation.
:param path: The URL of the request.
:type: string
:return: Header from retrying of the request (if retry is needed), response of the retry.
:rtype: string, string
'''
def check_status (path):
    print('Calling ' + host + path + '.')
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, None, headers)
    response = conn.getresponse ()
    # If the operation is not finished, /operations returns an HTTP header named
    # 'Retry-After' with the number of seconds to wait before querying the operation again.
    return response.getheader('Retry-After'), response.read ()

'''
Dictionary that holds the knowledge base.
Modifications to the knowledge base are made here, using 'update', 'delete' and so on.
'''
req = {
    'add': {
        'qnaList': [
            {
              'id': 1,
              'answer': 'You can change the default message if you use the QnAMakerDialog. '
                      + 'See this for details:  https://docs.botframework.com/en-us'
                      + '/azure-bot-service/templates/qnamaker/#navtitle',
              'source': 'Custom Editorial',
              'questions': [
                  'How can I change the default message from QnA Maker?'
              ],
              'metadata': []
            }
        ],
        'urls': []
    },
    'update' : {
        'name' : 'New KB Name'
    },
    'delete': {
        'ids': [
            0
        ]
    }
}

# Builds the path URL.
path = service + method + kb
# Convert the request to a string.
content = json.dumps(req)
# Retrieve the operation ID to check status, and JSON result.
operation, result = update_kb(path, content)
# Print request response in JSON with presentable formatting.
print(pretty_print(result))

'''
Iteratively gets the operation state, updating the knowledge base.
Once state is no longer "Running" or "NotStarted", the loop ends.
'''
done = False
while False == done:
    path = service + operation
    # Gets the status of the operation.
    wait, status = check_status(path)
    # Print status checks in JSON with presentable formatting
    print(pretty_print(status))

    # Convert the JSON response into an object and get the value of the operationState field.
    state = json.loads(status)['operationState']
    # If the operation isn't finished, wait and query again.
    if state == 'Running' or state == 'NotStarted':
        print('Waiting ' + wait + ' seconds...')
        time.sleep (int(wait))
    else:
        done = True # request has been processed, if successful, knowledge base is updated
```

## <a name="understand-what-qna-maker-returns"></a>Entender o que o QnA Maker retorna

Uma resposta bem-sucedida é retornada em JSON, conforme mostrado no seguinte exemplo. Seus resultados podem ser um pouco diferentes. Se a chamada final retornar um estado de "Bem-sucedido", isso indica que sua base de dados de conhecimento foi atualizada com sucesso. Para solucionar problemas, veja os códigos de resposta de [Atualizar base de conhecimentos](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) da API de QnA Maker.

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:48Z",
  "userId": "2280ef5917rt4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:50Z",
  "resourceLocation": "/knowledgebases/140a46f3-b248-4f1b-9349-614bfd6e5563",
  "userId": "2280ef5917rt4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
Press any key to continue.
```

Depois que sua base de dados de conhecimento tiver sido atualizada, você poderá exibi-la em seu Portal do QnA Maker, página [Minhas bases de dados de conhecimento](https://www.qnamaker.ai/Home/MyServices). Observe que um novo par do QnA foi adicionado e o nome do banco de dados agora é 'Novo nome de KB'.

Para modificar outros elementos de sua base de dados de conhecimento, consulte o [Esquema JSON](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) do QnA Maker e modifique a cadeia de caracteres `req`.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
