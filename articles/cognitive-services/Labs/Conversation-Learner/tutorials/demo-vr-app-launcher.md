---
title: Demonstração do modelo de Aprendiz de Conversa, inicializador de aplicativos de realidade virtual - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como criar um modelo de Aprendiz de Conversa de demonstração.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 896ec007c03e30e5c20a5344430be040271bc00b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171128"
---
# <a name="demo-virtual-reality-app-launcher"></a>Demonstração: Inicialização do aplicativo de realidade virtual

Esta demonstração ilustra um inicializador de modelo de realidade virtual, o qual dá suporte a comandos como "inicie o Skype e coloque na tela." Um usuário precisa informar um nome de aplicativo e local para inicializar o aplicativo. O lançamento do modelo é tratado por uma chamada à API. Quando um nome de aplicativo é reconhecido a partir do usuário, entityDetectionCallback verifica se o aplicativo solicitado corresponde a um ou mais aplicativos na lista de aplicativos instalados. Ele lida com o caso em que o aplicativo solicitado não está instalado e o nome do aplicativo é ambíguo (corresponde a mais de um aplicativo instalado).

## <a name="video"></a>Vídeo

[![Demonstração de aplicativo de realidade virtual - Versão prévia](http://aka.ms/cl-demo-vrapp-preview)](http://aka.ms/blis-demo-vrapp)

## <a name="requirements"></a>Requisitos

Este tutorial exige que o bot VRAppLauncher esteja em execução:

    npm run demo-vrapp
    
### <a name="open-the-demo"></a>Abrir a demonstração

Na lista Modelo da interface do usuário da Web, clique em VRAppLauncher. 

## <a name="entities"></a>Entidades

Criamos quatro entidades:

- AppName: por exemplo, Skype
- PlacementLocation: para uma parede de exemplo
- UnknownAppName: uma entidade de programação que o sistema define quando não reconhece o nome de uma entidade que o usuário informa, por exemplo, porque ela não foi instalada.
- DisAmbigAppNames: uma matriz de dois ou mais nomes de aplicativo instalados que correspondem ao que o usuário informou. 

![](../media/tutorial_vrapplauncher_entities.PNG)

### <a name="actions"></a>Ações

Você criou um conjunto de ações que inclui uma API chamada LaunchApp, que iniciará a chamada de função para inicializar um aplicativo.

![](../media/tutorial_vrapplauncher_actions.PNG)

### <a name="training-dialogs"></a>Caixas de Diálogo de Treinamento
Definimos várias caixas de diálogo de treinamento.

![](../media/tutorial_vrapplauncher_dialogs.PNG)

Por exemplo, vamos testar uma sessão de ensino.

1. Clique em Caixas de Diálogos de Treinamento, depois em Nova Caixa de Diálogo de Treinamento.
1. Digite ‘oi’.
2. Clique em Ação de Pontuação.
3. Clique para selecionar “quais aplicativos você deseja iniciar?”
4. Insira “outlook”.
    - O LUIS reconhece isso como uma entidade.
5. Clique em Ações de Pontuação.
3. Clique para selecionar “onde você deseja colocar?”
4. Insira “na parede”.
    - O LUIS reconhece como um PlacementLocation.
2. Insira as Ações de Pontuação.
6. Selecione “LaunchApp”
7. Sistema: “iniciando o outlook na parede”.
    - Isso acionou uma chamada à API. O código para a chamada está em C:\<\installedpath>\src\demos\demoVRAppLauncher.ts. No entanto, ele não contém o código para inicializar o Outlook para essa demonstração.
    - Ele limpa as entidades AppName e PlacementLocation. Isso retorna a cadeia de caracteres acima como resposta.
4. Clique em Ensino Concluído.

![](../media/tutorial_vrapplauncher_callbackcode.PNG)

Vamos começar outra sessão de treinamento para lidar com entidades desconhecidas e ambíguas.

1. Clique em Nova Caixa de Diálogo de Treinamento.
1. Insira “iniciar o OneNote”. 
    - O modelo reconhece o OneNote como um nome de aplicativo. A função `EntityDetectionCallback` definida no código resolve o nome digitado pelo usuário para um nome de aplicativo, correspondendo-o à lista de aplicativos definida no código. Ele então retorna o conjunto de todos os aplicativos correspondentes. 
    - Se a lista de correspondências for zero, isso significa que o aplicativo não está instalado. É colocado em unknownAppName.
    - Se localizar mais de um aplicativo, copiará os aplicativos para `DisambigAppNames` e limpará a entidade AppName.
2. Clique em Ação de Pontuação.
3. Clique para selecionar “Desculpe, não conheço o aplicativo $UknownAppName”.
4. Insira 'iniciar Amazon'. Tentaremos outro caminho.
5. Clique em Ações de Pontuação.
    - Agora, o Amazon Video e Amazon Music estão na memória `DisambigAppNames` e o OneNote foi apagado.
3. Clique para selecionar “Há alguns aplicativos que parecem...”
    - A classificação não é muito alta porque definimos apenas alguns diálogos de treinamento até este ponto. Definir mais diálogos de treinamento tornaria o modelo mais decisivo.
2. Insira as Ações de Pontuação.
4. Clique em Ensino Concluído.

Agora, você viu como fazer a resolução da entidade. A demonstração também ilustrou os retornos de chamada à API e mostrou um modelo para coletar informações, verificar presença e ambiguidade e tomar as medidas corretas adequadamente.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantando um bot Aprendiz de Conversa](../deploy-to-bf.md)
