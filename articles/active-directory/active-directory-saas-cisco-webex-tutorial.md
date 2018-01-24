---
title: "Tutorial: integração do Azure Active Directory com o Cisco Webex | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Cisco Webex."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory7
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 42632dcf8997ec5e987ac8a6615aae24e903399a
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Tutorial: integração do Azure Active Directory ao Cisco Webex

Neste tutorial, você aprenderá como integrar o Cisco Webex ao Azure AD (Azure Active Directory).

A integração do Cisco Webex ao Azure Active Directory oferece os seguintes benefícios:

- Você pode controlar no Azure Active Directory quem tem acesso ao Cisco Webex.
- Você pode permitir que os usuários façam logon automaticamente no Cisco Webex com as respectivas contas do Azure Active Directory.
- Você pode gerenciar suas contas em um único local: o novo Portal do Azure.

Para obter mais detalhes sobre a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure Active Directory ao Cisco Webex, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Cisco Webex

> [!NOTE]
> Para testar as etapas deste tutorial, não recomendamos o uso de um ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Cisco Webex da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="add-cisco-webex-from-the-gallery"></a>Adicionar o Cisco Webex da galeria
Para configurar a integração do Cisco Webex ao Azure Active Directory, você precisa adicionar o Cisco Webex da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Cisco Webex da galeria, execute as seguintes etapas:**

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o ícone do **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Executar **Aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Cisco Webex**. 

