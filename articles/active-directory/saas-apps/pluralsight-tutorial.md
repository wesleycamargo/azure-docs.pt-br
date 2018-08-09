---
title: 'Tutorial: integração do Azure Active Directory ao Pluralsight | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Pluralsight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4c3f07d2-4e1f-4ea3-9025-c663f1f2b7b4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: jeedes
ms.openlocfilehash: 81f7e6f7610eb855bd4df1eaf45c6d016befc133
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443607"
---
# <a name="tutorial-azure-active-directory-integration-with-pluralsight"></a>Tutorial: Integração do Azure Active Directory com o Pluralsight

Neste tutorial, você aprenderá como integrar o Pluralsight ao Azure AD (Azure Active Directory).

A integração do Pluralsight ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao Pluralsight.
- Você pode permitir que seus usuários façam logon automaticamente no Pluralsight (Logon único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure com o Pluralsight, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Pluralsight

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Como adicionar o Pluralsight da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-pluralsight-from-the-gallery"></a>Como adicionar o Pluralsight da galeria
Para configurar a integração do Pluralsight ao AD do Azure, você precisará adicionar o Pluralsight da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Pluralsight por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **Pluralsight**, selecione **Pluralsight** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Pluralsight na lista de resultados](./media/pluralsight-tutorial/tutorial_pluralsight_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Pluralsight, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Pluralsight é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Pluralsight.

No Pluralsight, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do AD do Azure com o Pluralsight, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do Pluralsight](#create-a-pluralsight-test-user)** – para ter um equivalente de Brenda Fernandes no Pluralsight que esteja vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único em seu aplicativo Pluralsight.

**Para configurar o logon único do AD do Azure com o Pluralsight, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Pluralsight**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/pluralsight-tutorial/tutorial_pluralsight_samlbase.png)

1. Na seção **URLs e Domínio do Pluralsight**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Pluralsight](./media/pluralsight-tutorial/tutorial_pluralsight_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<instancename>.pluralsight.com/sso/<companyname>`

    b. Na caixa de texto **Identificador**, digite a URL: `www.pluralsight.com`

    c. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<instancename>.pluralsight.com/sp/ACS.saml2`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do Pluralsight](mailto:support@pluralsight.com) para obter esses valores. 

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/pluralsight-tutorial/tutorial_pluralsight_certificate.png) 

1. O objetivo desta seção é habilitar o logon único do Azure AD no Portal do Azure e configurar o SSO no aplicativo Pluralsight.

    O aplicativo Pluralsight espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra um exemplo disso.

    ![Configurar o logon único](./media/pluralsight-tutorial/tutorial_pluralsight_attribute.png)

    >[!NOTE]
    >Também é possível adicionar o atributo **“ID Exclusiva”** com o valor apropriado, como EmployeeID, ou algo que se adapte à sua organização. Observe também que esse não é o atributo obrigatório; no entanto, é possível adicioná-lo para identificar o usuário exclusivo. 

1. Para adicionar os **atributos de token SAML**necessários, para cada linha mostrada na tabela abaixo, realize as seguintes etapas:
   
   | Nome do atributo | Valor do atributo |
   | ---| --- |
   | Nome |user.givenname |
   | Sobrenome |user.surname |
   | Email |user.mail |
   
   a. Clique em **add user attribute** to open the **Adicionar Atributo de Usuário** .
    
     ![Configurar o logon único](./media/pluralsight-tutorial/tutorial_pluralsight_addattribute.png)
  
   b. Na caixa de texto **Nome do Atributo** , digite o nome do atributo mostrado para essa linha.
  
   c. Na lista **Valor do Atributo** , selecione o valor do atributo mostrado para essa linha.
  
   d. Clique em **OK**.    

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/pluralsight-tutorial/tutorial_general_400.png)

1. Para configurar o SSO para seu aplicativo, entre em contato com a equipe de [Serviços Profissionais do Pluralsight](mailTo:professionalservices@pluralsight.com) e forneça o arquivo de metadados baixado.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/pluralsight-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/pluralsight-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/pluralsight-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/pluralsight-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-pluralsight-test-user"></a>Criar um usuário de teste do Pluralsight

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Pluralsight. Trabalhe com a [equipe de suporte ao cliente do Pluralsight](mailto:support@pluralsight.com) para adicionar usuários à conta do Pluralsight.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Pluralsight.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Pluralsight, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Pluralsight**.

    ![O link do Pluralsight na Lista de aplicativos](./media/pluralsight-tutorial/tutorial_pluralsight_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Pluralsight no Painel de Acesso, você deve ser conectado automaticamente ao aplicativo Pluralsight.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/pluralsight-tutorial/tutorial_general_01.png
[2]: ./media/pluralsight-tutorial/tutorial_general_02.png
[3]: ./media/pluralsight-tutorial/tutorial_general_03.png
[4]: ./media/pluralsight-tutorial/tutorial_general_04.png

[100]: ./media/pluralsight-tutorial/tutorial_general_100.png

[200]: ./media/pluralsight-tutorial/tutorial_general_200.png
[201]: ./media/pluralsight-tutorial/tutorial_general_201.png
[202]: ./media/pluralsight-tutorial/tutorial_general_202.png
[203]: ./media/pluralsight-tutorial/tutorial_general_203.png

