---
title: "Tutorial: integração do Azure Active Directory com o Igloo Software | Microsoft Docs"
description: "Saiba como usar o Igloo Software com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/20/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c9e5dfa98e47c6a7a4e34f008fabd1f63b93937c


---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Tutorial: integração do Active Directory do Azure ao Igloo Software
O objetivo deste tutorial é mostrar a integração do Azure ao Igloo Software.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura do [Igloo Software](http://www.igloosoftware.com/) habilitada para Logon único

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Igloo Software poderão fazer logon único no aplicativo em seu site de empresa do Igloo Software (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos com o Igloo Software
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-igloo-software-tutorial/IC783961.png "Scenario")

## <a name="enabling-the-application-integration-for-igloo-software"></a>Habilitando a integração de aplicativos com o Igloo Software
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Igloo Software.

### <a name="to-enable-the-application-integration-for-igloo-software-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o Igloo Software, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-igloo-software-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-igloo-software-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-igloo-software-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-igloo-software-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **Igloo Software**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-igloo-software-tutorial/IC783962.png "Application Gallery")
7. No painel de resultados, selecione **Igloo Software** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Igloo](./media/active-directory-saas-igloo-software-tutorial/IC783963.png "Igloo")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no Igloo Software com a própria conta no Azure AD usando federação baseada no protocolo SAML.  
Como parte deste procedimento, será necessário carregar um certificado codificado em base-64 no locatário do Desktop Central.  
Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração de aplicativos do **Igloo Software**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-igloo-software-tutorial/IC783964.png "Configure Single Sign-On")
2. Na página **Como você deseja que os usuários façam logon no Igloo Software**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Logon Único do AD do Microsoft Azure](./media/active-directory-saas-igloo-software-tutorial/IC783965.png "Microsoft Azure AD Single Sign-On")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do Igloo Software**, digite a URL usando o seguinte padrão "*https://company.igloocommunities.com/?signin*" e clique em **Avançar**.
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-igloo-software-tutorial/IC773625.png "Configure App URL")
4. Na página **Configurar logon único no Igloo Software**, para baixar o certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-igloo-software-tutorial/IC783966.png "Configure Single Sign-On")
5. Em uma janela diferente do navegador da Web, faça logon em seu site de empresa do Igloo Software como administrador.
6. Vá para o **Painel de Controle**.
   
   ![Painel de Controle](./media/active-directory-saas-igloo-software-tutorial/IC799949.png "Control Panel")
7. Na guia **Associação**, clique em **Configurações de Entrada**.
   
   ![Configurações de Entrada](./media/active-directory-saas-igloo-software-tutorial/IC783968.png "Sign in Settings")
8. Na seção Configuração do SAML, clique em **Configurar Autenticação SAML**.
   
   ![Configuração de SAML](./media/active-directory-saas-igloo-software-tutorial/IC783969.png "SAML Configuration")
9. Na seção **Configuração Geral** , realize as seguintes etapas:
   
   ![Configuração Geral](./media/active-directory-saas-igloo-software-tutorial/IC783970.png "General Configuration")
   
   1. Na caixa de texto **Nome da Conexão** , digite um nome personalizado para a sua configuração.
   2. No Portal clássico do Azure, na página do diálogo **Configurar logon único no Igloo Software**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do IdP**.
   3. No Portal clássico do Azure, na página do diálogo **Configurar logon único no Igloo Software**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff do IdP**.
   4. Para **Tipo de HTTP de Solicitação e Resposta de Logoff**, selecione **POST**.
   5. Crie um arquivo de texto do certificado baixado.
      
      > [!TIP]
      > Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   6. Remova a primeira e a última linhas da versão do arquivo de texto do seu certificado, copie o texto restante do certificado e cole-o na caixa de texto **Certificado Público** .
10. Em **Configuração de Resposta e Autenticação**, realize as seguintes etapas:
    
    ![Configuração de Resposta e Autenticação](./media/active-directory-saas-igloo-software-tutorial/IC783971.png "Response and Authentication Configuration")
    
    1. Para **Provedor de Identidade**, selecione **Microsoft ADFS**.
    2. Para **Tipo de Identificador**, selecione **Endereço de Email**.
    3. Na caixa de texto **Atributo de Email**, digite **emailaddress**.
    4. Na caixa de texto **Atributo de Nome**, digite **givenname**.
    5. Na caixa de texto **Atributo de Sobrenome**, digite **surname**.
11. Execute as seguintes etapas para concluir a configuração:
    
    ![Criação de usuário na entrada](./media/active-directory-saas-igloo-software-tutorial/IC783972.png "User creation on Sign in")
    
    1. Para **Criação de usuário na Entrada**, selecione **Criar um novo usuário em seu site ao entrar**.
    2. Para **Configurações de Entrada**, selecione **Usar botão do SAML na tela “Entrar”**.
    3. Clique em **Salvar**.
12. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar Logon Único](./media/active-directory-saas-igloo-software-tutorial/IC783973.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Não há item de ação para a configuração do provisionamento de usuário para o Igloo Software.  
Quando um usuário atribuído tenta fazer logon no Igloo Software usando o painel de acesso, o Igloo Software verifica se o usuário existe.  
Se ainda não houver conta de usuário, ela será criada automaticamente pelo Igloo Software.

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-igloo-software-perform-the-following-steps"></a>Para atribuir usuários ao Igloo Software, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **Igloo Software**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-igloo-software-tutorial/IC783974.png "Assign Users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-igloo-software-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


