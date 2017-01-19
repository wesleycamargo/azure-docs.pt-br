---
title: "Tutorial: integração do Azure Active Directory ao Atlassian Cloud | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Atlassian Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6d53de158a44116058ab8241d5182414f8f306fb
ms.openlocfilehash: 243e81ecd1e1648bfd6b2ca6e6268ea1a18d2036


---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Tutorial: Integração do Azure Active Directory com o Atlassian Cloud

Neste tutorial, você aprenderá a integrar o Atlassian Cloud ao Azure AD (Azure Active Directory).

A integração do Atlassian Cloud ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao Atlassian Cloud
- Você pode habilitar os usuários a fazer logon automaticamente no Atlassian Cloud (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Atlassian Cloud, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Atlassian Cloud


>[!NOTE] 
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Atlassian Cloud da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-atlassian-cloud-from-the-gallery"></a>Adição do Atlassian Cloud da galeria
Para configurar a integração do Atlassian Cloud ao Azure AD, você precisará adicionar o Atlassian Cloud da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Atlassian Cloud da galeria, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Atlassian Cloud**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_01.png)

7. No painel de resultados, selecione **Atlassian Cloud** e clique em **Concluir** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Atlassian Cloud, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Atlassian Cloud é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Atlassian Cloud.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD ao valor de **Nome de usuário** no Atlassian Cloud.

Para configurar e testar o logon único do Azure AD com o Atlassian Cloud, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do Atlassian Cloud](#creating-Atlassian-cloud-test-user)** - para ter um equivalente de Brenda Fernandes no Atlassian Cloud que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo Atlassian Cloud.


**Para configurar o logon único do Azure AD com o Atlassian Cloud, execute as seguintes etapas:**

1. No portal clássico, na página de integração do aplicativo **Atlassian Cloud**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
     
    ![Configurar o logon único][6] 

2. Na página **Como você deseja que os usuários façam logon no Atlassian Cloud**, selecione **Logon Único do Azure AD** e clique em **Avançar**.

    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_03.png) 

3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_04.png) 

    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para fazer logon no seu aplicativo Atlassian Cloud usando o seguinte padrão: `https://<instancename>.atlassian.net`
    
    b. Na caixa de texto **Identificador** , digite a URL no seguinte padrão:`https://id.atlassian.com/login`

    >[!NOTE] 
    >Você pode obter o valor exato do **Identificador** na tela Configuração do SAML do Atlassian Cloud.

    c. Clique em **Avançar**
 
4. Na página **Configurar logon único no Atlassian Cloud**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_05.png)

    a. Clique em **Baixar certificado**e salve o arquivo em seu computador.

    b. Clique em **Próximo**.


5. Para configurar o SSO para seu aplicativo, faça logon no Portal do Atlassian usando os direitos de administrador.

6. Na seção Autenticação do painel de navegação esquerdo, clique em **Domínios**.

    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

    a. Na caia de texto, digite seu nome de domínio e clique em **Adicionar domínio**.
        
    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_07.png)

    b. Para verificar o domínio, clique em **Verificar**. 

    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_08.png)

    c. Baixe o arquivo html de verificação de domínio, carregue-o na pasta raiz do site do seu domínio e, em seguida, clique em **Verificar domínio**.
    
    ![Configurar o logon único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_09.png)

    d. Depois que o domínio for verificado, o valor do campo **Status** será **Verificado**.

    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_10.png)

7. na barra de navegação à esquerda, clique em **SAML**.
 
    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

8. Crie uma nova Configuração de SAML e adicione a configuração do provedor de identidade.

    Copie a ID da entidade de valor do Azure AD e cole-a no campo ID de Entidade de Provedor de Identidade.
    
    b. Copie a URL de SSO do SAML e cole-a na caixa URL de SSO do provedor de identidade.

    c. Abra o certificado baixado do Azure AD no Bloco de Notas e copie os valores sem as linhas Begin e End e cole-os na caixa Certificado X509 público.
    
    d. Salve as configurações.

    ![Configurar o logon único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)
  
9. Atualize as configurações do Azure AD para certificar-se de que você configurou a URL correta do Identificador

    a. Copie a ID de identidade do SP da tela SAML e cole-a no Azure AD como o valor do **Identificador**.

    b. A URL de Entrada é a URL do locatário do seu Atlassian Cloud.   

    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)
    
10. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
    
    ![Logon Único do AD do Azure][10]

7. Na página **Confirmação de logon único**, clique em **Concluir**.  
 
    ![Logon Único do AD do Azure][11]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.


![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_09.png) 

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas:  ![criação de um usuário de teste do AD do Azure](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_05.png) 

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na caixa de diálogo **perfil de usuário**, realize as etapas a seguir: ![criação de um usuário de teste do AD do Azure](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **Nome**, digite **Brenda**.  

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_07.png) 

8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.   



### <a name="creating-an-atlassian-cloud-test-user"></a>Criação de um usuário de teste do Atlassian Cloud


Nesta seção, você deve criar uma usuária chamada Brenda Fernandes no Atlassian Cloud. É importante que o usuário esteja presente na Atlassian Cloud antes de fazer o logon único. Faça logon em sua instância do Atlassian Cloud com direitos de administrador e execute as etapas a seguir.

>[!NOTE] 
>Você também pode criar os usuários em massa clicando no botão **Criar em Massa** na seção Usuários.

1. Na seção Administração de sites, clique no botão **Usuários**

    ![Criar usuário do Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Clique no botão **Criar Usuário** para criar um usuário no Atlassian Cloud

    ![Criar usuário do Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. Insira o endereço de email, o nome de usuário e o nome completo do usuário e atribua o acesso ao aplicativo. 

    ![Criar usuário do Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. Clique no botão **Criar usuário** para enviar o convite por email para o usuário; quando aceitar o convite, o usuário ficará ativo no sistema. 


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Atlassian Cloud.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Atlassian Cloud, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Atlassian Cloud**.

    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_50.png) 

3. No menu na parte superior, clique em **Usuários**.

    ![Atribuir usuário][203]

4. Na lista de usuários, selecione **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

    ![Atribuir usuário][205]


### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Atlassian Cloud no Painel de Acesso, você deve entrar automaticamente no aplicativo Atlassian Cloud.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_205.png



<!--HONumber=Jan17_HO2-->


