---
title: Como usar a definição de versões e marcação com um aplicativo Conversation Learner - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar o controle de versão e a marcação de um aplicativo Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ea013db078ff33f8597b0e15a8fc951e8ae320e8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364238"
---
# <a name="how-to-use-versioning-and-tagging"></a>Como usar o controle de versão e a marcação

Este tutorial ilustra como marcar versões do seu aplicativo Conversation Learner e definir qual versão está "ativa".  

## <a name="requirements"></a>Requisitos
Este tutorial requer o uso do emulador de bot para criar diálogos de registro, e não a interface do usuário da Web do Diálogo de Registro.  

Este tutorial exige que o bot de tutorial geral esteja em execução:

    npm run tutorial-general

## <a name="details"></a>Detalhes

Ao editar, você sempre está editando a marca chamada "mestre" - você pode criar versões marcadas do mestre (o que basicamente tira um instantâneo de mestre), mas não é possível editar versões marcadas.

## <a name="steps"></a>Etapas

### <a name="install-the-bot-framework-emulator"></a>Instalar o emulador de estrutura do Bot

- Vá para [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
- Baixe e instale o emulador.

### <a name="create-an-app"></a>Criar um aplicativo

1. Clique em Novo Aplicativo
2. No campo Nome, insira Tutorial-16-Controle de Versão
3. Clicar em Criar 
4. Clique em Configurações
5. Copiar a ID do Aplicativo

### <a name="configure-the-emulator"></a>Configurar o emulador

- Na pasta raiz do Conversation Learner, abra o arquivo .env.
- Cole a ID do Aplicativo como o valor de CONVERSATION_LEARNER_APP_ID
- Reinicie o serviço Conversation Learner ao sair do prompt de comando e execute novamente:
 
    npm run tutorial-general 

### <a name="actions"></a>Ações

Vamos criar uma ação:

1. Alterne para a interface do usuário da Web.
1. Clique em Ações e, em seguida, em Nova Ação.
2. Em Resposta, insira "olá (versão 1)".
3. Clique em Salvar.


![](../media/tutorial16_action1.PNG)

Criar uma nova marca:

3. Clique em "configurações" e crie uma nova "marca".
    - Chame-a de "versão 1"
4. Defina "versão 1" como "ativa".  
    - O efeito de configurar a marca ativa como "versão 1" é que os canais usando este bot usarão a marca "versão 1".
    - Marcado versões de aplicativos não são afetadas por edições (ações, entidades, adicionando a caixas de diálogo de trem de alteração).  
    - As edições feitas em um aplicativo (alterar ações, entidades, adição de diálogos de treinamento) são sempre feitas na marca "mestre".  Em outras palavras, "mestre" é a única marca que pode ser alterada; as outras marcas são instantâneos corrigidos.
    - Os diálogos de registro na interface de usuário do Conversation Learner sempre usam o mestre (e não a marca ativa).

![](../media/tutorial16_v1_create.PNG)

Observe que a versão foi criada em configurações:

![](../media/tutorial16_settings.PNG)

Vamos adicionar uma segunda ação:

1. Clique em Ações e, em seguida, em Nova Ação.
2. Em Resposta, insira "adeus (versão 2)".

Edite a primeira ação:

1. Clique em Ações.
2. Em Ações, clique em "olá (versão 1)".
3. Altere a Resposta para "olá (versão 2)".

![](../media/tutorial16_hi_there_v2.PNG)

### <a name="switch-to-the-bot-emulator"></a>Alternar para o emulador de bot

1. Na interface do usuário do bot, insira "adeus".
2. Observe que o bot responde com "olá (versão 1)".
    - Isso mostra que a versão 1 está "ativa". 

![](../media/tutorial16_bf_response.PNG)

### <a name="switch-to-the-web-ui"></a>Alternar para a interface do usuário da Web

1. Clique em Registrar Diálogos (se nenhum diálogo for exibido, atualize o aplicativo).
2. Clique em "olá (versão 2)"

Observe que é possível corrigir fazendo escolhas dentre todas as ações disponíveis no momento. Essas edições serão feitas no mestre.

Agora você viu como funciona o controle de versão e como você pode interagir com o bot usando o emulador de estrutura de Bot.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Demonstração – redefinição de senha](./demo-password-reset.md)
