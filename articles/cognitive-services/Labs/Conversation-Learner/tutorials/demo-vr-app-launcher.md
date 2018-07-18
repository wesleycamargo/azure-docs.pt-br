---
title: Aplicativo de demonstração Aprendiz de Conversa, inicialização de aplicativo de realidade virtual – Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como criar um aplicativo de demonstração Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 3e41125bf7da9ee64d666d22cb275af01af54012
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364248"
---
# <a name="demo-virtual-reality-app-launcher"></a>Demonstração: Inicialização do aplicativo de realidade virtual

Essa demonstração ilustra uma inicialização do aplicativo de realidade virtual, dando suporte a comandos como "iniciar Skype e colocar na parede". Um usuário precisa informar um nome de aplicativo e local para inicializar o aplicativo. A inicialização do aplicativo é lidada por uma chamada à API. Quando um nome de aplicativo é reconhecido a partir do usuário, entityDetectionCallback verifica se o aplicativo solicitado corresponde a um ou mais aplicativos na lista de aplicativos instalados. Ele lida com o caso em que o aplicativo solicitado não está instalado e o nome do aplicativo é ambíguo (corresponde a mais de um aplicativo instalado).

## <a name="requirements"></a>Requisitos

Este tutorial requer que o bot VRAppLauncher esteja em execução

    npm run demo-vrapp

### <a name="open-the-demo"></a>Abrir a demonstração

Na lista de Aplicativos da IU da Web, clique em VRAppLauncher. 

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
1. Digite “olá”.
2. Clique em Ação de Pontuação.
3. Clique para selecionar “quais aplicativos você deseja iniciar?”
4. Insira “outlook”.
    - Observe que LUIS reconhece isso como uma entidade.
5. Clique em Ações de Pontuação.
3. Clique para selecionar “onde você deseja colocar?”
4. Insira “na parede”.
    - Observe que LUIS reconhece isso como PlacementLocation.
2. Insira as Ações de Pontuação.
6. Selecione “LaunchApp”
7. Sistema: “iniciando o outlook na parede”.
    - Observe que isso inicializou uma chamada de API. O código para a chamada está em C:\<\installedpath>\src\demos\demoVRAppLauncher.ts. Na verdade, não tem lógica inicializar o outlook para essa demonstração.
    - Ele limpa as entidades AppName e PlacementLocation. Isso retorna a cadeia de caracteres acima como resposta.
4. Clique em Ensino Concluído.

![](../media/tutorial_vrapplauncher_callbackcode.PNG)

Vamos começar outra sessão de treinamento para lidar com entidades desconhecidas e ambíguas.

1. Clique em Nova Caixa de Diálogo de Treinamento.
1. Insira “iniciar o OneNote”. 
    - É reconhecido como um nome de aplicativo porque o EntityDetectionCallback definido no código requer o nome de usuário inserido e determina-o como um nome de aplicativo combinando-o com a lista de aplicativos definida no código. Ele então retorna o conjunto de todos os aplicativos correspondentes. 
    - Se a lista de correspondências for zero, isso significa que o aplicativo não está instalado. É colocado em unknownAppName.
    - Se ele encontrar mais de um aplicativo, irá copiá-lo para DisambigAppNames e limpará a entidade AppName.
2. Clique em Ação de Pontuação.
3. Clique para selecionar “Desculpe, não conheço o aplicativo $UknownAppName”.
4. Insira “iniciar o amazon”. Tentaremos outro caminho.
5. Clique em Ações de Pontuação.
    - Observe que o Amazon Video e o Amazon Music agora estão na memória DisambigAppNames. E o OneNote foi limpo.
3. Clique para selecionar “Há alguns aplicativos que parecem...”
    - Observe que a pontuação não é muito alta, porque só temos algumas caixas de diálogo de treinamento até este ponto. Parece que precisaríamos de mais algumas aqui para tornar o modelo mais decisivo.
2. Insira as Ações de Pontuação.
4. Clique em Ensino Concluído.

Agora, você viu como fazer a resolução da entidade. A demonstração também mostrou as chamadas de API e um modelo para coletar informações, verificar a presença e a ambiguidade, e tomar a ação certa com base nisso.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantando um bot Aprendiz de Conversa](../deploy-to-bf.md)
