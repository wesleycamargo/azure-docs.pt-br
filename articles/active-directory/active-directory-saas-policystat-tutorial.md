---
title: "Tutorial: Integração do Azure Active Directory com o PolicyStat | Microsoft Docs"
description: "Saiba como usar o PolicyStat com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: 8290d90f22a80d5c585192b17cfa0aeba00aa891
ms.lasthandoff: 02/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Tutorial: Integração do Active Directory do Azure com o PolicyStat
O objetivo deste tutorial é mostrar a integração do Azure com o PolicyStat.  

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do PolicyStat

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao PolicyStat poderão fazer logon único no aplicativo em seu site de empresa do PolicyStat (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para PolicyStat
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-policystat-tutorial/IC808662.png "Cenário")

## <a name="enabling-the-application-integration-for-policystat"></a>Habilitando a integração de aplicativos para PolicyStat
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o PolicyStat.

**Para habilitar a integração de aplicativos para o PolicyStat, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-policystat-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-policystat-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-policystat-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-policystat-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **PolicyStat**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-policystat-tutorial/IC808627.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **PolicyStat** e clique em **Concluir** para adicionar o aplicativo.
   
   ![PolicyStat](./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
   
## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no PolicyStat com sua conta do AD do Azure usando federação baseada em protocolo SAML.  

Seu aplicativo PolicyStat espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de **atributos do token SAML** .  

As capturas de tela a seguir mostram um exemplo disso.

![Atributos](./media/active-directory-saas-policystat-tutorial/IC808628.png "Atributos")

**Para configurar o logon único, execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração de aplicativo do **PolicyStat**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-policystat-tutorial/IC808629.png "Configurar Logon Único")
2. Na página **Como você deseja que os usuários façam logon no PolicyStat**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar Logon Único](./media/active-directory-saas-policystat-tutorial/IC808630.png "Configurar Logon Único")
3. Na página **Definir Configurações do Aplicativo**, na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para fazer logon em seu aplicativo PolicyStat (por exemplo: *“https://demo-azure.policystat.com”*) e clique em **Avançar**.
   
   ![Definir Configurações de Aplicativo](./media/active-directory-saas-policystat-tutorial/IC808631.png "Definir Configurações de Aplicativo")
4. Na página **Configurar o logon único no PolicyStat**, clique em **Baixar metadados** e salve o arquivo de metadados no computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-policystat-tutorial/IC808632.png "Configurar Logon Único")
5. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do PolicyStat como administrador.
6. Clique na guia **Admin** e, em seguida, clique em **Configuração de Logon Único** no painel de navegação à esquerda.
   
   ![Menu Administrador](./media/active-directory-saas-policystat-tutorial/IC808633.png "Menu Administrador")
7. Na seção **Configuração**, selecione **Habilitar Integração de Logon Único**.
   
   ![Configuração de Logon Único](./media/active-directory-saas-policystat-tutorial/IC808634.png "Configuração de Logon Único")
8. Clique em **Configurar Atributos** e, em seguida, na seção **Configurar Atributos**, realize as seguintes etapas:
   
   ![Configuração de Logon Único](./media/active-directory-saas-policystat-tutorial/IC808635.png "Configuração de Logon Único")
   
   1. Na caixa de texto **Atributo do Nome de Usuário**, digite **uid**.
   2. Na caixa de texto **Atributo de Nome**, digite **firstname**.
   3. Na caixa de texto **Atributo de Sobrenome**, digite **lastname**.
   4. Na caixa de texto **Atributo de Email**, digite **emailaddress**.
   5. Clique em **Salvar Alterações**.
9. Clique em **Seus Metadados do IDP** e, na seção **Seus Metadados do IDP**, realize as seguintes etapas:
   
   ![Configuração de Logon Único](./media/active-directory-saas-policystat-tutorial/IC808635.png "Configuração de Logon Único")
   
   1. Abra o arquivo de metadados baixado, copie o conteúdo e cole-o na caixa de texto **Seus Metadados do Provedor de Identidade**.
   2. Clique em **Salvar Alterações**.
10. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar Logon Único](./media/active-directory-saas-policystat-tutorial/IC771723.png "Configurar Logon Único")
  
11. Na parte superior do menu, clique em **Atributos** to open the **SAML Token Atributos** .
    
    ![Atributos](./media/active-directory-saas-policystat-tutorial/IC795920.png "Atributos")
12. Para adicionar os mapeamentos de atributo necessários, execute as seguintes etapas:
    
    ![Atributos](./media/active-directory-saas-policystat-tutorial/IC804823.png "Atributos")
    
   1. Clique em **adicionar atributo de usuário**.
   2. Na caixa de texto **Nome do Atributo**, digite **uid**.
   3. Na caixa de texto **Valor do Atributo**, selecione **ExtractMailPrefix()**.    
   4. Na lista **Email**, selecione **User.mail**.
   5. Clique em **Concluído**.

##<a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no PolicyStat, eles devem ser provisionados no PolicyStat.  

O PolicyStat dá suporte ao provisionamento de usuário just in time. Isso significa que não é necessário adicionar usuários manualmente ao PolicyStat.  
Os usuários serão adicionados automaticamente ao fazerem seu primeiro logon por meio do logon único.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do PolicyStat ou as APIs fornecidas pelo PolicyStat para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao PolicyStat, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **PolicyStat**, clique em **Atribuir usuários**.
   
   ![Atribuir Usuários](./media/active-directory-saas-policystat-tutorial/IC808636.png "Atribuir Usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-policystat-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


