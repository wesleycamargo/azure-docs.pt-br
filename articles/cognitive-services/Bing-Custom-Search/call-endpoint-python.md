---
title: 'Início Rápido: chamar o ponto de extremidade usando Python – Pesquisa Personalizada do Bing'
titlesuffix: Azure Cognitive Services
description: Este início rápido mostra como solicitar resultados de pesquisa da instância de pesquisa personalizada usando Python para chamar o ponto de extremidade da Pesquisa Personalizada do Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: aahi
ms.openlocfilehash: d7977f49a484665f133a119f491bd8986c0b6fc4
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310712"
---
# <a name="quickstart-call-bing-custom-search-endpoint-python"></a>Início Rápido: chamar ponto de extremidade da Pesquisa Personalizada do Bing (Python)

Este início rápido mostra como solicitar resultados de pesquisa de sua instância de pesquisa personalizada usando o Python para chamar o ponto de extremidade da Pesquisa Personalizada do Bing. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa de:

- Uma instância de pesquisa personalizada pronta para uso. Consulte [Criar a primeira instância da Pesquisa Personalizada do Bing](quick-start.md).
- [Python](https://www.python.org/) instalado.
- Uma chave de assinatura. Você pode obter uma chave de assinatura quando ativar sua [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) ou pode usar uma chave de assinatura paga no painel do Azure (consulte [Conta da API de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).   Veja também [Preços dos Serviços Cognitivos - API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).


## <a name="run-the-code"></a>Executar o código

Para executar esse exemplo, siga estas etapas:

1. Crie uma pasta para o código.  
  
2. Em um prompt de comando de administrador ou terminal, navegue até a pasta que você acabou de criar.  
  
3. Instale o módulo python de **solicitações**:  
  
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>  
      
4. Crie um arquivo chamado BingCustomSearch.py na pasta que você criou e copie o código a seguir para ele. Substitua **YOUR-SUBSCRIPTION-KEY** e **YOUR-personalizado-CONFIG-ID** com sua chave de subscriptioin e a configuração de ID.  
  
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
  
7. Execute o código usando os seguintes comandos.  
  
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>Próximas etapas
- [Configurar a experiência de interface do usuário hospedada](./hosted-ui.md)
- [Usar marcadores de decoração para realçar texto](./hit-highlighting.md)
- [Paginar páginas da Web](./page-webpages.md)
