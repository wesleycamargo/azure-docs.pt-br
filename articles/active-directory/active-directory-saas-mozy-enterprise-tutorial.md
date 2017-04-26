---
title: "Tutorial: Integração do Azure Active Directory com o Mozy Enterprise | Microsoft Docs"
description: "Saiba como usar o Mozy Enterprise com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: b4c69228bd768a1f8b129338280a0baf5f980f0f
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Tutorial: Integração do Active Directory do Azure com o Mozy Enterprise
O objetivo deste tutorial é mostrar a integração do Azure com o Mozy Enterprise.  

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do Mozy Enterprise

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Mozy Enterprise poderão fazer SSO (logon único) no aplicativo em seu site de empresa do Mozy Enterprise (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos com o Mozy Enterprise
2. Configuração do SSO (logon único)
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-mozy-enterprise-tutorial/IC777308.png "Cenário")

## <a name="enabling-the-application-integration-for-mozy-enterprise"></a>Habilitando a integração de aplicativos com o Mozy Enterprise
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Mozy Enterprise.

**Para habilitar a integração de aplicativos com o Mozy Enterprise, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-mozy-enterprise-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-mozy-enterprise-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-mozy-enterprise-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-mozy-enterprise-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Mozy Enterprise**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-mozy-enterprise-tutorial/IC777309.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Mozy Enterprise** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Mozy Enterprise](./media/active-directory-saas-mozy-enterprise-tutorial/IC777310.png "Mozy Enterprise")
   
## <a name="configure-single-sign-on"></a>Configurar o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Mozy Enterprise com sua conta do AD do Azure usando federação baseada em protocolo SAML.  

Como parte desse procedimento, é necessário carregar um certificado codificado em base 64 no locatário do Mozy Enterprise. Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

**Para configurar o logon único, execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração de aplicativos do **Mozy Enterprise**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
   ![Configurar logon único](./media/active-directory-saas-mozy-enterprise-tutorial/IC771709.png "Configurar logon único")
2. Na página **Como você deseja que os usuários façam logon no Mozy Enterprise**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar logon único](./media/active-directory-saas-mozy-enterprise-tutorial/IC777311.png "Configurar logon único")
3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de entrada do Mozy Enterprise**, digite a URL usando o padrão “*https://\<tenant-name\>.Mozyenterprise.com*” e clique em **Avançar**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-mozy-enterprise-tutorial/IC777312.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no Mozy Enterprise**, para baixar o certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
   ![Configurar logon único](./media/active-directory-saas-mozy-enterprise-tutorial/IC777313.png "Configurar logon único")
5. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Mozy Enterprise como administrador.
6. Na seção **Configuração**, clique em **Política de Autenticação**.
   
   ![Política de autenticação](./media/active-directory-saas-mozy-enterprise-tutorial/IC777314.png "Política de autenticação")
7. Na seção **Política de Autenticação** , realize as seguintes etapas:
   
   ![Política de autenticação](./media/active-directory-saas-mozy-enterprise-tutorial/IC777315.png "Política de autenticação")
   
   1. Selecione **Serviço de Diretório** como **Provedor**.
   2. Selecione **Usar Push do LDAP**.
   3. Clique na guia **Autenticação SAML** .
   4. No portal clássico do Azure, na página da caixa de diálogo **Configurar logon único no Mozy Enterprise**, copie o valor da **URL da Solicitação de Autenticação** e cole-o na caixa de texto **URL de Autenticação**.
   5. No portal clássico do Azure, na página da caixa de diálogo **Configurar logon único no Mozy Enterprise**, copie o valor da **ID do Provedor de Identidade** e cole-o na caixa de texto **Ponto de Extremidade do SAML**.
   6. Crie um arquivo **codificado em base 64** usando o certificado baixado.  
   
      >[!TIP]
      >Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).
      >
      >

   7. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole todo o Certificado na caixa de texto **Certificado SAML** .
   8. Selecione **Habilitar SSO para que os Administradores façam logon com suas credenciais de rede**.
   9. Clique em **Salvar Alterações**.
8. No portal clássico do Azure, na página da caixa de diálogo **Configurar logon único no Mozy Enterprise**, selecione a confirmação de configuração de logon único e clique em **Concluir**.
   
   ![Configurar logon único](./media/active-directory-saas-mozy-enterprise-tutorial/IC777316.png "Configurar logon único")
   
## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário

Para permitir que os usuários do AD do Azure façam logon no Mozy Enterprise, eles devem ser provisionados no Mozy Enterprise.

No caso do Mozy Enterprise, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Mozy Enterprise** .
2. Clique em **Usuários** e em **Adicionar Novo Usuário**.
   
   ![Usuários](./media/active-directory-saas-mozy-enterprise-tutorial/IC777317.png "Usuários")
   
   >[!NOTE]
   >A opção **Adicionar Novo Usuário** será exibida apenas se **Mozy** estiver selecionado como o provedor em **Política de autenticação**. Se a autenticação SAML for configurada, os usuários serão adicionados automaticamente em seu primeiro logon por meio do Logon único.
   >
   > 
    
3. Na caixa de diálogo novo usuário, execute as seguintes etapas:
   
   ![Adicionar Usuários](./media/active-directory-saas-mozy-enterprise-tutorial/IC777318.png "Adicionar Usuários")
   
  1. Na lista **Escolher um Grupo** , selecione um grupo.
  2. Na lista **Wue tipo de usuário** , selecione um tipo.
  3. Na caixa de texto **Nome do Usuário** , digite o nome do usuário do AD do Azure.
  4. Na caixa de texto **Email** , digite o endereço de email do usuário do Azure AD.
  5. Selecione **Enviar email de instruções ao usuário**.
  6. Clique em **Adicionar Usuário(s)**.

     >[!NOTE]
     > Após a criação do usuário, um email será enviado ao usuário do AD do Azure com um link para confirmar a conta antes que ela se torne ativa.
     > 
     > 

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Mozy Enterprise ou as APIs fornecidas pelo Mozy Enterprise para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao Mozy Enterprise, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **Mozy Enterprise**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-mozy-enterprise-tutorial/IC777319.png "Atribuir usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-mozy-enterprise-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de SSO, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md

