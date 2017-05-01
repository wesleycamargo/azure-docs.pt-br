---
title: "Tutorial: integração do Azure Active Directory com a Adobe Creative Cloud | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e a Adobe Creative Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 9ba1171e-56b1-4475-b308-58637d35e5a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 3d13608612c77236346b0e98551d7fc427d602e1
ms.lasthandoff: 04/07/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Tutorial: integração do Azure Active Directory com a Adobe Creative Cloud

Neste tutorial, você aprenderá a integrar a Adobe Creative Cloud ao Azure AD (Azure Active Directory).

A integração da Adobe Creative Cloud ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso à Adobe Creative Cloud
- É possível permitir que seus usuários façam logon automaticamente na Adobe Creative Cloud (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a Adobe Creative Cloud, são necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura da Creative Cloud habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Adobe Creative Cloud da Galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Adicionando o Adobe Creative Cloud da Galeria
Para configurar a integração da Adobe Creative Cloud com o Azure AD, será necessário adicionar a Creative Cloud da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar a Adobe Creative Cloud da galeria, siga as etapas abaixo:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Adobe Creative Cloud**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_000.png)

5. No painel de resultados, selecione **Adobe Creative Cloud** e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com a Adobe Creative Cloud, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário da Adobe Creative Cloud é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado da Adobe Creative Cloud.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como o valor de **Nome de Usuário** na Adobe Creative Cloud.

Para configurar e testar o logon único do Azure AD com a Adobe Creative Cloud, é necessário concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste da Adobe Creative Cloud](#creating-an-adobe-creative-cloud-test-user)** – para ter um equivalente de Brenda Fernandes na Adobe Creative Cloud que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal de Gerenciamento do Azure e configura o logon único em seu aplicativo Adobe Creative Cloud.

**Para configurar o logon único do Azure AD com a Adobe Creative Cloud, siga as etapas abaixo:**

1. No portal de Gerenciamento do Azure, na página de integração de aplicativos do **Adobe Creative Cloud**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_01.png)

3. Na seção **Domínio e URLs da Adobe Creative Cloud**, se você desejar configurar o aplicativo em modo iniciado pelo **IdP**, siga as etapas abaixo:

    ![Configurar Logon Único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_url1.png)

    a. Na caixa de texto **Identificador**, digite o valor como `https://www.okta.com/saml2/service-provider/<token>`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<company name>.okta.com/auth/saml20/accauthlinktest`

    > [!NOTE] 
    > Observe que esses não são os valores reais. Você precisa atualizar esses valores com o Identificador e a URL de Resposta reais. Aqui, sugerimos que você use o valor exclusivo de cadeia de caracteres no Identificador. Se precisar criar um usuário manualmente, será necessário contatar a equipe de suporte da Adobe Creative Cloud.

4. Na seção **Domínio e URLs da Adobe Creative Cloud**, se você desejar configurar o aplicativo em modo iniciado por **SP**, siga as etapas abaixo:

    ![Configurar Logon Único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_url2.png)

    a. Clique na opção **Mostrar URL configurações avançadas**

    b. Na caixa de texto **URL de Logon**, digite o valor como: `https://adobe.com`

5. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_05.png) 

6. Na seção **Configuração do Adobe Creative Cloud**, clique em **Configurar a Adobe Creative Cloud** para abrir a janela **Configurar logon**. Copie a **ID da entidade SAML** e a **URL de serviço do SAML SSO** da seção Referência rápida.

    ![Configurar o logon único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_06.png) 

7. Em uma janela diferente do navegador da Web, faça logon em seu locatário da Adobe Creative Cloud como administrador.

8.  Vá para **Identidade** no painel de navegação esquerdo e clique em seu domínio. Em seguida, siga as etapas na seção **Configuração do logon único necessária**.

    ![Configurações](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_001.png "Configurações")

9. Clique em **Procurar** para carregar o certificado baixado do Azure AD para o **Certificado do IdP**.

10. Na caixa de texto **Emissor IdP**, coloque o valor da **ID da entidade SAML** que você copiou da seção **Configurar logon** no portal do Azure.

11. Na caixa de texto **URL de logon IdP**, coloque o valor da **URL de serviço do SAML SSO** que você copiou da seção **Configurar logon** no portal do Azure.

12. Selecione **Redirecionamento HTTP** como **Associação IdP**.

13. Selecione **Endereço de email** como **Configuração de logon do usuário**.
 
14. Clique no botão **Salvar** .

15. Agra o painel apresentará o arquivo XML **"Baixar metadados"**. Ele contém a URL EntityDescriptor e a URL AssertionConsumerService da Adobe. Abra o arquivo e configure-os no aplicativo Azure AD.

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_002.png)

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Use o valor EntityDescriptor que a Adobe forneceu a você para **Identificador** na caixa de diálogo **Definir configurações de aplicativo**.

    b. Use o valor AssertionConsumerService que a Adobe forneceu a você para **URL de resposta** na caixa de diálogo **Definir configurações de aplicativo**.
 
### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no portal de Gerenciamento do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**. 

### <a name="creating-an-adobe-creative-cloud-test-user"></a>Criando um usuário de teste da Adobe Creative Cloud

Para que os usuários do Azure AD façam logon na Adobe Creative Cloud, eles devem ser provisionados na Adobe Creative Cloud.  
No caso da Adobe Creative Cloud, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa da Adobe Creative Cloud como administrador.

2. Clique em **Pessoas**.

    ![Pessoas](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_001.png "Pessoas")

3. Clique em **Convidar Usuário**.

    ![Convidar Usuários](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_002.png "Convidar Usuários")

4. Na página **Convidar Pessoas**, execute as seguintes etapas:

    ![Convidar Pessoas](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_003.png "Convidar Pessoas")

    a. Na caixa de texto **Email**, digite o endereço de email da conta de Brenda Fernandes.
    
    b. Clique em **Convidar**.

    > [!NOTE]
    > O titular da conta do Active Directory do Azure receberá um email e seguirá um link para confirmar a conta antes que ela se torne ativa.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso à Adobe Creative Cloud.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes à Adobe Creative Cloud, siga as etapas abaixo:**

1. No portal de gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **aplicativos empresariais** e clique em **todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Adobe Creative Cloud**.

    ![Configurar o logon único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_50.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Adobe Creative Cloud no Painel de Acesso, seu logon deverá ser feito automaticamente no aplicativo Adobe Creative Cloud.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_203.png
