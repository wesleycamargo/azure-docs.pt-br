---
title: Concluir uma revisão de acesso para funções de diretório do Azure AD no PIM | Microsoft Docs
description: Saiba como concluir uma revisão de acesso para funções de diretório do Azure AD no Azure AD PIM (Privileged Identity Management) e exibir os resultados
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 3955f4bf9b579ae40424c2650f9d3b4c2ac4f030
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188579"
---
# <a name="complete-an-access-review-for-azure-ad-directory-roles-in-pim"></a>Concluir uma revisão de acesso para funções de diretório do Azure AD no PIM
Os administradores de função com privilégios podem examinar o acesso privilegiado quando uma [revisão de acesso tiver sido iniciada](pim-how-to-start-security-review.md). O Azure AD PIM (Privileged Identity Management) automaticamente enviará um email solicitando que os usuários revisem seu acesso. Se um usuário não tiver recebido um email, você poderá enviar para ele as instruções sobre [como executar uma revisão de acesso](pim-how-to-perform-security-review.md).

Depois do fim do período da revisão de acesso, ou quando todos os usuários tiverem terminado a autorrevisão, siga as etapas neste artigo para gerenciar a revisão e ver os resultados.

## <a name="manage-access-reviews"></a>Gerenciar revisões de acesso
1. Acesse o [portal do Azure](https://portal.azure.com/) e selecione o aplicativo **Azure AD Privileged Identity Management** no painel.
2. Clique na seção **Revisões de acesso** do painel.
3. Selecione a análise de acesso que você deseja gerenciar.

Na folha de detalhes da revisão de acesso, há uma série de opções para o gerenciamento da revisão.

![Botões de análise de acesso do PIM - captura de tela](./media/pim-how-to-complete-review/PIM_review_buttons.png)

### <a name="remind"></a>Lembrar
Se uma análise de acesso é configurada para que os usuários examinem a si mesmos, o botão **Lembrar** envia uma notificação. 

### <a name="stop"></a>Stop
Todas as revisões de acesso têm uma data de término, mas você pode usar o botão **Parar** para concluí-las mais cedo. Se quaisquer usuários ainda não tiverem sido examinados até este momento, eles não poderão ser após você parar a análise. Não é possível reiniciar uma análise após ela ter sido interrompida.

### <a name="apply"></a>Aplicar
Após uma análise de acesso ser concluída, seja porque você atingiu a data de término ou a interrompeu manualmente, o botão **Aplicar** implementa o resultado da análise. Se o acesso de um usuário foi negado na análise, esta é a etapa que removerá sua atribuição de função.  

### <a name="export"></a>Exportação
Se você quiser aplicar os resultados da revisão de acesso manualmente, poderá exportar a revisão. O botão **Exportar** começará a baixar um arquivo CSV. Você pode gerenciar os resultados no Excel ou em outros programas que abrem arquivos CSV.

### <a name="delete"></a>Excluir
Se você não estiver mais interessado na revisão, exclua-a. O botão **Excluir** remove a análise do aplicativo PIM.

> [!IMPORTANT]
> Você não receberá nenhum aviso antes da exclusão, portanto, certifique-se de que deseja excluir a revisão. 

## <a name="next-steps"></a>Próximas etapas

- [Começar uma revisão de acesso para funções de diretório do Azure AD no PIM](pim-how-to-start-security-review.md)
- [Executar uma revisão de acesso de minhas funções de diretório do Azure AD no PIM](pim-how-to-perform-security-review.md)
