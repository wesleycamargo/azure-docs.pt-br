---
title: 'Tutorial: integração do Azure Active Directory com o DocuSign | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 8838cb2479078a8b6b4f031128501f09284b6bdd
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159258"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Tutorial: integração do Active Directory do Azure com o DocuSign

Neste tutorial, você aprende a integrar o DocuSign ao Azure AD (Azure Active Directory).

A integração do DocuSign ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao DocuSign.
- É possível permitir que os usuários se conectem automaticamente ao DocuSign (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao DocuSign, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do DocuSign

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o DocuSign por meio da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-docusign-from-the-gallery"></a>Adicionando o DocuSign por meio da galeria

Para configurar a integração do DocuSign ao Azure AD, é necessário adicionar o DocuSign à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o DocuSign por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **DocuSign**, selecione **DocuSign** no painel de resultados e, depois, clique no botão **Adicionar** para incorporar o aplicativo.

    ![DocuSign na lista de resultados](./media/docusign-tutorial/tutorial_docusign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o DocuSign, com base em um usuário de teste chamado “Clara Barbosa”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do DocuSign é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do DocuSign.

Para configurar e testar o logon único do Azure AD com o DocuSign, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do DocuSign](#creating-a-docusign-test-user)** – para ter um equivalente de Brenda Fernandes no DocuSign que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo DocuSign.

**Para configurar o logon único do Azure AD com o DocuSign, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **DocuSign**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações sobre logon único de domínio e URLs do DocuSign](./media/docusign-tutorial/tutorial_docusign_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Logon e o Identificador reais, que são explicados em seguida na seção **Exibir pontos de extremidade do SAML 2.0** do tutorial.

5. Na página **Certificado de assinatura SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para baixar o **Certificado (Base64)** e salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/docusign-tutorial/tutorial_docusign_certificate.png) 

6. Na seção **Configurar o DocuSign**, copie a URL apropriada de acordo com os requisitos.

     a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![Configuração do DocuSign](common/configuresection.png)

7. Em outra janela do navegador da Web, faça logon no **portal de administração do DocuSign** como administrador.

8. No canto superior direito da página, clique no **logotipo** do perfil e, em seguida, em **Ir para Administrador**.
  
    ![Configurando o logon único][51]

9. Na página de soluções do domínio, clique em **Domínios**

    ![Configurando o logon único][50]

10. Na seção **Domínios**, clique em **DECLARAR DOMÍNIO**.

    ![Configurando o logon único][52]

11. Na caixa de diálogo **Declarar um domínio**, na caixa de texto **Nome do Domínio**, digite o domínio de sua empresa e clique em **DECLARAR**. Verifique o domínio e confira se o status está ativo.

    ![Configurando o logon único][53]

12. Na página de soluções do domínio, clique em **Provedores de Identidade**.
  
    ![Configurando o logon único][54]

13. Na seção **Provedores de Identidade**, clique em **ADICIONAR PROVEDOR DE IDENTIDADE**. 

    ![Configurando o logon único][55]

14. Na página **Configurações do Provedor de Identidade** , execute as seguintes etapas:

    ![Configurando o logon único][56]

     a. Na caixa de texto **Nome** , digite um nome exclusivo para sua configuração. Não use espaços.

    b. Na caixa de texto **Emissor de provedor de identidade**, cole o valor de **Identificador do Azure AD**, copiado do portal do Azure.

    c. Na caixa de texto **URL de Logon do Provedor de Identidade**, cole o valor de **URL de Logon** que você copiou do portal do Azure.

    d. Na caixa de texto **URL de Logout do Provedor de Identidade**, cole o valor de **URL de Logout** que você copiou do portal do Azure.

    e. Selecione **Assinar solicitação AuthN**.

    f. Para **Enviar solicitação AuthN por**, selecione **POST**.

    g. Em **Enviar solicitação de logoff por**, selecione **GET**.

    h. Na seção **Mapeamento de atributo personalizado**, clique em **ADICIONAR NOVO MAPEAMENTO**.

    ![Configurando o logon único][62]

    i. Escolha o campo a mapear com a declaração do Azure AD. Neste exemplo, a declaração **emailaddress** é mapeada com o valor de **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Esse é o nome de declaração padrão do Azure AD para a declaração de email; depois clique em **SALVAR**.

    ![Configurando o logon único][57]

    > [!NOTE]
    > Use o **Identificador de usuário** apropriado para mapear o usuário do Azure AD para o mapeamento de usuário do DocuSign. Selecione o campo adequado e insira o valor apropriado com base nas configurações de sua organização.

    j. Na seção **Certificado do Provedor de Identidade**, clique em **ADICIONAR CERTIFICADO** e, depois, carregue o certificado baixado do portal do Azure AD e clique em **SALVAR**.

    ![Configurando o logon único][58]

    k. Na seção **Provedores de Identidade**, clique em **AÇÕES** e em **Pontos de Extremidade**.

    ![Configurando o logon único][59]

    l. Na seção **Exibir Pontos de Extremidade do SAML 2.0** do **portal de administração do DocuSign**, realize as seguintes etapas:

    ![Configurando o logon único][60]

    * Copie a **URL de emissor de provedor de serviço** e, em seguida, cole-a na caixa de texto **Identificador** na seção **Domínio e URLs do DocuSign** do portal do Azure.

    * Copie a **URL de Logon do Provedor de Serviço** e, em seguida, cole-a na caixa de texto **URL de Logon** na seção **Domínio e URLs do DocuSign** do portal do Azure.

    * Clique em **Fechar**

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

### <a name="creating-a-docusign-test-user"></a>Criando um usuário de teste do DocuSign

O objetivo desta seção é criar um usuário chamado Clara Barbosa no DocuSign. O DocuSign dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o DocuSign, caso ele ainda não exista.
>[!Note]
>Se for necessário criar manualmente um usuário, contate a  [equipe de suporte da DocuSign](https://support.docusign.com/).

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Clara Barbosa use o logon único do Azure, concedendo acesso ao DocuSign.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **DocuSign**.

    ![Configurar o logon único](./media/docusign-tutorial/tutorial_docusign_app.png)

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do DocuSign no Painel de Acesso, deverá ser conectado automaticamente ao aplicativo DocuSign.
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
[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
