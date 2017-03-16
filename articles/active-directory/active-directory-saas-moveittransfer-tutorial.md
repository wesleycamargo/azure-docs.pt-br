---
title: "Tutorial: Integração do Azure Active Directory com o MOVEit Transfer | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o MOVEit Transfer."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: e1b4cda400205611a957c23d9a9b90c918d260f3
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer"></a>Tutorial: integração do Azure Active Directory ao MOVEit Transfer
O objetivo deste tutorial é mostrar como integrar o MOVEit Transfer ao Azure AD (Azure Active Directory).

A integração do MOVEit Transfer ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao MOVEit Transfer
* Você pode permitir que seus usuários faça logon automaticamente no MOVEit Transfer usando SSO (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao MOVEit Transfer, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do MOVEit Transfer habilitada para SSO

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando MOVEit Transfer da galeria
2. Configurar e testar o SSO do Azure AD

## <a name="adding-moveit-transfer-from-the-gallery"></a>Adicionando MOVEit Transfer da galeria
Para configurar a integração do MOVEit Transfer ao Azure AD, você precisa adicionar o MOVEit Transfer da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o MOVEit Transfer da galeria, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **MOVEit Transfer**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_01.png)
7. No painel de resultados, selecione **MOVEit Transfer** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Seleção do aplicativo na galeria](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar SSO do Azure AD
O objetivo desta seção é mostrar como configurar e testar o SSO do Azure AD com o MOVEit Transfer, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o SSO funcione, o Azure AD precisa saber qual usuário do MOVEit Transfer é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no MOVEit Transfer.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no MOVEIT Transfer.

Para configurar e testar o SSO do Azure AD com o MOVEit Transfer, você precisa concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criar um usuário de teste do MOVEit Transfer](#creating-a-moveit-transfer-test-user)** para ter um equivalente de Brenda Fernandes no MOVEit Transfer que seja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do Azure AD.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-sso"></a>Configurar o SSO do Azure AD
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o SSO em seu aplicativo do MOVEit Transfer.

**Para configurar o logon único do Azure AD com o MOVEit Transfer, execute as seguintes etapas:**

1. No portal clássico, na página de integração de aplicativos do **MOVEit Transfer**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no MOVEit Transfer**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_03.png)
3. Na página de diálogo **Definir Configurações do Aplicativo**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar Logon Único](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_04.png)
  1. Na caixa de texto **URL de logon** , digite a URL de logon com seu próprio domínio.
  2. Na caixa de texto **Identificador** , digite uma URL de ID de entidade.
  3. Na caixa de texto **URL de resposta** , digite uma URL de Interface do Consumidor de Declaração habilitada.
  4. Clique em **Próximo**.
   
   >[!NOTE]
   >Observe que você precisa atualizar esses valores com a URL de Logon e o Identificador reais. Para obter esses valores, confira a etapa 8 para ver mais detalhes ou entre em contato com o [MOVEit Transfer](https://www.ipswitch.com/support/technical-support).
   >  
4. Na página **Configurar logon único no MOVEit Transfer**, execute as seguintes etapas e clique em **Avançar**:
   
  ![Configurar Logon Único](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_05.png)  
  1. Clique em **Baixar metadados**e salve o arquivo no computador.
  2. Clique em **Próximo**.
5. Faça logon em seu locatário do MOVEit Transfer como administrador.
6. No painel de navegação esquerdo, clique em **Configurações**.
   
  ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_000.png)
7. Clique no link **Logon Único** que está em **Políticas de Segurança -> Autenticação de Usuário**.
   
  ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_001.png)
8. Clique no link de URL de Metadados para baixar o documento de metadados.
   
  ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_002.png)   
  * Verifique se **entityID** corresponde ao **Identificador** na etapa&3;.
  * Verifique se a URL de local **AssertionConsumerService** corresponde à **URL DE RESPOSTA** na etapa&3;.
     
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_007.png)
9. Clique no botão **Adicionar Provedor de Identidade** para adicionar um novo Provedor de Identidade Federada.
   
  ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_003.png)
10. Clique em **Procurar...** para selecionar o arquivo de metadados que você baixou na etapa 4 e clique em **Adicionar Provedor de Identidade** para carregar o arquivo baixado. 
    
  ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_004.png)
11. Selecione "**Sim**" como **Habilitado** na página **Editar Provedor de Identidade Federado...** e clique em **Salvar**.
    
   ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_005.png)
12. Na página **Editar Configurações de usuário de Provedor de Identidade Federado**, realize as ações a seguir e clique em **Salvar**.0   
  1. Selecione **SAML NameID** como **Nome de logon**. 
  2. Selecione **Outros** para **Nome completo** e, na caixa de texto **Nome do atributo**, coloque o valor: http://schemas.microsoft.com/identity/claims/displayname. 
  3. Selecione **Outros** para **Email** e, na caixa de texto **Nome do atributo**, coloque o valor: http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress. 
  4. Selecione **Sim** para **Criação automática de conta no momento da conexão**. 
  5. Clique no botão **Salvar** .
 
   ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_006.png)
13. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
    
    ![Logon Único do AD do Azure][10]
14. Na página **Confirmação de logon único**, clique em **Concluir**.  
    
   ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_09.png)
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_03.png)
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_04.png)
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_05.png)
  1. Em Tipo de Usuário, selecione Novo usuário na organização.
  2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
  3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_06.png)
  1. Na caixa de texto **Nome**, digite **Brenda**.  
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
  4. Na lista **Função**, selecione **Usuário**.
  5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_07.png)
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_08.png)
  1. Anote o valor da **Nova Senha**.
  2. Clique em **Concluído**.   

### <a name="create-a-moveit-transfer-test-user"></a>Criar um usuário de teste do MOVEit Transfer
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no MOVEit Transfer. O MOVEit Transfer dá suporte ao provisionamento just-in-time, que você habilitou.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o MOVEit Transfer, caso ele ainda não exista.

>[!NOTE]
>Se precisar criar um usuário manualmente, você precisará contatar a equipe de suporte do MOVEit Transfer.
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o SSO do Azure, concedendo a ela acesso ao MOVEit Transfer.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao MOVEit Transfer, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201]
2. Na lista de aplicativos, selecione **MOVEit Transfer**.
   
    ![Configurar Logon Único](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_50.png)
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do MOVEit Transfer no painel de acesso, você deverá ser conectado automaticamente ao aplicativo do MOVEit Transfer.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_205.png

