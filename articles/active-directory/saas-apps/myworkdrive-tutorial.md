---
title: 'Tutorial: Integração do Azure Active Directory ao MyWorkDrive | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: f0e2c499619df938bd6f4b05757ba607a9edf244
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55183349"
---
# <a name="tutorial-azure-active-directory-integration-with-myworkdrive"></a>Tutorial: Integração do Azure Active Directory ao MyWorkDrive

Neste tutorial, você aprenderá como integrar o MyWorkDrive ao Microsoft Azure AD (Azure Active Directory).

A integração do MyWorkDrive ao Microsoft Azure AD oferece os seguintes benefícios:

- É possível controlar no Microsoft Azure AD quem tem acesso ao MyWorkDrive.
- É possível permitir que seus usuários façam logon automaticamente no MyWorkDrive (logon único) com as contas do Microsoft Azure AD deles.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure AD ao MyWorkDrive, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura com logon único do MyWorkDrive habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o MyWorkDrive da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-myworkdrive-from-the-gallery"></a>Adicionar o MyWorkDrive da galeria

Para configurar a integração do MyWorkDrive ao Microsoft Azure AD, será necessário adicionar o MyWorkDrive por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o MyWorkDrive por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **MyWorkDrive**, selecione **MyWorkDrive** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![MyWorkDrive na lista de resultados](./media/myworkdrive-tutorial/tutorial_myworkdrive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o MyWorkDrive, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Microsoft Azure AD precisa saber qual usuário do MyWorkDrive é equivalente a um usuário do Microsoft Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure AD e o usuário relacionado no MyWorkDrive.

Para configurar e testar o logon único do Microsoft Azure AD com o MyWorkDrive, é necessário concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do MyWorkDrive](#creating-a-myworkdrive-test-user)** - para ter um equivalente de Brenda Fernandes no MyWorkDrive que esteja vinculado à representação do usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure AD no portal do Azure e configurará o logon único no aplicativo do MyWorkDrive.

**Para configurar o logon único do Microsoft Azure AD com o MyWorkDrive, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **MyWorkDrive**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração Básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações de logon único de Domínio e URLs do MyWorkDrive](./media/myworkdrive-tutorial/tutorial_myworkdrive_url.png)

    Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de Domínio e URLs do MyWorkDrive](./media/myworkdrive-tutorial/tutorial_myworkdrive_url1.png)

     Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SERVER.DOMAIN.COM>/Account/Login-saml` 

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon.  Insira o nome do host do MyWorkDrive Server da sua empresa: por exemplo,
    > 
    > URL de Resposta: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > URL de logon:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Entre em contato com a equipe de suporte ao cliente MyWorkDrive se você não tiver certeza de como configurar seu próprio nome de host e certificado SSL para esses valores.

6. Na página **Certificado de assinatura SAML**, na seção **Certificado de assinatura SAML**, clique em Copiar **ícone** para copiar **URL de metadados da federação de aplicativo** e salve-o no seu computador..

    ![O link de download do Certificado](./media/myworkdrive-tutorial/tutorial_myworkdrive_certificate.png)

7. Em uma janela de navegador da web diferente, faça logon como administrador de segurança do MyWorkDrive.

8. No MyWorkDrive Server no painel de administração, clique em **ENTERPRISE** e execute as seguintes etapas:

    ![O administrador](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

     a. Habilitar **SSO de SAML/ADFS**.

    b. Selecione **SAML - Microsoft Azure Active Directory**

    c. Na caixa de texto **Url Metadados da Federação de Aplicativos do Azure**, cole o valor de **URL de Metadados da Federação de Aplicativos** que você copiou do portal do Azure.

    d. Clique em **Salvar**

    >[!NOTE]
    >Para obter informações adicionais, consulte o [artigo de suporte do MyWorkDrive Azure AD](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

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

### <a name="creating-a-myworkdrive-test-user"></a>Criando um usuário de teste do MyWorkDrive

Nesta seção, você criará uma usuária chamada Brenda Fernandes no MyWorkDrive. Trabalhe com a equipe de suporte  [MyWorkDrive](mailto:support@myworkdrive.com) para adicionar os usuários à plataforma MyWorkDrive. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao MyWorkDrive.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, escolha **MyWorkDrive**.

    ![Configurar o logon único](./media/myworkdrive-tutorial/tutorial_myworkdrive_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do MyWorkDrive no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo MyWorkDrive.
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
