<properties
   pageTitle="Introdução ao Azure AD Privileged Identity Management | Microsoft Azure"
   description="Saiba como gerenciar identidades privilegiadas com o aplicativo Azure Active Directory Privileged Identity Management no portal do Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/29/2016"
   ms.author="kgremban"/>

# Introdução ao Azure AD Privileged Identity Management


O Azure AD (Active Directory) Privileged Identity Management permite gerenciar, controlar e monitorar suas identidades com privilégios e o acesso a recursos no Azure AD e em outros Microsoft Online Services, como o Office 365 ou o Microsoft Intune.

Este artigo lhe mostra como adicionar o aplicativo do Azure AD PIM o painel do portal do Azure.

## Adicionar o aplicativo Privileged Identity Management

Antes de usar o Azure AD Privileged Identity Management, você precisa adicionar o aplicativo ao painel do portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador global do seu diretório.
2. Se sua organização tiver mais de um diretório, clique em seu nome de usuário no canto superior direito do portal do Azure e selecione o diretório no qual você usará o PIM.
3. Selecione **Novo** > **Segurança + Identidade** > **Azure AD Privileged Identity Management**.

    ![Habilitar o PIM no portal][1]

4. Marque **Fixar no painel** e clique em **Criar**. O aplicativo Privileged Identity Management será aberto.


Se você for a primeira pessoa a usar o Azure AD Privileged Identity Management em seu diretório, o [assistente segurança](active-directory-privileged-identity-management-security-wizard.md) lhe guiará pela experiência de atribuição inicial. Depois disso, você se tornará automaticamente o primeiro **Administrador de segurança** e um **administrador de função com privilégios** do diretório. Somente um administrador com função privilegiada pode acessar esse aplicativo para gerenciar o acesso de outros administradores.

Se tiver sido atribuído a uma ou mais funções, você terá a opção de **Ativar minhas funções**. Se for um administrador com privilégios de função, você também verá uma opção para **Gerenciar funções privilegiadas**.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

A [visão geral do Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) inclui mais detalhes sobre como você pode gerenciar o acesso administrativo em sua organização.

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png

<!----HONumber=AcomDC_0706_2016-->