5. Selecione **Cisco Webex** no painel de resultados. Em seguida, selecione o botão **Adicionar** para adicionar o aplicativo.

    ![Cisco Webex na lista de resultados](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure Active Directory com o Cisco Webex com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure Active Directory precisa saber qual usuário do Cisco Webex é equivalente a um usuário do Azure Active Directory. Em outras palavras, você precisa estabelecer um vínculo entre um usuário do Azure Active Directory e um usuário relacionado no Cisco Webex.

No Cisco Webex, atribua ao **Username** o mesmo valor que o **nome de usuário** no Microsoft Azure AD. Agora, você estabeleceu o vínculo entre os dois usuários. 

Para configurar e testar o logon único do Azure Active Directory com o Cisco Webex, você precisará concluir os seguintes blocos de construção:

1. [Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on) para habilitar seus usuários a usar esse recurso.
2. [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
3. [Criar um usuário de teste do Cisco Webex](#create-a-cisco-webex-test-user) para ter um equivalente de Brenda Fernandes no Cisco Webex que esteja vinculado à representação do usuário no Azure Active Directory.
4. [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
5. [Teste o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure Active Directory no Portal do Azure e configura o logon único em seu aplicativo Cisco Webex.

**Para configurar o logon único do Azure Active Directory com o Cisco Webex, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Cisco Webex**, selecione **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, na caixa **Modo**, selecione **Logon com base em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. Na seção **Domínio e URLs do Cisco Webex**, execute as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. Na caixa **URL de Entrada**, digite uma URL usando o seguinte padrão: `https://<subdomain>.webex.com`

    b. Na caixa **Identificador**, digite a URL `http://www.webex.com`.

    c. Na caixa de **URL de Resposta**, digite uma URL com o seguinte padrão: `https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de resposta e a URL de logon reais. Contate a [equipe de suporte ao Cliente do Cisco Webex](https://www.webex.co.in/support/support-overview.html) para obter esses valores. 

5. Na seção **Certificado de autenticação SAML**, selecione **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

6. Selecione **Salvar**.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_400.png)
    
6. Na seção **Configuração do Cisco Webex**, selecione **Configurar Cisco Webex** para abrir a janela **Configurar o logon**. Copie a **URL de Saída**, a **ID da Entidade SAML** e a **URL do Serviço de Logon Único SAML** da seção **Referência Rápida**.

    ![Configurar o logon único](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

7. Em uma janela diferente do navegador da Web, entre em seu site de empresa do Cisco Webex como administrador.

8. No menu na parte superior, selecione **Administração de Site**.

    ![Administração de Site](./media/active-directory-saas-cisco-webex-tutorial/ic777621.png "Administração de Site")

9. Na seção **Gerenciar Site**, selecione em **Configuração de SSO**.
   
    ![Configuração de SSO](./media/active-directory-saas-cisco-webex-tutorial/ic777622.png "Configuração de SSO")

10. Na seção **Configuração de SSO da Web Federado**, realize as seguintes etapas:
   
    ![Configuração de SSO Federado](./media/active-directory-saas-cisco-webex-tutorial/ic777623.png "Configuração de SSO Federado")  

    a. Na lista **Protocolo de Federação**, selecione **SAML 2.0**.

    b. Para **perfil SSO**, selecione **Iniciado pelo SP**.

    c. Abra seu certificado baixado no Bloco de notas e copie o conteúdo.

    d. Selecione **Importar metadados de SAML** e, em seguida, cole o conteúdo copiado do certificado.

    e. Na caixa de texto **Emissor para SAML (ID do IdP)**, cole o valor da **ID da Entidade SAML** copiado do portal do Azure.

    f. Na caixa de **URL de logon do Serviço SSO do Cliente**, cole a **URL do Serviço de Logon Único SAML**, que você copiou do portal do Azure.

    g. Na lista **Formato de NameID**, selecione **Endereço de Email**.

    h. Na caixa **AuthnContextClassRef**, digite **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**.

    i. Na caixa **URL de logoff do Serviço SSO do Cliente**, cole o valor da **URL de saída** que você copiou do portal do Azure.
   
    j. Selecione **Atualização**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com) enquanto você estiver configurando o aplicativo. Depois de adicionar este aplicativo da seção **Active Directory** > **Aplicativos Empresariais**, selecione a guia **Logon Único** e, em seguida, acesse a documentação inserida por meio da seção **Configuração** na parte inferior. Você pode ler mais sobre o recurso de documentação inserida na [Documentação inserida do Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No Portal do Azure, no painel esquerdo, selecione o botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, em seguida, selecione **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, selecione **Adicionar** na parte superior da caixa de diálogo **Todos os usuários**.

    ![O botão Adicionar](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.
 
### <a name="create-a-cisco-webex-test-user"></a>Criar um usuário de teste do Cisco Webex

Para permitir que os usuários do Azure Active Directory entrem no Cisco Webex, eles devem ser provisionados no Cisco Webex. No caso do Cisco Webex, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, realize as seguintes etapas:**

1. Entre em seu locatário do **Cisco Webex**.

2. Vá para **Gerenciar Usuários** > **Adicionar Usuário**.
   
    ![Adicionar Usuários](./media/active-directory-saas-cisco-webex-tutorial/ic777625.png "Adicionar Usuários")

3. Na seção **Adicionar usuário**, realize as etapas a seguir:
   
    ![Adicionar Usuário](./media/active-directory-saas-cisco-webex-tutorial/ic777626.png "Adicionar Usuário")   

    a. Para **Tipo de Conta**, selecione **Host**.

    b. Na caixa **Nome**, digite o nome do usuário (neste caso, **Brenda**).

    c. Na caixa **Sobrenome**, digite o sobrenome do usuário (neste caso, **Fernandes**).

    d. Na caixa **Nome de usuário**, digite o email do usuário (neste caso, **Brittasimon@contoso.com**).

    e. Na caixa **Email**, digite o endereço de email do usuário (neste caso, **Brittasimon@contoso.com**).

    f. Na caixa **Password (Senha)**, digite a senha do usuário.

    g. Na caixa de **Confirmar** senha, insira a senha do usuário novamente.

    h. Selecione **Adicionar**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Cisco Webex ou APIs fornecidas pelo Cisco Webex para provisionar as contas de usuário do Azure Active Directory. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que o usuário Brenda Fernandes use o logon único do Azure, concedendo acesso ao Cisco Webex.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Cisco Webex, execute as seguintes etapas:**

1. No Portal do Azure, abra o modo de exibição de aplicativos. Em seguida, vá para a exibição de diretório e para **Aplicativos empresariais**.  

2. Selecione **Todos os aplicativos**.

    ![Atribuir usuário][201] 

3. Na lista de aplicativos, escolha **Cisco Webex**.

    ![O link do Cisco Webex na lista de Aplicativos](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Selecione o botão **Adicionar**. Em seguida, selecione **Usuários e grupos**, na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição][203]

5. Na caixa de diálogo **Usuários e grupos**, selecione **Britta Simon** na lista de **Usuários**.

6. Na caixa de diálogo **Usuários e grupos**, clique no botão **Selecionar**.

7. Selecione o botão **Atribuir** na caixa de diálogo **Adicionar atribuição**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Quando você seleciona o bloco Cisco Webex no painel de acesso, você entra automaticamente em seu aplicativo do Cisco Webex.

Para saber mais sobre o painel de acesso, veja [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_203.png

