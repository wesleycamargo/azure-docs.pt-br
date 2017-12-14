---
title: "Notificações de provisionamento de conta | Microsoft Docs"
description: "Saiba como garantir que você será notificado sobre problemas relacionados ao provisionamento de usuário que exigem sua atenção habilitando notificações de provisionamento de conta."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: a637aac7-f06b-48ef-a66d-639835a8edec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.openlocfilehash: d056ed0b7030079f3ea47a56c65e08a9ecfe4583
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="account-provisioning-notifications"></a>Notificações de provisionamento de conta
Com o provisionamento de usuário, você pode automatizar o processo de gerenciar usuários em aplicativos SaaS de terceiros. <br>
Embora esse seja um processo automatizado, sua interação com esse processo às vezes é necessária. <br>
É, por exemplo, o caso, quando a senha da conta que você configurou para trocar dados com um aplicativo SaaS de terceiros expirou. 

Ao habilitar notificações de provisionamento de conta você pode garantir que será notificado sobre problemas relacionados ao provisionamento de usuário, que exigem sua atenção.

Você ativa ou desativa notificações de provisionamento de conta como parte de sua configuração de provisionamento de usuário de um aplicativo SaaS de terceiro.

![Provisionamento do usuário][1] 

Para ativar notificações de provisionamento de conta, marque a caixa de seleção relacionada na página de diálogo **Confirmação** e digite o alias de email do destinatário.

![Notificações de provisionamento de conta][2]

Você pode inserir uma lista de distribuição como destinatário; no entanto, é importante observar que o email de notificação contém links para relatórios que só são acessíveis pelos administradores do AD do Azure.

Se você tiver notificações de provisionamento de conta habilitadas, receberá emails sobre problemas críticos relacionados ao provisionamento do usuário. No entanto, para evitar uma sobrecarga de emails, você só receberá um email de notificação por dia para cada aplicativo SaaS para o qual o email de notificação está habilitado.

## <a name="related-articles"></a>Artigos relacionados
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [Automatizar o provisionamento/desprovisionamento de usuários para aplicativos SaaS](active-directory-saas-app-provisioning.md)
* [Personalizando os mapeamentos de atributos para provisionamento de usuários](active-directory-saas-customizing-attribute-mappings.md)
* [Escrevendo expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtros de escopo para provisionamento de usuários](active-directory-saas-scoping-filters.md)
* [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](active-directory-scim-provisioning.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png
