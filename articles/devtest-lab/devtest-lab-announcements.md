---
title: "Postar comunicado em laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como adicionar um comunicado a um laboratório no Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 67a09946-4584-425e-a94c-abe57c9cbb82
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: v-craic
ms.openlocfilehash: d376909a46da11ac1b6b1fa968e53ebef8f3dbf7
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2018
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Postar um comunicado em um laboratório no Azure DevTest Labs

Como um administrador do sistema, você pode postar um comunicado personalizado em um laboratório existente para notificar os usuários sobre alterações ou adições ao laboratório recentes. Por exemplo, talvez você queira informar os usuários sobre:

- Novos tamanhos VM disponíveis
- Imagens que não podem ser utilizadas atualmente
- Atualizações nas políticas do laboratório

Quando postado, o aviso é exibido na página Visão geral do laboratório e o usuário pode selecioná-lo para obter mais detalhes.

O recurso de comunicado deve ser usado para notificações temporárias.  Você poderá desabilitar um comunicado facilmente quando ele não for mais necessário.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Etapas para postar um comunicado em um laboratório existente

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessário, selecione **Todos os Serviços** e selecione **DevTest Labs** na lista. (O laboratório já pode ser exibido no painel, em **Todos os Recursos**).
1. Na lista de laboratórios, selecione o laboratório no qual você deseja postar um comunicado.  
1. Na área **Visão geral** do laboratório, selecione **Configuração e políticas**.  

    ![Botão Configuração e políticas](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. À esquerda, em **CONFIGURAÇÕES**, selecione **Comunicado de laboratório**.

    ![Botão de comunicado do laboratório](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Para criar uma mensagem para os usuários neste laboratório, defina **Habilitado** como **Sim**.

1. Insira um **Título** e o **Texto** para o comunicado.

   O título pode ter até 100 caracteres e é mostrado ao usuário na página Visão geral do laboratório. Se o usuário selecionar o título, o texto de aviso será exibido.

   O texto do aviso aceita markdown. Quando você insere o texto do comunicado, pode exibir a mensagem na área de visualização na parte inferior da tela.

    ![Tela de comunicado do laboratório para criação da mensagem.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Selecione **Salvar** quando o comunicado estiver pronto para postagem.

Quando você não quiser mais mostrar esse aviso aos usuários do laboratório, retorne para a página **Comunicado do laboratório** e defina **Habilitado** como **Não**.

## <a name="steps-for-users-to-view-an-announcement"></a>Etapas para os usuários exibirem um comunicado

1. No [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), selecione um laboratório.

1. Se o laboratório tiver um comunicado postado, um aviso de informações será mostrado na parte superior da página Visão geral do laboratório. Esta notificação de informações é o título do comunicado que foi especificado quando ele foi criado.

    ![Comunicado do laboratório na página Visão geral](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. O usuário pode selecionar a mensagem para exibir todo o comunicado.

    ![Mais informações para o comunicado do laboratório](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximas etapas
* Se você alterar ou definir uma política de laboratório, talvez seja melhor postar um comunicado para informar aos usuários. [Definir políticas e agendas](devtest-lab-set-lab-policy.md) fornece informações sobre como aplicar as convenções e restrições em sua assinatura usando políticas personalizadas.
* Explore a [galeria de modelos de Início Rápido do Azure Resource Manager do DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
