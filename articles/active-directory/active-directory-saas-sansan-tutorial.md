---
title: 'Tutorial: integração do Azure Active Directory ao SanSan | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SanSan.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-sansan"></a>Tutorial: integração do Azure Active Directory ao SanSan
Neste tutorial, você aprenderá a integrar o SanSan ao Azure AD (Azure Active Directory).

A integração do SanSan ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao SanSan
* Você pode habilitar os usuários a entrar automaticamente no SanSan (Logon Único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao SanSan, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do SanSan

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o Logon Único do Microsoft Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o SanSan da galeria
2. Configurar e testar o Logon Único do Microsoft Azure AD

## <a name="adding-sansan-from-the-gallery"></a>Adicionando o SanSan da galeria
Para configurar a integração do SanSan ao Azure AD, você precisará adicionar o SanSan da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SanSan da galeria, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **SanSan**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_01.png)
7. No painel de resultados, selecione **SanSan** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_06.png)

## <a name="configuring-and-testing-microsoft-azure-ad-single-sign-on"></a>Configurar e testar o Logon Único do Microsoft Azure AD
Nesta seção, você configurará e testará o Logon Único do Microsoft Azure AD com o SanSan, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do SanSan é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SanSan.
Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como sendo o valor de **Nome de Usuário** no SanSan.

Para configurar e testar o Logon Único do Microsoft Azure AD com o SanSan, você precisará concluir os seguintes blocos de construção:

1. **[Configuração do Logon Único do Microsoft Azure AD](#configuring-azure-ad-single-single-sign-on)** - para habilitar os usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o Logon Único do Microsoft Azure AD com Brenda Fernandes.
3. **[Criação de um usuário de teste do SanSan](#creating-an-sansan-test-user)** : para ter um equivalente de Brenda Fernandes no SanSan que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o Logon Único do Microsoft Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-microsoft-azure-ad-single-sign-on"></a>Configuração do Logon Único do Microsoft Azure AD
Nesta seção, você habilitará o logon único do Microsoft Azure AD no portal clássico e configurará o logon único em seu aplicativo SanSan.

**Para configurar o Logon Único do Microsoft Azure AD com o SanSan, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **SanSan** , clique em Configurar logon único para abrir o diálogo Configurar Logon Único.
   
    ![Configurar Logon Único](./media/active-directory-saas-sansan-tutorial/tutorial_general_05.png) 
2. Na página **Como você deseja que os usuários façam logon no SanSan**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_04.png) 
   
    a. Na caixa de texto **URL de Logon** , digite a URL no seguinte padrão:
   
   | Ambiente | URL |
   |:--- |:--- |
   | PC Web |`https://ap.sansan.com/v/saml2/<company name>/acs` |
   | Aplicativo móvel nativo |`https://internal.api.sansan.com/saml2/<company name>/acs` |
   | Configurações de navegador móvel |`https://ap.sansan.com/s/saml2/<company name>/acs` |

    b. Na caixa de texto **Identificador** , digite a URL no seguinte padrão: 

      | Ambiente             | URL |
      | :--                     | :-- |
      | PC Web                  | `https://ap.sansan.com/v/saml2/<company name>`|
      | Aplicativo móvel nativo       | `https://internal.api.sansan.com/saml2/<company name>` |
      | Configurações de navegador móvel | `https://ap.sansan.com/s/saml2/<company name>` |


    c. Clique em **Avançar**.

1. Na página **Configurar logon único no SanSan** , execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_05.png) 
   
    a. Clique em **Baixar certificado**e salve o arquivo em seu computador.
   
    b. Clique em **Avançar**.
2. Para configurar o SSO para seu aplicativo, entre em contato com a equipe de suporte do SanSan e ela auxiliará na configuração do SSO. Forneça as seguintes informações:
   
   • O **certificado**
   
   • A **ID do Provedor de Identidade**
   
   • A **URL de SSO do SAML**
   
   • A **URL do Serviço de Logoff Único**

> [!NOTE]
> A configuração do navegador PC também funciona para aplicativos móveis e navegadores móveis, juntamente com o PC Web. 
> 
> 

1. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
2. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.
Na lista de usuários, selecione **Brenda Fernandes**.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sansan-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sansan-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sansan-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sansan-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sansan-tutorial/create_aaduser_06.png) 
   
   a. Na caixa de texto **Nome**, digite **Brenda**.  
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   
   e. Clique em **Próximo**.
7. Na página da caixa de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sansan-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sansan-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-an-sansan-test-user"></a>Criação de um usuário de teste do SanSan
Nesta seção, você criará uma usuária chamada Brenda Fernandes no SanSan. O aplicativo SanSan precisa que todos os usuários sejam provisionados no aplicativo antes de fazer o SSO. 

> [!NOTE]
> Se você precisa criar um usuário ou um lote de usuários manualmente, entre em contato com a equipe de suporte do SanSan.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao SanSan.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao SanSan, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, escolha **SanSan**.
   
    ![Configurar o logon único](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
Nesta seção, você testará sua configuração de logon único do Microsoft Azure AD usando o Painel de Acesso.
Quando você clica no bloco SanSan no Painel de Acesso, deve ser conectado automaticamente ao seu aplicativo SanSan.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_205.png



<!--HONumber=Oct16_HO2-->


