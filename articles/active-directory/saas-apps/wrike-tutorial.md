---
title: 'Tutorial: Integração do Azure Active Directory ao Wrike | Microsoft Docs'
description: Aprenda como configurar o logon único entre o Azure Active Directory e o Wrike.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 894b7520-5136-4973-a1ba-942a9f7f0a03
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96ceeec3acd77af7174f1394677f6a52b18dec67
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207187"
---
# <a name="tutorial-azure-active-directory-integration-with-wrike"></a>Tutorial: Integração do Azure Active Directory ao Wrike

Neste tutorial, você aprenderá como integrar o Wrike ao Azure AD (Azure Active Directory).

A integração do Wrike ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Wrike.
- Você pode permitir que seus usuários façam logon automaticamente no Wrike (Logon único) com suas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure ao Wrike, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do Wrike

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Wrike da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-wrike-from-the-gallery"></a>Adicionando o Wrike da galeria

Para configurar a integração do Wrike ao Azure Active Directory, você precisa adicionar o Wrike da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Wrike da galeria, execute os seguintes passos:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Wrike**, selecione **Wrike** no painel de resultados e clique no botão **Add** para adicionar o aplicativo.

    ![Wrike na lista de resultados](./media/wrike-tutorial/tutorial_wrike_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Wrike, com base em um usuário de teste chamado "Britta Simon".

Para que o logon único funcione, o Microsoft Azure Active Directory precisa saber qual usuário do Wrike é um usuário do Microsoft Azure Active Directory. Em outras palavras, um relacionamento de link entre um usuário do Microsoft Azure Active Directory e o usuário relacionado no Wrike precisa ser estabelecido.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o Wrike, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[ Criando um usuário de teste do Wrike ](#creating-a-wrike-test-user)** - para ter um equivalente de Brenda Fernandes no Wrike que esteja vinculado à representação do usuário do Microsoft Azure Active Directory.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure Active Directory no portal do Azure e configurará o logon único em seu aplicativo do Wrike.

**Para configurar o logon único do Microsoft Azure Active Directory com o Wrike, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Wrike**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

    ![Domínio do Wrike e informações de logon único de URLs](./media/wrike-tutorial/tutorial_wrike_url.png)
    
     a. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    b. Na caixa de texto **URL de Logon**, digite uma URL: `https://www.wrike.com/login/`

    ![Domínio do Wrike e informações de logon único de URLs](./media/wrike-tutorial/tutorial_wrike_url1.png)

5. Na página **Certificado de Assinatura SAML**, na seção **Certificado de Assinatura SAML**, clique em **Download** para fazer o download do **XML de Metadados da Federação** e salve o arquivo de metadados no computador.

    ![O link de download do Certificado](./media/wrike-tutorial/tutorial_wrike_certificate.png) 

6. Para configurar o logon único no **Wrike**, você precisa enviar o **XML de metadados de federação** para [equipe de suporte do Wrike](mailto:support@team.wrike.com) baixado. Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

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

### <a name="creating-a-wrike-test-user"></a>Criando um usuário de teste do Wrike

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Wrike. O Wrike oferece suporte ao provisionamento just-in-time, que é ativado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Wrike, se ele ainda não existir.
>[!Note]
>Se você precisar criar um usuário manualmente, entre em contato com a  [equipe de suporte do Wrike](mailto:support@team.wrike.com).

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Wrike.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Wrike**.

    ![Configurar o logon único](./media/wrike-tutorial/tutorial_wrike_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar na peça do Wrike no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Wrike.
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
