---
title: 'Tutorial: Integração do Azure Active Directory com o Dovetale | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Dovetale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 81da50c3-df94-458a-8b6a-a30827ee6358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2018
ms.author: jeedes
ms.openlocfilehash: dd2aa699c22518ebbe7f29ba8dfd296da7385476
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2018
ms.locfileid: "40161534"
---
# <a name="tutorial-azure-active-directory-integration-with-dovetale"></a>Tutorial: Integração do Azure Active Directory com o Dovetale

Neste tutorial, você aprenderá a integrar o Dovetale ao Azure AD (Azure Active Directory).

A integração do Dovetale com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Dovetale.
- Você pode permitir que os usuários façam logon automaticamente no Dovetale (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Dovetale, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Dovetale

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Dovetale por meio da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-dovetale-from-the-gallery"></a>Adicionando o Dovetale por meio da galeria

Para configurar a integração do Dovetale com o Azure AD, você precisa adicionar o Dovetale por meio da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o Dovetale por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Dovetale**, selecione **Dovetale** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Dovetale na lista de resultados](./media/dovetale-tutorial/tutorial_dovetale_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Dovetale, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Dovetale é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Dovetale.

Para configurar e testar o logon único do Azure AD com o Dovetale, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Dovetale](#create-a-dovetale-test-user)** – para ter um equivalente de Brenda Fernandes no Dovetale que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo Dovetale.

**Para configurar o logon único do Azure AD com o Dovetale, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Dovetale**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/dovetale-tutorial/tutorial_dovetale_samlbase.png)

3. Na seção **Domínio e URLs do Dovetale**, se você desejar configurar o aplicativo no modo iniciado pelo **IDP**, o usuário não precisará executar nenhuma etapa, pois o aplicativo já é pré-integrado ao Azure:

    ![Informações de logon único de Domínio e URLs do Dovetale](./media/dovetale-tutorial/tutorial_dovetale_url1.png)

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de Domínio e URLs do Dovetale](./media/dovetale-tutorial/tutorial_dovetale_url2.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `<COMPANYNAME>.dovetale.com`

    > [!NOTE]
    > Esse valor não é real. Atualize esse valor com a URL de Logon real. Contate a [equipe de suporte ao Cliente do Dovetale](mailto:support@dovetale.com) para obter esse valor.

5. O aplicativo Dovetale espera que as declarações SAML estejam em um formato específico. Configure as seguintes declarações para o aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo. A captura de tela a seguir mostra um exemplo disso.
    
    ![Configurar o logon único](./media/dovetale-tutorial/tutorial_attribute.png)

6. Clique na caixa de seleção **Exibir e editar todos os outros atributos de usuário** na seção **Atributos de Usuário** para expandir os atributos. Realize as seguintes etapas em cada um dos atributos exibidos:

    | Nome do atributo | Valor do atributo |
    | ---------------| --------------- |
    | email | user.mail |
    | first_name | user.givenname |
    | Nome | user.userprincipalname |
    | last_name | user.surname |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/dovetale-tutorial/tutorial_attribute_04.png)

    ![Configurar o logon único](./media/dovetale-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome**, digite o **nome do atributo** mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o valor **NAMESPACE** em branco.

    e. Clique em **OK**.

7. Na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no bloco de notas.

    ![O link de download do Certificado](./media/dovetale-tutorial/tutorial_dovetale_certificate.png) 

8. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/dovetale-tutorial/tutorial_general_400.png)

9. Na seção **Configuração do Dovetale**, clique em **Configurar Dovetale** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Dovetale](./media/dovetale-tutorial/tutorial_dovetale_configure.png)

10. Para configurar o logon único no **Dovetale**, é necessário enviar a **URL de Metadados de Federação de Aplicativos, a ID da Entidade SAML e a URL do Serviço de Logon Único do SAML** para a [equipe de suporte do Dovetale](mailto:support@dovetale.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/dovetale-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/dovetale-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/dovetale-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/dovetale-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="create-a-dovetale-test-user"></a>Criar um usuário de teste do Dovetale

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Dovetale. O Dovetale dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Dovetale, caso ele ainda não exista.

> [!Note]
> Caso precise criar um usuário manualmente, contate a [equipe de suporte do Dovetale](mailto:support@dovetale.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Dovetale.

![Atribuir a função de usuário][200]

**Para atribuir Brenda Fernandes ao Dovetale, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, escolha **Dovetale**.

    ![O link do Dovetale na lista de Aplicativos](./media/dovetale-tutorial/tutorial_dovetale_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Dovetale no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Dovetale.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/dovetale-tutorial/tutorial_general_01.png
[2]: ./media/dovetale-tutorial/tutorial_general_02.png
[3]: ./media/dovetale-tutorial/tutorial_general_03.png
[4]: ./media/dovetale-tutorial/tutorial_general_04.png

[100]: ./media/dovetale-tutorial/tutorial_general_100.png

[200]: ./media/dovetale-tutorial/tutorial_general_200.png
[201]: ./media/dovetale-tutorial/tutorial_general_201.png
[202]: ./media/dovetale-tutorial/tutorial_general_202.png
[203]: ./media/dovetale-tutorial/tutorial_general_203.png