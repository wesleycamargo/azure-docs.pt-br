---
title: "Tutorial: Integração do Azure Active Directory com o Lucidchart | Microsoft Docs"
description: "Saiba como usar o Lucidchart com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: c9a026e68526ea7f5fc5695349d04c724a8acd9b
ms.openlocfilehash: 6bb2f0672983fa3f4cb81291ee12306f21b9cebf
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Tutorial: Integração do Active Directory do Azure com o Lucidchart
O objetivo deste tutorial é mostrar a integração do Azure com o Lucidchart.  

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura com logon único (SSO) do Lucidchart habilitado

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Lucidchart poderão fazer logon único no aplicativo em seu site de empresa do Lucidchart (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos com o Lucidchart
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Cenário")

## <a name="enabling-the-application-integration-for-lucidchart"></a>Habilitando a integração de aplicativos com o Lucidchart
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Lucidchart.

**Para habilitar a integração de aplicativos com o Lucidchart, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Lucidchart**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Lucidchart** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Lucidchart](./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
   
## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Lucidchart com sua conta do AD do Azure usando federação baseada em protocolo SAML.

**Para configurar o logon único, execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração do aplicativo **Lucidchart**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Configurar Logon Único")
2. Na página **Como você deseja que os usuários façam logon no Lucidchart**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar Logon Único](./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Configurar Logon Único")
3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Entrada no Lucidchart**, digite a URL usada pelos usuários para fazer logon no seu aplicativo Lucidchart (por exemplo: "*https://chart2.office.lucidchart.com/saml/sso/azure*”) e clique em **Avançar**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no Lucidchart**, para baixar seus metadados, clique em **Baixar metadados** e, em seguida, salve o arquivo de dados localmente no computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Configurar Logon Único")
5. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Lucidchart como administrador.
6. No menu na parte superior, clique em **Equipe**.
   
   ![Equipe](./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Equipe")
7. Clique em **Aplicativo \> Gerenciar SAML**.
   
   ![Gerenciar SAML](./media/active-directory-saas-lucidchart-tutorial/IC791191.png "Gerenciar SAML")
8. Na página do diálogo **Configurações de Autenticação SAML** , realize as seguintes etapas:
   
   1. Selecione **Habilitar Autenticação SAML** e clique em **Opcional**.

  ![Configurações de Autenticação SAML](./media/active-directory-saas-lucidchart-tutorial/IC791192.png "Configurações de Autenticação SAML")
   2. Na caixa de texto **Domínio**, digite seu domínio e clique em **Alterar Certificado**.

  ![Alterar Certificado](./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Alterar Certificado")
   3. Abra o arquivo de metadados baixado, copie o conteúdo e cole-o na caixa de texto **Carregar Metadados** .

  ![Carregar Metadados](./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Carregar Metadados")
   4. Selecione **Adicionar Automaticamente novo usuário à equipe** e clique em **Salvar alterações**.

  ![Salvar Alterações](./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Salvar Alterações")
9. Selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar Logon Único**.
   
  ![Configurar Logon Único](./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Configurar Logon Único")
   
## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Não há nenhum item de ação para a configuração de provisionamento de usuário para o Lucidchart.  Quando um usuário atribuído tenta fazer logon no Lucidchart usando o painel de acesso, o Lucidchart verifica se o usuário existe.  

Se não houver conta de usuário ainda, ela é criada automaticamente pelo Lucidchart.

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao Lucidchart, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Lucidchart**, clique em **Atribuir usuários**.
   
   ![Atribuir Usuários](./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Atribuir Usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


