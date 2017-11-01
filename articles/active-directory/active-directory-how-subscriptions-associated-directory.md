---
title: "Como adicionar uma assinatura do Azure existente ao diretório do Azure AD | Microsoft Docs"
description: "Como adicionar uma assinatura existente ao seu diretório do Azure AD"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/17/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: abf207a3ceec708a828170936f7dc7948ccf34a9
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2017
---
# <a name="how-to-add-an-azure-subscription-to-azure-active-directory"></a>Como adicionar uma assinatura do Azure ao Azure Active Directory
Este artigo aborda informações sobre a relação entre uma assinatura do Azure e o Azure AD (Azure Active Directory) e como adicionar uma assinatura existente ao diretório do Azure AD. A sua assinatura do Azure tem uma relação de confiança com o Azure AD, o que significa que ela confia no diretório para autenticar usuários, serviços e dispositivos. Várias assinaturas podem confiar no mesmo diretório, mas cada assinatura confia em apenas um diretório. 

Essa relação de confiança que uma assinatura tem com um diretório é diferente da relação que uma assinatura tem com todos os outros recursos no Azure (sites, bancos de dados e assim por diante). Se uma assinatura expira, o acesso aos outros recursos associados à assinatura também é interrompido. Mas um diretório do Azure AD permanece no Azure e você pode associar outra assinatura a ele e gerenciar os usuários do diretório usando a nova assinatura.

Todos os usuários têm um único diretório inicial que os autentica, mas eles também podem ser convidados em outros diretórios. No Azure AD, você pode ver os diretórios dos quais a sua conta de usuário é um membro ou convidado.

## <a name="to-add-an-existing-subscription-to-your-azure-ad-directory"></a>Para adicionar uma assinatura existente ao diretório do Azure AD
Você deve entrar com uma conta existente tanto no diretório atual ao qual a assinatura está associada quanto no diretório ao qual deseja adicioná-la. 

1. Entre no [Centro de Contas do Azure](https://account.azure.com/Subscriptions) com uma conta que seja o Administrador da Conta da assinatura cuja propriedade você deseja transferir.
2. Certifique-se de que o usuário que você deseja que seja o proprietário da assinatura esteja no diretório de destino.
3. Clique em **Transferir assinatura**.
4. Especifique o destinatário. O destinatário recebe automaticamente um email com um link de aceitação.
5. O destinatário clica no link e segue as instruções, incluindo inserir suas informações de pagamento. Quando o destinatário for bem-sucedido, a assinatura será transferida. 
6. O diretório padrão da assinatura é alterado para o diretório no qual o usuário está.

Para saber mais, veja [Transferir a propriedade de assinatura do Azure para outra conta](../billing/billing-subscription-transfer.md).

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como alterar administradores para uma assinatura do Azure, consulte [Transferir a propriedade de uma assinatura do Azure para outra conta](../billing/billing-subscription-transfer.md)
* Para saber mais sobre como o acesso aos recursos é controlado no Microsoft Azure, confira [Noções básicas sobre o acesso aos recursos do Azure](active-directory-understanding-resource-access.md)
* Para saber sobre como atribuir funções no AD do Azure, veja [Atribuindo funções de administrador no Active Directory do Azure](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
