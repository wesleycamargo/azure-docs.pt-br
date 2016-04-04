<properties
   pageTitle="Como exigir a autenticação multifator | Microsoft Azure"
   description="Aprenda a exigir MFA (multi-factor authentication) para identidades com privilégios com a extensão de Privileged Identity Management do Azure Active Directory."
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

# Privileged Identity Management do Azure AD: Como exigir o MFA

Recomendamos que você exija a autenticação multifator para todos os administradores.

## Solicitação de MFA no Privileged Identity Management do Azure AD

Quando você entrar como administrador PIM, receberá alertas que sugerem que contas com privilégios devem exigir MFA (autenticação multifator). Clique no alerta de segurança no painel PIM e uma nova folha será aberta com uma lista das contas de administrador que devem exigir MFA. É possível exigir a MFA ao selecionar várias funções e clicar no botão **Corrigir**, ou você pode clicar nas reticências ao lado das funções individuais e clicar no botão **Corrigir**.

Além disso, você pode alterar o requisito de MFA para uma função específica, clicando na função na seção Funções do painel e então habilitando a MFA para essa função ao clicar em **Configurações** na folha da função e depois selecionar **Habilitar** em autenticação multifator.

## Como o PIM do Azure AD valida a MFA

> [AZURE.IMPORTANT] Como as contas da Microsoft (por exemplo, @outlook.com, @live.com ou @hotmail.com) atualmente não têm suporte para se registrar no Azure MFA, os usuários não serão aceitos como administradores temporários para funções altamente privilegiadas. Se os usuários precisarem continuar a gerenciar cargas de trabalho usando uma conta da Microsoft, converta-os em administradores permanentes por enquanto.

Há duas opções de validação de MFA quando um usuário ativa uma função.

A maneira mais simples é contar com o Azure MFA para usuários que ativam uma função privilegiada. Para fazer isso, primeiro verifique se esses usuários são licenciados, se necessário, e se estão registrados no Azure MFA. Para saber mais sobre isso, confira [Introdução à Azure Multi-Factor Authentication na nuvem](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md#assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users). Observe que é recomendável, mas não obrigatório, configurar o Azure AD para impor a MFA aos usuários quando eles entram. Isso ocorre porque as verificações de MFA serão feitas pelo próprio PIM do Azure AD.

Alternativamente, se os usuários se autenticam no local, você pode fazer com que o provedor de identidade seja responsável pela MFA. Por exemplo, se você tiver configurado os Serviços de Federação do AD para exigir a autenticação baseada em cartão inteligente antes de acessar o Azure AD, o [Protegendo recursos de nuvem com Azure Multi-Factor Authentication e AD FS](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md) inclui instruções para configurar o AD FS a fim de enviar solicitações ao Azure AD. Quando um usuário tenta ativar uma função e o PIM do Azure AD aceita que a MFA já tenha sido validada para o usuário quando receber as declarações apropriadas.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0323_2016-->