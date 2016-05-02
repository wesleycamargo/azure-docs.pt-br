<properties
   pageTitle="Introdução ao Azure AD Privileged Identity Management | Microsoft Azure"
   description="Saiba como gerenciar identidades privilegiadas com o aplicativo Azure Active Directory Privileged Identity Management no portal do Azure."
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

# Introdução ao Azure AD Privileged Identity Management


O Azure AD (Active Directory) Privileged Identity Management permite gerenciar, controlar e monitorar suas identidades com privilégios e o acesso a recursos no Azure AD e em outros Microsoft Online Services, como o Office 365 ou o Microsoft Intune.

Este artigo lhe mostra como adicionar o aplicativo do Azure AD PIM o painel do portal do Azure.

## Adicionar o aplicativo Privileged Identity Management

Antes de usar o Azure AD Privileged Identity Management, você precisa adicionar o aplicativo ao painel do portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador global do seu diretório.
2. Se sua organização tiver mais de um diretório, clique em seu nome de usuário no canto superior direito do portal do Azure e selecione o diretório no qual você usará o PIM.
3. Selecione o ícone **Novo** no painel de navegação esquerdo.
4. Selecione **Segurança + Identidade**.
5. Selecione **Azure AD Privileged Identity Management**.
6. Marque **Fixar no painel** e clique no botão **Criar**. O aplicativo Privileged Identity Management será aberto.


Se você for a primeira pessoa a usar o Azure AD Privileged Identity Management em seu diretório, o [assistente segurança](active-directory-privileged-identity-management-security-wizard.md) lhe guiará pela experiência de atribuição inicial. Após isso, você se tornará automaticamente o primeiro **Administrador de segurança** do diretório. Somente um administrador de segurança pode acessar esse aplicativo para gerenciar o acesso de outros administradores.

Caso contrário, se você tiver atribuído a uma ou mais funções por outro administrador de segurança, terá a opção de qual função ativar. Se você mesmo estiver em uma função de administrador de segurança, verá também uma opção para **Gerenciar Identidades**.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

A [visão geral do Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) inclui mais detalhes sobre como você pode gerenciar o acesso administrativo na sua organização.

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->