---
title: 'Tutorial: Integração do Azure Active Directory ao BorrowBox | Microsoft Docs'
description: Saiba como configurar logon único entre o Azure Active Directory e o BorrowBox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dd8e4178-9a63-492a-bd48-782e94e404af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a36b87f0a440bb2bf8d8d75cf0d29dbd521ff43f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56174989"
---
# <a name="tutorial-azure-active-directory-integration-with-borrowbox"></a>Tutorial: Integração do Azure Active Directory ao BorrowBox

Neste tutorial, você saberá como integrar o BorrowBox ao Azure AD (Azure Active Directory).

A integração do BorrowBox com Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao BorrowBox.
- Você pode permitir que os usuários entrem automaticamente no BorrowBox (Logon Único) com as contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao BorrowBox, os itens a seguir serão necessários:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do BorrowBox

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o BorrowBox da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-borrowbox-from-the-gallery"></a>Adicionar o BorrowBox da galeria
Para configurar a integração do BorrowBox ao Azure AD, é necessário adicionar o BorrowBox da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o BorrowBox da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![image](./common/selectazuread.png)

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![image](./common/a_select_app.png)
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![image](./common/a_new_app.png)

4. Na caixa de pesquisa, digite **BorrowBox**, selecione **BorrowBox** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![image](./media/borrowbox-tutorial/tutorial_borrowbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o BorrowBox, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do BorrowBox é equivalente a um usuário do Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no BorrowBox precisa ser estabelecida.

Para configurar e testar o logon único do Azure AD com o BorrowBox, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do BorrowBox](#create-a-borrowbox-test-user)** - para ter um equivalente de Brenda Fernandes no BorrowBox vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo portal do BorrowBox.

**Para configurar o logon único do Azure AD com o BorrowBox, execute as seguintes etapas:**

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **BorrowBox**, selecione **Logon único**.

    ![image](./common/B1_B2_Select_SSO.png)

2. Na caixa de diálogo **Selecionar um método de conexão única**, selecione o modo **SAML** para ativar o logon único.

    ![image](./common/b1_b2_saml_sso.png)

3. Na página **Definir logon único com SAML**, clique no botão **Editar** para abrir o diálogo **Configuração básica de SAML**.

    ![image](./common/b1-domains_and_urlsedit.png)

4. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_url.png)

     a. Clique em **definir URLs adicionais**.

    b. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://fe.bolindadigital.com/wldcs_bol_fo/b2i/mainPage.html?b2bSite=<ID>`

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_url1.png)

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao Cliente do BorrowBox](mailto:borrowbox@bolinda.com) para obter o valor.

5. O aplicativo BorrowBox espera as declarações SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **Atributos e reivindicações do usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **User Attributes & Claims**.

    ![image](./media/borrowbox-tutorial/i4-attribute.png)

6. Na seção **Reivindicações do usuário** da caixa de diálogo **Atributos e reivindicações do usuário**, configure o atributo do token SAML conforme mostrado na imagem acima e execute as seguintes etapas:
    
     a. Clique no ícone **Editar** para abrir a caixa de diálogo **Gerenciar declarações de usuário**.

    ![image](./media/borrowbox-tutorial/i2-attribute.png)

    ![image](./media/borrowbox-tutorial/i3-attribute.png)

    b. Na lista **Atributo de origem**, selecione **user.mail**.

    c. Clique em **Salvar**. 

7. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o certificado apropriado de acordo com seus requisitos e salve-o no computador.

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_certificate.png) 

8. Para configurar o logon único no lado do **BorrowBox** é necessário enviar o certificado/metadados que você baixou do portal do Azure para a [equipe de suporte do BorrowBox](mailto:borrowbox@bolinda.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![image](./common/d_users_and_groups.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![image](./common/d_adduser.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![image](./common/d_userproperties.png)

     a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.
 
### <a name="create-a-borrowbox-test-user"></a>Criar um usuário de teste do BorrowBox

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no BorrowBox. O BorrowBox dá suporte a provisionamento just-in-time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o BorrowBox, se ele ainda não existir.
>[!Note]
>Se você precisar criar um usuário manualmente, contate a  [equipe de suporte do BorrowBox](mailto:borrowbox@bolinda.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao BorrowBox.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![image](./common/d_all_applications.png)

2. Na lista de aplicativos, escolha **BorrowBox**.

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_app.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![image](./common/d_leftpaneusers.png)

4. Selecione o botão **Adicionar** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![image](./common/d_assign_user.png)

4. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

5. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco BorrowBox no Painel de Acesso, você deverá entrar automaticamente no aplicativo do BorrowBox.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
