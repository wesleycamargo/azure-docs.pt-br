---
title: "Notificações de Relatórios do Active Directory do Azure"
description: "Como usar as notificações de relatórios do Active Directory do Azure para entradas suspeitas."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ae6d4b0e-5931-4cb3-98bf-9be91b381c92
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: dhanyahk;markvi
ms.custom: oldportal
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 90ba006f25c27e1dc24c85fdc4ef1d5e2a8d0e3d
ms.contentlocale: pt-br
ms.lasthandoff: 12/29/2016

---
# Notificações de Relatórios do Active Directory do Azure
<a id="azure-active-directory-reporting-notifications" class="xliff"></a>
## Quais relatórios geram notificações por email
<a id="what-reports-generate-email-notifications" class="xliff"></a>
Neste momento, apenas o relatório de atividade de entrada irregular dispara as notificações por email.

## O que é uma "Entrada irregular"?
<a id="what-is-an-irregular-sign-in" class="xliff"></a>
Entradas irregulares são aquelas que foram identificadas por nossos algoritmos de aprendizado de máquina, com base em uma condição de “viagem impossível” combinada a um dispositivo e um local de entrada anômalos. Isso pode indicar que um hacker tentou entrar usando essa conta.

## Quem recebe as notificações por email?
<a id="who-receives-the-email-notifications" class="xliff"></a>
O email é enviado para todos os administradores globais as quais foi atribuída uma licença do Active Directory Premium. Para garantir que ele seja entregue, podemos enviá-lo também ao endereço de Email alternativo dos administradores. Os administradores devem incluir aad-alerts-noreply@mail.windowsazure.com em suas listas de remetentes seguros, para que eles não deixem de ver o email.

## Com que frequência esses emails são enviados?
<a id="how-often-are-these-emails-sent" class="xliff"></a>
O email será enviado se 10 novas atividades de entrada irregular ocorrerem nos últimos 30 dias, ou contando a partir da data em que o último email foi enviado, o período que for mais curto.

## Como acessar o relatório mencionado no email?
<a id="how-do-i-access-the-report-mentioned-in-the-email" class="xliff"></a>
Ao clicar no link, você será redirecionado à página do relatório no portal clássico do Azure. Para acessar o relatório, você precisa ser ambos:

* Um administrador ou coadministrador de sua assinatura do Azure
* Um administrador global no diretório e ter uma licença do Active Directory Premium atribuída a você. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).

## Posso desativar esses emails?
<a id="can-i-turn-off-these-emails" class="xliff"></a>
Sim. Para desligar as notificações relacionadas a entradas anômalas no Portal clássico do Azure, clique em **Configurar** e selecione **Desabilitado** na seção **Notificações**.

## O que vem a seguir
<a id="whats-next" class="xliff"></a>
* Curioso sobre que relatórios de segurança, auditoria e atividade estão disponíveis? Verifique [Relatórios de segurança, auditoria e atividade do AD do Azure](active-directory-view-access-usage-reports.md)
* [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md)
* [Adicionar identidade visual da empresa às páginas de Entrada e do Painel de acesso](active-directory-add-company-branding.md)


