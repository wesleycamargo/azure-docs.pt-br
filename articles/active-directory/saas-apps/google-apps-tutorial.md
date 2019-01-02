---
title: 'Tutorial: Integração do Azure Active Directory com o G Suite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2018
ms.author: jeedes
ms.openlocfilehash: bcea7848c7331ecd326f0ccb6ab9f543ce972205
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834682"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Tutorial: Integração do Azure Active Directory com o G Suite

Neste tutorial, você aprenderá a integrar o G Suite ao Microsoft Azure AD (Azure Active Directory).

A integração do G Suite ao Microsoft Azure AD oferece os seguintes benefícios:

- No Microsoft Azure AD, você pode controlar quem tem acesso ao G Suite.
- Você pode habilitar seus usuários a fazerem logon automaticamente no G Suite (logon único) com suas contas do Microsoft Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure AD com o G Suite, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do G Suite
- Uma assinatura do Google Apps ou uma assinatura do Google Cloud Platform.

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. Este documento foi criado usando o novo usuário, experiência de logon único. Se você ainda estiver usando o antigo, a instalação terá aparência distinta. Você pode habilitar a nova experiência nas configurações de logon único do aplicativo do G Suite. Vá para **Azure Active Directory, aplicativos empresariais**, selecione **G Suite**, selecione **Single Sign-on** e, em seguida, clique em **Tentar nossa nova experiência**.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

1. **P: Essa integração dá suporte para integração de SSO do Google Cloud Platform com o Microsoft Azure AD?**

    R: Sim. O Google Cloud Platform e o Google Apps compartilham a mesma plataforma de autenticação. Portanto, para fazer a integração com o GCP, será necessário configurar o SSO com o Google Apps.

2. **P: Os Chromebooks e outros dispositivos Chrome são compatíveis com o logon único do AD do AD do Azure?**
  
    R: Sim. Os usuários podem entrar em seus dispositivos Chromebook usando suas credenciais do Azure AD. Consulte este [artigo de suporte do G Suite](https://support.google.com/chrome/a/answer/6060880) para obter informações sobre o motivo dos usuários receberem solicitações de credenciais duas vezes.

3. **P: Se eu habilitar o logon único, os usuários conseguirão usar suas credenciais do Azure AD para entrar em qualquer produto do Google, como Google Sala de aula, GMail, Google Drive, YouTube e assim por diante?**

    R: Sim, dependendo de [qual G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) você quer habilitar ou desabilitar para sua organização.

4. **P: Eu posso habilitar logon único apenas para um subconjunto dos meus usuários do G Suite?**

    R: Não, ativar o logon único exige que todos os usuários do G Suite autentiquem-se imediatamente com as credenciais do Microsoft Azure AD. Como o G Suite não dá suporte a vários provedores de identidade, o provedor de identidade do ambiente do G Suite pode ser o Microsoft Azure AD ou o Google -- mas não os dois ao mesmo tempo.

5. **P: Se um usuário conectar através do Windows, ele será autenticado automaticamente no G Suite sem ser solicitado a fornecer uma senha?**

    R: Há duas opções para este cenário. Primeiro, os usuários podem entrar em dispositivos com Windows 10 por meio do [Ingresso no Active Directory do Azure](../device-management-introduction.md). Como alternativa, os usuários podem entrar em dispositivos com Windows que ingressaram em um domínio para um Active Directory local com logon único habilitado no AD do Azure por meio de uma implantação dos [Serviços de Federação do Active Directory (AD FS)](../hybrid/plan-connect-user-signin.md) . Ambas as opções exigem que você execute as etapas do tutorial a seguir para ativar o logon único entre o Microsoft Azure AD e o G Suite.

6. **Erro: Email Inválido**

    Para essa configuração, o atributo de email é necessário para os usuários entrarem. Esse atributo não pode ser definido manualmente.

    O atributo de email é populado automaticamente para qualquer usuário com uma licença válida do Exchange. Se o usuário não estiver habilitado para email, esse erro será recebido, o aplicativo precisa obter esse atributo para fornecer acesso.

    Você pode ir para portal.office.com com uma conta de Administrador, em seguida, clique no Centro de administração, cobrança, assinaturas, selecione sua assinatura do Office 365 e, em seguida, clique em atribuir a usuários, selecione os usuários que você deseja verificar a sua assinatura e no painel direito, clique em editar licenças.

    Depois que a licença do O365 for atribuída, pode levar alguns minutos para ser aplicado. Depois disso, o atributo user.mai será preenchido automaticamente e o problema deve ser resolvido.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o G Suite da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-g-suite-from-the-gallery"></a>Adicionar o G Suite da galeria

Para configurar a integração do G Suite ao Microsoft Azure AD, é necessário adicionar o G Suite da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o G Suite da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **G Suite**, selecione **G Suite** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![G Suite na lista de resultados](./media/google-apps-tutorial/tutorial_gsuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o G Suite, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Microsoft Azure AD precisa saber qual usuário do G Suite é equivalente a um usuário do Microsoft Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure AD e o usuário relacionado no G Suite.

Para configurar e testar o logon único do Microsoft Azure AD com o G Suite, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar de um usuário de teste do G Suite](#creating-a-g-suite-test-user)** – para ter um equivalente de Brenda Fernandes no G Suite vinculado à representação do usuário no Microsoft Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Microsoft Azure AD no Portal do Azure e configura o logon único no aplicativo G Suite.

**Para configurar o logon único do Microsoft Azure AD com o G Suite, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração do aplicativo **G Suite**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do G Suite](./media/google-apps-tutorial/tutorial_gsuite_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do G Suite](https://www.google.com/contact/) para obter esses valores.

5. Aplicativo do G Suite espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](./media/google-apps-tutorial/i3-attribute.png)

6. Na seção **Declarações de Usuário** do diálogo **Atributos de Usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:

     a. Clique em **Editar** para abrir a caixa de diálogo **Gerenciar declarações de usuário**.

    ![image](./media/google-apps-tutorial/i2-attribute.png)

    ![image](./media/google-apps-tutorial/i4-attribute.png)

    b. Da Lista **Atributo de origem**, selecione o valor do atributo.

    c. Clique em **Salvar**.

5. Na página **Certificado de assinatura SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para baixar o **Certificado (Base64)** e salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/google-apps-tutorial/tutorial_gsuite_certificate.png) 

