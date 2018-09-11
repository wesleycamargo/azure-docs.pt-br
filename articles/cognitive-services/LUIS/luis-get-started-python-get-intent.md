---
title: Início Rápido para aprender a chamar um aplicativo LUIS (Serviço Inteligente de Reconhecimento Vocal) usando Python | Microsoft Docs
description: Neste início rápido, você aprenderá a chamar um aplicativo LUIS usando Python.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 06/27/2018
ms.author: diberry
ms.openlocfilehash: bc7ae912d762a98c34b9a1b2d6a82d5630c4794b
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "43768543"
---
# <a name="quickstart-call-a-luis-endpoint-using-python"></a>Início Rápido: chamar um ponto de extremidade LUIS usando Python
Neste início rápido, passe enunciados para um ponto de extremidade LUIS e obtenha intenção e entidades.

<!-- green checkmark -->
<!--
> [!div class="checklist"]
> * Create LUIS subscription and copy key value for later use
> * View LUIS endpoint results from browser to public sample IoT app
> * Create Visual Studio C# console app to make HTTPS call to LUIS endpoint
-->

Para este artigo, você precisa de uma conta gratuita de [LUIS](luis-reference-regions.md#luis-website) para criar o seu aplicativo LUIS.

<a name="create-luis-subscription-key"></a>
## <a name="create-luis-endpoint-key"></a>Criar chave de ponto de extremidade LUIS
Você precisa de uma chave de API de Serviços Cognitivos para fazer chamadas para o exemplo de aplicativo LUIS usado neste passo a passo. 

Para obter uma chave de API, siga estas etapas: 

1. Primeiro é necessário criar uma [conta de API de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) no portal do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

2. Faça logon no Portal do Azure em https://portal.azure.com. 

3. Siga as etapas em [Como criar chaves de ponto de extremidade usando o Azure](./luis-how-to-azure-subscription.md) para obter uma chave.

4. Acesse o site do [LUIS](luis-reference-regions.md) e faça logon usando sua conta do Azure. 

    [![](media/luis-get-started-node-get-intent/app-list.png "Captura de tela da lista de aplicativos")](media/luis-get-started-node-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>Entender o que o LUIS retorna

Para entender o que um aplicativo de LUIS retorna, você pode colar a URL de um exemplo de aplicativo LUIS em uma janela do navegador. O aplicativo de exemplo é um aplicativo de IoT que detecta se o usuário deseja ativar ou desativar as luzes.

1. O ponto de extremidade do aplicativo de exemplo está no seguinte formato: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light` Copie a URL e substitua a chave do ponto de extremidade pelo valor do campo `subscription-key`.
2. Cole a URL em uma janela do navegador e pressione Enter. O navegador exibe um resultado JSON que indica que o LUIS detecta a intenção `HomeAutomation.TurnOn` e a entidade `HomeAutomation.Room` com o valor `bedroom`.

    ![O resultado JSON detecta a intenção TurnOn](./media/luis-get-started-node-get-intent/turn-on-bedroom.png)
3. Altere o valor do parâmetro `q=` na URL para `turn off the living room light`, e pressione enter. O resultado agora indica que o LUIS detectou a intenção `HomeAutomation.TurnOff` e a entidade `HomeAutomation.Room` com o valor `living room`. 

    ![O resultado JSON detecta a intenção TurnOff](./media/luis-get-started-node-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-python"></a>Consumir um resultado de LUIS usando a API de ponto de extremidade com Python

Você pode usar o Python para acessar os mesmos resultados que você viu na janela do navegador na etapa anterior.

1. Copie um dos seguintes snippets de código em um arquivo chamado `quickstart-call-endpoint.py`:

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. Substitua o valor do campo `Ocp-Apim-Subscription-Key` pela sua chave de ponto de extremidade do LUIS.

3. Instale as dependências com `pip install requests`.

4. Execute o script com `python ./quickstart-call-endpoint.py`. Ele exibe o mesmo JSON que você viu anteriormente na janela do navegador.
<!-- 
![Console window displays JSON result from LUIS](./media/luis-get-started-python-get-intent/console-turn-on.png)
-->

## <a name="clean-up-resources"></a>Limpar recursos
Os dois recursos criados neste tutorial são a chave do ponto de extremidade do LUIS e o projeto C#. Exclua a chave do ponto de extremidade LUIS do portal do Azure. Feche o projeto do Visual Studio e remova o diretório do sistema de arquivos. 

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Adicionar declarações](luis-get-started-python-add-utterance.md)