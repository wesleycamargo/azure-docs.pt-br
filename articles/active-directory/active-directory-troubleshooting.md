---
title: "Solução de problemas: o item “Active Directory” está ausente ou não está disponível | Microsoft Docs"
description: "O que fazer quando o item de menu do Active Directory não aparece no Portal de Gerenciamento do Azure."
services: active-directory
documentationcenter: na
author: bryanla
manager: mtillman
editor: 
ms.assetid: 3383020d-6397-43ea-b7aa-c6a9d6a1e3df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: bryanla
ms.openlocfilehash: 27a884b89224ed5765e9fcef134e711e3952ff72
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="troubleshooting-active-directory-item-is-missing-or-not-available"></a>Solução de problemas: o item “Active Directory” está ausente ou não está disponível
Muitas das instruções para usar os recursos e serviços do Azure Active Directory começam com "Vá para o Portal de Gerenciamento do Azure e clique em **Active Directory**." Mas o que fazer se o item de menu ou extensão do Active Directory não for exibido ou se ele está marcado como **Não disponível**? Este tópico foi criado para ajudar. Ele descreve as condições sob as quais o **Active Directory** não é exibido ou não está disponível e explica como proceder.

## <a name="active-directory-is-missing"></a>Active Directory ausente
Normalmente, um item do **Active Directory** aparece no menu de navegação à esquerda. As instruções nos procedimentos do Active Directory do Azure pressupõem que este item está no modo de exibição.

![Captura de tela: Active Directory no Azure](./media/active-directory-troubleshooting/typical-view.png)

O item Active Directory é exibido no menu de navegação à esquerda quando qualquer uma das seguintes condições for verdadeira. Caso contrário, o item não será exibido.

* O usuário atual conectado com uma conta da Microsoft (anteriormente conhecida como uma Windows Live ID).
  
    OU
* O locatário do Azure tem um diretório e a conta atual é um administrador do diretório.
  
    OU
* O locatário do Azure tem pelo menos um namespace de Controle de Acesso do AD do Azure (ACS). Para obter mais informações, confira [Namespace de Controle de Acesso](https://msdn.microsoft.com/library/azure/gg185908.aspx).
  
    OU
* O locatário do Azure tem pelo menos um provedor da Autenticação Multifator do Azure. Para obter mais informações, confira [Administrando Provedores da Autenticação Multifator do Azure](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

Para criar um namespace de Controle de Acesso ou um provedor de Autenticação Multifator, clique em **+Novo** > **Serviços de Aplicativos** > **Active Directory**.

Para obter direitos administrativos em um diretório, peça que o administrador atribua uma função de administrador à sua conta. Para obter detalhes, confira [Atribuindo funções de administrador](active-directory-assign-admin-roles-azure-portal.md).

## <a name="active-directory-is-not-available"></a>Active Directory não disponível
Quando você clica em **+Novo** > **Serviços de Aplicativos**, um item do **Active Directory** é exibido. Especificamente, o item Active Directory é exibido quando qualquer um dos recursos do Active Directory, como o Diretório, Controle de Acesso ou provedor do Multi-Factor Auth, estão disponíveis para o usuário atual.

No entanto, enquanto a página está carregando, o item fica esmaecido e é marcado como **Não Disponível**. Este é um estado temporário. Se você aguardar alguns segundos, o item será disponibilizado. Se o atraso for prolongado, atualizar a página da Web geralmente resolve o problema.

![Captura de tela: Active Directory não disponível](./media/active-directory-troubleshooting/not-available.png)

