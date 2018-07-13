---
title: Início rápido do Python para Visualização de URL de Projeto - Serviços Cognitivos da Microsoft | Microsoft Docs
description: Exemplo de script para começar a usar rapidamente a Visualização de URL de Projeto dos Serviços Cognitivos da Microsoft no Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 78b2d83b02aa9ea32509029c7456e04e420b8572
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364229"
---
# <a name="url-preview-python-quickstart"></a>Início rápido do Python de Visualização de URL

O exemplo de Python a seguir cria uma visualização de Url para o site SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>pré-requisitos

Obtenha uma chave de acesso para a avaliação gratuita de [Laboratórios dos Serviços Cognitivos](https://aka.ms/answersearchsubscription)

Este exemplo usa Python 3.6.

## <a name="code-scenario"></a>Cenário do código 

O código a seguir cria uma visualização de URL.
Ele é implementado nas etapas a seguir:
1. Declare variáveis para especificar o ponto de extremidade por host e caminho.
2. Especifique a URL de consulta para visualizar e adicione o parâmetro de consulta.  
3. Defina o parâmetro de consulta.
4. Defina a função Search que cria a solicitação e adicione o cabeçalho *Ocp-Apim-Subscription-Key*.
5. Adicione o cabeçalho *Ocp-Apim-Subscription-Key*. 
6. Faça a conexão e envie a solicitação.
7. Imprima os resultados JSON.

O código completo para esta demonstração é:

````
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'your-subscription-key'

host = 'api.labs.cognitive.microsoft.com'
path = '/urlpreview/v7.0/search'

query = 'https://SwiftKey.com'

params = '?q=' + urllib.parse.quote (query)

def get_preview ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_preview ()
print (json.dumps(json.loads(result), indent=4))
````
## <a name="next-steps"></a>Próximas etapas
- [Início rápido em C#](csharp.md)
- [Início rápido em Java](java-quickstart.md)
- [Início rápido em JavaScript](javascript.md)
- [Início rápido de URL em Node](node-quickstart.md)