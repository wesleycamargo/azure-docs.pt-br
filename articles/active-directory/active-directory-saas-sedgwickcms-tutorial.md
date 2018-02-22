---
title: "Tutorial: integração do Azure Active Directory com o Sedgwick CMS | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Sedgwick CMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 957931e0-e426-47e7-9904-3ed98d3f504c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: jeedes
ms.openlocfilehash: a704026dfe39457212f4237435c997996656db8f
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="tutorial-azure-active-directory-integration-with-sedgwick-cms"></a>Tutorial: integração do Azure Active Directory com o Sedgwick CMS

Neste tutorial, você aprenderá a integrar o Sedgwick CMS ao Azure AD (Azure Active Directory).

A integração do Sedgwick CMS ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao Sedgwick CMS.
- Você pode permitir que seus usuários entrem automaticamente no Sedgwick CMS (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD com o Sedgwick CMS, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Sedgwick CMS

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Sedgwick CMS da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-sedgwick-cms-from-the-gallery"></a>Adicionando o Sedgwick CMS da galeria
Para configurar a integração do Sedgwick CMS ao Azure AD, você precisará adicionar o Sedgwick CMS da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Sedgwick CMS da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Sedgwick CMS**, selecione **Sedgwick CMS** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Sedgwick CMS na lista de resultados](./media/active-directory-saas-sedgwickcms-tutorial/tutorial_sedgwickcms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Sedgwick CMS, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Sedgwick CMS é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Sedgwick CMS.

No Sedgwick CMS, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Sedgwick CMS, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Sedgwick CMS](#create-a-sedgwick-cms-test-user)** – para ter um equivalente de Brenda Fernandes no Sedgwick CMS vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Sedgwick CMS.

**Para configurar o logon único do Azure AD com o Sedgwick CMS, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração do aplicativo **Sedgwick CMS**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-sedgwickcms-tutorial/tutorial_sedgwickcms_samlbase.png)

3. Na seção **Domínio e URLs do Sedgwick CMS**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Sedgwick CMS](./media/active-directory-saas-sedgwickcms-tutorial/tutorial_sedgwickcms_url.png)

    a. Na caixa de texto **Identificador**, digite a URL: 
    | |
    |--|
    | `expresspreview.sedgwickcms.net/voe/sso` |
    | `claimlookup.com/Voe/sso` |

    b. Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão:
    | |
    |--|
    | `https://<subdomain>.sedgwickcms.net/voe/sso` |
    | `https://claimlookup.com/Voe/sso` |

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte do Sedgwick CMS](https://www.sedgwick.com/contact/Pages/contactform.aspx) para obter esses valores.
 

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-sedgwickcms-tutorial/tutorial_sedgwickcms_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-sedgwickcms-tutorial/tutorial_general_400.png)

6. Para configurar o logon único no lado do **Sedgwick CMS**, é necessário enviar o **XML de metadados** baixado para a [equipe de suporte do Sedgwick CMS](https://www.sedgwick.com/contact/Pages/contactform.aspx). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-sedgwickcms-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-sedgwickcms-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-sedgwickcms-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-sedgwickcms-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
  
### <a name="create-a-sedgwick-cms-test-user"></a>Criar um usuário de teste Sedgwick CMS

Nesta seção, você criará uma usuária chamado Brenda Fernandes no Sedgwick CMS. Trabalhe com a [equipe de suporte do Sedgwick CMS](https://www.sedgwick.com/contact/Pages/contactform.aspx) para adicionar os usuários na plataforma do Sedgwick CMS. Os usuários devem ser criados e ativados antes de usar o logon único.  

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará para Brenda Fernandes o uso do logon único do Azure, concedendo acesso ao Sedgwick CMS.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Sedgwick CMS, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Sedgwick CMS**.

    ![O link do Sedgwick CMS na lista de Aplicativos](./media/active-directory-saas-sedgwickcms-tutorial/tutorial_sedgwickcms_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Sedgwick CMS no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Sedgwick CMS.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sedgwickcms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sedgwickcms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sedgwickcms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sedgwickcms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sedgwickcms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sedgwickcms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sedgwickcms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sedgwickcms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sedgwickcms-tutorial/tutorial_general_203.png

