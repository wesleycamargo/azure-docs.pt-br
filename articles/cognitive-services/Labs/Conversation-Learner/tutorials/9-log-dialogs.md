---
title: Como registrar diálogos em um aplicativo de Aprendiz de Conversa – Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como registrar diálogos com um aplicativo de Aprendizado de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 477545c48aeca05d56fdae28ac65a8f381a482fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364262"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-application"></a>Como registrar diálogos com um aplicativo de Aprendizado de Conversa

Este tutorial mostra como fazer teste de usuário final dentro da interface do Aprendiz de Conversa; como os diálogo são registrados; e como fazer correções em diálogos registrados para melhorar seu modelo.

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
Você pode usar os registros de diálogos para revisar e fazer correções nos diálogos realizados com os usuários finais.  Especificamente, você pode corrigir os rótulos de entidade e as seleções de ação para melhorar o desempenho do sistema geral e do modelo treinado. 

## <a name="steps"></a>Etapas

### <a name="create-the-application"></a>Criar o aplicativo

1. Na Interface do Usuário da Web, clique em Novo Aplicativo
2. Em Nome, insira LogDialogs. Em seguida, clique em Criar.

### <a name="create-an-entity"></a>Criar uma entidade

1. Clique em Entidades e, em seguida, em Nova Entidade.
2. Em Nome da Entidade, insira a cidade.
3. Clique em Criar.

### <a name="create-two-actions"></a>Crie duas ações

1. Clique em Ações e, em seguida, em Nova Ação
2. Em Resposta, digite "Qual cidade?".
3. Em Entidades de Desqualificação, insira $city.
3. Clicar em Criar

Em seguida, crie a segunda ação:

1. Clique em Ações e, em seguida, em Nova Ação.
3. Em Resposta, digite "O clima em $city é provavelmente ensolarado".
4. Entidades Obrigatórias, insira $city.
4. Clique em Criar.

Agora você tem duas ações.

### <a name="train-the-bot"></a>Treinar o bot

1. Clique em Caixas de Diálogos de Treinamento, depois em Nova Caixa de Diálogo de Treinamento.
2. Digite "como está o clima".
3. Clique em Ações de Pontuação e selecione "Qual cidade?"
2. Insira "Seattle".
3. Clique duas vezes em "Seattle" e selecione a cidade.
    - Isso a marca como uma entidade de cidade.
5. Clique em Ações de Pontuação
6. Selecione "O clima em $city é provavelmente ensolarado".
7. Clique em Ensino Concluído.

Adicione outro exemplo de diálogo:

1. Clique em Nova Ação, depois Novo Diálogo de Treinamento.
2. Digite "como está o clima em Seattle?". Observe que Seattle está marcado como uma entidade.
5. Clique em Ações de Pontuação 
6. Selecione "O clima em $city é provavelmente ensolarado".
7. Clique em Ensino Concluído.

### <a name="try-the-bot-as-the-user"></a>Experimente o bot como o usuário
Vamos imaginar que implantamos essa bot para os usuários.

1. Clique em Registrar Diálogos.
2. Clique em Nova Sessão de Chat.
    - Isso apresenta o bot conforme o usuário o experimentaria no controle do chat da Web à esquerda da interface do usuário. Você pode ignorar a área de espaço em branco à direita.
3. Digite "olá".
4. Resposta do bot: "qual cidade?"
4. Digite "Boston".
5. Resposta do bot: "qual cidade?"
    - Isso não parece certo. Então, vamos salvar este diálogo.
2. Clique em Concluir Teste.

Vamos iniciar uma nova sessão:

2. Clique em Nova Sessão de Chat.
3. Digite "previsão para Boston".
4. Resposta do bot: "qual cidade?"
2. Clique em Ensino Concluído.

Agora vamos fazer correções no segundo diálogo:

1. Clique em "previsão para Boston" em Registrar Diálogos.
    - Isso abre a conversa.
    - Se você clicar no lado do usuário da conversa (aqui em "previsão para Boston"), você pode alterar os rótulos de entidade.
    - Se você clicar no lado do sistema (aqui em "qual cidade"), você pode alterar a ação selecionada.
5. Clique em "previsão para Boston". 
    - Aqui, a causa principal é que Boston não foi marcada como entidade. Precisamos alterar isso.
    - Clique duas vezes em "Boston" e selecione a cidade.
    - Clique em Enviar Alterações e clique em Salvar. Isso criará um diálogo de treinamento com base nas alterações feitas, e coloca você nos diálogos de treinamento no ponto da alteração feita.
6. Selecione "O clima em $city é provavelmente ensolarado".
7. Clique em Ensino Concluído. Se você acessar Diálogos de Treinamento agora, verá a nova ação adicionada.

![](../media/tutorial9_logdiag1.PNG)

Agora, vamos fazer correções no outro diálogo:

1. Clique em "olá" em Registrar Diálogos.
    - Isso abre a conversa.
3. Observe que a resposta para "olá" é "qual cidade". Mas queremos mudar isso para algo que faça mais sentido. Seria melhor ter uma resposta como "olá, sou o bot do tempo". Mas não há uma ação que faça isso, então teremos que criar uma.
4. Clique em Ação.
    - Na resposta, digite "sou o bot do tempo. Posso ajudar com previsões".
6. Desmarque a caixa de seleção Aguardar Resposta para torná-la uma ação sem espera.
7. Clique em Criar.
8. Depois, clique para Selecionar essa nova ação. Em seguida, clique em Salvar.
    - Isso traz você de volta àquele ponto na sessão de treinamento.
6. Clique para selecionar "qual cidade"?
7. Digite "Boston". Clique duas vezes para marcar Boston como uma entidade, se não o tiver feito ainda.
8. Clique em Ações de Pontuação.
9. Clique para selecionar "O clima em $city é provavelmente ensolarado".
10. Clique em Ensino Concluído.

![](../media/tutorial9_addnewaction.PNG)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Retorno de chamada de detecção de entidade](./10-entity-detection-callback.md)
