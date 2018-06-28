---
title: Chamar ponto de extremidade usando Python - Pesquisa Personalizada do Bing - Serviços Cognitivos da Microsoft
description: Este início rápido mostra como solicitar resultados de pesquisa da instância de pesquisa personalizada usando Python para chamar o ponto de extremidade da Pesquisa Personalizada do Bing.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 889762ae0b401438f25546738268c584ddd58389
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "35364802"
---
# <a name="call-bing-custom-search-endpoint-python"></a>Chamar ponto de extremidade da Pesquisa Personalizada do Bing (Python)

Este início rápido mostra como solicitar resultados de pesquisa da instância de pesquisa personalizada usando Python para chamar o ponto de extremidade da Pesquisa Personalizada do Bing. 

## <a name="prerequisites"></a>pré-requisitos
Para concluir este início rápido, você precisa de:

- Uma instância de pesquisa personalizada. Consulte [Criar a primeira instância da Pesquisa Personalizada do Bing](quick-start.md).

-  [Python](https://www.python.org/) instalado.

- Uma [Conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com **APIs de Pesquisa do Bing**. A [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) é suficiente para esse início rápido. É necessário ter a chave de acesso fornecida ao ativar a avaliação gratuita ou você poderá usar uma chave de assinatura paga no painel do Azure. 

## <a name="run-the-code"></a>Executar o código

Para chamar o ponto de extremidade da Pesquisa Personalizada do Bing, siga estas etapas:

1. Crie uma pasta para o código.
2. Em um prompt de comando de administrador ou terminal, navegue até a pasta que você acabou de criar.
3. Instale o módulo python de **solicitações**:
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>
7.Crie o arquivo BingCustomSearch.py e copie o seguinte código para ele.
8. Substitua **YOUR-SUBSCRIPTION-KEY** e **YOUR-CUSTOM-CONFIG-ID** com a chave e ID de configuração (consulte a etapa 1).

    ``` Python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=' + searchTerm + '&customconfig=' + customConfigId
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```
9. Execute o código usando os seguintes comandos.
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>Próximas etapas
- [Configurar a experiência de interface do usuário hospedada](./hosted-ui.md)
- [Usar marcadores de decoração para realçar texto](./hit-highlighting.md)
- [Paginar páginas da Web](./page-webpages.md)