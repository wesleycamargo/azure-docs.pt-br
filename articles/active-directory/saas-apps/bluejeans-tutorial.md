---
title: 'Tutorial: integração do Azure Active Directory com o BlueJeans | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o BlueJeans.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: jeedes
ms.openlocfilehash: cff1512c56dba9907adbf1bb4452f11d47d0787d
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095221"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Tutorial: Integração do Azure Active Directory ao BlueJeans

Neste tutorial, você aprende a integrar o BlueJeans ao Azure AD (Azure Active Directory).

A integração do BlueJeans ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao BlueJeans.
- É possível permitir que os usuários se conectem automaticamente ao BlueJeans (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao BlueJeans, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do BlueJeans

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o BlueJeans por meio da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-bluejeans-from-the-gallery"></a>Adicionando o BlueJeans por meio da galeria

Para configurar a integração do BlueJeans ao Azure AD, você precisa adicionar o BlueJeans à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o BlueJeans por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **BlueJeans**, selecione **BlueJeans** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![BlueJeans na lista de resultados](./media/bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o BlueJeans, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do BlueJeans é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do BlueJeans.

Para configurar e testar o logon único do Azure AD com o BlueJeans, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do BlueJeans](#creating-a-bluejeans-test-user)** – para ter um equivalente de Brenda Fernandes no BlueJeans que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo BlueJeans.

**Para configurar o logon único do Azure AD com o BlueJeans, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **BlueJeans**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](./media/bluejeans-tutorial/tutorial_general_301.png)

4. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](./media/bluejeans-tutorial/tutorial_bluejeans_editurl.png)

5. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do BlueJeans](./media/bluejeans-tutorial/tutorial_bluejeans_url.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.BlueJeans.com`

    > [!NOTE]
    > O valor de logon não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao Cliente do BlueJeans](https://support.bluejeans.com/contact) para obter o valor.

6. Na página **Certificado de assinatura SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para fazer o download do **Certificado (Base64)** e salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/bluejeans-tutorial/tutorial_bluejeans_certficate.png) 

7. Na seção **Configurar o BlueJeans**, copie a URL apropriada, de acordo com suas necessidades.

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![Configuração do BlueJeans](./media/bluejeans-tutorial/tutorial_bluejeans_configure.png) 

8. Em outra janela do navegador da Web, faça logon em seu site de empresa do **BlueJeans** como administrador.

9. Acesse **ADMINISTRADOR \> CONFIGURAÇÕES DE GRUPO \> SEGURANÇA**.

    ![Admin](./media/bluejeans-tutorial/IC785868.png "Admin")

10. Na seção **SEGURANÇA**, execute as seguintes etapas:

    ![Logon Único do SAML](./media/bluejeans-tutorial/IC785869.png "Logon Único do SAML")

    a. Selecione **Logon Único do SAML**.

    b. Selecione **Habilitar provisionamento automático**.

11. Siga em frente com as seguintes etapas:

    ![Caminho de Certificado](./media/bluejeans-tutorial/IC785870.png "Caminho de Certificado")

    a. Clique em **Escolher um Arquivo** para carregar o certificado codificado em base 64 que você baixou do portal do Azure.

    b. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Na caixa de texto **URL de Alteração de Senha**, cole o valor da **URL de Alteração de Senha** copiado do portal do Azure.

    d. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

12. Siga em frente com as seguintes etapas:

    ![Salvar Alterações](./media/bluejeans-tutorial/IC785874.png "Salvar Alterações")

    a. Na caixa de texto **ID de usuário**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. Na caixa de texto **Email**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Clique em **SALVAR ALTERAÇÕES**.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](./media/bluejeans-tutorial/create_aaduser_01.png) 

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](./media/bluejeans-tutorial/create_aaduser_02.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="creating-a-bluejeans-test-user"></a>Criando um usuário de teste do BlueJeans

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no BlueJeans. O BlueJeans dá suporte ao provisionamento automático de usuário, que é habilitado por padrão. Você pode encontrar [aqui](bluejeans-provisioning-tutorial.md) mais detalhes de como configurar o provisionamento automático de usuário.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **BlueJeans** como administrador.

2. Acesse **ADMINISTRADOR \> GERENCIAR USUÁRIOS \> ADICIONAR USUÁRIO**.

    ![Admin](./media/bluejeans-tutorial/IC785877.png "Admin")

    >[!IMPORTANT]
    >A guia **ADICIONAR USUÁRIO** só estará disponível se, na **guia SEGURANÇA**, a opção **Habilitar provisionamento automático** estiver desmarcada. 

3. Na seção **ADICIONAR USUÁRIO**, execute as etapas a seguir:

    ![Adicionar Usuário](./media/bluejeans-tutorial/IC785886.png "Adicionar Usuário")

    a. Na caixa de texto **Nome**, insira o nome do usuário como **Brenda**.

    b. Na **Sobrenome** texto, digite o sobrenome do usuário, como **simon**.

    c. Na caixa de texto **Escolher um Nome de Usuário do BlueJeans**, insira o nome de usuário, como **Brendafernandes**

    d. Na caixa de texto **Criar uma Senha**, insira sua senha.

    e. Na caixa de texto **Empresa**, insira a sua Empresa.

    f. Na caixa de texto **Endereço de Email**, insira o email do usuário como **brittasimon@contoso.com**.

    g. Na caixa de texto **Criar uma ID de Reunião do BlueJeans**, insira sua ID de reunião.

    h. Na caixa de texto **Escolher uma Senha de Moderador**, insira a sua senha.

    i. Clique em **CONTINUAR**.

    ![Adicionar Usuário](./media/bluejeans-tutorial/IC785887.png "Adicionar Usuário")

    J. Clique em **ADICIONAR USUÁRIO**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do BlueJeans ou as APIs fornecidas pelo BlueJeans para provisionar as contas de usuário do Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao BlueJeans.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **BlueJeans**.

    ![Configurar o logon único](./media/bluejeans-tutorial/tutorial_bluejeans_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco BlueJeans no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo BlueJeans.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/bluejeans-tutorial/tutorial_general_203.png
