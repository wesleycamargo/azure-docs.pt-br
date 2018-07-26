---
title: Senhas proibidas dinamicamente no Azure AD
description: Proibir senhas fracas do seu ambiente com senhas proibidas dinamicamente pelo Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: dfeacb266d6aa6a43e49a39bd19c9699ef65ce82
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162007"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminar senhas incorretas na organização

|     |
| --- |
| A lista de senhas proibidas personalizada e a proteção por senha do Azure AD são recursos de visualização pública do Azure Active Directory. Para obter mais informações sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Os líderes do setor orientam que você não utilize a mesma senha em vários lugares, que torne-a complexa e para não simplificá-la como Senha123. Como as organizações podem garantir que os usuários estejam seguindo as diretrizes? Como podem garantir que os usuários não utilizam senhas comuns ou senhas que são conhecidas por estarem incluídas em violações de dados recentes?

## <a name="global-banned-password-list"></a>Lista de senhas proibidas globalmente

A Microsoft está sempre trabalhando para se manter um passo à frente dos cibercriminosos. Portanto, a equipe do Azure AD Identity Protection procura continuamente por senhas comumente usadas e comprometidas. Em seguida, bloqueiam as senhas consideradas muito comuns na lista de senhas proibidas globalmente. Os criminosos cibernéticos também usam estratégias semelhantes em seus ataques, portanto, a Microsoft não publica o conteúdo dessa lista publicamente. Essas senhas vulneráveis são bloqueadas antes de tornarem-se uma ameaça real aos clientes da Microsoft. Para obter mais informações sobre os esforços atuais de segurança, consulte o [Relatório de inteligência de segurança da Microsoft](https://www.microsoft.com/security/intelligence-report).

## <a name="preview-custom-banned-password-list"></a>Versão prévia: lista de senhas proibidas personalizadas

Algumas organizações podem querer levar a segurança um passo adiante, adicionando suas próprias personalizações no topo da lista de senhas proibidas, em que a Microsoft chama de lista de senhas proibidas personalizada. Os clientes empresariais, como a Contoso, podem optar por bloquear variantes de nomes de marca, termos específicos da empresa ou outros itens.

A lista de senhas proibidas personalizada e a capacidade de habilitar a integração do Active Directory local são gerenciadas usando o portal do Azure.

![Modificar a lista de senhas proibidas personalizada em Métodos de Autenticação no portal do Azure](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>Cenários híbridos locais

A proteção de contas somente na nuvem é útil, mas muitas organizações mantêm cenários híbridos, incluindo o Windows Server Active Directory local. É possível instalar a proteção por senha do Azure AD para agentes do Windows Server Active Directory (versão prévia) local para estender as listas de senhas proibidas à infraestrutura existente. Agora, os usuários e administradores que alteram, definem ou redefinem senhas locais precisam obedecer à mesma política de senha que os usuários somente na nuvem.

## <a name="how-does-the-banned-password-list-work"></a>Como funciona a lista de senhas proibidas

A lista de senhas proibidas corresponde as senhas na lista convertendo a cadeia de caracteres para minúsculas e comparando com as senhas proibidas conhecidas em uma distância de edição de 1 com correspondência difusa.

Exemplo: a palavra senha está bloqueada para uma organização
   - Um usuário tenta definir a senha para "P@ssword" que é convertida em "senha" e porque é uma variante de senha bloqueada.
   - Um administrador tenta definir uma senha de usuário para "Senha123!" que convertida para "senha123!" e porque é uma variante de senha que está bloqueada.

Cada vez que um usuário redefinir ou alterar a senha do Azure AD passará por esse processo para confirmar que não está na lista de senhas proibidas. Essa verificação é incluída em cenários híbridos usando redefinição de senha de autoatendimento, sincronização de senha e autenticação de passagem.

## <a name="license-requirements"></a>Requisitos de licença

Os benefícios da lista global de senhas proibidas aplicam-se a todos os usuários do Azure AD (Active Directory do Azure).

A lista de senhas proibidas personalizada requer licenças do Azure AD Basic.

A proteção por senha do Azure AD para o Windows Server Active Directory requer licenças do Azure AD Premium. 

Informações adicionais sobre licenciamento, incluindo custos, podem ser encontradas no [site de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>O que os usuários veem

Quando um usuário tentar redefinir uma senha para alguma que seria proibida, a seguinte mensagem de erro será exibida:

Infelizmente, sua senha contém uma palavra, uma frase ou um padrão que pode ser facilmente adivinhado. Tente novamente com uma senha diferente.

## <a name="next-steps"></a>Próximas etapas

* [Configurar a lista de senhas proibidas personalizada](howto-password-ban-bad.md)
* [Habilitar agentes de proteção por senha do Azure AD local](howto-password-ban-bad-on-premises.md)
