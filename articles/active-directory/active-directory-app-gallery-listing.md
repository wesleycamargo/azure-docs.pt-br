<properties
   pageTitle="Listando seu aplicativo na galeria de aplicativos do Azure Active Directory"
   description="Um guia abrangente para recursos para desenvolvedores do Azure Active Directory"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/30/2015"
   ms.author="mbaldwin"/>


# Listando seu aplicativo na galeria de aplicativos do Azure Active Directory

Para listar um aplicativo que oferece suporte a logon único com o Azure Active Directory na [Galeria do Azure AD](http://azure.microsoft.com/marketplace/active-directory/all/) e no Azure Marketplace, a equipe do Azure AD precisará verificar sua integração. Isso requer que você envie as informações a seguir para <waadpartners@microsoft.com>:

- O nome de usuário e a senha de uma conta de usuário de teste em uma instância de teste do Azure Active Directory que é capaz de entrar em seu aplicativo
- URL e/ou instruções sobre como esse usuário de teste deve entrar no aplicativo
- Forneça uma breve descrição de como os clientes criam a conexão entre seu aplicativo e o Azure AD. Isso poderia ser:
  - Incorporado ao aplicativo usando a estrutura de consentimento do Azure AD
  - Configurando o Azure AD como um provedor de identidade (SAML 2.0, WS-Federation ou OpenID Connect) usando o portal administrativo de um aplicativo
  - Entrando em contato com alguém na sua organização para criar a conexão manualmente
- Se seu aplicativo oferecer suporte à configuração do Azure AD como um provedor de identidade por meio de um portal administrativo de autoatendimento, forneça também uma conta de administrador de teste para verificação.

Depois que o teste for concluído, o aplicativo poderá ser listado na galeria de aplicativos do Azure Active Directory e no Azure Marketplace. Responda às perguntas abaixo e envie para <waadpartners@microsoft.com>.

> Nome da empresa:
>
> Site da empresa:
>
> Nome do aplicativo:
>
> Descrição do aplicativo (limite de 256 caracteres):
>
> Site do aplicativo (informativo):
>
> Site de suporte técnico do aplicativo ou informações de contato:
>
> Você integrou seu aplicativo ao Azure AD e o testou (consulte [cenários de autenticação do Azure Active Directory](active-directory-authentication-scenarios.md))?
>
> ID do cliente do aplicativo, conforme mostrado nos detalhes do aplicativo em https://manage.windowsazure.com:
>
> URL de inscrição do aplicativo onde os clientes vão para se inscrever e/ou comprar o aplicativo:
>
> Escolha até três categorias em que seu aplicativo deva ser listado (para saber as categorias disponíveis, consulte o [Azure Active Directory Marketplace](http://go.microsoft.com/fwlink/?LinkId=327881)):
>
> Anexe o ícone pequeno do aplicativo (arquivo PNG, 45px por 45px, cor de plano de fundo sólida):
>
> Anexe o ícone grande do aplicativo (arquivo PNG, 215px por 215px, cor de plano de fundo transparente):
>
> Anexe o logotipo do aplicativo (arquivo PNG, 150px por 122px, cor de plano de fundo transparente):
 

<!---HONumber=August15_HO6-->