---
title: Perguntas frequentes sobre o acesso condicional ao Azure Active Directory | Microsoft Docs
description: 'Perguntas frequentes sobre acesso condicional  '
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: dee29df176389f39907d302cedc6143c0d3e3322
ms.lasthandoff: 04/12/2017


---
# <a name="azure-active-directory-conditional-access-faq"></a>Acesso condicional ao Azure Active Directory

## <a name="which-applications-work-with-conditional-access-policies"></a>Quais aplicativos funcionam com políticas de acesso condicional?

**A:** Consulte [Aplicativos e navegadores que utilizam regras de acesso condicional no Azure Active Directory](active-directory-conditional-access-supported-apps.md).

---

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>As políticas de acesso condicional são impostas para colaboração B2B e usuários convidados?
**R:** As políticas são impostas para usuários de colaboração B2B. No entanto, em alguns casos, o usuário não poderá atender o requisito de política, por exemplo, se uma organização não der suporte à autenticação multifator. Atualmente, a política não é imposta para usuários convidados do SharePoint. A relação de convidado é mantida no SharePoint. As contas de usuários convidados não estão sujeitas a políticas de acesso no servidor de autenticação. O acesso de convidado pode ser gerenciado no SharePoint.

---

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>A política do SharePoint Online também se aplica ao OneDrive for Business?
**R:** Sim.

---

## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Por que não é possível definir uma política em aplicativos cliente, como o Word ou o Outlook?
**R:** Uma política de acesso condicional define os requisitos para acessar um serviço e é imposta quando ocorre a autenticação para esse serviço. A política não é definida diretamente em um aplicativo cliente mas, em vez disso, ela é aplicada quando chama um serviço. Por exemplo, um conjunto de políticas no SharePoint se aplica a clientes que chamam o SharePoint e a política definida no Exchange se aplica ao Outlook.

--- 

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Uma política de acesso condicional se aplica a contas de serviço?
**R:** As políticas de acesso condicional se aplicam a todas as contas de usuário. Isso inclui contas de usuário usadas como contas de serviço. Em muitos casos, uma conta de serviço que é executada de forma autônoma não é capaz de atender a uma política. É o caso, por exemplo, quando a MFA é necessária. Nesses casos, as contas de serviços podem ser excluídas de uma política, usando configurações de gerenciamento de política de acesso condicional. Saiba mais sobre como aplicar uma política aos usuários aqui.

---

## <a name="are-graph-apis-available-to-configure-configure-conditional-access-policies"></a>As APIs do Graph estão disponíveis para configurar as políticas de acesso condicional configuradas?
**A:** ainda não. 

---
