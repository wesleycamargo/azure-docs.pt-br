---
title: "Tutorial: Integração do Azure Active Directory com o OpsGenie | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o OpsGenie."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: bf6cf459b258e14e9dd36b6ecc999f455d31f52f
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Tutorial: Integração do Active Directory do Azure com o OpsGenie
O objetivo desse tutorial é mostrar como integrar o OpsGenie ao Azure AD (Azure Active Directory).

A integração do OpsGenie ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao OpsGenie.
* Você pode permitir que os usuários façam logon automaticamente no SSO (logon único) do OpsGenie com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao OpsGenie, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura com SSO (logon único) do OpsGenie habilitado

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
>  

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o SSO do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

* Adição do OpsGenie da galeria
* Configurar e testar o SSO do Azure AD

## <a name="add-opsgenie-from-the-gallery"></a>Adicionar o OpsGenie da galeria
Para configurar a integração do OpsGenie ao Azure AD, você precisa adicionar o OpsGenie a partir da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o OpsGenie a partir da galeria, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **OpsGenie**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_01.png)
7. No painel de resultados, selecione **OpsGenie** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar SSO do Azure AD
O objetivo desta seção é mostrar como configurar e testar o SSO do Azure AD com o OpsGenie, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o SSO funcione, o Azure AD precisa saber qual usuário do OpsGenie é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no OpsGenie.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de Usuário** no OpsGenie.

Para configurar e testar o SSO do Azure AD com o OpsGenie, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste para OpsGenie](#creating-a-opsgenie-test-user)** : para ter um equivalente de Brenda Fernandes no OpsGenie que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD
O objetivo desta seção é habilitar o SSO do Azure AD no Portal Clássico do Azure e configurar o SSO em seu aplicativo OpsGenie.

**Para configurar o logon único do Azure AD com o OpsGenie, execute as seguintes etapas:**

1. No Portal clássico do Azure, na página de integração de aplicativos do **OpsGenie**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único][6] 
2. Na página **Como você deseja que os usuários façam logon no OpsGenie**, selecione **Logon único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_04.png)
  1. Na caixa de texto URL de Logon, digite a URL usada pelos usuários para fazer logon no seu aplicativo do OpsGenie usando o seguinte padrão: **“https://app.opsgenie.com/auth/login”**.

    >[!NOTE] 
    >Entre em contato com a [equipe de suporte do OpsGenie](mailto:support@opsgenie.com) se precisar de sua URL de Logon.
    >

  2. Clique em **Próximo**.

4. Na página **Configurar logon único no OpsGenie** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_05.png)   
  1. Clique em **Baixar Certificado**e salve o arquivo em seu computador. Precisaremos desse certificado e das URLs de metadados (ID da Entidade, URL de Entrada e URL de saída de SSO) para configurar o SSO no lado do OpsGenie.
  2. Clique em **Próximo**.
5. Abra outra instância do navegador e, em seguida, faça logon no OpsGenie como administrador.
6. Clique em **Configurações** e na guia **Logon único**.
   
    ![Logon único do OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_06.png) 
7. Para habilitar o SSO, selecione **Habilitado**.
   
    ![Configurações do OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_07.png) 
8. Na seção **Provedor**, clique na guia **Azure Active Directory**.
   
    ![Configurações do OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_08.png) 
9. Na página de diálogo Azure Active Directory, execute as seguintes etapas:
   
    ![Configurações do OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_09.png)  
  1. No Portal clássico do Azure, na página da caixa de diálogo **Configurar logon único no OpsGenie**, copie o valor da **URL do Serviço de Logon Único** e cole-o na caixa de texto **Ponto de Extremidade SAML 2.0**.
  2. Crie um arquivo codificado em base&64; usando o certificado baixado.      
   
    >[!NOTE]
    >Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](https://www.youtube.com/watch?v=PlgrzUZ-Y1o&feature=youtu.be).
    >

  3. Abra seu certificado codificado em base-64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado X.500** .
  4. Clique em **Salvar Alterações**.
10. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
11. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_05.png) 
  1. Em Tipo de Usuário, selecione Novo usuário na organização.  
  2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
  3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_06.png) 
  1. Na caixa de texto **Nome**, digite **Brenda**.    
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
  4. Na lista **Função**, selecione **Usuário**.
  5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_08.png) 
   1. Anote o valor da **Nova Senha**.
   2. Clique em **Concluído**.   

### <a name="create-a-opsgenie-test-user"></a>Criar de um usuário de teste do OpsGenie
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no OpsGenie. 

1. Em uma janela de navegador da Web, faça logon em seu locatário do OpsGenie como administrador.
2. Navegue até a lista Usuários clicando em **Usuário** no painel esquerdo.
   
   ![Configurações do OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_10.png) 
3. Clique em “**Adicionar Usuário**”.
4. No diálogo **Adicionar Usuário** , realize as seguintes etapas:
   
   ![Configurações do OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_11.png) 
  1. Na caixa de texto **Email** , digite o endereço de email de Brenda Fernandes no Active Directory do Azure.
  2. Na caixa de texto **Nome Completo**, digite **Brenda Fernandes**.
  3. Clique em **Salvar**. 

>[!NOTE]
>Brenda receberá um email com instruções para configurar seu perfil.
>

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o SSO do Azure, concedendo a ela acesso ao OpsGenie.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao OpsGenie, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **OpsGenie**.
   
    ![Configurar Logon Único](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do OpsGenie no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo do OpsGenie.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_205.png

