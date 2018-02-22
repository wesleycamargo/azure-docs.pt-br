---
title: "Tutorial: integração do Azure Active Directory com o FloQast | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o FloQast."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 013cb57d-567c-44d0-a119-e6ba6e607153
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: jeedes
ms.openlocfilehash: 63fdd5a5dabdd9083afa23e5400684b3b144b911
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="tutorial-azure-active-directory-integration-with-floqast"></a>Tutorial: Integração do Active Directory do Azure ao FloQast

Neste tutorial, você aprenderá como integrar o FloQast ao Azure AD (Azure Active Directory).

A integração do FloQast ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao FloQast.
- Você pode permitir que usuários façam logon automaticamente no FloQast (logon único) com as respectivas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD ao FloQast, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do FloQast

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do FloQast da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-floqast-from-the-gallery"></a>Adição do FloQast da galeria
Para configurar a integração de FloQast ao AD do Azure, você precisa adicionar FloQast por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o FloQast por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **FloQast**, selecione **FloQast** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![FloQast na lista de resultados](./media/active-directory-saas-floqast-tutorial/tutorial_floqast_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o FloQast, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do FloQast é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado em FloQast.

Para configurar e testar o logon único do Azure AD com o FloQast, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar de um usuário de teste do FloQast](#create-a-floqast-test-user)** - para ter um equivalente de Brenda Fernandes no FloQast que esteja vinculado à representação de usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo FloQast.

**Para configurar o logon único do Azure AD com o FloQast, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **FloQast**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-floqast-tutorial/tutorial_floqast_samlbase.png)

3. Na seção **Domínio e URLs do FloQast**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único de Domínio e URLs do FloQast](./media/active-directory-saas-floqast-tutorial/tutorial_floqast_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<subdomain>.floqast.com/`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<subdomain>.floqast.com/api/sso/saml/azure`

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de Domínio e URLs do FloQast](./media/active-directory-saas-floqast-tutorial/tutorial_floqast_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.floqast.com/login/sso`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Contate a [equipe de suporte ao cliente do FloQast](mailto:support@floqast.com) para obter esses valores.

5. O aplicativo FloQast espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. A captura de tela a seguir mostra um exemplo disso.
    
    ![Configurar logon único attb](./media/active-directory-saas-floqast-tutorial/tutorial_floqast_attribute.png)
    
6. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML como mostra a imagem e execute as etapas a seguir:
    
    | Nome do atributo | Valor do atributo |
    | ------------------- | -------------------- |    
    | Nome           | user.givenname |
    | Sobrenome        | user.surname |
    | Email       | user.mail    |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar logon único Add](./media/active-directory-saas-floqast-tutorial/tutorial_attribute_04.png)

    ![Configurar logon único Addattb](./media/active-directory-saas-floqast-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o **Namespace** em branco.
    
    e. Clique em **OK**.

7. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador e realize as seguintes etapas:

    ![O link de download do Certificado](./media/active-directory-saas-floqast-tutorial/tutorial_floqast_certificate.png)

    a. Marque **Mostrar configurações avançadas de assinatura de certificado**.

    ![A asserção de certificado](./media/active-directory-saas-floqast-tutorial/tutorial_floqast_certificateassertion.png)

    b. Selecione **Opção de Assinatura** como **Assinar resposta SAML e declaração**.

8. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-floqast-tutorial/tutorial_general_400.png)
    
9. Para configurar o logon único no lado do **FloQast**, é necessário enviar o **XML de metadados** baixado para a [equipe de suporte do FloQast](mailto:support@floqast.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-floqast-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-floqast-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-floqast-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-floqast-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-floqast-test-user"></a>Criar um usuário de teste FloQast

Nesta seção, você criará uma usuária chamado Brenda Fernandes no FloQast. Trabalhe com a [equipe de suporte do FloQast](mailto:support@floqast.com) para adicionar os usuários na plataforma do FloQast. Os usuários devem ser criados e ativados antes de usar o logon único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao FloQast.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao FloQast, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **FloQast**.

    ![O link do FloQast na lista de Aplicativos](./media/active-directory-saas-floqast-tutorial/tutorial_floqast_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco FloQast no Painel de Acesso, deverá ser automaticamente conectado ao seu aplicativo FloQast.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-floqast-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-floqast-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-floqast-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-floqast-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-floqast-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-floqast-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-floqast-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-floqast-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-floqast-tutorial/tutorial_general_203.png

