---
title: "Tutorial: integração do Azure Active Directory ao FileCloud | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o FileCloud."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f39f0ddd-b504-4562-971f-77b88d1e75fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: b672133e0604e35d6e398fbee4db303f70158111
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-filecloud"></a>Tutorial: integração do Azure Active Directory ao FileCloud
O objetivo desse tutorial é mostrar como integrar o FileCloud ao Azure AD (Azure Active Directory).

A integração do FileCloud ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao FileCloud
* Você pode habilitar os usuários a fazer logon automaticamente no FileCloud usando SSO (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com o FileCloud, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do FileCloud habilitada para SSO

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o SSO do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando FileCloud da Galeria
2. Configurar e testar o SSO do Azure AD

## <a name="add-filecloud-from-the-gallery"></a>Adicionando o FileCloud da Galeria
Para configurar a integração do FileCloud ao Azure AD, você precisará adicionar o FileCloud da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o FileCloud da galeria, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **FileCloud**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_01.png)

7. No painel de resultados, selecione **FileCloud** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Seleção do aplicativo na galeria](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
O objetivo desta seção é mostrar como configurar e testar o SSO do Azure AD com o FileCloud, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o SSO funcione, o Azure AD precisa saber qual usuário do FileCloud é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do FileCloud.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de Usuário** no FileCloud.

Para configurar e testar o SSO do Azure AD com o FileCloud, você precisa concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do FileCloud](#creating-a-filecloud-test-user)** – para ter um equivalente de Brenda Fernandes no FileCloud que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD
Nesta seção, você habilitará o logon único (SSO) do Azure AD no Portal Clássico e configurará o logon único (SSO) em seu aplicativo FileCloud.

**Para configurar o logon único do Azure AD com o FileCloud, execute as seguintes etapas:**

1. No Portal clássico, na página de integração de aplicativos do **FileCloud**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 

2. Na página **Como você deseja que os usuários façam logon no FileCloud**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_03.png)

3. Na página de diálogo **Definir Configurações do Aplicativo**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar o logon único](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_04.png)
  1. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.filecloudhosted.com`.
  2. Na caixa de texto **Identificador**, digite: `https://<subdomain>.filecloudhosted.com/simplesaml/module.php/saml/sp/metadata.php/default-sp`.
  3. Clique em **Próximo**.
   
    >[!NOTE]
    >Observe que você precisa atualizar esses valores com a URL de Logon e o Identificador reais. Para obter esses valores, entre em contato com a equipe de suporte do FileCloud por meio de <mailto:support@codelathe.com>.
    >  

4. Na página **Configurar logon único no FileCloud**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar Logon Único](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_05.png)
 1. Clique em **Baixar metadados**e salve o arquivo no computador.
 2. Clique em **Próximo**.

5. Em uma janela diferente do navegador da Web, faça logon em seu locatário do FileCloud como administrador.

6. No painel de navegação esquerdo, clique em **Configurações**. 
   
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_000.png)

7. Clique na guia **SSO** na seção Configurações. 
   
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_001.png)

8. Selecione **SAML** como **Tipo de SSO Padrão** em **Configurações de SSO (Logon Único)** painel.
   
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_002.png)

9. Na caixa de texto **URL de Ponto de Extremidade do IdP**, coloque o valor da **ID da Entidade** do assistente de configuração de aplicativo do Azure AD no painel **Configurações de SAML**.
   
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_003.png)

10. Abra o arquivo de metadados baixado no bloco de notas, copie o conteúdo dele para sua área de transferência e, em seguida, cole-o na caixa de texto **Metadados IdP** no painel **Configurações de SAML**.
    
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_004.png)

11. Clique no botão **Salvar** .

12. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
    
    ![Logon Único do AD do Azure][10]

13. Na página **Confirmação de logon único**, clique em **Concluir**.  
    
    ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-filecloud-tutorial/create_aaduser_09.png)

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-filecloud-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-filecloud-tutorial/create_aaduser_04.png)

5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-filecloud-tutorial/create_aaduser_05.png)
 1. Em Tipo de Usuário, selecione Novo usuário na organização.  
 2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**. 
 3. Clique em **Próximo**.

6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-filecloud-tutorial/create_aaduser_06.png) 
 1. Na caixa de texto **Nome**, digite **Brenda**.  
 2. Na caixa de texto **Sobrenome**, digite **Fernandes**. 
 3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**. 
 4. Na lista **Função**, selecione **Usuário**. 
 5. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-filecloud-tutorial/create_aaduser_07.png)

8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-filecloud-tutorial/create_aaduser_08.png) 
 1. Anote o valor da **Nova Senha**.  
 2. Clique em **Concluído**.   

### <a name="create-a-filecloud-test-user"></a>Criar um usuário de teste do FileCloud
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no FileCloud. O FileCloud dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o FileCloud, caso ele ainda não exista. 

>[!NOTE]
>Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do FileCloud. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o SSO do Azure, concedendo a ela acesso ao FileCloud.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao FileCloud, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201]

2. Na lista de aplicativos, escolha **FileCloud**.
   
    ![Configurar o logon único](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_50.png)

3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]

4. Na lista de usuários, selecione **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao clicar no bloco FileCloud no Painel de Acesso, você deve entrar automaticamente no aplicativo FileCloud.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_205.png

