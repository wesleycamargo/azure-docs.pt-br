---
title: "Tutorial: Integração do Azure Active Directory com o Rally Software | Microsoft Docs"
description: "Saiba como usar o Rally Software com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9a653ac435198e89a527070a0174a1adaf830dc3
ms.openlocfilehash: 504a5723f025d7383dbec78cdd268b7c1b94a1e5


---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Tutorial: Integração do Active Directory do Azure com o Rally Software
O objetivo deste tutorial é mostrar a integração do Azure com o Rally Software.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do Rally Software

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos com o Rally Software
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-rally-software-tutorial/IC769525.png "Cenário")

## <a name="enabling-the-application-integration-for-rally-software"></a>Habilitando a integração de aplicativos com o Rally Software
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Rally Software.

### <a name="to-enable-the-application-integration-for-rally-software-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o Rally Software, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-rally-software-tutorial/IC700994.png "Aplicativos")

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-rally-software-tutorial/IC749321.png "Adicionar aplicativo")

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-rally-software-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6. Na **caixa de pesquisa**, digite **rally**.
   
    ![Galeria de aplicativos](./media/active-directory-saas-rally-software-tutorial/IC769526.png "Galeria de aplicativos")

7. No painel de resultados, selecione **Rally Software** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Rally Software](./media/active-directory-saas-rally-software-tutorial/IC769527.png "Rally Software")
   
## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no Rally Software com a própria conta no AD do Azure usando federação baseada no protocolo SAML.  
Como parte desse procedimento, é necessário carregar um certificado para o Rally Software.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No Portal Clássico do Azure, na página de integração de aplicativos do **Rally Software**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar logon único](./media/active-directory-saas-rally-software-tutorial/IC749323.png "Configurar logon único")

2. Na página **Como você deseja que os usuários façam logon no Rally**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Logon Único do AD do Microsoft Azure](./media/active-directory-saas-rally-software-tutorial/IC769528.png "Logon Único do AD do Microsoft Azure")

3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Locatário do Rally Software**, digite a URL usando o padrão "*https://\<nome-do-locatario\>.rally.com*" e clique em **Avançar**.
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-rally-software-tutorial/IC769529.png "Configurar URL do Aplicativo")

4. Na página **Configurar o logon único no Rally** , clique em Baixar metadados e salve-o no computador.
   
    ![Configurar logon único](./media/active-directory-saas-rally-software-tutorial/IC769530.png "Configurar logon único")

5. Faça logon em seu locatário do **Rally Software** .

6. Na barra de ferramentas na parte superior, clique em **Instalação** e selecione **Assinatura**.
   
    ![Assinatura](./media/active-directory-saas-rally-software-tutorial/IC769531.png "Assinatura")

7. Clique no botão **Ação** na barra de ferramentas na parte superior à direita e selecione **Editar Assinatura**.

8. Na página de diálogo **Assinatura**, realize as seguintes etapas e clique em **Salvar e Fechar**:
   
    ![Autenticação](./media/active-directory-saas-rally-software-tutorial/IC769542.png "Autenticação")
   
    1. Selecione **Autenticação do Rally ou SSO** na lista suspensa Autenticação
 
    2. No portal clássico do Azure, na página de diálogo **Configurar logon único no Rally Software**, copie o valor da **ID do Provedor de Identidade** e cole-o na caixa de texto **URL do Provedor de Identidade**.

    3. No portal clássico do Azure, na página de diálogo **Configurar logon único no Rally Software**, copie o valor da **URL de Logoff Remoto**.

9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
    ![Configurar logon único](./media/active-directory-saas-rally-software-tutorial/IC769547.png "Configurar logon único")
   
## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para usuários do AAD conseguirem efetuar logon, eles devem ser provisionados para o aplicativo Rally Software usando seus nomes de usuário do Active Directory do Azure.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:
1. Faça logon no seu locatário do Rally Software.

2. Vá para **Instalação \> USUÁRIOS** e clique em **+ Adicionar Novo**.
   
    ![Usuários](./media/active-directory-saas-rally-software-tutorial/IC781039.png "Usuários")

3. Digite o nome na caixa de texto Novo Usuário e clique em **Adicionar com Detalhes**.

4. Na seção **Criar Usuário** , realize as seguintes etapas:
   
    ![Criar usuário](./media/active-directory-saas-rally-software-tutorial/IC781040.png "Criar usuário")
   
    1. Na caixa de texto **Nome do Usuário** , digite o nome do usuário do AD do Azure que você deseja provisionar.

    2. Na caixa de texto **Endereço de Email** , digite o endereço de email do usuário do Azure AD que você deseja provisionar.

    3. Clique em **Salvar e Fechar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Rally Software ou as APIs fornecidas pelo Rally Software para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-rally-software-perform-the-following-steps"></a>Para atribuir usuários ao Rally Software, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.

2. Na página de integração de aplicativos do **Rally Software**, clique em **Atribuir usuários**.
   
    ![Atribuir usuários](./media/active-directory-saas-rally-software-tutorial/IC769548.png "Atribuir usuários")

3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Sim](./media/active-directory-saas-rally-software-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Jan17_HO1-->


