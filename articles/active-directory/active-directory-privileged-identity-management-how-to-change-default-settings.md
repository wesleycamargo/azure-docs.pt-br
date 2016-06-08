<properties
   pageTitle="Como gerenciar configurações de ativação de função | Microsoft Azure"
   description="Aprenda a alterar as configurações padrão para identidades com privilégios com a extensão Privileged Identity Management do Azure Active Directory."
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
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Como gerenciar as configurações de ativação de função no Privileged Identity Management do Azure AD

Um administrador de função com privilégios pode personalizar o Azure AD PIM (Privileged Identity Management) em sua organização, incluindo alterar a experiência de um usuário que está ativando uma atribuição de função temporária.

## Gerenciar as configurações de ativação de função

1. Acesse o [portal do Azure](https://portal.azure.com) e selecione o aplicativo **Azure AD Privileged Identity Management** do painel.
2. Selecione a função que você deseja gerenciar na tabela de funções.
3. Clique em **Configurações**.
4. Defina a duração padrão da ativação em horas ajustando o controle deslizante ou digitando o número de horas no campo de texto. O máximo é 72 horas.
5. Clique em **Habilitar** ou em **Desabilitar** para enviar ou não notificações sobre a ativação para os administradores. (Habilitar as notificações pode ajudar a detectar atividade de administrador não autorizado.)
6. Clique em **Habilitar** para permitir que os administradores insiram informações de emissão de tíquetes em sua solicitação de ativação. (Essas informações podem ser úteis durante a auditoria de acesso de função posteriormente.)
7. Clique em **Habilitar** ou **Desabilitar** para exigir ou não a autenticação multifator para uma solicitação de ativação.
8. Clique em **Salvar**.

Você não pode desabilitar o MFA para funções com altos privilégios do Azure AD e do Office365, incluindo:
- Administrador global
- Administrador de função com privilégios   
- Administrador da conta de usuário  
- Gravador de diretório  
- Suporte de camada 1 do parceiro  
- Suporte de camada 2 do parceiro  
- Administrador de cobrança  
- Administrador de segurança  
- Administrador do Exchange  
- Administrador de caixa de correio  
- Administrador do Skype for Business  
- Administrador do SharePoint  
- Administrador de conformidade  

Para obter mais informações sobre como usar MFA com PIM, consulte [Como Exigir MFA](active-directory-privileged-identity-management-how-to-require-mfa.md).

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->