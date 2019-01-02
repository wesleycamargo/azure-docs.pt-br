---
title: 'Tutorial: Integração do Azure Active Directory ao Tableau Server | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.openlocfilehash: 84ea1d999a26ce0ce1d548da92549c6a718d5978
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850356"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Tutorial: Integração do Azure Active Directory ao Tableau Server

Neste tutorial, você aprenderá a integrar o Tableau Server ao Azure AD (Azure Active Directory).

A integração do Tableau Server ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure Active Directory quem tem acesso ao Tableau Server.
- Você pode permitir que seus usuários façam logon automaticamente no Tableau Server (Single Sign-On) com suas contas do Microsoft Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Tableau Server, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Tableau Server

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Tableau Server a partir da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-tableau-server-from-the-gallery"></a>Adição do Tableau Server a partir da galeria

Para configurar a integração do Tableau Server ao Azure AD, você precisa adicionar o Tableau Server da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Tableau Server por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Tableau Server**, selecione **Tableau Server** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Tableau Server na lista de resultados](./media/tableauserver-tutorial/tutorial-tableauserver-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Microsoft Azure Active Directory com o Tableau Server com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Tableau Server é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Tableau Server.

Para configurar e testar o logon único do Azure AD com o Tableau Server, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Tableau Server](#creating-a-tableau-server-test-user)** – para ter um equivalente de Brenda Fernandes no Tableau Server que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Tableau Server.

**Para configurar o logon único do Azure AD com o Tableau Server, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **Tableau Server**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial-general-301.png)

3. O aplicativo do Tableau Server espera uma declaração personalizada **nome de usuário** que precisa ser definida como abaixo. Isso está sendo usado como identificador de usuários, em vez de uma reivindicação exclusiva do identificador de usuários. Você pode gerenciar os valores desses atributos na seção **Atributos e reivindicações do usuário** na página de integração de aplicativos. Clique no botão **Editar** para abrir a caixa de diálogo **Atributos do usuário e reivindicações**.

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-attribute.png)

4. Na seção **Reivindicações do usuário** da caixa de diálogo **Atributos e reivindicações do usuário**, configure o atributo do token SAML conforme mostrado na imagem acima e execute as seguintes etapas:
    
    | Nome do atributo | Valor do atributo |
    | ---------------| --------------- |    
    | Nome de Usuário | user.userprincipalname |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-add-attribute.png)

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-manage-attribute.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Digite o valor do **Namespace**.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Salvar**.

5. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

6. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://azure.<domain name>.link`
    
    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://azure.<domain name>.link`

    c. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-url.png)
     
    > [!NOTE] 
    > Os valores anteriores não são valores reais. Atualize os valores com o URL e o identificador reais na página de configuração do Tableau Server, explicada posteriormente no tutorial.

7. Na página **Certificado de assinatura SAML**, na seção **Certificado de assinatura SAML**, clique em **Fazer o download** para fazer download do **XML de metadados de federação** e salvar o arquivo de certificado no seu computador.

    ![O link de download do Certificado](./media/tableauserver-tutorial/tutorial-tableauserver-certificate.png) 

8. Para configurar o SSO para o aplicativo, você precisa entrar no locatário Tableau Server como administrador.

9. Na página do **Tableau Server Configuration** , execute as seguintes etapas:
   
    ![Configurar o logon único](./media/tableauserver-tutorial/tutorial-tableauserver-001.png)

    a. Na configuração do Tableau Server, clique na guia **SAML** . 
  
    b. Marque a caixa de seleção **Usar SAML para logon único**.
   
    c. URL de retorno do Tableau Server — a URL que os usuários do Tableau Server acessarão, por exemplo http://tableau_server. Usar http://localhost não é recomendado. Usar uma URL com uma barra à direita (por exemplo, http://tableau_server/) não é aceito. Copie a **URL de retorno do Tableau Server** e cole-a na caixa de texto **URL de Logon** do Azure AD, na seção **URLs e Domínio do Tableau Server**.
   
    d. ID de entidade SAML - a ID de entidade identifica com exclusividade sua instalação do Tableau Server para o IdP. Você pode digitar a URL do Tableau Server novamente aqui, se desejar, mas ela não precisa ser sua URL do Tableau Server. Copie a **ID de Entidade do SAML** e cole-a na caixa de texto **Identificador** do Azure AD, na seção **URLs e Domínio do Tableau Server**.
     
    e. Clique em **Exportar Arquivo de Metadados** e abra-o no aplicativo de editor de texto. Localize a URL do Serviço de Declaração do Consumidor com Http Post e Índice 0 e copie a URL. Agora a cole na caixa de texto **URL de Resposta** do Azure AD, na seção **URLs e Domínio do Tableau Server**.
   
    f. Localize o arquivo de Metadados de Federação baixado do portal do Azure e carregue-o no **arquivo de metadados do IDP do SAML**.
   
    g. Clique no botão **OK** na página Configuração do Tableau Server.
   
    >[!NOTE] 
    >O cliente precisa carregar um certificado na configuração de SSO do SAML do Tableau Server e ele será ignorado no fluxo de SSO.
    >Se precisar de ajuda para configurar o SAML no Tableau Server, consulte o artigo [Configurar SAML](https://onlinehelp.tableau.com/current/server/en-us/config_saml.htm).

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](common/create-aaduser-01.png) 

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](common/create-aaduser-02.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No **nome de usuário** , digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.
  
### <a name="creating-a-tableau-server-test-user"></a>Criação de um usuário de teste do Tableau Server

O objetivo desta seção é criar uma usuária chamada Brenda Fernandes no Tableau Server. Será necessário provisionar todos os usuários no Tableau Server. 

O nome de usuário deve corresponder ao valor que você configurou no atributo personalizado **username** do Azure AD. Com o mapeamento correto, a integração deve funcionar, conforme [Configuração do Logon Único do Azure AD](#configuring-azure-ad-single-sign-on).

>[!NOTE]
>Se precisar criar um usuário manualmente, entre em contato com o administrador do Tableau Server em sua organização.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Tableau Server.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Tableau Server**.

    ![Configurar o logon único](./media/tableauserver-tutorial/tutorial-tableauserver-app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Tableau Server no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo Tableau Server.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial-general-01.png
[2]: common/tutorial-general-02.png
[3]: common/tutorial-general-03.png
[4]: common/tutorial-general-04.png

[100]: common/tutorial-general-100.png

[201]: common/tutorial-general-201.png
[202]: common/tutorial-general-202.png
[203]: common/tutorial-general-203.png
