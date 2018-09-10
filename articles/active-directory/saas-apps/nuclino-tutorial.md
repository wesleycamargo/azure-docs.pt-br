---
title: 'Tutorial: Integração do Azure Active Directory com o Nuclino | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Nuclino.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 74bbab82-5581-4dcf-8806-78f77c746968
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: jeedes
ms.openlocfilehash: 1a5346b98de48b1a2f8928c3c2bf30730588e9c1
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43145636"
---
# <a name="tutorial-azure-active-directory-integration-with-nuclino"></a>Tutorial: Integração do Azure Active Directory com o Nuclino

Neste tutorial, você aprenderá a integrar o Nuclino ao Azure AD (Azure Active Directory).

A integração do Nuclino com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Nuclino.
- Você pode permitir que os usuários façam logon automaticamente no Nuclino (Logon Único) com a conta do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Nuclino, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Nuclino habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Nuclino da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-nuclino-from-the-gallery"></a>Adicionando o Nuclino da galeria

Para configurar a integração do Nuclino ao Azure AD, você precisa adicionar o Nuclino da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Nuclino da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Nuclino**, selecione **Nuclino** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Nuclino na lista de resultados](./media/nuclino-tutorial/tutorial_nuclino_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Nuclino, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Nuclino é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Nuclino.

Para configurar e testar o logon único do Azure AD com o Nuclino, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Nuclino](#create-a-nuclino-test-user)** – para ter um equivalente de Brenda Fernandes no Nuclino vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo Nuclino.

**Para configurar o logon único do Azure AD com o Nuclino, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Nuclino**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/nuclino-tutorial/tutorial_nuclino_samlbase.png)

3. Na seção **Domínio e URLs do Nuclino**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de domínio e de URLs do Nuclino para logon único](./media/nuclino-tutorial/tutorial_nuclino_url1.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://api.nuclino.com/api/sso/<UNIQUE-ID>/metadata`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://api.nuclino.com/api/sso/<UNIQUE-ID>/acs`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador e a URL de resposta reais da seção **Autenticação**, que será explicada mais adiante neste tutorial.

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de domínio e de URLs do Nuclino para logon único](./media/nuclino-tutorial/tutorial_nuclino_url2.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://app.nuclino.com/<UNIQUE-ID>/login`

    > [!NOTE]
    > Esse valor não é real. Atualize esse valor com a URL de Logon real. Contate a [equipe de suporte ao cliente do Nuclino](mailto:contact@nuclino.com) para obter esse valor.

5. O aplicativo Nuclino espera as asserções SAML em um formato específico. Configure as seguintes declarações para o aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo. A captura de tela a seguir mostra um exemplo disso.

    ![Configurar o logon único](./media/Nuclino-tutorial/tutorial_attribute.png)

6. Clique na caixa de seleção **Exibir e editar todos os outros atributos de usuário** na seção **Atributos de Usuário** para expandir os atributos. Realize as seguintes etapas em cada um dos atributos exibidos:

    | Nome do atributo | Valor do atributo |
    | ---------------| --------------- |
    | first_name | user.givenname |
    | last_name | user.surname |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/nuclino-tutorial/tutorial_attribute_04.png)

    ![Configurar o logon único](./media/nuclino-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome**, digite o **nome do atributo** mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Clique em **OK**.

7. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/nuclino-tutorial/tutorial_nuclino_certificate.png)

8. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/nuclino-tutorial/tutorial_general_400.png)

9. Na seção **Configuração do Nuclino**, clique em **Configurar Nuclino** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Nuclino](./media/nuclino-tutorial/tutorial_nuclino_configure.png)

10. Em outra janela do navegador da Web, entre no site da empresa do Nuclino como administrador.

11. Clique no **ÍCONE**.

    ![Configuração do Nuclino](./media/nuclino-tutorial/configure1.png)

12. Clique em **SSO do Azure AD** e selecione **Configurações de equipe** na lista suspensa.

    ![Configuração do Nuclino](./media/nuclino-tutorial/configure2.png)

13. Selecione **Autenticação** no painel de navegação à esquerda.

    ![Configuração do Nuclino](./media/nuclino-tutorial/configure3.png)

14. Na seção **Autenticação**, execute as seguintes etapas:

    ![Configuração do Nuclino](./media/nuclino-tutorial/configure4.png)

    a. Selecione **SSO (logon único) baseado em SAML**.

    b. Copie o valor da **URL do ACS (você precisa copiar e colar isso no seu provedor de SSO)** e cole-o na caixa de texto **URL de Resposta** da seção **Domínio e URLs do Nuclino** no portal do Azure.

    c. Copie o valor da **ID da entidade (você precisa copiar e colar isso no seu provedor de SSO)** e cole-o na caixa de texto **Identificador** da seção **Domínio e URLs do Nuclino** no portal do Azure.

    d. Na caixa de texto **URL de SSO** cole o valor da **URL do Serviço de Logon Único SAML**, que você copiou do portal do Azure.

    e. Na caixa de texto **ID da Entidade**, cole o valor da **ID da Entidade SAML** copiado do portal do Azure.

    f. Abra o arquivo **Certificate (Base64)** baixado no Bloco de Notas. Copie o conteúdo dele para sua área de transferência e, em seguida, cole-o na caixa de texto **Certificado público**.

    g. Clique em **SALVAR ALTERAÇÕES**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/nuclino-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/nuclino-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/nuclino-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/nuclino-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="create-a-nuclino-test-user"></a>Criar um usuário de teste do Nuclino

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Nuclino. O Nuclino dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um usuário será criado durante uma tentativa de acessar o Nuclino, caso ele ainda não exista.

> [!Note]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Nuclino](mailto:contact@nuclino.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a você o acesso ao Nuclino.

![Atribuir a função de usuário][200]

**Para atribuir Brenda Fernandes ao Nuclino, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Nuclino**.

    ![O link do Nuclino na lista de aplicativos](./media/nuclino-tutorial/tutorial_nuclino_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Nuclino no Painel de Acesso, você será conectado automaticamente ao aplicativo Nuclino.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/nuclino-tutorial/tutorial_general_01.png
[2]: ./media/nuclino-tutorial/tutorial_general_02.png
[3]: ./media/nuclino-tutorial/tutorial_general_03.png
[4]: ./media/nuclino-tutorial/tutorial_general_04.png

[100]: ./media/nuclino-tutorial/tutorial_general_100.png

[200]: ./media/nuclino-tutorial/tutorial_general_200.png
[201]: ./media/nuclino-tutorial/tutorial_general_201.png
[202]: ./media/nuclino-tutorial/tutorial_general_202.png
[203]: ./media/nuclino-tutorial/tutorial_general_203.png