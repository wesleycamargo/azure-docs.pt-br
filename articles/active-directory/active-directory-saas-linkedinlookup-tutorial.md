---
title: "Tutorial: Integração do Azure Active Directory ao LinkedIn Lookup | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o LinkedIn Lookup."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a2757a39-1ead-4a3e-91e4-270be3055683
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: f343d8dbbd80de1a50b625e949377aa7c48d0cae
ms.contentlocale: pt-br
ms.lasthandoff: 04/29/2017


---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-lookup"></a>Tutorial: Integração do Azure Active Directory ao LinkedIn Lookup

Neste tutorial, você aprenderá a integrar o LinkedIn Lookup ao Azure AD (Active Directory).

A integração do LinkedIn Lookup ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao LinkedIn Lookup
- É possível permitir que seus usuários entrem automaticamente no LinkedIn Lookup (Logon Único) com as respectivas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao LinkedIn Lookup, são necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do LinkedIn Lookup

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o LinkedIn Lookup usando a galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-linkedin-lookup-from-the-gallery"></a>Adicionando o LinkedIn Lookup usando a galeria
Para configurar a integração do LinkedIn Lookup ao Azure AD, é necessário adicionar o LinkedIn Lookup da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o LinkedIn Lookup da galeria, siga as etapas abaixo:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **LinkedIn Lookup**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_search.png)

5. No painel de resultados, selecione **LinkedIn Lookup** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você vai configurar e testar o logon único do Azure AD com o LinkedIn Lookup, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do LinkedIn Lookup é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no LinkedIn Lookup.

Essa relação de vinculação é estabelecida por meio da atribuição do valor de **nome de usuário** no Azure AD como o valor de **Nome de Usuário** no LinkedIn Lookup.

Para configurar e testar o logon único do Azure AD com o LinkedIn Lookup, é necessário concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do LinkedIn Lookup](#creating-an-linkedin-lookup-test-user)**: para ter um equivalente de Brenda Fernandes no LinkedIn Lookup que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você vai habilitar o logon único do Azure AD no Portal do Azure e configurar o logon único em seu aplicativo LinkedIn Lookup.

**Para configurar o logon único do Azure AD com o LinkedIn Lookup, siga as etapas abaixo:**

1. No Portal do Azure, na página de integração de aplicativos do **LinkedIn Lookup**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, em **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_samlbase.png)

3. Em uma janela diferente do navegador da Web, faça logon no site do seu **LinkedIn Lookup** como administrador.

4. Em **Centro de Contas**, clique em **Configurações Globais** em **Configurações**. Além disso, selecione **Lookup** na lista suspensa.

    ![Configurar Logon Único](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_LinkedIn_admin_011.png)

5. Clique em **OU clique aqui para carregar e copiar campos individuais do formulário** e copie a **ID da Entidade** e a **URL do ACS (Serviço do Consumidor de Declaração)**

    ![Configurar Logon Único](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_LinkedIn_admin_032.png)

6. No Portal do Azure, em **Domínio e URLs do LinkedIn Lookup**, siga as etapas abaixo se quiser configurar o aplicativo em modo iniciado por **IDP**:

    ![Configurar o logon único](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_url.png)

    a. Na caixa de texto **Identificador**, insira a **ID da Entidade** copiada do Portal do LinkedIn 

    b. Na caixa de texto **URL de Resposta**, insira a **URL ACS (acesso do consumidor de declaração)** copiada do Portal do LinkedIn

7. Marque **Mostrar configurações avançadas de URL**, se quiser configurar o aplicativo no modo iniciado em **SP**:

    ![Configurar Logon Único](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://www.linkedIn.com/checkpoint/enterprise/login/<AccountId>?application=lookup`
     
    > [!NOTE] 
    > Observe que esse não é o valor real. O usuário precisa atualizar esses valores com a URL de Logon real. Contate a [equipe de suporte do Cliente LinkedIn Lookup](https://business.LinkedIn.com/lookup) para obter esse valor.

8. Seu aplicativo **LinkedIn Lookup** espera que as declarações SAML estejam em um formato específico. O usuário precisa adicionar mapeamentos de atributos personalizados à configuração de atributos de token SAML. A captura de tela a seguir mostra um exemplo. O valor padrão do **Identificador de Usuário** é **user.userprincipalname**, mas o LinkedIn Lookup espera que isso seja mapeado com o endereço de email do usuário. Você pode usar o atributo **user.mail** na lista ou usar o valor do atributo apropriado com base na configuração da sua organização. 

    ![Configurar Logon Único](./media/active-directory-saas-LinkedInlookup-tutorial/updateusermail.png)
    
9. Na seção **Atributos de Usuário**, clique em **Exibir e editar todos os outros atributos de usuário** e defina os atributos. O usuário precisa adicionar outra declaração denominada **departament** e o valor deve ser mapeado para **user.department**.

    | Nome do atributo | Valor do atributo |
    | --- | --- |    
    | department| user.department |

       ![Creating an Azure AD test user](./media/active-directory-saas-LinkedInlookup-tutorial/userattribute.png)

       a. Click **Add attribute** to open the attribute details page add the department attribute as shown below-

       ![Creating an Azure AD test user](./media/active-directory-saas-LinkedInlookup-tutorial/adduserattribute.png)
   
       b. Click **Ok** to save the attribute.

10. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo XML em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_certificate.png) 

11. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_400.png)

12. Vá para a seção **Configurações de administração do LinkedIn**. Carregue o arquivo XML que você baixou do Portal do Azure clicando na opção **Carregar o arquivo XML**.

    ![Configurar Logon Único](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedIn_metadata_03.png)

13. Clique em **Ativar** para habilitar o SSO. O status do SSO é alterado de **Não Conectado** para **Conectado**

    ![Configurar Logon Único](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedIn_admin_05.png)

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_01.png) 

2. Clicar em **Usuários e grupos** e **Todos os usuários**.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_02.png) 

3. Clique em **Adicionar** para abrir o diálogo **Usuário**.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-an-linkedin-lookup-test-user"></a>Criação de um usuário de teste do LinkedIn Lookup

O aplicativo LinkedIn Lookup dá suporte ao provisionamento de usuário JIT (Just-In-Time) e, após a autenticação, os usuários são automaticamente criados no aplicativo. Ative **atribuir licenças automaticamente** para atribuir uma licença ao usuário.
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedin_admin_license.png)

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure ao conceder acesso ao LinkedIn Lookup.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao LinkedIn Lookup, siga as etapas abaixo:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **LinkedIn Lookup**.

    ![Configurar Logon Único](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco LinkedIn Lookup no Painel de Acesso, você deverá ser redirecionado para a página Organizacional, na qual é preciso fornecer os detalhes da sua conta pessoal do LinkedIn. Ele vincula a sua conta pessoal à sua conta de negócios do LinkedIn. 

Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_203.png


