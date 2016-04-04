<properties
   pageTitle="Como alterar ou exibir as configurações padrão de uma função | Microsoft Azure"
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
   ms.date="03/17/2016"
   ms.author="kgremban"/>

# Privileged Identity Management do Azure AD: como alterar ou exibir as configurações padrão de ativação para uma função

## Alterando e exibindo a ativação de função padrão
1. No painel, clique na função a ser configurada da tabela de funções.
2. Clique em **Configurações**.
3. Defina a duração padrão da ativação em horas ao ajustar o controle deslizante ou ao digitar o número de horas no campo de texto.
4. Clique em **Habilitar** ou em **Desabilitar**, para enviar ou não as notificações sobre a ativação aos administradores.
5. Clique em **Habilitar** ou **Desabilitar** para permitir ou não que os administradores insiram informações de emissão de tíquetes em sua solicitação de ativação.
6. Clique em **Habilitar** ou **Desabilitar** para exigir ou não autenticação multifator para uma solicitação de ativação.

    Você não pode desabilitar o MFA para funções com altos privilégios do Azure AD e do Office365, incluindo:

    - Administrador global
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

7. Clique em **Habilitar** ou **Desabilitar** para permitir que os Administradores Globais sejam temporários.
8. Clique em **Salvar**.

Para obter mais informações sobre como usar MFA com PIM, consulte [Como Exigir MFA](active-directory-privileged-identity-management-how-to-require-mfa.md).

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0323_2016-->