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
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: e8dfd2e943143c88406c88d85fea6916be61a7ec
ms.openlocfilehash: 0fbadc98022f82782a39c61f8ce574cf30b1fbaa
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Tutorial: Integração do Active Directory do Azure com o Jitbit Helpdesk
O objetivo deste tutorial é mostrar a integração do Azure com o Jitbit Helpdesk.  

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do Jitbit Helpdesk

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Jitbit Helpdesk poderão fazer logon único no aplicativo em seu site de empresa do Jitbit Helpdesk (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

* Habilitando a integração de aplicativos com o Jitbit Helpdesk
* Configurando o logon único
* Configurando o provisionamento de usuários
* Atribuindo usuários

![Cenário](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "Cenário")

## <a name="enabling-the-application-integration-for-jitbit-helpdesk"></a>Habilitando a integração de aplicativos com o Jitbit Helpdesk
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Jitbit Helpdesk.

### <a name="to-enable-the-application-integration-for-jitbit-helpdesk-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o Jitbit Helpdesk, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Jitbit Helpdesk**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Jitbit Helpdesk** e clique em **Concluir** para adicionar o aplicativo.
   
   ![JitBit](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
   
## <a name="configure-single-sign-on"></a>Configurar o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Jitbit Helpdesk com sua conta do Azure AD usando federação baseada em protocolo SAML. .  

Como parte desse procedimento, será necessário criar um arquivo de certificado codificado em base&64;. Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

>[!IMPORTANT]
>Para poder configurar o logon único em seu locatário Jitbit Helpdesk, você precisa primeiro contatar o suporte técnico Jitbit Helpdesk para que esse recurso seja habilitado. 
> 

**Para configurar o logon único, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **Jitbit Helpdesk**, clique em **Configurar logon único** para abrir o diálogo** Configurar Logon Único**.
   
   ![Configurar logon único](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "Configurar logon único")
2. Na página **Como você deseja que os usuários façam logon no Jitbit Helpdesk**, selecione **Logon único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar logon único](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "Configurar logon único")
3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Entrada no Jitbit Helpdesk**, digite a URL usando o padrão "*https://\<nome-locatário\>.Jitbit.com*" e clique em **Avançar**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no Jitbit Helpdesk**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente como **c:\\Jitbit Helpdesk.cer**.
   
   ![Configurar logon único](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "Configurar logon único")
5. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Jitbit Helpdesk como administrador.
6. Na barra de ferramentas na parte superior, clique em **Administração**.
   
   ![Administração](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administração")
7. Clique em **Configurações gerais**.
   
   ![Usuários, empresas e permissões](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Usuários, empresas e permissões")
8. Na seção de configuração **Configurações de autenticação** , realize as seguintes etapas:
   
   ![Configurações de autenticação](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "Configurações de autenticação")
   
   1. Selecione **Habilitar logon único SAML 2.0** usando SSO (logon único) com **OneLogin**.
   2. No portal clássico do Azure, na página de diálogo **Configurar logon único no Jitbit Helpdesk**, copie o valor do **Ponto de Extremidade Iniciado pelo Provedor de Serviços** e cole-o na caixa de texto **URL do Ponto de Extremidade**.
   3. Crie um arquivo **codificado em base&64;** usando o certificado baixado. 
      >[!TIP]
      >Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o) 
      > 
   4. Abra seu certificado codificado em Base&64;, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado X.509**
   5. Clique em **Salvar alterações**.
9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar logon único](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "Configurar logon único")
   
## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Jitbit Helpdesk, eles devem ser provisionados no Helpdesk.  No caso do Jitbit Helpdesk, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Jitbit Helpdesk** .
2. No menu na parte superior, clique em **Administração**.
   
   ![Administração](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administração")
3. Clique em **Usuários, empresas e permissões**.
   
   ![Usuários, empresas e permissões](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Usuários, empresas e permissões")
4. Clique em **Adicionar usuário**.
   
   ![Adicionar usuário](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "Adicionar usuário")
5. Na seção Criar, digite os dados da conta do Azure AD que você deseja provisionar nas seguintes caixas de texto: **Nome de usuário**, **Email**, **Nome** e **Sobrenome**
   
   ![Criar](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "Criar")
6. Clique em **Criar**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Jitbit Helpdesk ou APIs fornecidas pelo Jitbit Helpdesk para provisionar as contas de usuário do AAD.
> 

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao Jitbit Helpdesk, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Jitbit Helpdesk**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "Atribuir usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


