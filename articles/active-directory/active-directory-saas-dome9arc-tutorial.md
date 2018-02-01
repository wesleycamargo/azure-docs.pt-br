---
title: "Tutorial: Integração do Azure Active Directory com o Dome9 Arc | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o Dome9 Arc."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: 2ce4bb1be8b0124c69991765e18ce9922bd2f4a4
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>Tutorial: integração do Azure Active Directory com o Dome9 Arc

Neste tutorial, você aprenderá a integrar o Dome9 Arc ao Azure AD (Azure Active Directory).

A integração do Dome9 Arc ao Azure Active Directory oferece os seguintes benefícios:

- Você pode controlar no Azure Active Directory quem terá acesso ao Dome9 Arc.
- Você pode permitir que seus usuários façam logon automaticamente no Dome9 Arc (logon único) com as contas do Azure Active Directory deles.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure Active Directory com o Dome9 Arc, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Dome9 Arc

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. adicionar o Dome9 Arc da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-dome9-arc-from-the-gallery"></a>adicionar o Dome9 Arc da galeria
Para configurar a integração do Dome9 Arc ao Azure Active Directory, será necessário adicionar o Dome9 Arc da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Dome9 Arc da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Dome9 Arc**, selecione **Dome9 Arc** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Dome9 Arc na lista de resultados](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure Active Directory com o Dome9 Arc, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure Active Directory precisa saber qual usuário do Dome9 Arc é equivalente a um usuário do Azure Active Directory. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure Active Directory e o usuário relacionado no Dome9 Arc.

No Dome9 Arc, atribua o valor do **nome de usuário** no Azure Active Directory como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure Active Directory com o Dome9 Arc, será necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Dome9 Arc](#create-a-dome9-arc-test-user)** - para ter um equivalente de Brenda Fernandes no Dome9 Arc vinculado à representação do usuário no Azure Active Directory.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure Active Directory no Portal do Azure e configurará o logon único no seu aplicativo Dome9 Arc.

**Para configurar o logon único do Azure Active Directory com o Dome9 Arc, realize as seguintes etapas:**

1. No Portal do Azure, na página de integração do aplicativo **Dome9 Arc**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_samlbase.png)

3. Na seção **Domínio e URLs do Dome9 Arc**, execute as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único de Domínio e URLs do Dome9 Arc](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_url.png)

    a. Na caixa de texto **Identificador**, digite a URL: `https://secure.dome9.com/`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Você selecionará o valor do nome da empresa no portal de administração do dome9, que será explicado mais adiante no tutorial.

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de Domínio e URLs do Dome9 Arc](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://secure.dome9.com/sso/saml/<yourcompanyname>`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao Cliente do Dome9 Arc](https://dome9.com/about/contact-us/) para obter esses valores. 

5. O aplicativo Dome9 Arc Software espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo. A captura de tela a seguir mostra um exemplo disso.

    ![Configurar logon único attb](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_attribute.png)

6. Na seção **Atributos do usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML na imagem acima e siga as etapas abaixo:
    
    | Nome do atributo  | Valor do atributo | 
    | --------------- | --------------- | 
    | memberof | user.assignedroles | 
    
    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar logon único add attb](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9_04.png)

    ![Configurar logon único edit attb](./media/active-directory-saas-dome9arc-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    d. Clique em **OK**.

7. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_certificate.png) 

8. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-dome9arc-tutorial/tutorial_general_400.png)
    
9. Na seção **Configuração do Dome9 Arc**, clique em **Configurar Dome9 Arc** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Dome9 Arc](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_configure.png) 

10. Em uma janela do navegador da Web diferente, faça logon no seu site da empresa Dome9 Arc como administrador.

11. Clique nas **Configurações de Perfil** no canto superior direito e depois em **Configurações de Conta**. 

    ![Configuração do Dome9 Arc](./media/active-directory-saas-dome9arc-tutorial/configure1.png)

12. Navegue até **SSO** e, em seguida, clique em **HABILITAR**.

    ![Configuração do Dome9 Arc](./media/active-directory-saas-dome9arc-tutorial/configure2.png)

13. Na seção de Configuração de SSO, execute as seguintes etapas:

    ![Configuração do Dome9 Arc](./media/active-directory-saas-dome9arc-tutorial/configure3.png)

    a. Digite o nome da empresa na caixa de texto **ID da Conta**. Esse valor deve ser usado na URL de resposta mencionada na seção URL do Portal do Azure.

    b. Na caixa de texto **Emissor**, cole o valor da **ID de Entidade do SAML** que você copiou do Portal do Azure.

    c. Na caixa de texto **URL do Ponto de Extremidade IdP**, cole o valor da **URL de Serviço de Logon Único do SAML** que você copiou do Portal do Azure.

    d. Abra seu certificado codificado com Base64 baixado no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado X.509**.

    e. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-dome9-arc-test-user"></a>Criar um usuário de teste Dome9 Arc

Para habilitar que os usuários do Azure Active Directory façam logon no Dome9 Arc, eles devem ser provisionados no aplicativo. O Dome9 Arc suporta provisionamento just-in-time, mas para que ele funcione corretamente, o usuário deve selecionar a **Função** específica e atribuir o mesmo ao usuário.

   >[!Note] 
   >Para a criação da **Função** e outros detalhes contate a [Equipe de suporte ao cliente Dome9 Arc](https://dome9.com/about/contact-us/).

**Para provisionar uma conta de usuário manualmente, execute as seguintes etapas:**

1. Faça logon em seu site da empresa Dome9 Arc como administrador.

2. Clique em **Usuários e Funções** e, em seguida, clique em **Usuários**.

    ![Adicionar Funcionário](./media/active-directory-saas-dome9arc-tutorial/user1.png)

3. Clique em **ADICIONAR USUÁRIO**.

    ![Adicionar Funcionário](./media/active-directory-saas-dome9arc-tutorial/user2.png)

4. Na seção **Criar Usuário** , realize as seguintes etapas:
    
    ![Adicionar Funcionário](./media/active-directory-saas-dome9arc-tutorial/user3.png)

    a. Na caixa de texto **Email**, digite o email do usuário como Brittasimon@contoso.com.

    b. Na caixa de texto **Nome**, digite o nome do usuário, como Brenda.

    c. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como Fernandes.

    d. Faça o **Usuário do SSO** como **On**.

    e. Clique em **CRIAR**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Dome9 Arc.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Dome9 Arc, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Dome9 Arc**.

    ![O link do Dome9 Arc na lista de Aplicativos](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco Dome9 Arc no Painel de Acesso, deverá ser automaticamente conectado ao seu aplicativo Dome9 Arc.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_203.png

