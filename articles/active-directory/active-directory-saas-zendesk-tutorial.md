---
title: "Tutorial: Integração do Azure Active Directory com o Zendesk | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Zendesk."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: 78c4f5c2f48393dfd76621847063918c10b9ff52
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Tutorial: Integração do Active Directory do Azure com o Zendesk

Neste tutorial, você aprenderá a integrar o Zendesk ao Azure AD (Azure Active Directory).

A integração do Zendesk ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Zendesk.
- Você pode permitir que seus usuários façam logon automaticamente no Zendesk (logon único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Zendesk, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Zendesk

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Zendesk da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-zendesk-from-the-gallery"></a>Adicionar o Zendesk da galeria
Para configurar a integração do Zendesk ao Azure AD, você precisa adicionar o Zendesk por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Zendesk por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Zendesk**, selecione **Zendesk** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Zendesk na lista de resultados](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Zendesk, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Zendesk é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no Zendesk.

No Zendesk, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Zendesk, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Crie um usuário de teste do Zendesk](#create-a-zendesk-test-user)** – para ter um equivalente de Brenda Fernandes no Zendesk vinculado à representação de um usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único em seu aplicativo Zendesk.

**Para configurar o logon único do Azure AD com o Zendesk, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Zendesk**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_samlbase.png)

3. Na seção **URLs e Domínio do Zendesk**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Zendesk](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.zendesk.com`

    b. Na caixa de texto **Identificador**, digite o valor usando o seguinte padrão: `<subdomain>.zendesk.com`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de atendimento ao cliente do Zendesk](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) para obter esses valores. 
 
4. Na seção **Certificado de Autenticação SAML**, copie o valor da **IMPRESSÃO DIGITAL** do certificado.

    ![O link de download do Certificado](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_certificate.png)

5. O Zendesk espera que as declarações SAML estejam em um formato específico. Não há nenhum atributo SAML obrigatório, mas opcionalmente você pode adicionar um atributo da seção **Atributos de Usuário** seguindo as etapas abaixo: 

     ![Configurar Logon Único](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_attributes1.png)

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar logon único Add](./media/active-directory-saas-zendesk-tutorial/tutorial_attribute_04.png)

    ![Configurar logon único Addattb](./media/active-directory-saas-zendesk-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    d. Clique em **OK**.
 
    > [!NOTE] 
    > Você pode usar atributos de extensão para adicionar atributos que não estão no Azure AD por padrão. Clique em [Atributos do usuário que podem ser definidos no SAML](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) para obter a lista completa de atributos SAML que o **Zendesk** aceita.  

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-zendesk-tutorial/tutorial_general_400.png)

7. Na seção **Configuração do Zendesk**, clique em **Configurar o Zendesk** para abrir a janela **Configurar logon**. Copie a **URL do serviço de logon único do SAML e a URL de logoff** da **seção de Referência Rápida.**

    ![Configuração de Zendesk](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_configure.png) 

8. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa Zendesk como administrador.

9. Clique em **Administrador**.

10. No painel de navegação à esquerda, clique em **Configurações** e em **Segurança**.

11. Na página **Segurança**, realize as seguintes etapas: 
   
     ![Segurança](./media/active-directory-saas-zendesk-tutorial/ic773089.png "Segurança")

    ![Logon Único](./media/active-directory-saas-zendesk-tutorial/ic773090.png "Logon Único")

     a. Clique na guia **Administrador e Agentes**.

     b. Selecione **SSO (logon único) e SAML** e, em seguida, selecione **SAML**.

     c. Na caixa de texto **URL do SSO do SAML**, cole o valor da **URL de Serviço de Logon Único do SAML** que você copiou do Portal do Azure. 

     d. Na caixa de texto **URL de Logoff Remoto**, cole o valor da **URL de Saída** que você copiou do Portal do Azure.
        
     e. Na caixa de texto **Impressão Digital do Certificado**, cole o valor de **Impressão Digital** do certificado copiado do Portal do Azure.
     
     f. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-zendesk-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-zendesk-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-zendesk-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-zendesk-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-zendesk-test-user"></a>Criar um usuário de teste do Zendesk

Para permitir que os usuários do Azure AD façam logon no **Zendesk**, eles devem ser provisionados no **Zendesk**.  
Dependendo da função atribuída nos aplicativos, esse é o comportamento esperado:

 1. As contas de **Usuário final** são provisionadas automaticamente ao entrar.
 2. Contas de **Agente** e **Admin** precisam ser provisionadas manualmente no **Zendesk** antes de entrar.
 
**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Zendesk** .

2. Selecione a guia **Lista de Clientes** .

3. Selecione a guia **Usuário** e clique em **Adicionar**.
   
    ![Adicionar Usuário](./media/active-directory-saas-zendesk-tutorial/ic773632.png "Adicionar Usuário")
4. Digite o **Nome** e **Email** de uma conta existente do Azure AD que você deseja provisionar e clique em **Salvar**.
   
    ![Novo Usuário](./media/active-directory-saas-zendesk-tutorial/ic773633.png "Novo Usuário")

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Zendesk ou APIs fornecidas pelo Zendesk para provisionar as contas de usuário do AAD.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Zendesk.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Zendesk, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Zendesk**.

    ![O link do Zendesk na lista de Aplicativos](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Zendesk no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Zendesk.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_203.png

