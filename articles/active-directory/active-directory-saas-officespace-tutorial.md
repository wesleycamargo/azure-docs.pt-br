---
title: "Tutorial: Integração do Azure Active Directory com o OfficeSpace Software | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o OfficeSpace Software."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: fb61a2d622b0d829a134b6ce5dfef6e9fb44fa1e
ms.lasthandoff: 03/22/2017


---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Tutorial: Integração do Active Directory do Azure com o OfficeSpace Software

Neste tutorial, você aprende a integrar o OfficeSpace Software ao Azure AD (Azure Active Directory).

A integração do OfficeSpace Software ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao OfficeSpace Software
- É possível permitir que os usuários sejam conectados automaticamente ao OfficeSpace Software (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao OfficeSpace Software, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do OfficeSpace Software


> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o OfficeSpace Software por meio da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-officespace-software-from-the-gallery"></a>Adicionando o OfficeSpace Software por meio da galeria
Para configurar a integração do OfficeSpace Software no Azure AD, é necessário adicionar o OfficeSpace Software por meio da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o OfficeSpace Software por meio da galeria, realize as seguintes etapas:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **OfficeSpace Software**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_001.png)

5. No painel de resultados, escolha **OfficeSpace Software** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o OfficeSpace Software, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do OfficeSpace Software é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do OfficeSpace Software.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no OfficeSpace Software.

Para configurar e testar o logon único do Azure AD com o OfficeSpace Software, é necessário concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do OfficeSpace Software](#creating-an-officespace-software-test-user)** – para ter um equivalente de Brenda Fernandes no OfficeSpace Software que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal de Gerenciamento do Azure e configurará o logon único em seu aplicativo OfficeSpace Software.

**Para configurar o logon único do Azure AD com o OfficeSpace Software, realize as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, na página de integração do aplicativo do **OfficeSpace Software**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_01.png)

3. Na seção **URLs e Domínio do OfficeSpace Software**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_02.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. Na caixa de texto **Identificador**, digite um valor usando o seguinte padrão: `<company name>.officespacesoftware.com`

    > [!NOTE] 
    > Observe que esses não são os valores reais. É necessário atualizar esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do OfficeSpace Software](mailto:support@officespacesoftware.com) para obter esses valores. 

4. O aplicativo OfficeSpace Software espera que as declarações SAML estejam em um formato específico. Configure as seguintes declarações para o aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo. A captura de tela a seguir mostra um exemplo disso.
    
    ![Configurar o logon único](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_03.png)

5. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, selecione **user.mail** como **Identificador de Usuário** e, para cada linha mostrada na tabela a seguir, execute as seguintes etapas:
    
    | Nome do atributo | Valor do atributo |
    | --- | --- |    
    | email | user.mail |
    | name | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_04.png)

    ![Configurar Logon Único](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_05.png)
    
    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.
    
    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    d. Clique em **Ok**

6. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_08.png) 

7. Clique em **Salvar**.

    ![Configurar Logon Único](./media/active-directory-saas-officespace-tutorial/tutorial_general_400.png)

8. Na seção **Configuração do OfficeSpace Software**, clique em **Configurar o OfficeSpace Software** para abrir a janela **Configurar logon**.

    ![Configurar Logon Único](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_09.png) 

    ![Configurar Logon Único](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_10.png)

9. Em outra janela do navegador da Web, faça logon no locatário do OfficeSpace Software como administrador.

10. Acesse **Configurações** e clique em **Conectores**.

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_002.png)

11. Clique em **Autenticação SAML**.

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_003.png)

12. Na seção **Autenticação SAML** , realize as seguintes etapas:

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_004.png)

    a. Na caixa de texto **URL do provedor de logoff**, insira o valor da **URL de Saída** da janela de configuração de aplicativo do Azure AD.

    b. Na caixa de texto **URL de destino IdP do cliente**, insira o valor da **URL de Serviço de Logon Único SAML** da janela de configuração de aplicativo do Azure AD.

    c. Copie o valor de **Impressão Digital** do certificado baixado e cole-o na caixa de texto **Impressão digital do certificado do IdP do cliente**. 

    d. Clique em **Salvar Configurações**.

    > [!NOTE]
    > Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI) 
  

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no portal de Gerenciamento do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-officespace-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-officespace-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-officespace-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-officespace-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**. 



### <a name="creating-an-officespace-software-test-user"></a>Criando um usuário de teste do OfficeSpace Software

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no OfficeSpace Software. O OfficeSpace Software dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o OfficeSpace Software, caso ele ainda não exista.

> [!NOTE]
> Se precisar criar um usuário manualmente, será necessário contatar a [equipe de suporte do OfficeSpace Software](mailto:support@officespacesoftware.com).


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao OfficeSpace Software.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao OfficeSpace Software, realize as seguintes etapas:**

1. No portal de gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **aplicativos empresariais** e clique em **todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **OfficeSpace Software**.

    ![Configurar o logon único](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_50.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    


### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco OfficeSpace Software no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo OfficeSpace Software.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_203.png
