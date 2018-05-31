---
title: 'Tutorial: Integração do Azure Active Directory ao Arc Publishing | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Arc Publishing - SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ae609583-f875-4cb8-b68e-1b0b7938e9a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: 26e80153438eb9894753c74b81d6622c7b4c7b14
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34337904"
---
# <a name="tutorial-azure-active-directory-integration-with-arc-publishing---sso"></a>Tutorial: integração do Microsoft Azure Active Directory com o Arc Publishing - SSO

Neste tutorial, você aprende a integrar o Arc Publishing - SSO ao Microsoft Azure Active Directory.

A integração do Arc Publishing - SSO com o Microsoft Azure Active Directory fornece os seguintes benefícios:

- Você pode controlar no Microsoft Azure Active Directory que tem acesso ao Arc Publishing.
- Você pode permitir que seus usuários façam logon automaticamente no Arc Publishing - SSO (logon único) com suas contas do Microsoft Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory ao Ar Publishing - SSO, você precisará dos itens a seguir:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do SSO - Arc Publishing

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Arc Publishing - SSO da Galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-arc-publishing---sso-from-the-gallery"></a>Adicionando o Arc Publishing - SSO da Galeria
Para configurar a integração do Arc Publishing - SSO no Microsoft Azure Active Directory, você precisa adicionar o Arc Publishing - SSO da galeria para a sua lista de aplicativos SaaS gerenciados.

**Adicionar Arc Publishing - SSO da galeria, realize as etapas a seguir:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Arc Publishing - SSO**, selecione **Arc Publishing - SSO** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Arc Publishing - SSO na lista de resultados](./media/active-directory-saas-arc-tutorial/tutorial_arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Microsoft Azure Active Directory  - SSO em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Microsoft Azure AD precisa saber qual usuário equivalente no Arc Publishing - SSO que é um usuário no Microsoft Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure AD e o usuário relacionado no Arc Publishing - SSO precisa ser instalado.

Para configurar e testar o logon único do Microsoft Azure AD com o Arc Publishing - SSO, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar usuário de teste do Arc Publishing - SSO](#create-an-arc-publishing---sso-test-user)** – para ter um equivalente de Brenda Fernandes  no Arc Publishing - SSO que é vinculado à representação de usuário do Microsoft Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Microsoft Azure AD no Portal do Azure e configura o logon único em seu aplicativo Insignia Arc Publishing - SSO.

**Para configurar o logon único do Microsoft Azure AD ao Arc Publishing, execute as etapas abaixo:**

1. No Portal do Azure, na página de integração do aplicativo do **Arc Publishing - SSO**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-arc-tutorial/tutorial_arc_samlbase.png)

3. Na seção **Arc Publishing - domínio SSO e na seção URLs**, se desejar configurar o aplicativo em modo iniciado pelo **IDP**, siga as etapas abaixo:

    ![Arc Publishing - Domínio SS informações de logon único em URLs](./media/active-directory-saas-arc-tutorial/tutorial_arc_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://www.okta.com/saml2/service-provider/<Unique ID>`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Arc Publishing - Domínio SS informações de logon único em URLs](./media/active-directory-saas-arc-tutorial/tutorial_arc_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Contate a [equipe de suporte ao Cliente do Arc Publishing - SSO ](mailto:inf@washpost.com) para obter esses valores. 

5. Arc Publishing - o aplicativo EBSCO espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. A captura de tela a seguir mostra um exemplo disso.
    
    ![Configurar o logon único](./media/active-directory-saas-arc-tutorial/tutorial_arc_attribute.png)

6. Na seção **Atributos do usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML na imagem acima e siga as etapas abaixo:
    
    | Nome do atributo | Valor do atributo |
    | ---------------| --------------- |    
    | firstName | user.givenname |
    | lastName | user.surname |
    | email | user.mail |
    | groups | user.assignedroles |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

     ![Configurar o logon único](./media/active-directory-saas-arc-tutorial/tutorial_attribute_04.png)

     ![Configurar o logon único](./media/active-directory-saas-arc-tutorial/tutorial_attribute_05.png)
    
    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.
    
    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o **Namespace** em branco.
    
    d. Clique em **Ok**

    > [!NOTE]
    > Aqui o atributo de **grupos** é mapeado com **user.assignedroles**. Essas são funções personalizadas criadas no Azure AD para mapear os nomes de grupo no aplicativo. Você pode encontrar mais diretrizes [aqui](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-enterprise-app-role-management) sobre como criar funções personalizadas no Microsoft AD do Azure. 

7. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-arc-tutorial/tutorial_arc_certificate.png) 

8. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-arc-tutorial/tutorial_general_400.png)
    
9. Na seção **Configuração do Arc Publishing - SSO SSO**, clique em **Configurar Arc Publishing - SSO** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Arc Publishing - configuração de SSO](./media/active-directory-saas-arc-tutorial/tutorial_arc_configure.png) 

10. Para configurar o logon único no lado **Arc Publishing - SSO**, é necessário enviar o **Certificado (Base64)** baixado, a [URL de Saída, ID da Entidade SAML e URL do Serviço de Logon Único Arc Publishing ](mailto:inf@washpost.com) para a equipe de suporte do Arc Publishing - SSO. Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-arc-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-arc-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-arc-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-arc-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-arc-publishing---sso-test-user"></a>Criar um Arc Publishing - SSO usuário de teste

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Arc Publishing - SSO. O Arc Publishing - SSO dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Arc Publishing - SSO, caso ele ainda não exista.

>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Arc Publishing - SSO](mailto:inf@washpost.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Arc Publishing - SSO.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Arc Publishing - SSO, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Arc Publishing - SSO**.

    ![O link do Arc Publishing - SSO na lista de Aplicativos](./media/active-directory-saas-arc-tutorial/tutorial_arc_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Arc Publishing - SSO no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Arc Publishing - SSO.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-arc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-arc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-arc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-arc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-arc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-arc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-arc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-arc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-arc-tutorial/tutorial_general_203.png

