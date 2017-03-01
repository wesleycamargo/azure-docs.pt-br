---
title: "Tutorial: integração do Azure Active Directory ao Lifesize Cloud | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Lifesize Cloud."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: 8edbee8e554a7818b97669a4bb64b31ada67b1a5
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Tutorial: Integração do Azure Active Directory com o Lifesize Cloud
Neste tutorial, você aprenderá a integrar o Lifesize Cloud ao Azure AD (Azure Active Directory).

A integração do Lifesize Cloud ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao Lifesize Cloud
* Você pode permitir que os usuários façam logon automaticamente no Lifesize Cloud via SSO (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com o Lifesize Cloud, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para SSO (logon único) do Lifesize Cloud

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

* Adição do Lifesize Cloud da galeria
* Configurar e testar o SSO do Azure AD

## <a name="add-lifesize-cloud-from-the-gallery"></a>Adicionar do Lifesize Cloud da galeria
Para configurar a integração do Lifesize Cloud ao Azure AD, você precisará adicionar o Lifesize Cloud da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Lifesize Cloud da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Lifesize Cloud**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_01.png)
7. No painel de resultados, selecione **Lifesize Cloud** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configurará e testará o logon único do Azure AD com o Lifesize Cloud, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Lifesize Cloud é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Lifesize Cloud.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como o valor de **Nome de Usuário** no Lifesize Cloud.

Para configurar e testar o logon único do Azure AD com o Lifesize Cloud, você precisa concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do Lifesize Cloud](#creating-a-lifesize-cloud-test-user)** - para ter um equivalente de Brenda Fernandes no Lifesize Cloud que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD
Nesta seção, você habilitará o SSO do Azure AD no Portal Clássico e configurará o logon único em seu aplicativo Lifesize Cloud.

**Para configurar o logon único do Azure AD com o Lifesize Cloud, execute as seguintes etapas:**

1. No portal clássico, na página de integração do aplicativo **Lifesize Cloud**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no Lifesize Cloud**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
   ![Configurar Logon Único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_04.png)   
  1. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para fazer logon em seu aplicativo do Lifesize Cloud usando o seguinte padrão: **https://login.lifesizecloud.com/ls/?acs**.
  2. Clique em **Avançar**.
4. Na página **Configurar logon único no Lifesize Cloud**, execute as seguintes etapas:
   
   ![Configurar Logon Único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_05.png)
   1. Clique em **Baixar certificado**e salve o arquivo em seu computador.
   2. Clique em **Próximo**.
5. Para configurar o SSO para seu aplicativo, faça logon no aplicativo Lifesize Cloud com privilégios de administrador.
6. No canto superior direito, clique em seu nome e, em seguida, clique em **Configurações Avançadas**.
   
    ![Configurar Logon Único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)
7. Nas configurações de adiantamento agora, clique em de **configuração de SSO** link. Isso abrirá a página de configuração de SSO para sua instância.
   
    ![Configurar Logon Único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)
8. Agora, configure os seguintes valores na interface do usuário de configuração de SSO.    
   
    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
  1. Copie o valor da URL do emissor do Azure AD e colar isto na caixa de texto **Emissor do Provedor de Identidade**. 
  2. Copie o valor da URL de logon remoto do Azure AD e colar isto no **URL de logon** caixa de texto.   
  3. Abra o certificado baixado no bloco de notas e copie o conteúdo do certificado, excluindo as linhas com Begin Certificate e End Certificate, colar isto na caixa de texto **Certificado x.509**.
  4. No mapeamento de atributo SAML para a caixa de texto **Nome**, insira o valor como **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
  5. No mapeamento de atributo SAML para a caixa de texto **Sobrenome**, insira o valor como **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
  6. No mapeamento de atributo SAML para a caixa de texto **Email**, insira o valor como **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**
9. Para verificar a configuração, você pode clicar no botão **Testar**.
   
   >[!NOTE]
   >Para realizar um teste bem-sucedido, você precisa concluir o assistente de configuração no Azure AD e também fornecer acesso a usuários ou grupos que possam executar o teste.
   >  
10. Habilite o SSO ao marcar o botão **Habilitar SSO**.
11. Agora, clique no botão **Atualizar** para que todas as configurações sejam salvas. Isso irá gerar o valor de RelayState. Copie o valor de RelayState gerado na caixa de texto. Precisaremos desse valor nas próximas etapas.
12. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
    
   ![Logon Único do AD do Azure][10]
13. Na página **Confirmação de logon único**, clique em **Concluir**.  
    
    ![Logon Único do AD do Azure][11]
    
**Para fazer logon no Portal de Gerenciamento do Azure**

1. Faça logon em **https://portal.azure.com** usando credenciais de administrador.
2. Clique no link **Mais Serviços** no painel de navegação à esquerda.
    
    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_09.png)
3. Pesquise no Azure Active Directory e clique no link **Azure Active Directory**.
    
    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_10.png)
4. Você encontrará todos os seus aplicativos SaaS sob o botão **Aplicativos Empresariais**.
    
    ![Configurar Logon Único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_11.png)
5. Agora clique no link **Todos os Aplicativos** na folha seguinte.
    
    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_12.png)
6. Procure o aplicativo Lifesize para o qual você deseja configurar o RelayState. 
    
    ![Configurar Logon Único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_13.png)
7. Agora clique no link **Logon único** na folha.
    
    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_14.png)
8. Você verá a caixa de seleção **Mostrar configurações avançadas de URL**. Clique na caixa de seleção.
    
    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_15.png)
9. Configure o RelayState para o aplicativo, o que você vê na página de configuração de SSO do aplicativo Lifesize. 
    
    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_16.png)
10. Salve as configurações.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_05.png) 
   1. Em Tipo de Usuário, selecione Novo usuário na organização.
   2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:

   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_06.png) 
   1. Na caixa de texto **Nome**, digite **Brenda**.   
   2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   4. Na lista **Função**, selecione **Usuário**.
   5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_08.png) 
    1. Anote o valor da **Nova Senha**.
    2. Clique em **Concluído**.   

### <a name="create-an-lifesize-cloud-test-user"></a>Criar de um usuário de teste do Lifesize Cloud
Nesta seção, você criará um usuário chamado Brenda Fernandes no Lifesize Cloud. O Lifesize Cloud oferece suporte ao provisionamento automático de usuário. Após a autenticação bem-sucedida no Azure AD, o usuário será provisionado no aplicativo automaticamente. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Lifesize Cloud.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Lifesize Cloud, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **Lifesize Cloud**.
   
    ![Configurar Logon Único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
Nesta seção, você testará sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Lifesize Cloud no Painel de Acesso, você deve entrar automaticamente no aplicativo Lifesize Cloud.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_205.png

