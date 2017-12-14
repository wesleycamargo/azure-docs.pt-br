---
title: "Tutorial: Integração do Azure Active Directory com o Achieve3000 | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Achieve3000."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 83a83d07-ff9c-46c4-b5ba-25fe2b2cd003
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: jeedes
ms.openlocfilehash: 03a6eb581dae88e9789b73c04d5424c0ed14cf62
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-achieve3000"></a>Tutorial: Integração do Azure Active Directory com o Achieve3000

Neste tutorial, você aprenderá a integrar o Achieve3000 ao Azure AD (Azure Active Directory).

A integração do Achieve3000 com o Azure AD oferece os seguintes benefícios:

- Controle no Azure AD quem terá acesso ao Achieve3000.
- Permita que seus usuários façam logon automaticamente no Achieve3000 (logon único) usando a conta do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Achieve3000, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Achieve3000 habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Achieve3000 por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-achieve3000-from-the-gallery"></a>Adicionar o Achieve3000 por meio da galeria
Para configurar a integração do Achieve3000 ao Azure AD, você precisará adicionar o Achieve3000 da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Achieve3000 por meio galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Achieve3000**, selecione **Achieve3000** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Achieve3000 na lista de resultados](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Achieve3000, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Achieve3000 é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Achieve3000.

No Achieve3000, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Achieve3000, você precisará concluir as seguintes etapas essenciais:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Achieve3000](#create-an-achieve3000-test-user)** – para ter um equivalente de Brenda Fernandes no Achieve3000 que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Achieve3000.

**Para configurar o logon único do Azure AD com o Achieve3000, realize as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Achieve3000**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_samlbase.png)

3. Na seção **Domínio e URLs do Achieve3000**, execute as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Achieve3000](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://saml.achieve3000.com/district/<District Identifier>`

    b. Na caixa de texto **Identificador**, digite o valor: `achieve3000-saml`

    > [!NOTE] 
    > O valor da URL de logon não é real. Atualize o valor com a URL de Logon real. Contate a [Equipe de suporte ao cliente do Achieve3000](https://www.achieve3000.com/contact-us/) para obter o valor. 

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_certificate.png) 

5. O aplicativo Achieve3000 espera o valor exclusivo **studentID** na declaração do Identificador de Nome. O cliente pode mapear o valor correto para a declaração do Identificador de Nome. Nesse caso, mapeamos o **user.mail** do usuário para fins demonstrativos. Porém, você deverá mapear o valor correto de acordo com seu identificador exclusivo.   

    ![Configurar logon único attb](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_attribute.png)

6. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML como mostra a imagem e execute as etapas a seguir:
    
    | Nome do atributo | Valor do atributo |
    | ------------------- | -------------------- |    
    | studentID               | user.mail |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar logon único Add](./media/active-directory-saas-achieve3000-tutorial/tutorial_officespace_04.png)

    ![Configurar logon único Addattb](./media/active-directory-saas-achieve3000-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    d. Clique em **OK**.

7. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-achieve3000-tutorial/tutorial_general_400.png)
    
8. Para configurar o logon único no lado do **Achieve3000**, é necessário enviar o **XML de metadados** baixado para a [equipe de suporte do Achieve3000](https://www.achieve3000.com/contact-us/). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-achieve3000-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-achieve3000-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-achieve3000-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-achieve3000-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-achieve3000-test-user"></a>Criar um usuário de teste do Achieve3000

Nesta seção, você criará um usuário chamado Brenda Fernandes no Achieve3000. Trabalhe com a [equipe de suporte do Achieve3000](https://www.achieve3000.com/contact-us/) para adicionar os usuários à plataforma do Achieve3000. Os usuários devem ser criados e ativados antes de usar o logon único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Achieve3000.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Achieve3000, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Achieve3000**.

    ![O link do Achieve3000 na Lista de aplicativos](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Achieve3000 no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Achieve3000.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_203.png

