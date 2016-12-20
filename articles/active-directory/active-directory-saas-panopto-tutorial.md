---
title: "Tutorial: Integração do Azure Active Directory com o Panopto | Microsoft Docs"
description: "Saiba como usar o Panopto com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 89c88e23-93ce-4970-9baa-1104c4e8fe4a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6084b2761e33609310461d90868179770d7acc01


---
# <a name="tutorial-azure-active-directory-integration-with-panopto"></a>Tutorial: Integração do Active Directory do Azure com o Panopto
O objetivo deste tutorial é mostrar a integração do Azure com o Panopto.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do Panopto

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Panopto poderão fazer logon único no aplicativo em seu site de empresa do Panopto (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para Panopto
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-panopto-tutorial/IC777665.png "Scenario")

## <a name="enabling-the-application-integration-for-panopto"></a>Habilitando a integração de aplicativos para Panopto
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Panopto.

### <a name="to-enable-the-application-integration-for-panopto-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o Panopto, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-panopto-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-panopto-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-panopto-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-panopto-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **Panopto**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-panopto-tutorial/IC777666.png "Appkication Gallery")
7. No painel de resultados, selecione **Panopto** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Panopto](./media/active-directory-saas-panopto-tutorial/IC782936.png "Panopto")
   
   ## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Panopto com sua conta do AD do Azure usando federação baseada em protocolo SAML.  
Como parte deste procedimento, será necessário criar um arquivo de certificado codificado em base 64.  
Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **Panopto**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-panopto-tutorial/IC777667.png "Configure single sign-on")
2. Na página **Como você deseja que os usuários façam logon no Panopto**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-panopto-tutorial/IC777668.png "Configure single sign-on")
3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Entrada no Panopto**, digite sua URL usando o padrão "*https://\<nome-locatário\>. Panopto.com*" e clique em **Avançar**.
   
   ![Configurar a URL do aplicativo](./media/active-directory-saas-panopto-tutorial/IC777528.png "Configure app URL")
4. Na página **Configurar logon único no Panopto**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
   ![Configurar o logon único](./media/active-directory-saas-panopto-tutorial/IC777669.png "Configure single sign-on")
5. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do Panopto como administrador.
6. Na barra de ferramentas à esquerda, clique em **Sistema** e, em seguida, clique em **Provedores de Identidade**.
   
   ![System](./media/active-directory-saas-panopto-tutorial/IC777670.png "System")
7. Clique em **Adicionar Provedor**.
   
   ![Provedores de Identidade](./media/active-directory-saas-panopto-tutorial/IC777671.png "Identity Providers")
8. Na seção de provedor SAML, execute as seguintes etapas:
   
   ![Configuração do SaaS](./media/active-directory-saas-panopto-tutorial/IC777672.png "SaaS configuration")
   
   1. Na lista **Tipo de Provedor**, selecione **SAML20**
   2. Na caixa de texto **Nome da Instância** , digite um nome para a instância.
   3. Na caixa de texto **Descrição Amigável** , digite uma descrição amigável.
   4. No portal clássico do Azure, na página de diálogo **Configurar logon único no Panopto**, copie o valor da **URL do Emissor** e cole-o na caixa de texto **Emissor**.
   5. No portal clássico do Azure, na página de diálogo **Configurar logon único no Panopto**, copie o valor da **URL de SSO do SAML** e cole-o na caixa de texto **URL de Página de Devolução**.
   6. Crie um arquivo **codificado em base 64** usando o certificado baixado.  
      
      > [!TIP]
      > Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   7. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Chave Pública**
   8. Clique em **Salvar**.
      ![Salvar](./media/active-directory-saas-panopto-tutorial/IC777673.png "Save")
9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar o logon único](./media/active-directory-saas-panopto-tutorial/IC777674.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Não há nenhum item de ação para a configuração de provisionamento de usuário para o Panopto.  
Quando um usuário atribuído tenta fazer logon no Panopto usando o painel de acesso, o Panopto verifica se o usuário existe.  
Se ainda não houver nenhuma conta de usuário, ela será criada automaticamente pelo Panopto.

> [!NOTE]
> Você pode usar quaisquer outras ferramentas de criação de contas de usuários do Panopto ou APIs fornecidas pela Panopto para provisionar contas de usuário do AD do Azure.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-panopto-perform-the-following-steps"></a>Para atribuir usuários ao Panopto, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Panopto**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-panopto-tutorial/IC777675.png "Assign users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-panopto-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


