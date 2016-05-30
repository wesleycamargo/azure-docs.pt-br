<properties
   pageTitle="O Assistente de Segurança do Privileged Identity Management do Azure AD"
   description="Na primeira vez que você usar a extensão Privileged Identity Management do Azure Active Directory, verá um assistente de segurança. Este artigo descreve as etapas para usar o assistente."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# O Assistente de Segurança do Privileged Identity Management do Azure AD

Se você for a primeira pessoa a executar o Azure PIM (Privileged Identity Management) para sua organização, verá um assistente. O assistente ajuda você a entender os riscos à segurança de identidades com privilégios e como usar o PIM para reduzir esses riscos. Você não precisará fazer nenhuma alteração nas atribuições de função existentes no assistente, se preferir fazer isso posteriormente.

## O que esperar

Antes de sua organização começar a usar o PIM, todas as atribuições de função são permanentes: os usuários sempre estarão sempre nessas funções, mesmo se não precisarem dos privilégios no momento. A primeira etapa do assistente mostra uma lista de funções com privilégios altos e quantos usuários atualmente estão nessas funções. Você poderá analisar uma função específica para saber mais sobre os usuários se um ou mais não forem familiares.

A segunda etapa do assistente lhe fornece a oportunidade de alterar as atribuições de função do administrador.

> [AZURE.WARNING] É importante que você tenha pelo menos um administrador global e mais de um administrador de segurança com uma conta organizacional (não uma conta da Microsoft). Se houver apenas um administrador de segurança, a organização não poderá gerenciar o PIM se essa conta for excluída. Além disso, não altere as atribuições de função para temporárias se um usuário tiver uma conta da Microsoft. Se você planeja exigir o MFA para ativação para essa função, esse usuário será bloqueado fora da função.


Depois de ter feito alterações, o assistente não aparecerá mais. Na próxima vez que você ou outro administrador de segurança usar o PIM, você verá o painel PIM.

- Se você desejar adicionar ou remover usuários das funções ou alterar as atribuições de permanentes para temporárias, leia mais em [como adicionar ou remover uma função de usuário](active-directory-privileged-identity-management-how-to-add-role-to-user.md).
- Se desejar fornecer aos usuários mais acesso para gerenciar o PIM, leia mais em [como conceder acesso para gerenciar no PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).



## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0518_2016-->