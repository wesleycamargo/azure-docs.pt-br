---
title: "Como concluir uma análise de acesso | Microsoft Docs"
description: "Depois de iniciar uma análise de acesso no Azure AD Privileged Identity Management, saiba como concluí-la e exibir os resultados"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: abc2d3dd-afd5-42cf-8a17-6c11f5674c35
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/30/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a5a9d2aeb5d9d13d0561e21ea90160511659d6a3


---
# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Como concluir uma análise de acesso no Azure AD Privileged Identity Management
os administradores de função com privilégios podem examinar o acesso privilegiado após uma [revisão de segurança ter sido iniciada](active-directory-privileged-identity-management-how-to-start-security-review.md). O Azure AD PIM (Privileged Identity Management) automaticamente enviará um email solicitando que os usuários revisem seu acesso. Se um usuário não recebeu um email, você pode enviar para ele as instruções em [Como executar uma análise de segurança](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Depois do período de revisão de segurança, ou todos os usuários terminarem a autorrevisão, siga as etapas neste artigo para gerenciar a revisão e ver os resultados.

## <a name="manage-security-reviews"></a>Gerenciar revisões de segurança
1. Acesse o [portal do Azure](https://portal.azure.com/) e selecione o aplicativo **Azure AD Privileged Identity Management** no painel.
2. Clique na seção **Revisões de acesso** do painel.
3. Selecione a análise de acesso que você deseja gerenciar.

Na folha de detalhes da análise de acesso, há uma série de opções para o gerenciamento da análise.

![Botões de análise de acesso do PIM - captura de tela][1]

### <a name="remind"></a>Lembrar
Se uma análise de acesso é configurada para que os usuários examinem a si mesmos, o botão **Lembrar** envia uma notificação. 

### <a name="stop"></a>Parar
Todas as revisões de acesso têm uma data de término, mas você pode usar o botão **Parar** para concluí-las mais cedo. Se quaisquer usuários ainda não tiverem sido examinados até este momento, eles não poderão ser após você parar a análise. Não é possível reiniciar uma análise após ela ter sido interrompida.

### <a name="apply"></a>Aplicar
Após uma análise de acesso ser concluída, seja porque você atingiu a data de término ou a interrompeu manualmente, o botão **Aplicar** implementa o resultado da análise. Se o acesso de um usuário foi negado na análise, esta é a etapa que removerá sua atribuição de função.  

### <a name="export"></a>Exportação
Se você desejar aplicar os resultados da revisão de segurança manualmente, poderá exportar a revisão. O botão **Exportar** começará a baixar um arquivo CSV. Você pode gerenciar os resultados no Excel ou em outros programas que abrem arquivos CSV.

### <a name="delete"></a>Excluir
Se você não estiver mais interessado na revisão, exclua-a. O botão **Excluir** remove a análise do aplicativo PIM.

> [!IMPORTANT]
> Você não receberá nenhum aviso antes da exclusão, portanto, certifique-se de que deseja excluir a revisão.
> 
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png



<!--HONumber=Nov16_HO3-->


