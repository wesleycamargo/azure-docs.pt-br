---
title: "Tutorial: Integração do Azure Active Directory com o Jitbit Helpdesk | Microsoft Docs"
description: "Saiba como usar o Jitbit Helpdesk com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: acffb1e0b3f1ac05034d130ca3a24246154a0cc2


---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Tutorial: Integração do Active Directory do Azure com o Jitbit Helpdesk
O objetivo deste tutorial é mostrar a integração do Azure com o Jitbit Helpdesk.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do Jitbit Helpdesk

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Jitbit Helpdesk poderão fazer logon único no aplicativo em seu site de empresa do Jitbit Helpdesk (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos com o Jitbit Helpdesk
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "Scenario")

## <a name="enabling-the-application-integration-for-jitbit-helpdesk"></a>Habilitando a integração de aplicativos com o Jitbit Helpdesk
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Jitbit Helpdesk.

### <a name="to-enable-the-application-integration-for-jitbit-helpdesk-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o Jitbit Helpdesk, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **Jitbit Helpdesk**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "Application Gallery")
7. No painel de resultados, selecione **Jitbit Helpdesk** e clique em **Concluir** para adicionar o aplicativo.
   
   ![JitBit](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Jitbit Helpdesk com sua conta do Azure AD usando federação baseada em protocolo SAML. .  
Como parte desse procedimento, será necessário criar um arquivo de certificado codificado em base 64.  
Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

> [!IMPORTANT]
> Para poder configurar o logon único em seu locatário Jitbit Helpdesk, você precisa primeiro contatar o suporte técnico Jitbit Helpdesk para que esse recurso seja habilitado.
> 
> 

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **Jitbit Helpdesk**, clique em **Configurar logon único** para abrir o diálogo** Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "Configure single sign-on")
2. Na página **Como você deseja que os usuários façam logon no Jitbit Helpdesk**, selecione **Logon único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "Configure single sign-on")
3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Entrada no Jitbit Helpdesk**, digite a URL usando o padrão "*https://\<nome-locatário\>.Jitbit.com*" e clique em **Avançar**.
   
   ![Configurar a URL do aplicativo](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "Configure app URL")
4. Na página **Configurar logon único no Jitbit Helpdesk**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente como **c:\\Jitbit Helpdesk.cer**.
   
   ![Configurar o logon único](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "Configure single sign-on")
5. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Jitbit Helpdesk como administrador.
6. Na barra de ferramentas na parte superior, clique em **Administração**.
   
   ![Administração](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")
7. Clique em **Configurações gerais**.
   
   ![Usuários, empresas e permissões](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Users, companies and permissions")
8. Na seção de configuração **Configurações de autenticação** , realize as seguintes etapas:
   
   ![Configurações de autenticação](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "Authentication settings")
   
   1. Selecione **Habilitar logon único SAML 2.0** usando SSO (logon único) com **OneLogin**.
   2. No portal clássico do Azure, na página de diálogo **Configurar logon único no Jitbit Helpdesk**, copie o valor do **Ponto de Extremidade Iniciado pelo Provedor de Serviços** e cole-o na caixa de texto **URL do Ponto de Extremidade**.
   3. Crie um arquivo **codificado em base 64** usando o certificado baixado.
      
      > [!TIP]
      > Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. Abra seu certificado codificado em Base 64, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado X.509**
   5. Clique em **Salvar alterações**.
9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar o logon único](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Jitbit Helpdesk, eles devem ser provisionados no Helpdesk.  
No caso do Jitbit Helpdesk, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:
1. Faça logon em seu locatário do **Jitbit Helpdesk** .
2. No menu na parte superior, clique em **Administração**.
   
   ![Administração](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")
3. Clique em **Usuários, empresas e permissões**.
   
   ![Usuários, empresas e permissões](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Users, companies and permissions")
4. Clique em **Adicionar usuário**.
   
   ![Adicionar usuário](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "Add user")
5. Na seção Criar, digite os dados da conta do Azure AD que você deseja provisionar nas seguintes caixas de texto: **Nome de usuário**, **Email**, **Nome** e **Sobrenome**
   
   ![Criação](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "Create")
6. Clique em **Criar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Jitbit Helpdesk ou APIs fornecidas pelo Jitbit Helpdesk para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

### <a name="to-assign-users-to-jitbit-helpdesk-perform-the-following-steps"></a>Para atribuir usuários ao Jitbit Helpdesk, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Jitbit Helpdesk**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "Assign users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


