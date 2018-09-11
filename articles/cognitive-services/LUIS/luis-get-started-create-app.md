---
title: Criar seu primeiro aplicativo LUIS (Serviço Inteligente de Reconhecimento Vocal) em 10 minutos – LUIS de Serviços Cognitivos | Microsoft Docs
description: Neste início rápido, crie um aplicativo LUIS que use o domínio predefinido `HomeAutomation` para ligar e desligar luzes e dispositivos. Este domínio predefinido fornece intenções, entidades e exemplos de enunciados a você. Quando terminar, você terá um ponto de extremidade do LUIS em execução na nuvem.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/22/2018
ms.author: diberry
ms.openlocfilehash: 457f23936dec0cf85e9aebbf3e54bba37c2f3ca3
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/23/2018
ms.locfileid: "43768527"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Início Rápido: Usar o aplicativo de automação Inicial predefinido

Neste início rápido, crie um aplicativo LUIS que use o domínio predefinido `HomeAutomation` para ligar e desligar luzes e dispositivos. Este domínio predefinido fornece intenções, entidades e exemplos de enunciados a você. Quando terminar, você terá um ponto de extremidade do LUIS em execução na nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Para este artigo, você precisa de uma conta gratuita do LUIS, criada no portal do LUIS em [http://www.luis.ai](http://www.luis.ai). 

## <a name="create-a-new-app"></a>Criar um novo aplicativo
Você pode criar e gerenciar seus aplicativos em **Meus Aplicativos**. 

1. Entre no portal do LUIS.

2. Selecione **Criar novo aplicativo**.

    [![](media/luis-quickstart-new-app/app-list.png "Captura de tela da lista de aplicativos")](media/luis-quickstart-new-app/app-list.png)

3. Na caixa de diálogo, dê ao seu aplicativo o nome "Automação Residencial".

    [![](media/luis-quickstart-new-app/create-new-app-dialog.png "Captura de tela da caixa de diálogo pop-up Criar novo aplicativo")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. Escolha sua cultura do aplicativo. Para este aplicativo de Automação Residencial, escolha inglês. Em seguida, selecione **Concluído**. O LUIS cria o aplicativo de Automação Residencial. 

    >[!NOTE]
    >A cultura não poderá ser alterada depois que o aplicativo for criado. 

## <a name="add-prebuilt-domain"></a>Adicionar domínio predefinido

Selecione **Domínios predefinidos** no painel de navegação à esquerda. Em seguida, pesquise "Início". Selecione **Adicionar domínio**.

[![](media/luis-quickstart-new-app/home-automation.png "Captura de tela do domínio de Automação Residencial destacado no menu de domínio predefinido")](media/luis-quickstart-new-app/home-automation.png)

Quando o domínio for adicionado com êxito, a caixa de domínio predefinido exibirá um botão **Remover domínio**.

[![](media/luis-quickstart-new-app/remove-domain.png "Captura de tela do domínio de Automação Residencial com o botão Remover")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Intenções e entidades

Selecione **Intenções** no painel de navegação à esquerda para examinar as intenções do domínio HomeAutomation. 

[![](media/luis-quickstart-new-app/home-automation-intents.png "Captura de tela da lista de intenções com nomes de Intenção na tabela destacados")](media/luis-quickstart-new-app/home-automation-intents.png)

Cada intenção tem exemplos de enunciados.

> [!NOTE]
> **None** é uma intenção fornecida por todos os aplicativos LUIS. Você pode usá-la para lidar com enunciados que não correspondem à funcionalidade que seu aplicativo fornece. 

Selecione a intenção **HomeAutomation.TurnOff**. Você pode ver que a intenção contém uma lista de enunciados que rotulados com entidades.

[![](media/luis-quickstart-new-app/home-automation-turnon.png "Captura de tela da intenção HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-your-app"></a>Treinar seu aplicativo

Selecione **Treinar** na barra de navegação superior.

[![](media/luis-quickstart-new-app/trained.png "Captura de tela da intenção HomeAutomation.TurnOff com a notificação de sucesso em verde")](media/luis-quickstart-new-app/trained.png)

## <a name="test-your-app"></a>Testar seu aplicativo
Depois de treinar seu aplicativo, você pode testá-lo. Selecione **Testar** na navegação superior. Digite uma expressão de teste, como "Desligar as luzes" no painel de Teste Interativo e pressione Enter. 

```
Turn off the lights
```

Verifique se a intenção de pontuação superior corresponde à intenção esperada para cada enunciado de teste.

Neste exemplo, "Desligar as luzes" é identificado corretamente como a intenção de pontuação superior de "HomeAutomation.TurnOff".

[![](media/luis-quickstart-new-app/test.png "Captura de tela do painel de Teste com o enunciado realçado")](media/luis-quickstart-new-app/test.png)


Selecione **Testar** novamente para recolher o painel de teste. 

## <a name="publish-your-app"></a>Publicar seu aplicativo
Selecione **Publicar** na navegação superior. 

[![](media/luis-quickstart-new-app/publish.png "Captura de tela do aplicativo com o botão Publicar realçado")](media/luis-quickstart-new-app/publish.png)

Selecione o Slot de produção e o botão **Publicar**.

A barra de notificação verde na parte superior indica que o aplicativo foi publicado com êxito.

[![](media/luis-quickstart-new-app/published.png "Captura de tela do aplicativo com sucesso na publicação")](media/luis-quickstart-new-app/published.png)

Depois de publicar com êxito, você pode usar a URL do ponto de extremidade exibida na página **Publicar aplicativo**.

[![](media/luis-quickstart-new-app/endpoint.png "Captura de tela da página Publicar com a URL do ponto de extremidade realçada")](media/luis-quickstart-new-app/endpoint.png)

## <a name="use-your-app"></a>Usar seu aplicativo
Você pode testar seu ponto de extremidade publicado em um navegador usando a URL gerada. Defina essa URL no navegador, defina o parâmetro de URL "&q" para a sua consulta de teste. Por exemplo, adicione `turn off the living room light` ao final da URL e pressione Enter. O navegador exibe a resposta JSON do seu ponto de extremidade HTTP.


[![](media/luis-quickstart-new-app/turn-off-living-room.png "Captura de tela do navegador com o resultado JSON detecta a intenção de TurnOff")](media/luis-quickstart-new-app/turn-off-living-room.png)

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, exclua o aplicativo LUIS. Para fazer isso, selecione o botão de reticências (***...***) à direita do nome do aplicativo na lista de aplicativos e selecione **Excluir**. Na caixa de diálogo pop-up **Excluir aplicativo?**, selecione **OK**.

## <a name="next-steps"></a>Próximas etapas

Você pode chamar o ponto de extremidade do código:

> [!div class="nextstepaction"]
> [Chamar um ponto de extremidade do LUIS usando código](luis-get-started-cs-get-intent.md)
