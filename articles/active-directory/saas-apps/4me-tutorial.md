---
title: 'Tutorial: Integração do Azure Active Directory ao 4me | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e 4me.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 983eecc6-41f8-49b7-b7f6-dcf833dde121
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: jeedes
ms.openlocfilehash: c9134ceebca696ed2b3376a69e26c2ea06f4f0f6
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39507117"
---
# <a name="tutorial-azure-active-directory-integration-with-4me"></a>Tutorial: Integração do Active Directory do Azure ao 4me

Neste tutorial, você aprende a integrar o 4me ao Microsoft Azure Active Directory (Azure AD).

A integração di 4me ao Microsoft Azure Active Directory oferece os seguintes benefícios:

- No Microsoft Azure Active Directory que tem acesso a 4me.
- Você pode permitir que seus usuários façam logon automaticamente em 4me (logon único) com suas contas do Microsoft Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory com 4me, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único 4me

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do 4me da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-4me-from-the-gallery"></a>Adição do 4me da galeria
Para configurar a integração de 4me ao Microsoft Azure Active Directory, você precisa adicionar 4me por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o 4me por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **4me**, selecione **4me** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![4me na lista de resultados](./media/4me-tutorial/tutorial_4me_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure Active Directory com o 4me, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do 4me é equivalente a um usuário do Microsoft Azure Active Directory. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure Active Directory e o usuário relacionado em 4me.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o 4me, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar de um usuário de teste do 4me](#create-a-4me-test-user)** – para ter um equivalente de Brenda Fernandes no 4me vinculado à representação do usuário no Microsoft Azure Active Directory.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Microsoft Azure Active Directory no portal do Azure e configura o logon único no aplicativo 4me.

**Para configurar o logon único do Microsoft Azure Active Directory com o 4me, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **4me**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/4me-tutorial/tutorial_4me_samlbase.png)

3. Na seção **URLs e Domínio do 4me**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do 4me](./media/4me-tutorial/tutorial_4me_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão:

    | Ambiente| URL|
    |---|---|
    | PRODUÇÃO | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
  
    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:
    
    | Ambiente| URL|
    |---|---|
    | PRODUÇÃO | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do 4me](mailto:support@4me.com) para obter esses valores. 
 
4. O aplicativo 4me espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. A captura de tela a seguir mostra um exemplo disso.
    
    ![Configurar o logon único](./media/4me-tutorial/tutorial_4me_attribute.png)

5. Na seção **Atributos do usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML na imagem acima e siga as etapas abaixo:
    
    | Nome do atributo | Valor do atributo |
    | ---------------| --------------- |    
    | first_name | user.givenname |
    | last_name | user.surname |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/4me-tutorial/tutorial_attribute_04.png)

    ![Configurar o logon único](./media/4me-tutorial/tutorial_attribute_05.png)
    
    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.
    
    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o **Namespace** em branco.
    
    d. Clique em **Ok**

6. Na seção **Certificado de Autenticação SAML**, copie o valor da **IMPRESSÃO DIGITAL** do seu computador.

    ![O link de download do Certificado](./media/4me-tutorial/tutorial_4me_certificate.png) 

7. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/4me-tutorial/tutorial_general_400.png)

8. Na seção **Configuração do 4me**, clique em **Configurar 4me** para abrir a janela **Configurar logon**. Copie a **URL do serviço de logon único do SAML e a URL de logoff** da **seção de Referência Rápida.**

    ![Configuração do 4me](./media/4me-tutorial/tutorial_4me_configure.png) 

9. Em outra janela do navegador da Web, faça logon no 4me como administrador.

10. No canto superior esquerdo, clique no logo **Configurações** e, na barra do lado esquerdo, clique em **Logon único**.

    ![Configurações do 4me](./media/4me-tutorial/tutorial_4me_settings.png)

11. Na página **Logon Único**, execute as seguintes etapas:

    ![4me singleasignon](./media/4me-tutorial/tutorial_4me_singlesignon.png)

    a. Selecione a opção **Habilitado**.

    b. Na caixa de texto **URL de Logoff Remoto**, cole o valor da **URL de Saída** que você copiou do portal do Azure.

    c. Na seção **SAML**, na caixe de texto cole o valor da **URL de Serviço** de Logon Único do **SAML** que você copiou do portal do Azure.

    d. Na caixa de texto **Impressão digital do certificado** caixa de texto, cole o valor **IMPRESSÃO DIGITAL** separado por dois-pontos na ordem de duplets (AA:BB:CC:DD:EE:FF:GG:HH:II), que você copiou do portal do Azure.

    e. Clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/4me-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/4me-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/4me-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/4me-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-4me-test-user"></a>Criar um usuário de teste 4me

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no 4me. O 4me dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o 4me, caso ele ainda não exista.

>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte do 4me](mailto:support@4me.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao 4me.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao 4me, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **4me**.

    ![O link do 4me na lista de Aplicativos](./media/4me-tutorial/tutorial_4me_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar na peça de 4me no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo de 4me.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/4me-tutorial/tutorial_general_01.png
[2]: ./media/4me-tutorial/tutorial_general_02.png
[3]: ./media/4me-tutorial/tutorial_general_03.png
[4]: ./media/4me-tutorial/tutorial_general_04.png

[100]: ./media/4me-tutorial/tutorial_general_100.png

[200]: ./media/4me-tutorial/tutorial_general_200.png
[201]: ./media/4me-tutorial/tutorial_general_201.png
[202]: ./media/4me-tutorial/tutorial_general_202.png
[203]: ./media/4me-tutorial/tutorial_general_203.png

