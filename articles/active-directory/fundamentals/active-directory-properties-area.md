---
title: Adicionar informações de privacidade da sua organização no Azure Active Directory | Microsoft Docs
description: Como adicionar informações de privacidade da sua organização à área de Propriedades do Azure AD (Azure Active Directory).
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: lizross
ms.reviewer: bpham
ms.custom: it-pro
ms.openlocfilehash: f32eeee8f02cee32eb05e14aa98387a20cb2e626
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48903657"
---
# <a name="how-to-add-your-privacy-info-using-azure-active-directory"></a>Instruções: adicionar informações de privacidade com o Azure Active Directory
Este artigo explica como um administrador de locatário pode adicionar informações relacionadas à privacidade para o locatário do Azure Active Directory (Azure AD) de uma organização, por meio do portal do Azure.

É altamente recomendável adicionar o contato de privacidade global e a declaração de privacidade da sua organização, para que os funcionários internos e os convidados externos possam revisar suas políticas. Como as declarações de privacidade são exclusivamente criadas e personalizadas para cada empresa, é altamente recomendável que você entre em contato com um advogado para obter assistência.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Adicionar suas informações de privacidade no Azure AD
Você adiciona informações de privacidade da sua organização na área **Propriedades** do Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Para acessar a área de Propriedades e adicionar suas informações de privacidade

1.  Entre no portal do Azure como um administrador de locatário.

2.  Na barra de navegação à esquerda, selecione **Azure Active Directory**e, em seguida, selecione **Propriedades**.

    A área **Propriedades** é exibida.

    ![A área de Propriedades do Azure AD realçando a área de informações de privacidade](media/active-directory-properties-area/properties-area.png)

3.  Adicione as informações de privacidade de seus funcionários:

    - **Contato técnico.** Digite o endereço de email da pessoa de contato para suporte técnico dentro de sua organização.
    
    - **Contato de privacidade global.** Digite o endereço de email da pessoa de contato para consultas sobre a privacidade dos dados pessoais. Essa pessoa é também quem a Microsoft contatará se houver uma violação de segurança. Se não houver nenhuma pessoa listada aqui, a Microsoft entrará em contato com seus administradores globais.

    - **URL da política de privacidade.** Digite o link para documento de sua organização que descreve como a organização lida com a privacidade dos dados de convidados internos e externos.

        >[!Important]
        >Se você não incluir sua própria política de privacidade ou seu contato de privacidade, os convidados externos verão o texto na caixa **Permissões de Revisão**, que informa que **<_nome da sua organização_> não forneceu links para seus termos para você revisar**. Por exemplo, um usuário convidado verá essa mensagem quando receber um convite para acessar uma organização por meio de colaboração B2B.

        ![Caixa Permissões de Revisão de Colaboração B2B com mensagem](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas
- [Resgate do convite de colaboração do Azure Active Directory B2B](https://aka.ms/b2bredemption)
- [Adicionar ou alterar as informações de perfil de um usuário no Azure Active Directory](active-directory-users-profile-azure-portal.md)