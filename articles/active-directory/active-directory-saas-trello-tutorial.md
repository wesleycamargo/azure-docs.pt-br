---
title: "Tutorial: integração do Azure Active Directory ao Trello | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Trello."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: jeedes
ms.openlocfilehash: 8c1816fa272386a9839d93423a8ae81d2cdc8567
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2017
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>Tutorial: integração do Azure Active Directory ao Trello

Neste tutorial, você aprenderá a integrar o Trello ao Azure AD (Azure Active Directory).

A integração do Trello ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure Active Directory quem tem acesso ao Trello.
- Você pode permitir que usuários façam logon automaticamente no Trello (Logon Único) com as respectivas contas do Microsoft Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Trello, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Trello

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Trello da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-trello-from-the-gallery"></a>Adicionando o Trello da galeria
Para configurar a integração do Trello ao Azure AD, você precisará adicionar o Trello da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Trello da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Trello**, selecione **Trello** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Trello na lista de resultados](./media/active-directory-saas-trello-tutorial/tutorial_trello_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Trello, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Trello é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Trello.

No Trello, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Trello, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Trello](#create-a-trello-test-user)** – para ter um equivalente de Brenda Fernandes no Trello que esteja vinculado à representação de usuário no Microsoft Azure Active Directory.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Trello.

>[!NOTE]
>Você deve obter o campo de dados dinâmico **\<empresa\>** do Trello. Se você não tiver o valor do campo de dados dinâmico, contate a [equipe de suporte do Trello](mailto:support@trello.com) a fim de obtê-lo para sua empresa.
    > 

**Para configurar o logon único do Azure AD com o Trello, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Trello**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-trello-tutorial/tutorial_trello_samlbase.png)

3. Na seção **URLs e Domínio do Trello**, se você quiser configurar o aplicativo no **modo iniciado pelo IDP**, siga as etapas abaixo:

    ![Informações de logon único em Domínio e URLs do Trello](./media/active-directory-saas-trello-tutorial/tutorial_trello_url.png)
    
    a. Na caixa de texto **Identificador**, digite a seguinte URL: `https://trello.com/auth/saml/metadata`
    
    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://trello.com/auth/saml/consume/<enterprise>`

4. Se desejar configurar o aplicativo no **modo iniciado pelo SP**, realize as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Trello](./media/active-directory-saas-trello-tutorial/tutorial_trello_url1.png)

    a. Marque **Mostrar configurações de URL avançadas**.

    b. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://trello.com/auth/saml/login/<enterprise>` 

5. O aplicativo Trello espera que as asserções SAML contenham atributos específicos. Configure as atribuições a seguir para o aplicativo. É possível gerenciar os valores desses atributos em **“Atributos de Usuário”** do aplicativo. A captura de tela a seguir mostra um exemplo disso.

    ![Configurar Logon Único](./media/active-directory-saas-trello-tutorial/tutorial_trello_attribute.png)

6. Na caixa de diálogo **Atributos de token SAML** , para cada linha mostrada na tabela a seguir, execute as seguintes etapas:
 
    | Nome do atributo | Valor do atributo |
    | --- | --- |
    | User.Email | user.mail |
    | User.FirstName | user.givenname |
    | User.LastName | user.surname |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/active-directory-saas-trello-tutorial/tutorial_officespace_04.png)

    ![Configurar Logon Único](./media/active-directory-saas-trello-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha. 

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    d. Clique em **OK**. 

7. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-trello-tutorial/tutorial_trello_certificate.png) 

8. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-trello-tutorial/tutorial_general_400.png)
    
9. Na seção **Configuração do Trello**, clique em **Configurar o Trello** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configuração do Trello](./media/active-directory-saas-trello-tutorial/tutorial_trello_configure.png) 

10. Para configurar o logon único no lado do **Trello**, você precisa ir à página [Configuração de SSO corporativo do Trello](https://trello.com/sso-configuration) para enviar o **Certificado (Base64)** e a **URL do serviço de logon único SAML** baixados à [Equipe de suporte do Trello](mailto:support@trello.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-trello-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-trello-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-trello-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-trello-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-trello-test-user"></a>Criar um usuário de teste do Trello

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Trello. O Trello dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Trello, caso ele ainda não exista.

>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Trello](mailto:support@trello.com).


### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Trello.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Trello, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Trello**.

    ![O link do Trello na lista de Aplicativos](./media/active-directory-saas-trello-tutorial/tutorial_trello_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Trello no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Trello.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-trello-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trello-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trello-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trello-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-trello-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trello-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trello-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-trello-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-trello-tutorial/tutorial_general_203.png

