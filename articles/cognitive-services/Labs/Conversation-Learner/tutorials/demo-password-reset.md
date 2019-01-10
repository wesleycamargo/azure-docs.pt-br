---
title: Demonstração do Modelo do Aprendiz de Conversa, redefinição de senha - Serviços Cognitivos da Microsoft | Microsoft Docs
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
ms.openlocfilehash: 409647da146a2844384204cb03de5028d45e5763
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792383"
---
# <a name="demo-password-reset"></a>Demonstração: Redefinição de senha
Este tutorial apresenta um bot simples de suporte técnico que pode ajudar com a redefinição de senha da plataforma Conversation Learner. O modelo do bot pode aprender fluxos de diálogo não triviais e sequências de vários turnos, incluindo uma classe fora do domínio. A tarefa pode ser feita sem código ou entidades.

## <a name="video"></a>Vídeo

[![Demonstração da visualização de senha](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de redefinição de senha esteja em execução

    npm run demo-password

### <a name="open-the-demo"></a>Abrir a demonstração

Na lista de modelos da interface do usuário da Web, clique em “Tutorial Demo Password Reset” (Tutorial de demonstração de redefinição de senha). 

### <a name="actions"></a>Ações

O modelo contém um conjunto de ações projetado para ajudar os usuários a resolver problemas comuns de senha.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Diálogos de treinamento

O modelo também contém várias Caixas de Diálogo de Treinamento incluindo algumas que demonstram treinamento fora da classe de domínio. Por exemplo, os usuários podem solicitar como direções de condução. O bot de exemplo foi treinado em poucos para fins de demonstração e simplesmente responde informando que "não pode ajudar com isso." A lista de caixas de diálogo de treinamento existentes é encontrada em "Caixas de Diálogo de Treinamento" no painel esquerdo.

![](../media/tutorial_pw_reset_entities.PNG)

1. No painel esquerdo, clique em "Caixas de Diálogo de Treinamento" e, em seguida, no botão "Nova Caixa de Diálogo de Treinamento".
2. No painel de chat, onde está escrito "Digite sua mensagem...", digite "perdi minha senha".
3. Clique no botão "Ações de Pontuação".
4. Selecione a resposta, “É para sua conta local ou conta Microsoft?”
5. No painel de chat, onde está escrito "Digite sua mensagem...", digite "conta local".
6. Clique no botão "Ações de Pontuação".
7. Selecione a resposta, “Qual versão do Windows você tem?”
8. No painel de chat, onde está escrito "Digite sua mensagem...", digite "windows xp"
9. Clique no botão "Ações de Pontuação".
10. Clique no botão “+Ação”.
11. No campo "Resposta do Bot...", digite "SOLUÇÃO: Como redefinir senha no Windows XP...”
12. Clique no botão “Criar”.

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>Caixas de diálogo de treinamento para cenários fora do domínio

1. No painel esquerdo, clique em "Caixas de Diálogo de Treinamento" e, em seguida, a caixa de diálogo “toy stores”.
2. No painel de chat, clique no enunciado “toy stores”.
3. No campo "Adicionar entrada alternativa...", digite "pesquisa web" e aperte enter.
4. No campo "Adicionar entrada alternativa...", digite "reserva de voo" e aperte enter.
5. Clique no botão “Salvar Alterações”.
6. Clique no botão “Salvar Edição”.
7. No painel esquerdo, clique em "Registrar Diálogos" e, em seguida, no botão "Novo Registro de Diálogo".
8. No painel de chat, onde está escrito "Digite sua mensagem...", digite "não encontro minha senha”
9. No painel de chat, onde está escrito "Digite sua mensagem...", digite "conta Microsoft”
10. No painel de chat, onde está escrito "Digite sua mensagem...", digite "obrigado"
11. Clique no botão "Teste Concluído".
12. Clique na caixa de diálogo “não encontro minha senha” na exibição de grade.
13. No painel de chat, clique na resposta incorretamente renderizada “Solução: Como redefinir a senha da sua conta da Microsoft”.
14. Clique no botão “+Ação”.
15. No campo "Resposta do Bot...", digite "Bem-Vindo”
16. Clique no botão “Criar”.
17. Clique no botão "Salvar como Diálogo de Treinamento".

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Demonstração - pedido de pizza](./demo-pizza-order.md)