6. Na seção **Configurar o G Suite**, copie a URL apropriada, de acordo com suas necessidades.

     a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![Configurar o G Suite](common/configuresection.png)

9. Abra uma nova guia no navegador e entre no [Console de Admin do G Suite](https://admin.google.com/) usando sua conta de administrador.

10. Clique em **Segurança**. Se você não enxergar o link, ele pode estar oculto sob o menu **Mais Controles** , na parte inferior da tela.

    ![Clique em Segurança.][10]

11. Na página **Segurança**, clique em **Configurar logon único (SSO).**

    ![Clique em SSO.][11]

12. Realize as seguintes alterações de configuração:

    ![Configure o SSO][12]

     a. Selecione **Configurar SSO com um provedor de identidade de terceiros**.

    b. No campo **URL da página de login** no G Suite, cole o valor de  **URL de login** que você copiou do portal do Azure.

    c. No campo **URL da página de saída** no G Suite, cole o valor de  **URL de logoff**  que você copiou do portal do Azure.

    d. No campo **Alterar URL de senha** no G Suite, cole o valor de  **Alterar URL da senha**  que você copiou do portal do Azure.

    e. No G Suite, para o **Certificado de verificação**, carregue o certificado baixado do Portal do Azure.

    f. Selecione **Usar um emissor de domínio específico**.

    g. Clique em **Salvar Alterações**.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_01.png) 

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_02.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No **nome de usuário** , digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="creating-a-g-suite-test-user"></a>Criar um usuário de teste do G Suite

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no G Suite Software. O G Suite dá suporte ao provisionamento automático, que é habilitado por padrão. Não há nenhuma ação para você nesta seção. Se um usuário ainda não existir no G Suite Software, um novo será criado quando você tentar acessar o G Suite Software.

> [!NOTE]
> Verifique se o usuário já existe no G Suite se o provisionamento no Microsoft Azure Active Directory não foi ativado antes de testar o logon único.

> [!NOTE]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Google](https://www.google.com/contact/).

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao G Suite.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **G Suite**.

    ![Configurar o logon único](./media/google-apps-tutorial/tutorial_gsuite_app.png)

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do G Suite no Painel de Acesso, deverá fazer logon automaticamente no aplicativo do G Suite.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png