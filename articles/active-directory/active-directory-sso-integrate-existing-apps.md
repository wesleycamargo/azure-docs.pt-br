<properties
   pageTitle="Integrar o SSO do Active Directory do Azure com os aplicativos existentes | Microsoft Azure"
   description="Gerencie os aplicativos de SaaS que você já usa habilitando a autenticação de logon único e o provisionamento do usuário no Active Directory do Azure."
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
   ms.date="10/16/2015"
   ms.author="kgremban; liviodlc"/>

# Integrar o SSO do Active Directory do Azure com os aplicativos existentes

## Visão geral

Configurar o logon único (SSO) para um aplicativo que sua organização já usa é um processo diferente de criar novas contas para um novo aplicativo. Há algumas etapas preliminares incluindo: mapeamento das identidades do usuário no aplicativo para as identidades do Active Directory (AD) do Azure e compreender como os usuários farão logon em um aplicativo depois dele ser integrado.

> [AZURE.NOTE]Para configurar o SSO para um aplicativo existente, você precisa ter direitos de administrador globais no AD do Azure e no aplicativo de SaaS.

## Mapeamento das contas de usuário

A identidade de um usuário geralmente tem um identificador exclusivo que pode ser um endereço de email ou o nome pessoal universal (UPN). Você precisará vincular (mapear) a identidade do aplicativo de cada usuário com sua respectiva identidade do AD do Azure. Existem algumas maneiras de fazer isso, dependendo do requisito de autenticação de seu aplicativo.

Para obter mais informações sobre como mapear as identidades do aplicativo com as identidades do AD do Azure, confira [Personalizando as declarações emitidas no token SAML](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) e [Personalizando os mapeamentos de atributos para o provisionamento](active-directory-saas-customizing-attribute-mappings.md).

## Compreensão da experiência de logon do usuário

Ao integrar o SSO para um aplicativo que já está em uso, é importante perceber que a experiência do usuário será afetada. Para todos os aplicativos, os usuários começarão usando suas credenciais do AD do Azure para entrar. Além disso, eles podem precisar usar um portal diferente para acessar os aplicativos.

O SSO de alguns aplicativos pode ser feito na interface de logon do aplicativo, mas em outros aplicativos, o usuário terá que passar por um portal central como ([Meus Aplicativos](http://myapps.microsoft.com) ou [Office365](http://portal.office.com/myapps)) para entrar. Saiba mais sobre os diferentes tipos de SSO e suas experiências de usuário em [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

Confira também *Suprimir o consentimento do usuário* no artigo [Orientando os desenvolvedores](active-directory-applications-guiding-developers-for-lob-applications.md).

## Artigos relacionados
links para suprimir o consentimento do usuário e outros subartigos de orientação do desenvolvedor

<!---HONumber=Oct15_HO4-->