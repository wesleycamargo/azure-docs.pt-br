---
title: 'Tutorial: Integração do Azure Active Directory com o TextMagic | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TextMagic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3e5b49d2-7096-46bc-a9ce-90e09177ba28
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: jeedes
ms.openlocfilehash: 5ab193d908063230946ebb2bb6320ab50bf14971
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53014881"
---
# <a name="tutorial-azure-active-directory-integration-with-textmagic"></a>Tutorial: Integração do Azure Active Directory ao TextMagic

Neste tutorial, você aprenderá a integrar o TextMagic ao Azure AD (Azure Active Directory).

A integração do TextMagic ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao TextMagic.
- Você pode permitir que seus usuários façam logon automaticamente no TextMagic (logon único) com as contas do Azure AD deles.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao TextMagic, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do TextMagic

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando TextMagic da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-textmagic-from-the-gallery"></a>Adicionando TextMagic da galeria

Para configurar a integração do TextMagic ao Azure AD, você precisa adicionar o TextMagic da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o TextMagic da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **TextMagic**, selecione **TextMagic** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![TextMagic na lista de resultados](./media/textmagic-tutorial/tutorial_textmagic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Microsoft Azure Active Directory com o TextMagic com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do TextMagic é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no TextMagic.

Para configurar e testar o logon único do Azure AD com o TextMagic, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do TextMagic](#creating-a-textmagic-test-user)** – para ter um equivalente de Brenda Fernandes no TextMagic vinculado à representação do usuário no Microsoft Azure Active Directory.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo TextMagic.

**Para configurar o logon único do Azure AD com o TextMagic, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **TextMagic**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de URLs e Domínio TextMagic](./media/textmagic-tutorial/tutorial_textmagic_url.png)

    Na caixa de texto **Identificador**, digite uma URL: `https://my.textmagic.com/saml/metadata`

5. O aplicativo TextMagic espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **Atributos e reivindicações do usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **User Attributes & Claims**.

    ![image](./media/textmagic-tutorial/i4-attribute.png)

6. Na seção **Reivindicações do usuário** da caixa de diálogo **Atributos e reivindicações do usuário**, configure o atributo do token SAML conforme mostrado na imagem acima e execute as seguintes etapas:

    | NOME  | Atributo de Origem  | Namespace |
    | --------------- | --------------- | --------------- |
    | company | user.companyname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | firstName               | user.givenname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastName            | user.surname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | phone               | user.telephonenumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    
     a. Clique no **Ícone de edição** para editar o **Nomear o valor do identificador** de **user.userprinicipalname** para **User. mail**.

    ![Atributo do TextMagic](./media/textmagic-tutorial/tutorial_textmagic_email.png)

    b. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](./common/new_save_attribute.png)

    ![image](./common/new_attribute_details.png)

    c. Na caixa de texto **Nome**, digite o nome do atributo mostrado para essa linha.

    d. Digite o valor do **Namespace**.

    e. Escolha Origem como **Atributo**.

    f. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    g. Clique em **OK**.

    h. Clique em **Salvar**. 

7. Na página **Certificado de assinatura SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para baixar o **Certificado (Base64)** e salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/textmagic-tutorial/tutorial_textmagic_certificate.png) 

8. Sobre a seção **Configurar TextMagic**, copie a URL apropriada, de acordo com suas necessidades.

     a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![Configuração do TextMagic](common/configuresection.png)

9. Em outra janela do navegador da Web, faça logon em seu site de empresa TextMagic como administrador.

10. Selecione **Configurações da conta** sob o nome de usuário.

    ![Configuração do TextMagic](./media/textmagic-tutorial/config1.png)

11. Clique na guia **SSO (Logon Único)** e preencha os campos a seguir:  
    
    ![Configuração do TextMagic](./media/textmagic-tutorial/config2.png)

     a. Na caixa de texto **Provedor de Identidade ID de Entidade** , cole o valor do **Identificador do Microsoft Azure Active Directory**, que você copiou do portal do Azure.

    b. Na caixa de texto **URL SSO do provedor de identidade:** , cole o valor da **URL de Login**, que você copiou do portal do Azure.

    c. Na caixa de texto **URL SSO do provedor de identidade:** , cole o valor da **URL de Logout**, que você copiou do portal do Azure.

    d. Abra seu certificado codificado em **base-64** no bloco de notas baixado do portal do Azure, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto  **Certificado Público x509** .

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_01.png) 

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_02.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No **nome de usuário** , digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.
  
### <a name="creating-a-textmagic-test-user"></a>Criar um usuário de teste do TextMagic

O aplicativo dá suporte ao **provisionamento de usuário just-in-time** e, após a autenticação, os usuários serão automaticamente criados no aplicativo. Você precisa preencher as informações uma vez no primeiro logon para ativar a subconta no sistema.
Não há itens de ação para você nesta seção.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao TextMagic.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **TextMagic**.

    ![Configurar o logon único](./media/textmagic-tutorial/tutorial_textmagic_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco TextMagic no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo TextMagic.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
