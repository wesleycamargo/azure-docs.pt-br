---
title: "Introdução ao Azure AD Privileged Identity Management | Microsoft Docs"
description: Saiba como gerenciar identidades privilegiadas com o aplicativo Azure Active Directory Privileged Identity Management no portal do Azure.
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 2299db7d-bee7-40d0-b3c6-8d628ac61071
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: billmath
ms.custom: pim ; H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 17cdff033cc3dbb199d11c3b8ac1acbc92499877
ms.contentlocale: pt-br
ms.lasthandoff: 07/10/2017

---
# <a name="start-using-azure-ad-privileged-identity-management"></a>Começar a usar o Azure AD Privileged Identity Management
Com o Privileged Identity Management do Azure Active Directory (AD), você pode gerenciar, controlar e monitorar o acesso em sua organização. Esse escopo inclui o acesso a recursos no Azure AD e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

Este artigo lhe mostra como adicionar o aplicativo do Azure AD PIM o painel do portal do Azure.

## <a name="add-the-privileged-identity-management-application"></a>Adicionar o aplicativo Privileged Identity Management
Antes de usar o Azure AD Privileged Identity Management, você precisa adicionar o aplicativo ao painel do portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador global do seu diretório.
2. Se sua organização tiver mais de um diretório, selecione seu nome de usuário no canto superior direito do portal do Azure. Selecione o diretório onde você deseja usar o PIM.
3. Selecione **Mais serviços** e use a caixa de texto Filtrar para procurar **Azure AD Privileged Identity Management**.
4. Marque **Fixar no painel** e então clique em **Criar**. O aplicativo Privileged Identity Management é aberto.

Se você for a primeira pessoa a usar o Azure AD Privileged Identity Management em seu diretório, receberá automaticamente as funções **Administrador de segurança** e **Administrador com privilégios de função** no diretório. Somente os administradores com privilégios de função podem gerenciar atribuições de função de usuários. Além disso, você pode optar por executar o [assistente de segurança.](active-directory-privileged-identity-management-security-wizard.md) que orienta você durante a experiência inicial de detecção e atribuição.

## <a name="navigate-to-your-tasks"></a>Navegue até as tarefas
Depois que o Azure AD Privileged Identity Management estiver configurado, você verá a folha de navegação sempre que abrir o aplicativo. Use essa folha para realizar suas tarefas de gerenciamento de identidade.

![Tarefas de nível superior para o PIM - captura de tela](./media/active-directory-privileged-identity-management-getting-started/PIM_Tasks_New.png)

* **Minhas Funções** leva à lista de funções atribuídas a você. Essa seção é onde você ativará as funções para as quais está qualificado.
* **Aprovar Solicitações (Versão prévia)** exibe uma lista de solicitações de ativação de usuários pendentes no seu diretório. [Saiba mais.](./privileged-identity-management/azure-ad-pim-approval-workflow.md)
* **Solicitações Pendentes (Versão prévia)** exibe todas as solicitações atuais feitas a serem ativadas.
* **Examinar Acesso** leva você a qualquer revisão de acesso pendente que tenha de ser concluída, esteja você examinando o acesso para si mesmo ou para outra pessoa.
* As **Funções de Diretório do Azure AD** localizadas na seção ‘Gerenciar’ é o painel onde os administradores de função com privilégios gerenciam atribuições de função, alteram configurações de ativação de função, iniciam revisões de acesso e muito mais. As opções desse painel estão desabilitadas para todos que não forem administradores de função com privilégios.

## <a name="next-steps"></a>Próximas etapas
A [visão geral do Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) inclui mais detalhes sobre como você pode gerenciar o acesso administrativo em sua organização.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png

