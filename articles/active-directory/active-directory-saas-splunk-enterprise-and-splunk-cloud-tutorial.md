---
title: "Tutorial: Integração do Azure Active Directory com o Splunk Enterprise e o Splunk Cloud | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Splunk Enterprise e o Splunk Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b3e2b4a9-749c-4895-813d-db46f8dfdbf8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 44bf5ae5df09b564b4b3ade495ed3aa4e52676e7
ms.openlocfilehash: 7dcb70766c132ec00ee9199ea0152cd07aa50c8f
ms.lasthandoff: 12/07/2016


---

# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Tutorial: Integração do Azure Active Directory com o Splunk Enterprise e o Splunk Cloud

Neste tutorial, você aprenderá como integrar o Splunk Enterprise e o Splunk Cloud ao Azure AD (Azure Active Directory).

A integração do Splunk Enterprise e do Splunk Cloud ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Splunk Enterprise e ao Splunk Cloud
- Você pode habilitar seus usuários a fazerem logon automaticamente no Splunk Enterprise e no Splunk Cloud (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Splunk Enterprise e o Splunk Cloud, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Splunk Enterprise ou do Splunk Cloud habilitada para logon único


>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Splunk Enterprise e o Splunk Cloud da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Adicionar o Splunk Enterprise e o Splunk Cloud da galeria
Para configurar a integração do Splunk Enterprise e do Splunk Cloud com o Azure AD, você precisará adicionar o Splunk Enterprise e o Splunk Cloud à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Splunk Enterprise e o Splunk Cloud por meio da galeria, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory][1]

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Splunk Enterprise ou Splunk Cloud **.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_01.png)

7. No painel de resultados, selecione **Splunk Enterprise e Splunk Cloud ** e clique em **Concluir** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Splunk Enterprise e o Splunk Cloud, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Splunk Enterprise e do Splunk Cloud é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Splunk Enterprise e do Splunk Cloud.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD ao valor de **Nome de usuário** no Splunk Enterprise e no Splunk Cloud.

Para configurar e testar o logon único do Azure AD com o Splunk Enterprise e o Splunk Cloud, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Splunk Enterprise e do Splunk Cloud ](#creating-a-splunk-enterprise-and-splunk-cloud-test-user)**: para ter um equivalente de Brenda Fernandes no Splunk Enterprise e no Splunk Cloud que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo Splunk Enterprise e Splunk Cloud.


**Para configurar o logon único do Azure AD com o Splunk Enterprise e o Splunk Cloud, execute as seguintes etapas:**

1. No portal clássico, na página de integração de aplicativos do **Splunk Enterprise e Splunk Cloud **, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
     
    ![Configurar o logon único][6] 

2. Na página **Como você deseja que os usuários façam logon no Splunk Enterprise e no Splunk Cloud **, selecione **Logon Único do Azure AD** e clique em **Avançar**.

    ![Configurar Logon Único](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_03.png) 

3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_04.png) 

    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para fazer logon no seu aplicativo Splunk Enterprise e Splunk Cloud usando o seguinte padrão: `https://<splunkserverUrl>/en-US/app/launcher/home`
    
    b. Na caixa de texto **Identificador**, digite a URL de seu Servidor Splunk

    c. Na caixa de texto **URL de Resposta**, digite a URL no seguinte padrão: `https://<splunkserver>/saml/acs`

    d. Clique em **Próximo**
 
4. Na página **Configurar o logon único no Splunk Enterprise e o Splunk Cloud**, execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_05.png)

    a. Clique em **Baixar metadados**e salve o arquivo no computador.

    b. Clique em **Próximo**.


5. Para que o SSO seja configurado para seu aplicativo, entre em contato com a equipe de suporte do Splunk Enterprise e do Splunk Cloud e forneça o seguinte:

    a. Os **metadados de federação** baixados

6. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
    
    ![Logon Único do AD do Azure][10]

7. Na página **Confirmação de logon único**, clique em **Concluir**.  
 
    ![Logon Único do AD do Azure][11]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.


![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_09.png) 

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas:  ![criação de um usuário de teste do AD do Azure](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_05.png) 

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na caixa de diálogo **perfil de usuário**, realize as etapas a seguir: ![criação de um usuário de teste do AD do Azure](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **Nome**, digite **Brenda**.  

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_07.png) 

8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.   



### <a name="creating-an-splunk-enterprise-and-splunk-cloud-test-user"></a>Criação de um usuário de teste do Splunk Enterprise e do Splunk Cloud

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Splunk Enterprise e no Splunk Cloud. Trabalhe com a equipe de suporte do Splunk Enterprise e do Splunk Cloud para adicionar os usuários à plataforma do Splunk Enterprise e do Splunk Cloud.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure concedendo-lhe acesso ao Splunk Enterprise e Splunk Cloud.

![Atribuir usuário][200] 

**Para adicionar Brenda Fernandes ao Splunk Enterprise e o Splunk Cloud, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Splunk Enterprise e Splunk Cloud**.

    ![Configurar Logon Único](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_50.png) 

3. No menu na parte superior, clique em **Usuários**.

    ![Atribuir usuário][203]

4. Na lista de usuários, selecione **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

    ![Atribuir usuário][205]


### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Splunk Enterprise e do Splunk Cloud no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo Splunk Enterprise e Splunk Cloud.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_205.png

