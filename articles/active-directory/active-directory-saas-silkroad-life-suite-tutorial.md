---
title: "Tutorial: Integração do Azure Active Directory com o SilkRoad Life Suite | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o SilkRoad Life Suite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: 0d6af7af7d6b28ff3ea9d518a65b8267a83b71b4
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Tutorial: integração do Active Directory do Azure com o SilkRoad Life Suite

Neste tutorial, você aprenderá a integrar o SilkRoad Life Suite ao Microsoft Azure AD (Azure Active Directory).

A integração do SilkRoad Life Suite ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure AD quem tem acesso ao SilkRoad Life Suite.
- Você pode habilitar seus usuários a fazer logon automaticamente no SilkRoad Life Suite (logon único) com suas contas do Microsoft Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure com o SilkRoad Life Suite, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do SilkRoad Life Suite

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o SilkRoad Life Suite por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Adicionando o SilkRoad Life Suite por meio da galeria
Para configurar a integração do SilkRoad Life Suite com o AD do Azure, você precisa adicionar o SilkRoad Life Suite por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SilkRoad Life Suite por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **SilkRoad Life Suite**, selecione **SilkRoad Life Suite** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![SilkRoad Life Suit na lista de resultados](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o SilkRoad Life Suite, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Microsoft Azure AD precisa saber qual usuário do SilkRoad Life Suite é equivalente a um usuário do Microsoft Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no SilkRoad Life Suite.

No SilkRoad Suite, atribua o valor do **nome de usuário** no Microsoft Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do AD do Azure com o SilkRoad Life Suite, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do SilkRoad Life Suite](#create-a-silkroad-life-suite-test-user)** - para ter um equivalente de Brenda Fernandes no SilkRoad Life Suite que esteja vinculado à representação dela no Microsoft Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Microsoft Azure AD no portal do Azure e configura o logon único no aplicativo SilkRoad Life Suite.

**Para configurar o logon único do AD do Azure com o SilkRoad Life Suite, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **SilkRoad Life Suite**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_samlbase.png)

3. Na seção **URLs e Domínio do SilkRoad Life Suite**, execute as seguintes etapas:

    ![Informações de logon único de URLs e Domínio do SilkRoad Life Suite](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_url1.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP` |
    | `https://<subdomain>.silkroad.com/Authentication/SP` |

    c. Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/` |
    | `https://<subdomain>.silkroad.com/Authentication/` |
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Contate a [equipe de suporte ao Cliente do SilkRoad Life Suite](https://www.silkroad.com/locations/) para obter esses valores. 

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_400.png)
    
6. Na seção **Configuração do SilkRoad Life Suite**, clique em **Configurar o SilkRoad Life Suite** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do SilkRoad Life Suite](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_configure.png) 

7. Faça logon no site da empresa SilkRoad como administrador. 
 
    >[!NOTE] 
    > Para obter acesso ao aplicativo de Autenticação do SilkRoad Life Suite para configurar a federação com o AD do Microsoft Azure, entre em contato com o Suporte da SilkRoad ou com seu representante de Serviços da SilkRoad.

8. Vá para **Provedor de Serviços** e clique em **Detalhes de Federação**. 
   
    ![Logon Único do AD do Azure][10]

9. Clique em **Baixar Metadados de Federação**e salve o arquivo de metadados no computador.
   
    ![Logon Único do AD do Azure][11] 

10. No aplicativo **SilkRoad**, clique em **Fontes de Autenticação**.
   
    ![Logon Único do AD do Azure][12] 

11. Clique em **Adicionar Fonte de Autenticação**. 
   
    ![Logon único do AD do Azure][13] 

12. Na seção **Adicionar Fonte de Autenticação** , execute as seguintes etapas: 
   
    ![Logon único do AD do Azure][14]
  
    a. Em **Opção 2 – Arquivo de Metadados**, clique em **Procurar** para carregar o arquivo de metadados baixado a partir do portal do Azure.
  
    b. Clique em **Criar Provedor de Identidade usando Dados de Arquivo**.

13. Na seção **Fontes de Autenticação**, clique em **Editar**. 
    
     ![Logon Único do AD do Azure][15] 

14. No diálogo **Editar Fonte de Autenticação** , execute as seguintes etapas: 
    
     ![Logon único do AD do Azure][16] 

    a. Para **Habilitado**, selecione **Sim**.

    b. Na caixa de texto **ID da Entidade**, cole o valor da **ID da Entidade do SAML** copiado no portal do Azure.
   
    c. Na caixa de texto **Descrição do IdP**, digite uma descrição para a sua configuração (por exemplo: *SSO do Microsoft Azure AD*).

    d. Na caixa de texto **Arquivo de metadados**, carregue o arquivo de **metadados** baixado no portal do Azure.
  
    e. Na caixa de texto **Nome do IdP**, digite um nome específico para a sua configuração (por exemplo: *Azure SP*).
  
    f. Na caixa de texto **URL de Serviço de Logon**, cole o valor da **URL de Serviço de Saída** que você copiou do portal do Azure.

    g. Na caixa de texto **URL de Serviço de Logon**, cole o valor da **URL de Serviço de Logon Único do SAML** que você copiou do portal do Azure.

    h. Clique em **Salvar**.

15. Desabilite todas as outras fontes de autenticação. 
    
     ![Logon Único do AD do Azure][17]

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-silkroad-life-suite-test-user"></a>Criar um usuário de teste do SilkRoad Life Suite

Nesta seção, você cria um usuário chamado Brenda Fernandes no SilkRoad Life Suite. Trabalhe com a [equipe de suporte ao cliente do SilkRoad Life Suite](https://www.silkroad.com/locations/) para adicionar os usuários na plataforma do SilkRoad Life Suite. Os usuários devem ser criados e ativados antes de usar o logon único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao SilkRoad Life Suite.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao SilkRoad Life Suite, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **SilkRoad Life Suite**.

    ![Link do SilkRoad Life Suite na lista de aplicativos](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clica no bloco SilkRoad Life Suite no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo SilkRoad Life Suite.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
