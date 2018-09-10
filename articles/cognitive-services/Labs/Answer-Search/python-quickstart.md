---
title: Início rápido em Python para Serviços Cognitivos da Microsoft, Pesquisa de Resposta de Projeto | Microsoft Docs
description: Exemplo de Python para começar a usar a Pesquisa de Resposta de Projeto, Serviços Cognitivos da Microsoft no Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 9cb5406c616ed8e96d73c00c788a0d20f66dcabd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364201"
---
# <a name="project-answer-search-python-quickstart"></a>Guia de início rápido do Python de Pesquisa de Resposta de Projeto

O exemplo de Python a seguir cria e envia uma solicitação para obter informações sobre a “Pedra de Gibraltar”.

## <a name="prerequisites"></a>pré-requisitos

Obtenha uma chave de acesso para a avaliação gratuita de [Laboratórios dos Serviços Cognitivos](https://aka.ms/answersearchsubscription)

Este exemplo usa Python 3.6.4

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
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'https://api.labs.cognitive.microsoft.com'
path = '/answerSearch/v7.0/search '

query = 'Rock of Gibraltar'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

````
## <a name="next-steps"></a>Próximas etapas
- [Início rápido em C#](c-sharp-quickstart.md)
- [Início rápido em Java](java-quickstart.md)
- [Início rápido em Node](node-quickstart.md)