---
title: "Configurar aplicativos SaaS para colaboração B2B no Azure Active Directory | Microsoft Docs"
description: "Exemplos de código e do PowerShell para colaboração B2B do Azure Active Directory"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c05cd490ee9125f7e5182cb502db6f4e9390094
ms.openlocfilehash: 3f41fdaa4f0ec31c9f11f2826b5cb9ccbf4db30d


---

# <a name="configure-saas-apps-for-b2b-collaboration"></a>Configurar aplicativos SaaS para colaboração B2B

A colaboração B2B do Azure Active Directory (Azure AD) funciona com a maioria dos aplicativos que se integram ao Azure AD. Nesta seção, examinaremos as instruções de como configurar alguns aplicativos populares de SAS para uso com o Azure AD B2B.
Antes de examinarmos as instruções específicas do aplicativo, confira aqui algumas regras gerais:

* Lembre-se de que para a maioria dos aplicativos, o provisionamento de usuário precisa acontecer manualmente (ou seja, os usuários também precisam ser criados manualmente no aplicativo).

* Para aplicativos que dão suporte ao provisionamento automático (como o Dropbox), haverá a criação de convites separados a partir dos aplicativos. Usuário deve aceitar cada convite.

* Nos Atributos de Usuário, sempre defina o Identificador de Usuário como user.mail (para atenuar problemas com UPD danificado em usuários convidados)


##<a name="dropbox-for-business"></a>DropBox for Business

Para que os usuários possam entrar usando a conta da organização, o Dropbox for Business deve ser configurado manualmente para usar o Azure AD como um provedor de identidade SAML. O Dropbox for Business não pode solicitar ou permitir de outra forma que os usuários entrem usando o Azure AD se não tiver sido configurado para isso.

1. Adicione o aplicativo DropBox for Business no Azure AD, conforme mostra a captura de tela.

  ![adicionar o Dropbox ao Azure AD](media/active-directory-b2b-configure-saas-apps/add-dropbox.png)

  ![adicionar o Dropbox ao Azure AD](media/active-directory-b2b-configure-saas-apps/add-app-dialog.png)

2. Configure o aplicativo.

  ![Configurar o logon único para o aplicativo](media/active-directory-b2b-configure-saas-apps/configure-app-sso.png)

3. Selecione a configuração de logon único e altere o identificador de usuário para user.mail (seu UPN por padrão)

4. Baixe o certificado a ser usado para a configuração do DropBox.

  ![baixar certificado](media/active-directory-b2b-configure-saas-apps/download-certificate.png)

5. Use a opção Configurar DropBox (o processo está explicado detalhadamente nas capturas de tela abaixo)

6. Obtenha a URL de logon único do SAML a ser usada na configuração.

7. Obtenha a URL de logon da página de configuração do DropBox.

  ![entrar no Dropbox](media/active-directory-b2b-configure-saas-apps/sign-in-to-dropbox.png)

  ![Menu Dropbox](media/active-directory-b2b-configure-saas-apps/dropbox-menu.png)

  ![Caixa de diálogo de autenticação do Dropbox recolhida](media/active-directory-b2b-configure-saas-apps/dropbox-auth-01.png)

  ![Caixa de diálogo de autenticação do Dropbox expandida](media/active-directory-b2b-configure-saas-apps/dropbox-auth-02.png)

8. Carregue o certificado e cole a URL de logon único do SAML aqui.

  ![Colar a URL de SSO do SAML](media/active-directory-b2b-configure-saas-apps/paste-single-sign-on-URL.png)

9. Configure o provisionamento automático de usuário no Portal do Azure.

  ![configurar o provisionamento automático de usuários](media/active-directory-b2b-configure-saas-apps/set-up-automatic-provisioning.png)

10. Após o provisionamento no aplicativo DropBox, o usuário convidado/membro receberá um convite separado do DropBox. Os convidados devem aceitá-lo clicando no link, a fim de usar o logon único no DropBox.

## <a name="box"></a>Box
Esta seção descreve como permitir que os usuários autentiquem um usuário convidado no Box com a respectiva conta do Azure AD usando federação baseada no protocolo SAML. Como parte desse procedimento, é necessário carregar metadados no Box.com.

1. Adicionar o Box dos aplicativos da empresa

2. Configurar o logon único

  ![Configurar logon único do Box](media/active-directory-b2b-configure-saas-apps/configure-box-sso.png)

3. Primeiro, certifique-se de que a URL de logon esteja definida corretamente para o Box no Portal de Gerenciamento do Azure. Essa é a URL de seu locatário Box.com e deve seguir o formato: https://.box.com.

4. Esteja ciente do fato de que o Identificador não é aplicável para este aplicativo, mas ainda aparece como o campo obrigatório.

5. Identificador de usuário para user.mail (para garantir o SSO para contas de convidado)

6. Criar um novo certificado SAML

7. Para começar a configurar seu locatário do Box.com a fim de usar o Azure Active Directory como um provedor de identidade, comece baixando o seguinte arquivo de metadados e salve-o localmente em seu computador: baixe o arquivo de metadados (lembre-se de torná-lo ativo)

8. Encaminhe esse arquivo de metadados à equipe de suporte do Box. A equipe de suporte configura o logon único para você.

9. Configurar o provisionamento automático de usuário do Azure AD.

  ![Autorizar o Azure AD a conectar-se ao Box](media/active-directory-b2b-configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Os convidados também devem resgatar seu convite no aplicativo Box.

## <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriedades de usuário de colaboração B2B](active-directory-b2b-user-properties.md)
* [Adicionar um usuário de colaboração B2B a uma função](active-directory-b2b-add-guest-to-role.md)
* [Delegar convites de colaboração B2B](active-directory-b2b-delegate-invitations.md)
* [Grupos dinâmicos e colaboração B2B](active-directory-b2b-dynamic-groups.md)
* [Código de colaboração B2B e exemplos do PowerShell](active-directory-b2b-code-samples.md)
* [Tokens de usuário de colaboração B2B](active-directory-b2b-user-token.md)
* [Mapeamento de declarações do usuário de colaboração B2B](active-directory-b2b-claims-mapping.md)
* [Compartilhamento externo do Office 365](active-directory-b2b-o365-external-user.md)
* [Limitações atuais da colaboração B2B](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


