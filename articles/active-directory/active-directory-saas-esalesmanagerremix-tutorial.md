---
title: "Tutorial: Integração do Azure Active Directory com o E Sales Manager Remix | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o E Sales Manager Remix."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeedes
ms.openlocfilehash: 56c2860b6aeac9fdc66f2b1fdd1ed9126bf77d3f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2018
---
# <a name="tutorial-azure-active-directory-integration-with-e-sales-manager-remix"></a>Tutorial: Integração do Azure Active Directory ao E Sales Manager Remix

Neste tutorial, você aprenderá a integrar o E Sales Manager Remix ao Azure Active Directory (Azure AD).

A integração do E Sales Manager Remix com o Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao E Sales Manager Remix.
- Você pode permitir que seus usuários façam logon automaticamente no E Sales Manager Remix (Logon Único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD ao E Sales Manager Remix, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do E Sales Manager Remix

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o E Sales Manager Remix da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-e-sales-manager-remix-from-the-gallery"></a>Adicionando o E Sales Manager Remix da galeria
Para configurar a integração do E Sales Manager Remix com o Azure AD, você precisará adicionar o E Sales Manager Remix da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o E Sales Manager Remix, execute as etapas a seguir:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **E Sales Manager Remix**, selecione **E Sales Manager Remix** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![E Sales Manager Remix na lista de resultados](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o E Sales Manager Remix com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do E Sales Manager Remix é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no E Sales Manager Remix.

Para configurar e testar o logon único do Azure AD com o E Sales Manager Remix, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do E Sales Manager Remix](#create-an-e-sales-manager-remix-test-user)**: para ter um equivalente de Brenda Fernandes no E Sales Manager Remix que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo E Sales Manager Remix.

**Para configurar o logon único do Azure AD com o E Sales Manager Remix, execute as etapas a seguir:**

1. No portal do Azure, na página de integração do aplicativo **E Sales Manager Remix**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

3. Na seção **Domínio e URLs do E Sales Manager Remix**, execute as etapas a seguir:

    ![Informações de logon único em Domínio e URLs do E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<Server-Based-URL>/<sub-domain>/esales-pc`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<Server-Based-URL>/<sub-domain>/`

    c. Copie o valor do **Identificador** no bloco de notas. Você usará o valor do identificador mais adiante neste tutorial.
    
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de atendimento ao cliente do E Sales Manager Remix](mailto:esupport@softbrain.co.jp) para obter esses valores.

4. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

5. Selecione **Exibir e editar todos os outros atributos de usuário** e clique no atributo **emailaddress**.
    
    ![Configuração do E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure1.png)

6. Copie os valores **Namespace** e **Nome** da declaração da caixa de texto. Gere o valor neste padrão: `<Namespace>/<Name>`. Você usará esse valor posteriormente neste tutorial.

    ![Configuração do E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure2.png)

7. Na seção **Configuração do E Sales Manager Remix**, clique em **Configurar E Sales Manager Remix** para abrir a janela **Configurar logon**. Copie a **URL do serviço de logon único do SAML e a URL de logoff** da **seção de Referência Rápida.**

    ![Configuração do E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

8. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_400.png)

9. Entre em seu aplicativo E Sales Manager Remix como um administrador.

10. Selecione **Menu Para o Administrador** no menu na parte superior direita.

    ![Configuração do E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

11. Selecione **Configurações do sistema**>**Cooperação com o sistema externo**

    ![Configuração do E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure5.png)
    
12. Selecione **SAML**

    ![Configuração do E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure6.png)

13. Na seção **Configurações de autenticação SAML**, realize as etapas a seguir:

    ![Configuração do E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure3.png)
    
    a. Selecione **Versão do PC**
    
    b. Selecione **email** na lista suspensa da seção do item Colaboração.

    c. Na caixa de texto do item Colaboração, cole o **valor de declaração** copiado do portal do Azure, ou seja `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Na caixa de texto **Emissor (ID da entidade)**, cole o valor do **Identificador** que você copiou do portal do Azure da seção **Domínio e URLs do E Sales Manager Remix**.

    e. Para carregar seu **certificado** baixado do portal do Azure, selecione **Seleção de arquivo**.

    f. Na caixa de texto **URL de logon do provedor de ID**, cole a **URL do Serviço de Logon Único SAML** copiada do portal do Azure.

    g. Na caixa de texto **URL de Logoff do Provedor de Identidade**, cole o valor **URL de Saída** copiado do portal do Azure.

    h. Clique em **Configuração concluída**

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Criar um usuário de teste do E Sales Manager Remix

1. Entre em seu aplicativo E Sales Manager Remix como um administrador.

2. Selecione **Menu Para o Administrador** no menu na parte superior direita.

    ![Configuração do E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

3. Selecione **Configurações da empresa**>**Manutenção de departamentos e funcionários** e selecione **Funcionários registrados**.

    ![O usuário](./media/active-directory-saas-esalesmanagerremix-tutorial/user1.png)

4. No **Novo registro de funcionário**, execute as etapas a seguir:
    
    ![O usuário](./media/active-directory-saas-esalesmanagerremix-tutorial/user2.png)

    a. Na caixa de texto **Nome do Funcionário**, digite o nome de usuário, como Brenda.

    b. Preencha os respectivos campos obrigatórios com as informações do usuário.
    
    c. Se você habilitar SAML, o administrador não conseguirá entrar pela tela de logon. Conceda privilégios de logon de administrador para o usuário selecionando **Logon de Administrador**

    d. Clique em **Registro**

5. No futuro, se você quiser fazer logon como um administrador, faça logon com o usuário que recebeu a permissão de administrador e clique em **Menu Para o Administrador** no menu na parte superior direita.

    ![O usuário](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao E Sales Manager Remix.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao E Sales Manager Remix, execute as etapas a seguir:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **E Sales Manager Remix**.

    ![O link do E Sales Manager Remix na lista de Aplicativos](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do E Sales Manager Remix no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo E Sales Manager Remix.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_203.png

