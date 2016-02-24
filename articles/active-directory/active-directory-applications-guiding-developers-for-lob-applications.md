<properties
	pageTitle="AD do Azure e aplicativos: orientando os desenvolvedores | Microsoft Azure"
	description="Escrito para profissionais de TI, este artigo fornece diretrizes para a integração de aplicativos do Azure com o Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="kgremban"/>

# AD do Azure e aplicativos: orientando os desenvolvedores

## Visão geral

Este guia fornece uma visão geral do desenvolvimento de aplicativos LoB (linhas de negócios) do AD (Active Directory) do Azure e foi escrito especificamente para os administradores globais do Active Directory/Office 365.

A criação de aplicativos integrados ao AD do Azure oferece aos usuários em sua organização logon único com o Office 365. Ter o aplicativo no AD do Azure dá controle sobre a política de autenticação definida para o aplicativo. Para saber mais sobre acesso condicional e como proteger aplicativos com MFA (autenticação multifator), consulte o seguinte documento: [Configurando regras de acesso](active-directory-conditional-access-azuread-connected-apps.md)

Seu aplicativo precisa ser registrado para usar o Active Directory do Azure. Registrar o aplicativo permite que os desenvolvedores na sua organização autentiquem os membros de sua organização usando o AD do Azure e solicitem acesso aos seus recursos de usuário como email, calendário, documentos etc.

Qualquer membro do diretório (não convidados) pode registrar um aplicativo, também conhecido como *criação de um objeto de aplicativo*.

Registrar um aplicativo permite que qualquer usuário faça o seguinte:

- Obter uma identidade para o aplicativo que reconhece o AD do Azure
- Obter um ou mais segredos/chaves que o aplicativo pode usar para se autenticar no AD
- Marcar o aplicativo com um nome, logotipo, etc. no portal do Azure
- Aproveitar os recursos de autorização do AD do Azure para seu aplicativo
  - RBAC (controle de acesso baseado em função) de aplicativo
  - Active Directory do Azure como servidor de autorização oAuth (proteger uma API exposta pelo aplicativo)

- Declare permissões exigidas necessárias para que o aplicativo funcione conforme o esperado. Elas incluem:
	  - Permissões de aplicativo (somente administradores globais). Por exemplo:
	    - Associação de função em outro aplicativo do AD do Azure ou associação de grupo relativa a um recurso, grupo de recursos ou assinatura do Azure
	  - Permissões (qualquer usuário). Por exemplo:
	    - (AD do Azure) Entrar e ler o perfil
	    - (Exchange) Ler emails, enviar email
	    - (SharePoint) Leitura

> [AZURE.NOTE]Por padrão, qualquer membro pode registrar um aplicativo. Para saber como restringir as permissões para o registro de aplicativos para membros específicos, confira o documento [Como os aplicativos são adicionados ao AD do Azure](active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

Aqui está o que você, o administrador global, precisará fazer para ajudar os desenvolvedores a preparar o aplicativo para produção:

- Configurar regras de acesso (política de acesso/MFA)
- Configurar o aplicativo para exigir a atribuição de usuário e atribuir usuários
- Suprimir a experiência de consentimento do usuário padrão

## Configurando regras de acesso

Como mencionamos anteriormente, consulte o seguinte artigo para obter mais informações sobre como configurar regras de acesso para qualquer aplicativo.

[Configurando regras de acesso](active-directory-conditional-access-azuread-connected-apps.md).

## Configurar o aplicativo para exigir a atribuição de usuário e atribuir usuários

Por padrão, a atribuição de usuário não é necessária para que ele acesse um aplicativo. No entanto, se o aplicativo expõe funções ou se você deseja que o aplicativo seja exibido no painel de acesso do usuário, você deve exigir a atribuição de usuário e atribuir usuários e/ou grupos.

[Exigindo atribuição do usuário](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Se você for um assinante do AD do Azure Premium ou Enterprise Mobility Suite (EMS), é altamente recomendável utilizar grupos. A atribuição de grupos ao aplicativo permite delegar o gerenciamento de acesso contínuo ao proprietário do grupo. Você pode criar o grupo ou, se preferir, pedir à parte responsável da sua organização para criar o grupo usando o recurso de gerenciamento de grupos.

[Atribuindo usuários a um aplicativo](active-directory-applications-guiding-developers-assigning-users.md) [Atribuindo grupos a um aplicativo](active-directory-applications-guiding-developers-assigning-groups.md)

## Suprimindo o consentimento do usuário

Por padrão, o usuário precisará consentir à permissão necessária para fazer logon. A experiência de autorização, que está sendo solicitada a conceder permissões para um aplicativo, pode ser desconcertante para usuários que não estão familiarizados com a necessidade de tomar essa decisão.

Para aplicativos em que você confia, é possível consentir ao aplicativo em nome de todos os usuários em sua organização.

Para saber mais sobre o consentimento do usuário e sobre a experiência de consentimento no Azure, confira [Integrando aplicativos com o Active Directory do Azure](active-directory-integrating-applications.md).

##Artigos relacionados

- [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)

<!---HONumber=AcomDC_0211_2016-->