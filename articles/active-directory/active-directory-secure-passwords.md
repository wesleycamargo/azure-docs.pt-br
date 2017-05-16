---
title: "Proteger senhas no Azure AD e redefinir senhas que são bloqueadas pelo Bloqueio Inteligente de Senha | Microsoft Docs"
description: "Explica o que é um locatário do Azure AD e como gerenciar o Azure por meio do Active Directory do Azure"
services: active-directory
documentationcenter: 
author: markvi
writer: v-lorisc
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/02/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 910ca62c331df0825e1403c19fe2f53672a7359d
ms.contentlocale: pt-br
ms.lasthandoff: 05/08/2017


---
# <a name="secure-passwords--in-azure-ad-and-reset-passwords-that-get-blocked-by-smart-password-lockout"></a>Proteger senhas no Azure AD e redefinir senhas que são bloqueadas pelo Bloqueio Inteligente de Senha
Este artigo discute práticas recomendadas que você pode seguir como um usuário ou como um administrador para proteger suas contas de serviço de Conta da Microsoft e do Azure AD (Azure Active Directory). 

 >[!NOTE]
 >Os administradores do Azure AD podem redefinir senhas de usuário clicando no nome do diretório. No [portal de gerenciamento do Azure](https://manage.windowsazure.com), escolha a página Usuários, clique no nome do usuário e em Redefinir Senha. 
 >

O Azure AD inclui as seguintes abordagens comuns para proteger senhas:
 *    Requisitos de comprimento de senha
 *    Requisitos de "complexidade" de senha
 *    Expiração de senha regular e periódica 

Para obter informações sobre os recursos de gerenciamento de senha, confira [Gerenciar senhas no Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-manage-passwords). 

## <a name="azure-ad-password-protection"></a>Proteção de senha do Azure AD
O Azure AD e o Sistema de Conta da Microsoft usam abordagens comprovadas do setor para assegurar a proteção segura de senhas de usuário e administrador. 

Esta seção discute como o Azure AD protege senhas usando os seguintes métodos:
 *    Senhas banidas dinamicamente
 *    Bloqueio de Senha Inteligente

Para obter informações sobre o gerenciamento de senha com base na pesquisa atual, confira o white paper [Diretrizes de senha](http://aka.ms/passwordguidance). 

### <a name="dynamically-banned-passwords"></a>Senhas banidas dinamicamente
O Azure AD e o Sistema de Conta da Microsoft garantem a proteção por senha banindo dinamicamente todas as senhas comumente usadas. A equipe de Proteção de Identidade do Azure ID analisa rotineiramente as listas de senhas proibidas, impedindo que os usuários selecionem senhas usadas comumente. Este serviço está disponível para o Azure AD e os clientes do serviço de Conta da Microsoft. 

Durante a criação de senhas, é importante que os administradores incentivem os usuários a escolher frases de senha incomuns que incluam uma combinação exclusiva de letras, números e caracteres. Isso ajuda a tornar as senhas de usuário praticamente impossíveis de serem comprometidas. 

**Listas de violação**

O Azure AD está sempre trabalhando para se manter um passo à frente dos cibercriminosos. Uma forma de fazermos isso é impedir que os usuários criem senhas que estão na lista de ataque atual.

A equipe do Azure AD Identity Protection analisa continuamente as senhas que são usadas comumente. Os cibercriminosos também usam estratégias semelhantes para preparar seus ataques, como criar uma [tabela rainbow](https://en.wikipedia.org/wiki/Rainbow_table) de violação de hashes de senha. 

A Microsoft analisa continuamente as [violações de dados](https://www.privacyrights.org/data-breaches) para manter uma lista de senhas banidas atualizada dinamicamente, que garante que as senhas vulneráveis sejam banidas antes de se tornarem realmente uma ameaça para os clientes do Azure AD. Para obter mais informações sobre nossas iniciativas de segurança atuais, confira o [Relatório de inteligência de segurança da Microsoft](https://www.microsoft.com/security/sir/default.aspx). 

### <a name="smart-password-lockout"></a>Bloqueio de Senha Inteligente

Quando o Azure AD detecta que um cibercriminoso potencial está tentando invadir uma senha de usuário, bloqueamos a conta de usuário com o Bloqueio de Senha Inteligente. O Azure AD foi projetado para determinar o risco associado a sessões de logon específico. 

Usando os dados de segurança mais recentes, aplicamos semântica de bloqueio a ameaças cibernéticas. Assim, os usuários não são bloqueados, no caso de um cibercriminoso ter obtido acesso a senhas de usuário na rede.

Se um usuário estiver bloqueado do Azure AD, a tela será semelhante ao exemplo mostrado abaixo:

  ![Bloqueado no Azure AD](./media/active-directory-secure-passwords/locked-out-azuread.png)
  
E, para outras contas da Microsoft, a tela é semelhante ao exemplo mostrado abaixo:

  ![Bloqueado em uma conta da Microsoft](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Para obter informações sobre o gerenciamento de senhas no Azure Active Directory, confira [Como funciona o gerenciamento de senha](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-how-it-works).

  >[!NOTE]
  >Se você é administrador do Azure AD, convém usar o [Windows Hello](https://www.microsoft.com/en-us/windows/windows-hello) para evitar que os usuários criem senhas tradicionais.
  >

## <a name="next-steps"></a>Próximas etapas
[Como atualizar sua própria senha](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-update-your-own-password)<br>
[Os fundamentos do gerenciamento de identidades do Azure](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity)<br>
[Como obter percepções operacionais com relatórios de gerenciamento de senhas](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-get-insights#view-password-reset-activity)



