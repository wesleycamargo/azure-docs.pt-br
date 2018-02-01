---
title: "Tutorial: Integração do Azure Active Directory ao BambooHR | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o BambooHR."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: 6582a0b05539f322801273374d7c8fbccfe2da60
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Tutorial: Integração do Azure Active Directory ao BambooHR

Neste tutorial, você aprende a integrar o BambooHR ao Azure AD (Azure Active Directory).

A integração do BambooHR ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao BambooHR.
- É possível permitir que os usuários se conectem automaticamente ao BambooHR (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD ao BambooHR, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do BambooHR

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o BambooHR por meio da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-bamboohr-from-the-gallery"></a>Adicionando o BambooHR por meio da galeria
Para configurar a integração do BambooHR ao Azure AD, você precisa adicionar o BambooHR à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o BambooHR por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **BambooHR**, selecione **BambooHR** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![BambooHR na lista de resultados](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o BambooHR, com base em um usuário de teste chamado “Britta Simon”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do BambooHR é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do BambooHR.

No BambooHR, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o BambooHR, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do BambooHR](#create-a-bamboohr-test-user)** – para ter um equivalente de Britta Simon no BambooHR que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo BambooHR.

**Para configurar o logon único do Azure AD com o BambooHR, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **BambooHR**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

3. Na seção **Domínio e URLs do BambooHR**, realize as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<company>.bamboohr.com`

    b. Na caixa de texto **Identificador**, digite um valor: `BambooHR-SAML`

    > [!NOTE] 
    > O valor da URL de logon não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao Cliente do BambooHR](https://www.bamboohr.com/contact.php) para obter o valor. 
 
4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do BambooHR**, clique em **Configurar o BambooHR** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configuração do BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

6. Em outra janela do navegador da Web, faça logon em seu site de empresa BambooHR como um administrador.

7. Na página inicial, execute as seguintes etapas:
   
    ![Logon Único](./media/active-directory-saas-bamboo-hr-tutorial/ic796691.png "Logon Único")   

    a. Clique em **Aplicativos**.
   
    b. No menu de aplicativos à esquerda, clique em **Logon Único**.
   
    c. Clique em **Logon Único do SAML**.

8. Na seção **Logon Único do SAML** , realize as seguintes etapas:
   
    ![Logon Único do SAML](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "Logon Único do SAML")
   
    a. Na caixa de texto **URL de Logon SSO**, cole o valor da **URL do Serviço de Logon Único SAML** copiado do portal do Azure.
      
    b. Abra o certificado codificado em Base 64 baixado no portal do Azure no bloco de notas, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **Certificado X.509**
   
    c. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-bamboohr-test-user"></a>Crie um usuário de teste do BambooHR

Para permitir que os usuários do Azure AD façam logon no BambooHR, eles devem ser provisionados no BambooHR.  

No caso do BambooHR, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **BambooHR** como administrador.

2. Na barra de ferramentas na parte superior, clique em **Configurações**.
   
    ![Configuração](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Configuração")

3. Clique em **Visão Geral**.

4. No painel de navegação à esquerda, vá para **Segurança \> Usuários**.

5. Digite o nome de usuário, a senha e o endereço de email de uma conta válida do AAD que deseja provisionar nas caixas de texto relacionadas.

6. Clique em **Salvar**.
        
>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do BambooHR ou as APIs fornecidas pelo BambooHR para provisionar as contas de usuário do AAD.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao BambooHR.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao BambooHR, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **BambooHR**.

    ![O link do BambooHR na lista de Aplicativos](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco BambooHR no Painel de Acesso, deverá ser automaticamente conectado ao aplicativo BambooHR.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_203.png

