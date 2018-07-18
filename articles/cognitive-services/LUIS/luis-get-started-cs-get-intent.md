---
title: Tutorial para aprender a chamar um aplicativo de Reconhecimento Vocal (LUIS) usando C# | Microsoft Docs
description: Neste tutorial, você aprenderá a chamar um aplicativo LUIS usando C#.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 0416d19d27810a2ab8eeb20e16b2f921fc7826ee
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263482"
---
# <a name="tutorial-call-a-luis-endpoint-using-c"></a>Tutorial: Chamar um ponto de extremidade de LUIS usando C#

Passar enunciados para um ponto de extremidade LUIS com intenção e entidades como retorno.

<!-- green checkmark -->
> [!div class="checklist"]
> * Crie uma assinatura de LUIS assinatura e copie o valor da chave para uso posterior
> * Exibir resultados de ponto de extremidade LUIS do navegador para aplicativo IoT de exemplo público
> * Criar aplicativo de console do Visual Studio C# para fazer a chamada HTTPS para o ponto de extremidade de LUIS

<!-- link to free account --> Para este artigo, você precisará de uma conta gratuita do [LUIS][LUIS] para criar o seu aplicativo de LUIS.

## <a name="create-luis-subscription-key"></a>Criar chave de assinatura de LUIS
1. Primeiro é necessário criar uma [conta de API de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) no portal do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

2. Faça logon no Portal do Azure em https://portal.azure.com. 

3. Siga as etapas em [Criar chaves de assinatura usando o Azure](./luis-how-to-azure-subscription.md) para obter uma chave.

4. Volte para o site de [LUIS](luis-reference-regions.md). Faça logon usando sua conta do Azure. 

    [![](media/luis-get-started-cs-get-intent/app-list.png "Captura de tela da lista de aplicativos")](media/luis-get-started-cs-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>Entender o que o LUIS retorna

Para entender o que um aplicativo de LUIS retorna, você pode colar a URL de um exemplo de aplicativo LUIS em uma janela do navegador. O aplicativo de exemplo é um aplicativo de IoT que detecta se o usuário deseja ativar ou desativar as luzes.

1. O ponto de extremidade do aplicativo de exemplo está no seguinte formato: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light` Copie a URL e substitua a chave de assinatura pelo valor do campo `subscription-key`.
2. Cole a URL em uma janela do navegador e pressione Enter. O navegador exibe um resultado JSON que indica que o LUIS detecta a intenção `HomeAutomation.TurnOn` e a entidade `HomeAutomation.Room` com o valor `bedroom`.

    ![O resultado JSON detecta a intenção TurnOn](./media/luis-get-started-cs-get-intent/turn-on-bedroom.png)
3. Altere o valor do parâmetro `q=` na URL para `turn off the living room light`, e pressione enter. O resultado agora indica que o LUIS detectou a intenção `HomeAutomation.TurnOff` e a entidade `HomeAutomation.Room` com o valor `living room`. 

    ![O resultado JSON detecta a intenção TurnOff](./media/luis-get-started-cs-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-c"></a>Consumir um resultado de LUIS usando a API de ponto de extremidade com C# 

Você pode usar o C# para acessar os mesmos resultados que você viu na janela do navegador na etapa anterior. 

1. Crie um novo aplicativo de console no Visual Studio. Copie o código a seguir e salve-o em um arquivo *.cs:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/endpoint-api-samples/csharp/Program.cs)]
1. Substitua o valor da variável `subscriptionKey` pela sua chave de assinatura de LUIS.

3. No projeto do Visual Studio, adicione uma referência para **System. Web**.

4. Execute o aplicativo de console. Ele exibe o mesmo JSON que você viu anteriormente na janela do navegador.

![A janela de console exibe resultados JSON do LUIS](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="clean-up-resources"></a>Limpar recursos
Os dois recursos criados neste tutorial são a chave de assinatura LUIS e o projeto C#. Exclua a chave de assinatura de LUIS do portal do Azure. Feche o projeto do Visual Studio e remova o diretório do sistema de arquivos. 

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Adicionar declarações](luis-get-started-cs-add-utterance.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website